---
title: Azure Container Registry 작업과 함께 관리 되는 id 사용
description: Azure 리소스에 대 한 관리 되는 id를 할당 하 여 다른 개인 컨테이너 레지스트리를 비롯 한 Azure 리소스에 대 한 Azure Container Registry 태스크 액세스를 제공 합니다.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 46351af375ab4c6e59a3ddfba3c05c1e517fab0d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311529"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>ACR 작업에서 Azure 관리 id 사용 

[Azure 리소스에 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 코드에서 자격 증명을 제공 하거나 관리 하지 않고도 ACR 작업에서 azure container registry 또는 다른 azure 리소스에 인증 합니다. 예를 들어 관리 되는 id를 사용 하 여 작업의 한 단계로 컨테이너 이미지를 끌어오거나 다른 레지스트리로 푸시합니다.

이 문서에서는 관리 되는 id 및 방법에 대해 자세히 알아봅니다.

> [!div class="checklist"]
> * ACR 작업에서 시스템 할당 id 또는 사용자 할당 id를 사용 하도록 설정
> * 다른 Azure container registry와 같은 Azure 리소스에 대 한 id 액세스 권한 부여
> * 관리 id를 사용 하 여 작업에서 리소스에 액세스 

이 문서에서는 Azure 리소스를 만들기 위해 Azure CLI 버전 2.0.66 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="why-use-a-managed-identity"></a>관리 ID를 사용하는 이유

Azure 리소스에 대한 관리 ID는 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 관리 id를 사용 하 여 ACR 작업을 비롯 한 [특정 Azure 리소스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 구성할 수 있습니다. 그런 다음, ID를 사용하여 코드 또는 스크립트에서 자격 증명을 전달하지 않고 다른 Azure 리소스에 액세스할 수 있습니다.

관리 ID에는 다음과 같은 두 가지 유형이 있습니다.

* *사용자 할당 ID*는 여러 리소스에 할당할 수 있으며 원하는 만큼 오랫동안 유지할 수 있습니다. 사용자 할당 ID는 현재 미리 보기 중입니다.

* ACR 작업과 같이 특정 리소스에 대해 고유 하 고 해당 리소스의 수명 동안 지속 되는 *시스템 관리 id*입니다.

관리 id를 사용 하 여 Azure 리소스를 설정한 후에는 모든 보안 주체와 마찬가지로 id에 다른 리소스에 대 한 액세스 권한을 부여 합니다. 예를 들어 관리 되는 id를 풀, 푸시 및 끌어오기를 사용 하는 역할 또는 Azure의 개인 컨테이너 레지스트리에 할당 합니다. (전체 레지스트리 역할 목록은 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.) 하나 이상의 리소스에는 ID 액세스 권한을 제공할 수 있습니다.

## <a name="create-container-registries"></a>컨테이너 레지스트리 만들기

이 자습서에서는 다음과 같은 세 개의 컨테이너 레지스트리가 필요 합니다.

* 첫 번째 레지스트리를 사용 하 여 ACR 작업을 만들고 실행 합니다. 이 문서에서는이 원본 레지스트리의 이름이 *myregistry*입니다. 
* 두 번째 및 세 번째 레지스트리는 빌드하는 이미지를 푸시하는 첫 번째 예제 작업의 대상 레지스트리입니다. 이 문서에서 대상 레지스트리 이름은 *customregistry1* 및 *customregistry2*입니다.

이후 단계에서를 사용자 고유의 레지스트리 이름으로 바꿉니다.

필요한 Azure 컨테이너 레지스트리가 아직 없는 경우 빠른 시작을 참조 하세요 [. Azure CLI를 사용하여 프라이빗 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조하세요. 아직 레지스트리에 이미지를 푸시할 필요는 없습니다.

## <a name="example-task-with-a-system-assigned-identity"></a>예제: 시스템이 할당 한 id를 사용 하는 작업

이 예에서는 시스템 할당 id를 사용 하 여 [다단계 작업](container-registry-tasks-multi-step.md) 을 만드는 방법을 보여 줍니다. 작업은 이미지를 빌드한 다음 id를 사용 하 여 두 대상 레지스트리로 인증 하 고 이미지를 푸시합니다.

이 예제 작업의 단계는 라는 `testtask.yaml` [yaml 파일](container-registry-tasks-reference-yaml.md) 에 정의 되어 있습니다. 파일은 [acr-작업](https://github.com/Azure-Samples/acr-tasks) 샘플 리포지토리의 multipleRegistries 디렉터리에 있습니다. 파일은 다음 위치에서 재현 됩니다.

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>시스템이 할당 한 id를 사용 하 여 작업 만들기

다음 [az acr task create][az-acr-task-create] 명령을 실행 하 여 *여러 reg* 작업을 만듭니다. 작업 컨텍스트는 샘플 리포지토리의 multipleRegistries 폴더이 고 명령은 리포지토리의 파일 `testtask.yaml` 을 참조 합니다. 추가 `--assign-identity` 값이 없는 매개 변수는 태스크에 대 한 시스템 할당 id를 만듭니다. 이 작업이 설정 되어 있으므로 수동으로 트리거해야 하지만 커밋이 리포지토리에 푸시되는 경우 또는 끌어오기 요청이 만들어질 때 실행 되도록 설정할 수 있습니다. 

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

명령 출력에서 섹션은 `identity` 작업에 설정 된 형식의 `SystemAssigned` id를 보여 줍니다. 는 `principalId` id의 서비스 사용자 id입니다.

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

[Az acr task show][az-acr-task-show] 명령을 사용 하 여를 변수에 `principalId` 저장 하 고 이후 명령에서 사용할 수 있습니다.

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>두 대상 컨테이너 레지스트리에 id 푸시 권한 제공

이 섹션에서는 *customregistry1* 및 *customregistry2*라는 두 대상 레지스트리에 푸시할 시스템 할당 id 권한을 부여 합니다.

먼저 [az acr show][az-acr-show] 명령을 사용 하 여 각 레지스트리의 리소스 ID를 가져오고 변수에 id를 저장 합니다.

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

[Az role assign create][az-role-assignment-create] 명령을 사용 하 여 각 레지스트리에 역할의 `acrpush` id를 할당 합니다. 이 역할에는 컨테이너 레지스트리에 이미지를 끌어오거나 푸시할 수 있는 권한이 있습니다.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>작업에 대상 레지스트리 자격 증명 추가

이제 [az acr task credential add][az-acr-task-credential-add] 명령을 사용 하 여 id의 자격 증명을 작업에 추가 합니다. 이렇게 하면 두 대상 레지스트리를 모두 사용 하 여 인증할 수 있습니다.

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

### <a name="manually-run-the-task"></a>수동으로 작업 실행

[Az acr task run][az-acr-task-run] 명령을 사용 하 여 수동으로 작업을 트리거합니다. 매개 변수는 두 대상 레지스트리의 로그인 서버 이름을 작업 변수 `REGISTRY1` 및 `REGISTRY2`에 대 한 값으로 전달 하는 데 사용 됩니다. `--set`

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

## <a name="example-task-with-a-user-assigned-identity"></a>예제: 사용자 할당 id를 사용 하는 작업

이 예제에서는 Azure key vault에서 비밀을 읽을 수 있는 권한이 있는 사용자 할당 id를 만듭니다. 비밀을 읽고, 이미지를 작성 하 고 Azure CLI에 로그인 하 여 이미지 태그를 읽을 수 있도록이 id를 다단계 작업에 할당 합니다.

### <a name="create-a-key-vault-and-store-a-secret"></a>주요 자격 증명 모음을 만들고 비밀을 저장 합니다.

먼저 필요한 경우 다음 [az group create][az-group-create] 명령을 사용 하 여 *myresourcegroup* 이라는 이름의 리소스 그룹을 *e us* 위치에 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[Az keyvault create][az-keyvault-create] 명령을 사용 하 여 주요 자격 증명 모음을 만듭니다. 고유 키 자격 증명 모음 이름을 지정 해야 합니다. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

[Az keyvault secret set][az-keyvault-secret-set] 명령을 사용 하 여 주요 자격 증명 모음에 샘플 암호를 저장 합니다.

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

예를 들어 개인 이미지를 끌어올 수 있도록 개인 Docker 레지스트리로 인증 하기 위한 자격 증명을 저장할 수 있습니다.

### <a name="create-an-identity"></a>ID 만들기

[Az identity create][az-identity-create] 명령을 사용 하 여 구독에 *Myacrtasksid* 라는 id를 만듭니다. 이전에 사용 했던 것과 동일한 리소스 그룹을 사용 하 여 컨테이너 레지스트리 또는 주요 자격 증명 모음을 만들 수 있습니다.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

다음 단계에서 id를 구성 하려면 [az identity show][az-identity-show] 명령을 사용 하 여 id의 리소스 id 및 서비스 주체 id를 변수에 저장 합니다.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>비밀 자격 증명 모음에 id 액세스를 부여 하 여 비밀 읽기

다음 [az keyvault set-policy][az-keyvault-set-policy] 명령을 실행 하 여 key vault에 대 한 액세스 정책을 설정 합니다. 다음 예에서는 사용자 할당 id를 사용 하 여 키 자격 증명 모음에서 암호를 가져올 수 있습니다. 이 액세스는 나중에 다중 단계 작업을 성공적으로 실행 하는 데 필요 합니다.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>레지스트리에 대 한 리소스 그룹에 id 판독기 액세스 권한 부여

다음 [az role assign create][az-role-assignment-create] 명령을 실행 하 여 Id를 판독기 역할 (이 경우에는 원본 레지스트리를 포함 하는 리소스 그룹)에 할당 합니다. 이 역할은 나중에 다중 단계 작업을 성공적으로 실행 하는 데 필요 합니다.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>사용자 할당 id를 사용 하 여 작업 만들기

이제 [다중 단계 작업](container-registry-tasks-multi-step.md) 을 만들고 사용자 할당 id를 할당 합니다. 이 예제 작업의 경우 로컬 작업 디렉터리에 라는 `managed-identities.yaml` [yaml 파일](container-registry-tasks-reference-yaml.md) 을 만들고 다음 내용을 붙여넣습니다. 파일의 키 자격 증명 모음 이름을 키 자격 증명 모음의 이름으로 바꾸어야 합니다.

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

이 작업은 다음을 수행 합니다.

* 키 자격 증명 모음의 암호에 액세스할 수 있는지 확인 합니다. 이 단계는 데모용입니다. 실제 시나리오에서는 개인 Docker 허브 리포지토리에 액세스 하기 위한 자격 증명을 가져오는 작업 단계가 필요할 수 있습니다.
* 이미지를 `mywebsite` 빌드하고 소스 레지스트리에 푸시합니다.
* Azure CLI에 로그인 하 여 원본 레지스트리의 `my-website` 이미지 태그를 나열 합니다.

*Msitask* 라는 작업을 만들고 이전에 만든 사용자 할당 id의 리소스 ID를 전달 합니다. 이 예제 작업은 로컬 작업 디렉터리 `managed-identities.yaml` 에 저장 한 파일에서 만들어지므로 수동으로 트리거해야 합니다.

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

명령 출력에서 섹션은 `identity` 작업에 설정 된 형식의 `UserAssigned` id를 보여 줍니다. 는 `principalId` id의 서비스 사용자 id입니다.

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

### <a name="manually-run-the-task"></a>수동으로 작업 실행

[Az acr task run][az-acr-task-run] 명령을 사용 하 여 수동으로 작업을 트리거합니다. `--set` 매개 변수는 소스 레지스트리 이름을 작업에 전달 하는 데 사용 됩니다.

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

출력에는 보안이 확인 되 고, 이미지가 성공적으로 빌드되고 푸시되 며, 작업은 id로 Azure CLI에 로그인 하 여 원본 레지스트리에서 이미지 태그를 읽습니다.

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

이 문서에서는 Azure Container Registry 작업에서 관리 id를 사용 하는 방법과 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * ACR 작업에서 시스템 할당 id 또는 사용자 할당 사용
> * 다른 Azure container registry와 같은 Azure 리소스에 대 한 id 액세스 권한 부여
> * 관리 id를 사용 하 여 작업에서 리소스에 액세스  

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
