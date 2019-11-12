---
title: Azure Container Registry 태스크에서의 크로스 레지스트리 인증
description: Azure 리소스에 관리 되는 id를 사용 하 여 다른 개인 Azure 컨테이너 레지스트리에 액세스 하는 Azure Container Registry 작업 (ACR 작업) 구성
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: f2ffb42ce109f5e6f7186461f931b7f8da57ff32
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931509"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Azure로 관리 되는 id를 사용 하 여 ACR 작업에서의 크로스 레지스트리 인증 

[ACR 작업](container-registry-tasks-overview.md)에서 [Azure 리소스에 대해 관리 되는 id를 사용 하도록 설정할](container-registry-tasks-authentication-managed-identity.md)수 있습니다. 작업은 자격 증명을 제공 하거나 관리할 필요 없이 id를 사용 하 여 다른 Azure 리소스에 액세스할 수 있습니다. 

이 문서에서는 작업을 실행 하는 데 사용 된 것과 다른 레지스트리에서 이미지를 끌어오는 작업에서 관리 되는 id를 사용 하도록 설정 하는 방법에 대해 알아봅니다.

이 문서에서는 Azure 리소스를 만들기 위해 Azure CLI 버전 2.0.68 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="scenario-overview"></a>시나리오 개요

예제 작업은 다른 Azure container registry에서 기본 이미지를 가져와서 응용 프로그램 이미지를 빌드하고 푸시합니다. 기본 이미지를 꺼내려면 관리 되는 id를 사용 하 여 작업을 구성 하 고 적절 한 권한을 할당 합니다. 

이 예에서는 사용자 할당 또는 시스템 할당 관리 id를 사용 하는 단계를 보여 줍니다. 선택한 id는 조직의 요구 사항에 따라 달라 집니다.

실제 시나리오에서 조직은 모든 개발 팀이 응용 프로그램을 빌드하기 위해 사용 하는 기본 이미지 집합을 유지할 수 있습니다. 이러한 기본 이미지는 회사 레지스트리에 저장 되며, 각 개발 팀에는 끌어오기 권한만 있습니다. 

## <a name="prerequisites"></a>선행 조건

이 문서에서는 두 개의 Azure 컨테이너 레지스트리가 필요 합니다.

* 첫 번째 레지스트리를 사용 하 여 ACR 작업을 만들고 실행 합니다. 이 문서에서이 레지스트리의 이름은 *myregistry*입니다. 
* 두 번째 레지스트리는 이미지를 빌드하기 위해 작업에 사용 되는 기본 이미지를 호스팅합니다. 이 문서에서 두 번째 레지스트리의 이름은 *mybaseregistry*입니다. 

이후 단계에서를 사용자 고유의 레지스트리 이름으로 바꿉니다.

