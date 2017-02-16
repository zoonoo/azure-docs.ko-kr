---
title: "Azure Kubernetes 클러스터의 서비스 주체 | Microsoft 문서"
description: "Kubernetes를 사용하여 Azure Container Service 클러스터에서 Azure Active Directory 서비스 주체를 만들고 관리합니다."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 24e12e4606a5ec4fabf7046fe9847123033bb70a
ms.openlocfilehash: 073a9e66ac68643b27ecdd44a4ecac3ad79ec218


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Azure Container Service의 Kubernetes 클러스터에 대한 Azure Active Directory 서비스 주체 정보



Azure Container Service에서 Kubernetes는 Azure API와 상호 작용하는 서비스 계정으로 [Azure Active Directory 서비스 주체](../active-directory/active-directory-application-objects.md)를 요구합니다. 서비스 주체는 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md) 및 계층 4 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)와 같은 리소스를 동적으로 관리하는 데 필요합니다.

이 문서에서는 Kubernetes 클러스터의 서비스 주체를 지정하는 다양한 옵션을 보여 줍니다. 예를 들어 [Azure CLI 2.0(미리 보기)](https://docs.microsoft.com/cli/azure/install-az-cli2)를 설치하고 설정한 경우 [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) 명령을 실행하여 Kubernetes 클러스터와 서비스 주체를 동시에 만들 수 있습니다 .

> [!NOTE]
> Azure Container Service의 Kubernetes 지원은 현재 미리 보기로 제공됩니다.


## <a name="requirements-for-the-service-principal"></a>서비스 주체에 대한 요구 사항

다음은 Azure Container Service의 Kubernetes 클러스터에서 Azure Active Directory 서비스 주체에 대한 요구 사항입니다. 

* **범위** - 클러스터가 배포된 Azure 구독

* **역할** - **참가자**

* **클라이언트 비밀** - 암호여야 합니다. 현재 인증서 인증을 위해 설정된 서비스 주체는 사용할 수 없습니다.

> [!NOTE]
> 모든 서비스 주체는 Azure Active Directory 응용 프로그램과 연결됩니다. Kubernetes 클러스터의 서비스 주체는 유효한 Azure Active Directory 응용 프로그램 이름으로 연결할 수 있습니다.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Kubernetes 클러스터의 서비스 주체 옵션

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>옵션 1: 서비스 주체 클라이언트 ID 및 클라이언트 비밀 전달

Kubernetes 클러스터를 만들 때 기존 서비스 주체의 **클라이언트 ID**(응용 프로그램 ID의 경우 종종 `appId`라고도 함) 및 **클라이언트 비밀**(`password`)을 매개 변수로 제공합니다. 기존 서비스 주체를 사용하는 경우 이전 섹션의 요구 사항을 충족해야 합니다. 서비스 주체를 만들어야 하는 경우 이 문서 뒷부분의 [서비스 주체 만들기](#create-a-service-principal-in-azure-active-directory)를 참조하세요.

포털, Azure CLI(Command-Line Interface) 또는 Azure PowerShell을 사용하여 [Kubernetes 클러스터를 배포](./container-service-deployment.md)할 때 이러한 매개 변수를 지정할 수 있습니다.

>[!TIP] 
>**클라이언트 ID**를 지정하는 경우 서비스 주체의 `ObjectId`가 아니라 `appId`를 사용해야 합니다.
>

다음 예제에서는 [Resource Manager 모드](../xplat-cli-connect.md)에서 [Azure CLI](../xplat-cli-install.md)로 매개 변수를 전달하는 한 가지 방법을 보여 줍니다. 여기서는 [Kubernetes 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)을 사용합니다.

1. GitHub에서 azuredeploy.parameters.json 템플릿 매개 변수 파일을 [다운로드](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json)합니다.

2. 서비스 주체를 지정하려면 파일에 `servicePrincipalClientId` 및 `servicePrincipalClientSecret`의 값을 입력합니다. (또한 `dnsNamePrefix` 및 `sshRSAPublicKey`에 대한 고유한 값을 제공해야 합니다. 후자는 클러스터에 액세스하기 위한 SSH 공개 키입니다.) 파일을 저장합니다.

    ![서비스 주체 매개 변수 전달](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. `-e` 매개 변수를 사용하여 azuredeploy.parameters.json 파일의 경로를 설정하는 다음 명령을 실행합니다. 이 명령은 `myResourceGroup`이라는 기존 리소스 그룹에 클러스터를 배포합니다.

    ```CLI
    azure group deployment create -n myClusterName -g myResourceGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" -e azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>옵션 2: Azure CLI 2.0 미리 보기를 사용하여 클러스터를 만들 때 서비스 주체 생성

[Azure CLI 2.0 (미리 보기)](https://docs.microsoft.com/cli/azure/install-az-cli2)를 설치하고 설정한 경우 [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) 명령을 실행하면 [클러스터를 만들](./container-service-create-acs-cluster-cli.md) 수 있습니다.

다른 Kubernetes 클러스터 만들기 옵션과 마찬가지로 `az acs create`를 실행할 때 기존 서비스 주체에 대한 매개 변수를 지정할 수 있습니다. 그러나 이러한 매개 변수를 생략하면 Azure Container Service에서 자동으로 서비스 주체를 만듭니다. 이는 배포 중에 투명하게 발생합니다. 

다음 명령은 Kubernetes 클러스터를 만들고 SSH 키와 서비스 주체 자격 증명을 모두 생성합니다.

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Azure Active Directory에 서비스 주체 만들기

Kubernetes 클러스터에서 사용할 서비스 주체를 Azure Active Directory에 만들려는 경우 Azure에서 여러 가지 방법을 제공합니다. 

다음 예제 명령은 [Azure CLI 2.0(미리 보기)](https://docs.microsoft.com/cli/azure/install-az-cli2)에서 이 작업을 수행하는 방법을 보여 줍니다. 또는 [Azure CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md), [ Azure PowerShell ](../azure-resource-manager/resource-group-authenticate-service-principal.md) 또는 [클래식 포털](../azure-resource-manager/resource-group-create-service-principal-portal.md)을 사용하여 서비스 주체를 만들 수 있습니다.

> [!IMPORTANT]
> 이 문서 앞부분의 서비스 주체에 대한 요구 사항을 검토합니다.
>

```console
az login

az account set --subscription="mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

그러면 다음과 비슷한 출력이 반환됩니다(여기서 수정된 내용이 표시됨).

![서비스 주체 만들기](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

클러스터 배포에 대한 서비스 주체 매개 변수로 사용하는 **클라이언트 ID**(`appId`) 및 **클라이언트 비밀**(`password`)이 강조 표시됩니다.


새 셸을 열고 서비스 주체를 확인하고 `appId`, `password` 및 `tenant`로 대체하여 다음 명령을 실행합니다.

```console 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>추가 고려 사항


* **클라이언트 ID** 서비스 주체를 지정하는 경우 `appId`의 값(이 문서에서 표시한 대로) 또는 해당 `name` 서비스 주체의 값(예: `https://www.contoso.org/example`)을 사용할 수 있습니다.

* `az acs create` 명령을 사용하여 서비스 주체를 자동으로 생성하는 경우 서비스 주체 자격 증명은 명령을 실행하는 데 사용되는 컴퓨터의 ~/.azure/acsServicePrincipal.json 파일에 기록됩니다.

* Kubernetes 클러스터의 마스터 및 노드 VM에서 서비스 주체 자격 증명은 /etc/kubernetes/azure.json 파일에 저장됩니다.

## <a name="next-steps"></a>다음 단계

* 컨테이너 서비스 클러스터에서 [Kubernetes를 시작합니다](container-service-kubernetes-walkthrough.md).



<!--HONumber=Dec16_HO3-->


