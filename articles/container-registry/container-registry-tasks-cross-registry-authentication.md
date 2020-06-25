---
title: ACR 작업에서 크로스 레지스트리 인증
description: Azure 리소스에 대해 관리 ID를 사용하여 다른 프라이빗 Azure 컨테이너 레지스트리에 액세스하도록 ACR 작업(Azure Container Registry Task)을 구성합니다.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76842499"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Azure 관리 ID를 사용하는 ACR 작업의 레지스트리 간 인증 

[ACR 작업](container-registry-tasks-overview.md)에서 [Azure 리소스에 대한 관리 ID를 사용](container-registry-tasks-authentication-managed-identity.md)하도록 설정할 수 있습니다. 이 작업은 ID를 사용하여 자격 증명을 제공하거나 관리할 필요 없이 다른 Azure 리소스에 액세스할 수 있습니다. 

이 문서에서는 작업에서 관리 ID를 사용하여 작업을 실행하는 데 사용된 것과 다른 레지스트리에서 이미지를 끌어오는 방법에 대해 알아봅니다.

Azure 리소스를 만들려면 이 문서에서는 Azure CLI 버전 2.0.68 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="scenario-overview"></a>시나리오 개요

예제 작업은 다른 Azure 컨테이너 레지스트리에서 기본 이미지를 끌어와 애플리케이션 이미지를 빌드하여 푸시합니다. 기본 이미지를 끌어오려면 관리 ID로 작업을 구성하고 적절한 사용 권한을 할당합니다. 

이 예에서는 사용자가 할당한 관리 ID 또는 시스템 할당 관리 ID를 사용하는 단계를 보여줍니다. 선택한 ID는 조직의 요구 사항에 따라 달라집니다.

실제 시나리오에서 조직은 모든 개발 팀이 애플리케이션을 빌드하기 위해 사용하는 기본 이미지 세트를 유지 관리할 수 있습니다. 이러한 기본 이미지는 회사 레지스트리에 저장되며, 각 개발 팀은 끌어오기 권한만 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 두 개의 Azure Container Registry가 필요합니다.

* 첫 번째 레지스트리를 사용하여 ACR 작업을 만들고 실행합니다. 이 문서에서 이 레지스트리의 이름은 *myregistry*입니다. 
* 두 번째 레지스트리는 이미지를 빌드하는 작업에 사용되는 기본 이미지를 호스팅합니다. 이 문서에서 두 번째 레지스트리의 이름은 *mybaseregistry*입니다. 

이후 단계에서 사용자 고유의 레지스트리 이름으로 바꿉니다.

