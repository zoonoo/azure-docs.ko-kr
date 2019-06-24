---
title: 관리 되는 id를 사용 하 여 Azure 컨테이너 레지스트리 작업
description: Azure 리소스에 대 한 관리 되는 id를 할당 하 여 다른 개인 컨테이너 레지스트리를 비롯 한 Azure 리소스에 대 한 Azure Container Registry 작업 액세스를 제공 합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148040"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Azure를 사용 하 여 ACR 작업의 id 관리 

사용 하 여는 [Azure 리소스에 대 한 id 관리](../active-directory/managed-identities-azure-resources/overview.md) 없이 Azure container registry 또는 기타 Azure 리소스에 ACR 작업에서 인증을 제공 하거나 코드에서 자격 증명을 관리 하려면 필요 합니다. 예를 들어, 끌어오기 또는 작업의 일환으로 다른 registry에 컨테이너 이미지를 푸시 하려면 관리 되는 id를 사용 합니다.

이 문서에 알아봅니다 관리 되는 id에 대 한 자세한 내용은 방법:

> [!div class="checklist"]
> * 시스템 할당 id 또는 ACR 작업에서 사용자 할당 id를 사용 하도록 설정
> * 다른 Azure 컨테이너 레지스트리와 같은 Azure 리소스에 id 액세스 부여
> * 관리 되는 id를 사용 하 여 작업에서 리소스에 액세스 

이 문서에서는 Azure 리소스를 만들려면 Azure CLI 버전 2.0.66 실행을 해야 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="why-use-a-managed-identity"></a>관리 ID를 사용하는 이유

Azure 리소스에 대한 관리 ID는 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 구성할 수 있습니다 [특정 Azure 리소스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), 관리 되는 id를 사용 하 여 ACR 작업을 포함 합니다. 그런 다음, ID를 사용하여 코드 또는 스크립트에서 자격 증명을 전달하지 않고 다른 Azure 리소스에 액세스할 수 있습니다.

관리 ID에는 다음과 같은 두 가지 유형이 있습니다.

* *사용자 할당 ID*는 여러 리소스에 할당할 수 있으며 원하는 만큼 오랫동안 유지할 수 있습니다. 사용자 할당 ID는 현재 미리 보기 중입니다.

* A *시스템 관리 id*, ACR 작업을 같은 특정 리소스에 고유 하며 해당 리소스의 수명 동안 지속 됩니다.

관리 되는 id 사용 하 여 Azure 리소스를 설정한 후에 모든 보안 주체와 마찬가지로 다른 리소스 id 액세스를 부여 합니다. 예를 들어, 관리 되는 id를 역할을 할당 끌어오기, 푸시 및 끌어오기 또는 다른 사용 권한을 사용 하 여 Azure에서 개인 컨테이너 레지스트리에 합니다. (전체 레지스트리 역할 목록은 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.) 하나 이상의 리소스에는 ID 액세스 권한을 제공할 수 있습니다.

## <a name="create-container-registries"></a>컨테이너 레지스트리 만들기

이 자습서에 대 한 세 개의 컨테이너 레지스트리를 해야합니다.

* 첫 번째 레지스트리를 사용 하 여 만들고 ACR 작업을 실행 합니다. 이 문서에서는이 원본 레지스트리 이름이 *myregistry*합니다. 
* 두 번째와 세 번째 레지스트리는 첫 번째 예제에서는 작업 기반 이미지를 푸시 하려면 대상 레지스트리입니다. 이 문서에서는 대상 레지스트리 라고 *customregistry1* 하 고 *customregistry2*합니다.

이후 단계에서 고유한 레지스트리 이름으로 바꿉니다.

