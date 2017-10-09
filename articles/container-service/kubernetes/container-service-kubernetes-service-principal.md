---
title: "Azure Kubernetes 클러스터의 서비스 주체 | Microsoft 문서"
description: "Azure Container Service에서 Kubernetes 클러스터에 대한 Azure Active Directory 서비스 주체를 만들고 관리합니다."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 14975454cbc0afcfbdbd3aa6b52983be4d4b1785
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>Container Service에서 Kubernetes 클러스터에 대한 Azure AD 서비스 주체 설정


Azure Container Service에서 Kubernetes 클러스터는 Azure API와 상호 작용하기 위해 [Azure Active Directory 서비스 주체](../../active-directory/develop/active-directory-application-objects.md)를 요구합니다. 서비스 주체는 [사용자 정의 경로](../../virtual-network/virtual-networks-udr-overview.md) 및 [계층 4 Azure Load Balancer](../../load-balancer/load-balancer-overview.md)와 같은 리소스를 동적으로 관리하는 데 필요합니다.


이 문서에서는 Kubernetes 클러스터에 대한 서비스 주체를 설정하기 위한 다양한 옵션을 보여 줍니다. 예를 들어 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 설정한 경우 [`az acs create`](/cli/azure/acs#create) 명령을 실행하여 Kubernetes 클러스터와 서비스 주체를 동시에 만들 수 있습니다 .


## <a name="requirements-for-the-service-principal"></a>서비스 주체에 대한 요구 사항

다음 요구 사항을 충족하는 기존 Azure AD 서비스 주체를 사용하거나 새 서비스 주체를 만들 수 있습니다.

* **범위**: 클러스터를 배포하는 데 사용하는 구독입니다.

* **역할**: **참여자**

* **클라이언트 암호**: 암호여야 합니다. 현재 인증서 인증을 위해 설정된 서비스 주체는 사용할 수 없습니다.

> [!IMPORTANT]
> 서비스 주체를 만들려면 Azure AD 테넌트에 응용 프로그램을 등록하고 구독의 역할에 해당 응용 프로그램을 할당할 수 있는 권한이 있어야 합니다. 필요한 권한이 있는지 확인하려면 [포털에서 확인합니다](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>옵션 1: Azure AD에서 서비스 주체 만들기

Kubernetes 클러스터를 배포하기 전에 Azure AD 서비스 주체를 만들려는 경우 Azure에서 여러 가지 방법을 제공합니다.

다음 예제 명령은 [Azure CLI 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)을 사용하여 이 작업을 수행하는 방법을 보여 줍니다. 또는 [Azure PowerShell](../../azure-resource-manager/resource-group-authenticate-service-principal.md), [포털](../../azure-resource-manager/resource-group-create-service-principal-portal.md) 또는 다른 방법을 사용하여 서비스 주체를 만들 수 있습니다.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

출력은 다음과 비슷합니다(여기서는 수정된 내용이 표시됨).

![서비스 주체 만들기](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

클러스터 배포에 대한 서비스 주체 매개 변수로 사용하는 **클라이언트 ID**(`appId`) 및 **클라이언트 비밀**(`password`)이 강조 표시됩니다.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Kubernetes 클러스터를 만들 때 서비스 주체 지정

Kubernetes 클러스터를 만들 때 기존 서비스 주체의 **클라이언트 ID**(응용 프로그램 ID의 경우 `appId`라고도 함) 및 **클라이언트 비밀**(`password`)을 매개 변수로 제공합니다. 서비스 주체가 이 문서의 시작 부분에 나오는 요구 사항을 충족하는지 확인합니다.

포털, [Azure CLI(명령줄 인터페이스) 2.0](container-service-kubernetes-walkthrough.md), [Azure Portal](../dcos-swarm/container-service-deployment.md) 또는 다른 방법을 사용하여 Kubernetes 클러스터를 배포할 때 이러한 매개 변수를 지정할 수 있습니다.

>[!TIP]
>**클라이언트 ID**를 지정하는 경우 서비스 주체의 `ObjectId`가 아니라 `appId`를 사용해야 합니다.
>

다음 예제에서는 Azure CLI 2.0에서 매개 변수를 전달하는 한 가지 방법을 보여 줍니다. 여기서는 [Kubernetes 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)을 사용합니다.

1. GitHub에서 템플릿 매개 변수 파일 `azuredeploy.parameters.json`을 [다운로드](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json)합니다.

2. 서비스 주체를 지정하려면 파일에 `servicePrincipalClientId` 및 `servicePrincipalClientSecret`의 값을 입력합니다. (또한 `dnsNamePrefix` 및 `sshRSAPublicKey`에 대한 고유한 값을 제공해야 합니다. 후자는 클러스터에 액세스하기 위한 SSH 공개 키입니다.) 파일을 저장합니다.

    ![서비스 주체 매개 변수 전달](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. `--parameters`를 사용하여 azuredeploy.parameters.json 파일의 경로를 설정하는 다음 명령을 실행합니다. 이 명령은 미국 서부 지역에서 `myResourceGroup`이라고 만든 리소스 그룹의 클러스터를 배포합니다.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>옵션 2: `az acs create`를 사용하여 클러스터를 만들 때 서비스 주체 생성

[`az acs create`](/cli/azure/acs#create) 명령을 실행하여 Kubernetes 클러스터를 만드는 경우 자동으로 서비스 주체를 생성하는 옵션이 있습니다.

다른 Kubernetes 클러스터 만들기 옵션과 마찬가지로 `az acs create`를 실행할 때 기존 서비스 주체에 대한 매개 변수를 지정할 수 있습니다. 그러나 이러한 매개 변수를 생략하면 Container Service에서 사용할 수 있도록 Azure CLI에서 하나의 서비스 주체를 자동으로 만듭니다. 이는 배포 중에 투명하게 발생합니다.

다음 명령은 Kubernetes 클러스터를 만들고 SSH 키와 서비스 주체 자격 증명을 모두 생성합니다.

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> 계정에 Azure AD 및 서비스 주체를 만들 수 있는 구독 권한이 없는 경우 명령은 `Insufficient privileges to complete the operation.`과 비슷한 오류를 생성합니다.
>

## <a name="additional-considerations"></a>추가 고려 사항

* 구독에 서비스 주체를 만들 수 있는 권한이 없는 경우 Azure AD 또는 구독 관리자에게 필요한 권한을 할당하도록 요청하거나 Azure Container Service에서 사용할 서비스 주체를 요청해야 할 수도 있습니다.

* Kubernetes에 대한 서비스 주체는 클러스터 구성의 일부입니다. 그러나 클러스터를 배포하는 데에는 이 ID를 사용하지 마세요.

* 모든 서비스 주체는 Azure AD 응용 프로그램과 연결됩니다. Kubernetes 클러스터에 대한 서비스 주체는 유효한 모든 Azure AD 응용 프로그램 이름(예: `https://www.contoso.org/example`)과 연결할 수 있습니다. 응용 프로그램에 대한 URL은 실제 끝점일 필요가 없습니다.

* 서비스 주체 **클라이언트 ID**를 지정하는 경우 `appId`(이 문서에서 표시한 대로) 또는 해당되는 `name` 서비스 주체(예: `https://www.contoso.org/example`)의 값을 사용할 수 있습니다.

* Kubernetes 클러스터의 마스터 및 에이전트 VM에서 서비스 주체 자격 증명은 /etc/kubernetes/azure.json 파일에 저장됩니다.

* `az acs create` 명령을 사용하여 서비스 주체를 자동으로 생성하는 경우 서비스 주체 자격 증명은 명령을 실행하는 데 사용되는 컴퓨터의 ~/.azure/acsServicePrincipal.json 파일에 기록됩니다.

* `az acs create` 명령을 사용하여 자동으로 서비스 주체를 생성하는 경우 서비스 주체는 동일한 구독에서 만들어진 [Azure 컨테이너 레지스트리](../../container-registry/container-registry-intro.md)를 사용하여 인증할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* 컨테이너 서비스 클러스터에서 [Kubernetes를 시작합니다](container-service-kubernetes-walkthrough.md).

* Kubernetes에 대한 서비스 주체 문제를 해결하려면 [ACS 엔진 설명서](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting)(영문)를 참조하세요.
