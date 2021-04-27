---
title: ACR 작업에서 관리되는 ID
description: 작업이 다른 개인 컨테이너 레지스트리를 포함하여 다른 Azure 리소스에 액세스할 수 있도록 Azure Container Registry 작업에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정합니다.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 19d63861a98884ff2f5103946c19e2226c4b14b7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781166"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>ACR 작업에서 Azure 관리 ID 사용 

[ACR 작업](container-registry-tasks-overview.md)에서 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정하면 자격 증명을 제공하거나 관리할 필요 없이 작업이 다른 Azure 리소스에 액세스할 수 있습니다. 예를 들어 관리되는 ID를 사용하여 작업 단계에서 컨테이너 이미지를 풀하거나 다른 레지스트리로 푸시할 수 있습니다.

이 문서에서는 Azure CLI를 사용하여 ACR 작업에서 사용자 할당 또는 시스템 할당 관리 ID를 사용하도록 설정하는 방법에 대해 알아봅니다. Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용할 수 있습니다. 로컬로 사용하려는 경우 2.0.68 이상 버전이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

설명을 위해 이 문서의 명령 예는 [az acr task create][az-acr-task-create]를 사용하여 관리 ID를 사용하도록 설정하는 기본 이미지 빌드 작업을 만듭니다. 관리 ID를 사용하여 ACR 작업에서 보안된 리소스에 액세스하는 샘플 시나리오는 다음을 참조하세요.

