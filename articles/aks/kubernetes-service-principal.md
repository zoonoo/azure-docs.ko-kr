---
title: "Azure Kubernetes 클러스터의 서비스 주체 | Microsoft 문서"
description: "AKS에서 Kubernetes 클러스터에 대한 Azure Active Directory 서비스 주체를 만들기 및 관리"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a532c8f69bfb19d26538aafe7c74f062dee06d9f
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Azure Container Service(AKS)를 사용하는 서비스 주체

Azure API와 상호 작용하기 위해 AKS 클러스터에는 [Azure Active Directory 서비스 주체](../active-directory/develop/active-directory-application-objects.md)가 필요합니다. 서비스 주체는 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md) 및 [계층 4 Azure Load Balancer](../load-balancer/load-balancer-overview.md)와 같은 리소스를 동적으로 관리하는 데 필요합니다.

이 문서에서는 AKS의 Kubernetes 클러스터에 대한 서비스 주체를 설정하는 다양한 옵션을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 AKS 클러스터를 만들고 클러스터와 kubectl 연결을 설정했다고 가정합니다. 이러한 항목이 필요한 경우 [AKS 빠른 시작](./kubernetes-walkthrough.md)을 참조하세요.

Azure AD 서비스 주체를 만들려면 Azure AD 테넌트에 응용 프로그램을 등록하고 구독의 역할에 해당 응용 프로그램을 할당할 수 있는 사용 권한이 있어야 합니다. 필요한 사용 권한이 없으면 필요한 사용 권한을 할당하거나 Kubernetes 클러스터에 대한 서비스 주체를 미리 만들도록 Azure AD 또는 구독 관리자에 요청해야 합니다.

또한 Azure CLI 버전 2.0.20 이상이 설치되고 구성되어 있어야 합니다. 버전을 찾으려면 az --version을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-sp-with-aks-cluster"></a>AKS 클러스터를 사용하여 SP 만들기

`az aks create` 명령을 사용하여 AKS 클러스터를 배포하는 경우 서비스 주체를 자동으로 생성하는 옵션이 있습니다.

기존 서비스 주체를 지정하지 않았기 때문에 다음 예제에서 AKS 클러스터를 만들고, 클러스터에 대한 서비스 주체를 만듭니다. 이 작업을 완료하려면 사용자 계정에는 서비스 주체를 만드는 적절한 권한이 있어야 합니다. 

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>기존 SP 사용

AKS 클러스터에서 사용하기 위해 기존 Azure AD 서비스 주체를 사용하거나 미리 만들 수 있습니다. 서비스 주체 정보를 제공하도록 요구하는 Azure Portal에서 클러스터를 배포하는 경우에 유용합니다.

기존 서비스 주체를 사용하는 경우 다음 요구 사항을 충족해야 합니다.

- 범위: 클러스터를 배포하는 데 사용되는 구독입니다.
- 역할: 참여자
- 클라이언트 암호: 암호여야 합니다.

## <a name="pre-create-a-new-sp"></a>새 SP 미리 만들기

Azure CLI에서 서비스 주체를 만들려면 [az ad sp create-for-rbac]() 명령을 사용합니다.

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

출력은 다음과 유사합니다. `appId` 및 `password`를 기록해 둡니다. 이러한 값은 AKS 클러스터를 만들 때 사용됩니다.

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>기존 SP 사용

미리 만든 서비스 주체를 사용하는 경우 `az aks create` 명령에 대한 인수 값으로 `appId` 및 `password`를 입력합니다.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-princal <appId> ----client-secret <password>
```

Azure Portal에서 AKS 클러스터를 배포하는 경우 AKS 클러스터 구성 양식에 이러한 값을 입력합니다.

![Azure Vote로 이동하는 이미지](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>추가 고려 사항

AKS와 Azure AD 서비스 주체를 사용하는 경우 다음 사항에 유의하세요.

* Kubernetes에 대한 서비스 주체는 클러스터 구성의 일부입니다. 그러나 클러스터를 배포하는 데에는 이 ID를 사용하지 마세요.
* 모든 서비스 주체는 Azure AD 응용 프로그램과 연결됩니다. Kubernetes 클러스터에 대한 서비스 주체는 유효한 모든 Azure AD 응용 프로그램 이름(예: `https://www.contoso.org/example`)과 연결할 수 있습니다. 응용 프로그램에 대한 URL은 실제 끝점일 필요가 없습니다.
* 서비스 주체 **클라이언트 ID**를 지정하는 경우 `appId`(이 문서에서 표시한 대로) 또는 해당되는 `name` 서비스 주체(예: `https://www.contoso.org/example`)의 값을 사용할 수 있습니다.
* Kubernetes 클러스터의 마스터 및 노드 VM에서 서비스 주체 자격 증명은 /etc/kubernetes/azure.json 파일에 저장됩니다.
* `az aks create` 명령을 사용하여 서비스 주체를 자동으로 생성하는 경우 서비스 주체 자격 증명은 명령을 실행하는 데 사용되는 컴퓨터의 ~/.azure/acsServicePrincipal.json 파일에 기록됩니다.
* `az aks create` 명령을 사용하여 자동으로 서비스 주체를 생성하는 경우 서비스 주체는 동일한 구독에서 만들어진 [Azure 컨테이너 레지스트리](../container-registry/container-registry-intro.md)를 사용하여 인증할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Active Directory 서비스 주체에 대한 자세한 내용은 Azure AD 응용 프로그램 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [응용 프로그램 및 서비스 주체 개체](../active-directory/develop/active-directory-application-objects.md)