필요한 Azure 컨테이너 레지스트리가 아직 없는 경우 [빠른 시작: Azure CLI을 사용 하 여 개인 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조 하세요. 아직 레지스트리에 이미지를 푸시할 필요는 없습니다.

## <a name="prepare-base-registry"></a>기본 레지스트리 준비

먼저 작업 디렉터리를 만든 다음, 다음 콘텐츠를 사용 하 여 Dockerfile 이라는 파일을 만듭니다. 이 간단한 예제에서는 Docker 허브의 공용 이미지에서 node.js 기본 이미지를 빌드합니다.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
현재 디렉터리에서 [az acr build][az-acr-build] 명령을 실행 하 여 기본 이미지를 빌드하고 기본 레지스트리에 푸시합니다. 실제로 조직의 다른 팀 또는 프로세스에서 기본 레지스트리를 유지 관리할 수 있습니다.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>YAML 파일에서 작업 단계를 정의 합니다.

이 예제 [다단계 작업](container-registry-tasks-multi-step.md) 의 단계는 [yaml 파일](container-registry-tasks-reference-yaml.md)에 정의 되어 있습니다. 로컬 작업 디렉터리에 `helloworldtask.yaml` 라는 파일을 만들고 다음 내용을 붙여넣습니다. 빌드 단계의 `REGISTRY_NAME` 값을 기본 레지스트리의 서버 이름으로 업데이트 합니다.

```yml
version: v1.0.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

빌드 단계에서는 [Azure-Samples/acr-helloworld-노드](https://github.com/Azure-Samples/acr-build-helloworld-node.git) 리포지토리의 `Dockerfile-app` 파일을 사용 하 여 이미지를 작성 합니다. `--build-arg`는 기본 이미지를 풀 하는 기본 레지스트리를 참조 합니다. 성공적으로 빌드되면 이미지는 작업을 실행 하는 데 사용 되는 레지스트리에 푸시됩니다.

## <a name="option-1-create-task-with-user-assigned-identity"></a>옵션 1: 사용자 할당 id를 사용 하 여 작업 만들기

이 섹션의 단계에서는 작업을 만들고 사용자 할당 id를 사용 하도록 설정 합니다. 시스템 할당 id를 대신 사용 하도록 설정 하려면 [옵션 2: 시스템 할당 id를 사용 하 여 작업 만들기](#option-2-create-task-with-system-assigned-identity)를 참조 하세요. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>작업 만들기

다음 [az acr task create][az-acr-task-create] 명령을 실행 하 여 *helloworldtask* 작업을 만듭니다. 소스 코드 컨텍스트 없이 태스크가 실행 되 고 명령이 작업 디렉터리에서 `helloworldtask.yaml` 파일을 참조 합니다. `--assign-identity` 매개 변수는 사용자 할당 id의 리소스 ID를 전달 합니다. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>옵션 2: 시스템 할당 id를 사용 하 여 작업 만들기

이 섹션의 단계에서는 작업을 만들고 시스템 할당 id를 사용 하도록 설정 합니다. 사용자 할당 id를 대신 사용 하려면 [옵션 1: 사용자 할당 id를 사용 하 여 작업 만들기](#option-1-create-task-with-user-assigned-identity)를 참조 하세요. 

### <a name="create-task"></a>작업 만들기

다음 [az acr task create][az-acr-task-create] 명령을 실행 하 여 *helloworldtask* 작업을 만듭니다. 소스 코드 컨텍스트 없이 태스크가 실행 되 고 명령이 작업 디렉터리에서 `helloworldtask.yaml` 파일을 참조 합니다. 값이 없는 `--assign-identity` 매개 변수는 작업에 대해 시스템 할당 id를 사용 하도록 설정 합니다. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>기본 레지스트리에 id 끌어오기 권한 제공

이 섹션에서는 기본 레지스트리 인 *mybaseregistry*에서 끌어올 수 있는 관리 id 권한을 제공 합니다.

[Az acr show][az-acr-show] 명령을 사용 하 여 기본 레지스트리의 리소스 ID를 가져온 다음 변수에 저장 합니다.

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

[Az role assign create][az-role-assignment-create] 명령을 사용 하 여 id를 기본 레지스트리에 `acrpull` 역할에 할당 합니다. 이 역할에는 레지스트리에서 이미지를 끌어올 수 있는 권한만 있습니다.

```azurecli
az role assignment create --assignee $principalID --scope $baseregID --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>작업에 대상 레지스트리 자격 증명 추가

이제 [az acr task credential add][az-acr-task-credential-add] 명령을 사용 하 여 id의 자격 증명을 작업에 추가 합니다. 이렇게 하면 기본 레지스트리를 사용 하 여 인증할 수 있습니다. 작업에서 사용 하도록 설정 된 관리 id의 형식에 해당 하는 명령을 실행 합니다. 사용자 할당 id를 사용 하도록 설정한 경우 id의 클라이언트 ID를 사용 하 여 `--use-identity`를 전달 합니다. 시스템이 할당 한 id를 사용 하도록 설정한 경우 `--use-identity [system]`를 전달 합니다.

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

관리 id를 사용 하도록 설정한 태스크가 성공적으로 실행 되는지 확인 하려면 [az acr task run][az-acr-task-run] 명령을 사용 하 여 수동으로 작업을 트리거합니다. 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

태스크가 성공적으로 실행 되 면 출력은 다음과 유사 합니다.

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

[Az acr repository show-tags][az-acr-repository-show-tags] 명령을 실행 하 여 이미지가 작성 되어 *myregistry*에 성공적으로 푸시되는 지 확인 합니다.

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

예제 출력:

```console
cf10
```

## <a name="next-steps"></a>다음 단계

* [ACR 작업에서 관리 id를 사용 하도록 설정 하](container-registry-tasks-authentication-managed-identity.md)는 방법에 대해 자세히 알아보세요.
* [ACR 작업 YAML 참조](container-registry-tasks-reference-yaml.md) 를 참조 하세요.

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