* [크로스 레지스트리 인증](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault에 저장된 비밀을 사용하여 외부 리소스에 액세스](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>관리 ID를 사용하는 이유

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 선택한 Azure 서비스에 제공합니다. ACR 작업을 관리되는 ID로 구성하면 작업 단계에서 자격 증명을 전달하지 않고도 작업에서 다른 보안된 Azure 리소스에 액세스할 수 있습니다.

관리 ID에는 다음과 같은 두 가지 유형이 있습니다.

* *사용자 할당 ID*: 여러 리소스에 할당하고 원하는 기간 동안 유지할 수 있습니다. 사용자 할당 ID는 현재 미리 보기 중입니다.

* *시스템 할당 ID*: ACR 작업과 같은 특정 리소스에 고유하며 해당 리소스의 수명 동안 지속됩니다.

ACR 작업에서 두 유형의 ID 중 하나 또는 둘 다 사용하도록 설정할 수 있습니다. 보안 주체와 마찬가지로 ID에 다른 리소스 액세스 권한을 부여합니다. 작업이 실행될 때 ID를 사용하여 액세스가 필요한 모든 작업 단계에서 리소스에 액세스합니다.

## <a name="steps-to-use-a-managed-identity"></a>관리 ID를 사용하는 단계

ACR 작업과 함께 관리 ID를 사용하려면 다음 개략적인 단계를 따릅니다.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (선택 사항) 사용자 할당 ID 만들기

사용자 할당 ID를 사용하려는 경우 기존 ID를 사용하거나 Azure CLI 또는 다른 Azure 도구를 사용하여 ID를 만듭니다. 예를 들어 [az identity create][az-identity-create] 명령을 사용합니다. 

시스템 할당 ID만 사용하려는 경우 이 단계를 건너뜁니다. ACR 작업을 만들 때 시스템 할당 ID를 만듭니다.

### <a name="2-enable-identity-on-an-acr-task"></a>2. ACR 작업에서 ID 사용

ACR 작업을 만들 때 필요에 따라 사용자 할당 ID, 시스템 할당 ID 또는 둘 다를 사용하도록 설정합니다. 예를 들어 Azure CLI에서 [az acr task create][az-acr-task-create] 명령을 실행할 때 `--assign-identity` 매개 변수를 전달합니다.

시스템 할당 ID를 사용하도록 설정하려면 값이 없는 `--assign-identity` 또는 `assign-identity [system]`를 전달합니다. 다음 명령 예는 `hello-world` 이미지를 빌드하고 시스템 할당 관리 ID를 사용하도록 설정하는 공용 GitHub 리포지토리에서 Linux 작업을 만듭니다.

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

사용자 지정 ID를 사용하도록 설정하려면 ID의 *리소스 ID* 값과 함께 `--assign-identity`를 전달합니다. 다음 명령 예는 `hello-world` 이미지를 빌드하고 사용자 할당 관리 ID를 사용하도록 설정하는 공용 GitHub 리포지토리에서 Linux 작업을 만듭니다.

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

[az identity show][az-identity-show] 명령을 실행하여 ID의 리소스 ID를 가져올 수 있습니다. 리소스 그룹 *myResourceGroup* 의 ID *myUserAssignedIdentity* 에 대한 리소스 ID는 다음 형식입니다. 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. ID에 다른 Azure 리소스에 대한 액세스 권한 부여

작업 요구 사항에 따라 ID에 다른 Azure 리소스에 대한 액세스 권한을 부여할 수 있습니다. 다음은 이러한 템플릿의 예입니다.

* 관리되는 ID에 Azure의 대상 컨테이너 레지스트리에 대한 풀, 푸시 및 풀 또는 기타 권한이 있는 역할을 할당합니다. 전체 레지스트리 역할 목록은 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요. 
* 관리되는 ID에 Azure 주요 자격 증명 모음에서 비밀 읽기에 대한 역할을 할당합니다.

[Azure CLI](../role-based-access-control/role-assignments-cli.md) 또는 기타 Azure 도구를 사용하여 리소스에 대한 역할 기반 액세스를 관리합니다. 예를 들어 [az role assignment create][az-role-assignment-create] 명령을 실행하여 ID에 리소스에 대한 역할을 할당합니다. 

다음 예에서는 관리되는 ID에 컨테이너 레지스트리에서 풀 권한을 할당합니다. 이 명령은 작업 ID의 *주체 ID* 와 대상 레지스트리의 *리소스 ID* 를 지정합니다.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (선택 사항) 작업에 자격 증명 추가

작업에 다른 사용자 지정 레지스트리로 이미지를 풀하거나 푸시하는 데 또는 다른 리소스에 액세스하는 데 자격 증명이 필요한 경우 작업에 자격 증명을 추가합니다. [az acr task credential add][az-acr-task-credential-add] 명령을 실행하여 자격 증명을 추가하고 `--use-identity` 매개 변수를 전달하여 ID가 자격 증명에 액세스할 수 있음을 표시합니다. 

예를 들어 시스템 할당 ID에 대한 자격 증명을 추가하여 Azure 컨테이너 레지스트리 *targetregistry* 로 인증하려면 `use-identity [system]`을 전달합니다.

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

사용자 지정 ID에 대한 자격 증명을 추가하여 레지스트리 *targetregistry* 로 인증하려면 ID의 *클라이언트 ID* 값과 함께 `use-identity`를 전달합니다. 예를 들면 다음과 같습니다.

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

[az identity show][az-identity-show] 명령을 실행하여 ID의 클라이언트 ID를 가져올 수 있습니다. 클라이언트 ID는 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 형식의 GUID입니다.

### <a name="5-run-the-task"></a>5. 작업 실행

관리 ID를 사용하여 작업을 구성한 후 작업을 실행합니다. 예를 들어 이 문서에서 만든 작업 중 하나를 테스트하려면 [az acr task run][az-acr-task-run] 명령을 사용하여 수동으로 트리거합니다. 자동화된 작업 트리거를 추가로 구성한 경우 작업이 자동으로 트리거될 때 실행됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 ACR 작업에서 사용자 할당 또는 시스템 할당 관리 ID를 사용하도록 설정하고 사용하는 방법을 배웠습니다. 관리 ID를 사용하여 ACR 작업에서 보안된 리소스에 액세스하는 시나리오는 다음을 참조하세요.

* [크로스 레지스트리 인증](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault에 저장된 비밀을 사용하여 외부 리소스에 액세스](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[azure-cli-install]: /cli/azure/install-azure-cli
