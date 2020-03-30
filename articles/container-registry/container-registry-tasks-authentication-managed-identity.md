---
title: ACR 작업에서 관리되는 ID
description: Azure 컨테이너 레지스트리 작업에서 Azure 리소스에 대한 관리되는 ID를 활성화하여 태스크가 다른 개인 컨테이너 레지스트리를 포함한 다른 Azure 리소스에 액세스할 수 있도록 허용합니다.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111772"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>ACR 작업에서 Azure 관리 ID 사용 

[ACR 작업에서](container-registry-tasks-overview.md) [Azure 리소스에 대해 관리되는 ID를](../active-directory/managed-identities-azure-resources/overview.md) 사용하도록 설정하여 자격 증명을 제공하거나 관리할 필요 없이 작업이 다른 Azure 리소스에 액세스할 수 있도록 합니다. 예를 들어 관리되는 ID를 사용하여 작업 단계가 컨테이너 이미지를 다른 레지스트리로 가져오거나 푸시할 수 있도록 합니다.

이 문서에서는 Azure CLI를 사용하여 ACR 작업에서 사용자에게 할당되었거나 시스템 할당된 관리되는 ID를 사용하도록 설정하는 방법을 알아봅니다. Azure 클라우드 셸 또는 Azure CLI의 로컬 설치를 사용할 수 있습니다. 로컬에서 사용하려면 버전 2.0.68 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하십시오.

설명하기 위해 이 문서의 예제 명령은 [az acr 태스크 만들기를][az-acr-task-create] 사용하여 관리되는 ID를 가능하게 하는 기본 이미지 빌드 작업을 만듭니다. 관리되는 ID를 사용하여 ACR 태스크에서 보안 리소스에 액세스하는 샘플 시나리오는 다음을 참조하십시오.