필요한 Azure 컨테이너 레지스트리를 아직 없는 경우 [빠른 시작: Azure CLI를 사용하여 프라이빗 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조하세요. 아직 레지스트리에 이미지를 푸시할 필요가 없습니다.

## <a name="example-task-with-a-system-assigned-identity"></a>예제: 시스템 할당 id 사용 하 여 작업

이 예제에서는 만드는 방법을 보여 줍니다.는 [다중 단계 작업](container-registry-tasks-multi-step.md) 시스템 할당 id를 사용 하 여 합니다. 작업 이미지를 빌드하고 id를 사용 하 여 이미지를 푸시하는 두 개의 대상 레지스트리를 사용 하 여 인증 합니다.

이 예제에서는 작업 단계에 정의 되어는 [YAML 파일](container-registry-tasks-reference-yaml.md) 라는 `testtask.yaml`합니다. 파일의 multipleRegistries 디렉터리에는 [acr 작업](https://github.com/Azure-Samples/acr-tasks) 샘플 리포지토리. 파일을 여기에 재현 되어 있습니다.

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>시스템 할당 id를 사용 하 여 작업 만들기

작업을 만듭니다 *여러 reg* 실행 하 여 [az acr 작업 만들기] [ az-acr-task-create] 명령입니다. 작업 컨텍스트가 샘플 리포지토리의 multipleRegistries 폴더 및 파일을 참조 하는 명령을 `testtask.yaml` 리포지토리에서 합니다. `--assign-identity` 추가 값이 없는 매개 변수를 태스크에 대 한 시스템 할당 id를 만듭니다. 이 작업을 수동으로 트리거할 해야 하지만 커밋이 리포지토리에 푸시되 또는 끌어오기 요청이 수행 될 때 실행할를 설정할 수 있습니다 설정 됩니다. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

명령 출력에는 `identity` 섹션에서는 형식의 id를 보여 줍니다. `SystemAssigned` 태스크에서 설정 됩니다. `principalId` id의 서비스 주체 ID입니다.

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

사용 합니다 [az acr 작업 표시] [ az-acr-task-show] 스토어로 명령은 `principalId` 후속 명령에서 사용 하는 변수에:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>두 개의 대상 컨테이너 레지스트리에 identity 푸시 권한 부여

이 섹션에서는 라는 두 개의 대상 레지스트리를 푸시하기 위해 시스템 할당 id 권한을 제공 *customregistry1* 하 고 *customregistry2*합니다.

먼저 사용 합니다 [az acr show] [ az-acr-show] 각 레지스트리 리소스 ID를 가져오고 Id 변수에 저장 하는 명령:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

사용 하 여는 [az 역할 할당 만들기] [ az-role-assignment-create] 명령 id를 할당 하는 `acrpush` 각 레지스트리에 역할입니다. 이 역할에는 컨테이너 레지스트리로 이미지 끌어오기 및 밀어넣기 권한을 있습니다.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>작업에 대상 레지스트리 자격 증명을 추가 합니다.

이제 사용 합니다 [az acr 작업 자격 증명 추가] [ az-acr-task-credential-add] 모두 대상 레지스트리를 사용 하 여 인증할 수 있는 작업에이 id의 자격 증명을 추가 하는 명령입니다.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>작업을 수동으로 실행

사용 하 여는 [az acr 작업 실행] [ az-acr-task-run] 수동으로 작업을 트리거하는 명령입니다. 합니다 `--set` 매개 변수를 사용 하는 두 개의 대상 레지스트리의 로그인 서버 이름을 작업 변수에 대 한 값으로 전달 `REGISTRY1` 및 `REGISTRY2`합니다.

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

출력은 다음과 비슷합니다.

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>예제: 사용자 할당 id 사용 하 여 작업

이 예제에서는 Azure key vault에서 비밀을 읽을 권한이 있는 사용자 할당 id를 만듭니다. 비밀을 읽고 이미지를 빌드, 이미지 태그를 읽을 Azure CLI로 로그인 하는 다단계 작업에이 id를 할당 합니다.

### <a name="create-a-key-vault-and-store-a-secret"></a>Key vault를 만들고 비밀을 저장

먼저 해야 할 경우 이라는 리소스 그룹을 만듭니다 *myResourceGroup* 에 *eastus* 다음을 사용 하 여 위치 [az 그룹 만들기] [ az-group-create]명령:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

사용 합니다 [az keyvault를 만듭니다] [ az-keyvault-create] key vault를 만드는 명령입니다. 고유 키 자격 증명 모음 이름을 지정 해야 합니다. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

사용 하 여 키 자격 증명 모음에 샘플 비밀을 저장 합니다 [az keyvault secret set] [ az-keyvault-secret-set] 명령:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

예를 들어, 다음 개인 이미지를 끌어올 수 있도록 개인 Docker 레지스트리를 사용 하 여 인증할 때 자격 증명을 저장 하는 것이 좋습니다.

### <a name="create-an-identity"></a>ID 만들기

라는 id를 만들고 *myACRTasksId* 사용 하 여 구독에는 [az identity 만듭니다] [ az-identity-create] 명령입니다. 컨테이너 레지스트리 또는 다른 키 자격 증명 모음을 만들려면 이전에 사용한 동일한 리소스 그룹을 사용할 수 있습니다.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

다음 단계에서 ID를 구성하려면 [az identity show][az-identity-show] 명령을 사용하여 ID의 리소스 ID 및 서비스 주체 ID를 변수에 저장합니다.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Keyvault 비밀을 읽을 id 액세스 부여

다음을 실행 합니다 [정책 설정 az keyvault] [ az-keyvault-set-policy] key vault에 액세스 정책을 설정 하는 명령입니다. 다음 예제에서는 사용자 할당 id를 key vault에서 비밀을 가져올 수 있습니다. 이 액세스는 다중 단계 작업을 성공적으로 실행 하려면 나중에 필요 합니다.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>레지스트리에 대 한 리소스 그룹에 identity 읽기 권한자 액세스 권한을 부여합니다

다음을 실행 합니다 [az 역할 할당 만들기] [ az-role-assignment-create] 명령 id를 읽기 권한자 역할 할당,이 경우 원본 레지스트리에 포함 된 리소스 그룹입니다. 이 역할 다중 단계 작업을 성공적으로 실행 하려면 나중에 필요 합니다.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>사용자 할당 id를 사용 하 여 작업 만들기

이제 만듭니다는 [다중 단계 작업](container-registry-tasks-multi-step.md) 사용자 할당 id를 할당 합니다. 이 예제에서는 태스크에 대 한 만들기는 [YAML 파일](container-registry-tasks-reference-yaml.md) 라는 `managed-identities.yaml` 로컬 작업 디렉터리에 다음 콘텐츠를 붙여 넣습니다. 주요 자격 증명 모음의 이름으로 파일에서 키 자격 증명 모음 이름을 대체 해야 합니다.

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

이 작업은 다음을 수행합니다.

* Key vault에서 비밀에 액세스할 수 있는지 확인 합니다. 이 단계는 데모용입니다. 실제 시나리오에서 개인 Docker 허브 리포지토리에 액세스 하려면 자격 증명을 가져오려면 작업 단계를 사용 해야 합니다.
* 빌드 및 푸시를 `mywebsite` 원본 레지스트리에 이미지입니다.
* 목록에 Azure CLI에 대 한 로그는 `my-website` 이미지 원본 레지스트리에 태그입니다.

호출 작업을 만듭니다 *msitask* 이전에 만든 사용자 할당 id의 리소스 ID를 전달 합니다. 이 예제에서는 작업에서 만든는 `managed-identities.yaml` 저장 한 파일을 로컬 작업 디렉터리에 있으므로 수동으로 트리거할 필요가 있습니다.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

명령 출력에는 `identity` 섹션에서는 형식의 id를 보여 줍니다. `UserAssigned` 태스크에서 설정 됩니다. `principalId` id의 서비스 주체 ID입니다.

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>작업을 수동으로 실행

사용 하 여는 [az acr 작업 실행] [ az-acr-task-run] 수동으로 작업을 트리거하는 명령입니다. `--set` 소스 레지스트리 이름의 작업에 전달할 매개 변수를 사용 합니다.

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

출력을 보여 줍니다 및 원본 레지스트리에서 이미지 태그를 읽을 수 id 사용 하 여 Azure CLI로 작업 로그 이미지를 성공적으로 빌드 및 푸시, 암호 확인 됩니다.

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 컨테이너 레지스트리 작업을 사용 하 여 관리 되는 id를 사용 하는 방법에 대 한 학습 및 방법:

> [!div class="checklist"]
> * 시스템 할당 id를 사용 하도록 설정 또는 ACR 작업에서 사용자 할당
> * 다른 Azure 컨테이너 레지스트리와 같은 Azure 리소스에 id 액세스 부여
> * 관리 되는 id를 사용 하 여 작업에서 리소스에 액세스  

* [Azure 리소스에 대한 관리 ID](/azure/active-directory/managed-identities-azure-resources/)에 대해 자세히 알아보세요.

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