필요한 Azure Container Registry가 아직 없는 경우 [빠른 시작: Azure CLI를 사용하여 프라이빗 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조하세요. 아직 레지스트리에 이미지를 푸시할 필요는 없습니다.

## <a name="prepare-base-registry"></a>기본 레지스트리 준비

먼저 작업 디렉터리를 만든 후, 다음 콘텐츠를 사용하여 Dockerfile이라는 파일을 만듭니다. 이 간단한 예제는 Docker Hub의 공용 이미지에서 Node.js 기본 이미지를 빌드합니다.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
현재 디렉터리에서 [az acr build][az-acr-build] 명령을 실행하여 기본 이미지를 빌드하고 기본 레지스트리에 푸시합니다. 실제로 조직의 다른 팀 또는 프로세스가 기본 레지스트리를 유지 관리할 수 있습니다.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>YAML 파일에서 작업 단계 정의

이 예제 [multi-step task](container-registry-tasks-multi-step.md)의 단계는 [YAML 파일](container-registry-tasks-reference-yaml.md)에서 정의됩니다. 로컬 작업 디렉터리에 `helloworldtask.yaml`이라는 파일을 만들고 다음 콘텐츠를 붙여넣습니다. 빌드 단계에서 `REGISTRY_NAME` 값을 기본 레지스트리의 서버 이름으로 업데이트합니다.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

빌드 단계는 [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) 리포지토리의 `Dockerfile-app` 파일을 사용하여 이미지를 빌드합니다. `--build-arg`는 기본 이미지를 끌어오는 기본 레지스트리를 참조합니다. 성공적으로 빌드된 이미지는 작업을 실행하는 데 사용되는 레지스트리에 푸시됩니다.

## <a name="option-1-create-task-with-user-assigned-identity"></a>옵션 1: 사용자 할당 ID로 작업 만들기

이 섹션의 단계에서는 작업을 만들고 사용자 할당 ID를 사용합니다. 대신 시스템 할당 ID를 사용하도록 설정하려면 [옵션 2: 시스템 할당 ID로 작업 만들기](#option-2-create-task-with-system-assigned-identity)를 참조하세요. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>작업 만들기

다음 [az acr task create][az-acr-task-create] 명령을 실행하여 *helloworldtask* 작업을 만듭니다. 소스 코드 컨텍스트 없이 작업이 실행되고 명령이 작업 디렉터리의 `helloworldtask.yaml` 파일을 참조합니다. `--assign-identity` 매개 변수는 사용자 할당 ID의 리소스 ID를 전달합니다. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>옵션 2: 시스템 할당 ID로 작업 만들기

이 섹션의 단계에서는 작업을 만들고 시스템 할당 ID를 사용합니다. 대신 사용자 할당 ID를 사용하도록 설정하려면 [옵션 1: 사용자 할당 ID로 작업 만들기](#option-1-create-task-with-user-assigned-identity)를 참조하세요. 

### <a name="create-task"></a>작업 만들기

다음 [az acr task create][az-acr-task-create] 명령을 실행하여 *helloworldtask* 작업을 만듭니다. 소스 코드 컨텍스트 없이 작업이 실행되고 명령이 작업 디렉터리의 `helloworldtask.yaml` 파일을 참조합니다. 값이 없는 `--assign-identity` 매개 변수는 작업에서 시스템 할당 ID를 사용합니다. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>기본 레지스트리에 ID 끌어오기 권한 부여

이 섹션에서는 기본 레지스트리  *mybaseregistry*에서 끌어올 수 있는 관리 ID 권한을 부여합니다.

[az acr show][az-acr-show] 명령을 사용하여 기본 레지스트리의 리소스 ID를 가져와서 이를 변수에 저장합니다.

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] 명령을 사용하여 기본 레지스트리에 `acrpull` 역할 ID를 할당합니다. 이 역할에는 레지스트리에서 이미지를 끌어올 수 있는 권한만 있습니다.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>작업에 대상 레지스트리 자격 증명 추가

이제 [az acr task credential add][az-acr-task-credential-add] 명령을 사용하여 해당 작업에서 ID의 자격 증명을 사용하여 기본 레지스트리로 인증할 수 있습니다. 작업에서 사용하도록 설정된 관리 ID의 형식에 해당하는 명령을 실행합니다. 사용자 할당 ID를 사용하는 경우 ID의 클라이언트 ID를 사용하여 `--use-identity`를 전달합니다. 시스템 할당 ID를 사용한 경우 `--use-identity [system]`를 전달합니다.

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>수동으로 작업 실행

관리 ID를 사용하는 작업이 성공적으로 실행되는지 확인하려면 [az acr task run][az-acr-task-run] 명령을 사용하여 작업을 수동으로 트리거합니다. 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

작업이 성공적으로 실행되면 출력 내용은 다음과 유사합니다.

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

[az acr repository show-tags][az-acr-repository-show-tags] 명령을 실행하여 이미지가 빌드되어 *myregistry*에 성공적으로 푸시되었는지 확인합니다.

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

예제 출력:

```console
cf10
```

## <a name="next-steps"></a>다음 단계

* [ACR 작업에서 관리 ID 사용](container-registry-tasks-authentication-managed-identity.md)에 대해 자세히 알아보세요.
* [ACR 작업 YAML 참조](container-registry-tasks-reference-yaml.md)를 참조하세요.

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