* [크로스 레지스트리 인증](container-registry-tasks-cross-registry-authentication.md)
* [Azure 키 볼트에 저장된 비밀로 외부 리소스에 액세스](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>관리 ID를 사용하는 이유

Azure 리소스에 대한 관리되는 ID는 선택한 Azure 서비스에 Azure Active Directory에서 자동으로 관리되는 ID를 제공합니다. 작업 단계에서 자격 증명을 전달하지 않고 작업이 다른 보안 Azure 리소스에 액세스할 수 있도록 관리되는 ID로 ACR 작업을 구성할 수 있습니다.

관리 ID에는 다음과 같은 두 가지 유형이 있습니다.

* *사용자가 할당한 ID로*여러 리소스에 할당하고 원하는 기간 동안 유지될 수 있습니다. 사용자 할당 ID는 현재 미리 보기 중입니다.

* ACR 작업과 같은 특정 리소스에 고유하며 해당 리소스의 수명 동안 지속되는 *시스템 할당 ID입니다.*

ACR 작업에서 두 가지 유형의 ID를 활성화할 수 있습니다. 보안 주체와 마찬가지로 다른 리소스에 대한 ID 액세스 권한을 부여합니다. 작업이 실행되면 ID를 사용하여 액세스가 필요한 모든 작업 단계에서 리소스에 액세스합니다.

## <a name="steps-to-use-a-managed-identity"></a>관리되는 ID를 사용하는 단계

ACR 작업과 함께 관리되는 ID를 사용하려면 다음 상위 수준 단계를 따릅니다.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (선택 사항) 사용자 할당된 ID 만들기

사용자 할당된 ID를 사용하려는 경우 기존 ID를 사용하거나 Azure CLI 또는 기타 Azure 도구를 사용하여 ID를 만듭니다. 예를 들어 [az ID create][az-identity-create] 명령을 사용합니다. 

시스템에서 할당된 ID만 사용하려는 경우 이 단계를 건너뜁니다. ACR 작업을 만들 때 시스템 할당된 ID를 만듭니다.

### <a name="2-enable-identity-on-an-acr-task"></a>2. ACR 작업에서 ID 사용

ACR 작업을 만들 때 선택적으로 사용자 할당 된 ID, 시스템 할당 된 ID 또는 둘 다 를 사용 합니다. 예를 들어 Azure `--assign-identity` CLI에서 [az acr 태스크 만들기][az-acr-task-create] 명령을 실행할 때 매개 변수를 전달합니다.

시스템 할당 된 ID를 `--assign-identity` 사용 하려면 `assign-identity [system]`값 또는 없이 전달 합니다. 다음 예제 명령은 `hello-world` 이미지를 빌드하고 시스템에서 할당된 관리되는 ID를 활성화하는 공용 GitHub 리포지토리에서 Linux 작업을 만듭니다.

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

사용자 할당된 ID를 사용하려면 ID의 `--assign-identity` *리소스 ID* 값을 전달합니다. 다음 예제 명령은 `hello-world` 이미지를 빌드하고 사용자가 할당한 관리되는 ID를 활성화하는 공용 GitHub 리포지토리에서 Linux 작업을 만듭니다.

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

az ID [표시][az-identity-show] 명령을 실행하여 ID의 리소스 ID를 얻을 수 있습니다. 리소스 그룹 *myResourceGroup의* ID *myUserAssignedId에* 대한 리소스 ID는 다음과 같은 형식입니다. 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. 다른 Azure 리소스에 액세스할 수 있는 ID 권한을 부여합니다.

작업의 요구 사항에 따라 다른 Azure 리소스에 액세스할 수 있는 ID 권한을 부여합니다. 다음은 이러한 템플릿의 예입니다.

* Azure의 대상 컨테이너 레지스트리에 끌어오기, 푸시 및 끌어오기 또는 기타 사용 권한을 사용하여 관리되는 ID역할을 할당합니다. 레지스트리 역할의 전체 목록은 [Azure 컨테이너 레지스트리 역할 및 사용 권한을](container-registry-roles.md)참조하십시오. 
* 관리되는 ID를 Azure 키 자격 증명 모음에서 참조 할 수 있는 역할을 할당합니다.

Azure [CLI](../role-based-access-control/role-assignments-cli.md) 또는 기타 Azure 도구를 사용하여 리소스에 대한 역할 기반 액세스를 관리합니다. 예를 들어 [az 역할 할당 만들기][az-role-assignment-create] 명령을 실행하여 ID를 리소스에 역할로 할당합니다. 

다음 예제는 관리되는 ID에 컨테이너 레지스트리에서 가져올 수 있는 권한을 할당합니다. 명령은 작업 ID의 *주 ID와* 대상 레지스트리의 *리소스 ID를* 지정합니다.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (선택 사항) 작업에 자격 증명 추가

작업에 이미지를 다른 사용자 지정 레지스트리로 가져오거나 푸시하거나 다른 리소스에 액세스하기 위해 자격 증명이 필요한 경우 작업에 자격 증명을 추가합니다. az [acr 작업 자격 증명 추가][az-acr-task-credential-add] 명령을 실행하여 `--use-identity` 자격 증명을 추가하고 매개 변수를 전달하여 ID가 자격 증명에 액세스할 수 있음을 나타냅니다. 

예를 들어 Azure 컨테이너 레지스트리 *대상 레지스트리를*사용하여 인증할 시스템 할당 ID에 대한 `use-identity [system]`자격 증명을 추가하려면 다음을 전달합니다.

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

레지스트리 *대상 레지스트리로*인증하기 위해 사용자에게 할당된 ID에 대한 자격 `use-identity` 증명을 추가하려면 ID의 *클라이언트 ID* 값을 전달합니다. 예를 들어:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

az ID [표시][az-identity-show] 명령을 실행하여 ID의 클라이언트 ID를 얻을 수 있습니다. 클라이언트 ID는 양식의 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID입니다.

### <a name="5-run-the-task"></a>5. 작업 실행

관리되는 ID로 작업을 구성한 후 작업을 실행합니다. 예를 들어 이 문서에서 만든 작업 중 하나를 테스트하려면 [az acr 작업 실행][az-acr-task-run] 명령을 사용하여 수동으로 트리거합니다. 자동화된 추가 작업 트리거를 구성한 경우 작업이 자동으로 트리거되면 실행됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 ACR 작업에서 사용자 할당 또는 시스템 할당된 관리되는 ID를 사용하도록 설정하고 사용하는 방법을 배웠습니다. 관리되는 ID를 사용하여 ACR 작업에서 보안 리소스에 액세스하는 시나리오는 다음을 참조하십시오.

* [크로스 레지스트리 인증](container-registry-tasks-cross-registry-authentication.md)
* [Azure 키 볼트에 저장된 비밀로 외부 리소스에 액세스](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
