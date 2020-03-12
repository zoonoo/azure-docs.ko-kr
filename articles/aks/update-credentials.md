---
title: AKS(Azure Kubernetes Service) 클러스터의 자격 증명 다시 설정
description: AKS (Azure Kubernetes Service) 클러스터에 대 한 서비스 주체 또는 AAD 응용 프로그램 자격 증명을 업데이트 또는 다시 설정 하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 5dab9a778653d2ec6e32ddb3833ddcf6a95cae13
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096110"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에 대 한 자격 증명 업데이트 또는 순환

기본적으로 AKS 클러스터는 만료 시간이 1년인 서비스 주체로 만들어집니다. 만료 날짜가 가까워지면 자격 증명을 다시 설정하여 서비스 주체를 추가 기간 동안 연장할 수 있습니다. 정의된 보안 정책의 일부로 자격 증명을 업데이트하거나 회전할 수도 있습니다. 이 문서에서는 AKS 클러스터의 이 자격 증명을 업데이트하는 방법을 자세히 설명합니다.

[AKS 클러스터를 Azure Active Directory와 통합][aad-integration]하 고 클러스터에 대 한 인증 공급자로 사용할 수도 있습니다. 이 경우 클러스터, AAD 서버 앱 및 AAD 클라이언트 앱에 대해 생성 된 id가 2 개 더 있으므로 해당 자격 증명을 다시 설정할 수도 있습니다. 

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.65 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>AKS 클러스터에 대 한 새 서비스 주체를 업데이트 하거나 만듭니다.

AKS 클러스터의 자격 증명을 업데이트하려면 다음을 선택하면 됩니다.

* 클러스터에서 사용하는 기존 서비스 주체의 자격 증명을 업데이트하거나,
* 서비스 주체를 만들고 이 새 자격 증명을 사용하도록 클러스터를 업데이트합니다.

### <a name="reset-existing-service-principal-credential"></a>기존 서비스 사용자 자격 증명 다시 설정

기존 서비스 사용자에 대 한 자격 증명을 업데이트 하려면 [az aks show][az-aks-show] 명령을 사용 하 여 클러스터의 서비스 사용자 ID를 가져옵니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myAKSCluster* 클러스터의 ID를 가져옵니다. 서비스 사용자 ID는 추가 명령에서 사용할 *SP_ID* 라는 변수로 설정 됩니다.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

서비스 사용자 ID를 포함 하는 변수 집합을 사용 하 여 이제 [az ad sp credential reset][az-ad-sp-credential-reset]을 사용 하 여 자격 증명을 다시 설정 합니다. 다음 예제에서는 Azure 플랫폼에서 서비스 주체의 새 보안 비밀을 생성해보겠습니다. 이 새 보안 비밀도 변수로 저장됩니다.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

이제 [새 서비스 주체 자격 증명을 사용 하 여 AKS 클러스터](#update-aks-cluster-with-new-service-principal-credentials)를 계속 업데이트 합니다. 이 단계는 AKS 클러스터에 대 한 서비스 사용자 변경 내용을 반영 하기 위해 필요 합니다.

### <a name="create-a-new-service-principal"></a>새 서비스 주체 만들기

이전 섹션에서 기존 서비스 주체 자격 증명을 업데이트하도록 선택한 경우 이 단계를 건너뜁니다. [새 서비스 주체 자격 증명을 사용 하 여 AKS 클러스터](#update-aks-cluster-with-new-service-principal-credentials)를 계속 업데이트 합니다.

서비스 주체를 만든 다음 이러한 새 자격 증명을 사용 하도록 AKS 클러스터를 업데이트 하려면 [az ad sp create-rbac][az-ad-sp-create] 명령을 사용 합니다. 다음 예제에서 `--skip-assignment` 매개 변수는 추가 기본 할당이 할당되는 것을 방지합니다.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

다음 예제와 유사하게 출력됩니다. 사용자 고유의 `appId` 및 `password`를 기록해 둡니다. 해당 값은 다음 단계에서 사용됩니다.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

이제 다음 예제와 같이 사용자 고유의 [az ad sp create-rbac][az-ad-sp-create] 명령의 출력을 사용 하 여 서비스 사용자 ID 및 클라이언트 암호에 대 한 변수를 정의 합니다. *SP_ID*는 사용자의 ‘앱 ID’이고, *SP_SECRET*은 사용자의 ‘암호’입니다.

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

이제 [새 서비스 주체 자격 증명을 사용 하 여 AKS 클러스터](#update-aks-cluster-with-new-service-principal-credentials)를 계속 업데이트 합니다. 이 단계는 AKS 클러스터에 대 한 서비스 사용자 변경 내용을 반영 하기 위해 필요 합니다.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>새 서비스 주체 자격 증명을 사용 하 여 AKS 클러스터 업데이트

기존 서비스 사용자에 대 한 자격 증명을 업데이트 하거나 서비스 주체를 만들지 여부와 상관 없이 이제 [az AKS][az-aks-update-credentials] AKS 명령을 사용 하 여 새 자격 증명으로 클러스터를 업데이트 합니다. *--service-principal* 및 *--client-secret*의 변수는 다음과 같이 사용됩니다.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

서비스 주체 자격 증명이 AKS에서 업데이트되는 데 몇 분 정도 걸립니다.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>새 AAD 응용 프로그램 자격 증명으로 AKS 클러스터 업데이트

[Aad 통합 단계][create-aad-app]를 수행 하 여 새 aad 서버 및 클라이언트 응용 프로그램을 만들 수 있습니다. 또는 [서비스 사용자 재설정에 대 한 것과 동일한 방법으로](#reset-existing-service-principal-credential)기존 AAD 응용 프로그램을 다시 설정 합니다. 그 후에는 동일한 [az aks update-credentials][az-aks-update-credentials] 명령을 사용 하지만 *--reset-AAD* 변수를 사용 하 여 클러스터 AAD 응용 프로그램 자격 증명을 업데이트 하기만 하면 됩니다.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터 자체와 AAD 통합 응용 프로그램의 서비스 주체를 업데이트 했습니다. 클러스터 내에서 작업에 대 한 id를 관리 하는 방법에 대 한 자세한 내용은 [AKS의 인증 및 권한 부여에 대 한 모범 사례][best-practices-identity]를 참조 하세요.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
