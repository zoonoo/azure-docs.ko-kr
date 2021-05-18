---
title: 템플릿을 사용한 빠른 작업 실행
description: Azure Resource Manager 템플릿을 사용하여 이미지를 빌드하기 위한 ACR 작업 실행 큐
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: af7bebc311f81bb489fcc8be419f167ff6f9460a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781238"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용한 ACR 작업 실행

[ACR 작업](container-registry-tasks-overview.md)은 컨테이너 수명 주기에 걸쳐 컨테이너 이미지를 관리하고 수정할 수 있는 Azure Container Registry 내부 기능 모음입니다. 

이 문서에서는 [az acr build][az-acr-build] 명령을 사용하여 수동으로 만들 수 있는 것과 유사하게 빠른 작업 실행을 큐에 대기하는 Azure Resource Manager 템플릿 예제를 보여 줍니다.

작업 실행을 큐에 대기하는 Resource Manager 템플릿은 자동화 시나리오에서 유용하며 `az acr build`의 기능을 확장합니다. 예를 들면 다음과 같습니다.

* 템플릿을 사용하여 컨테이너 레지스트리를 만들고 작업 실행을 즉시 큐에 대기하여 컨테이너 이미지를 빌드하고 푸시합니다.
* Azure 리소스에 대한 관리 ID와 같이 빠른 작업 실행에서 사용할 수 있는 추가 리소스를 만들거나 사용합니다.

## <a name="limitations"></a>제한 사항

* 작업 실행의 [원본 위치로](container-registry-tasks-overview.md#context-locations) GitHub 리포지토리와 같은 원격 컨텍스트를 지정해야 합니다. 로컬 원본 컨텍스트는 사용할 수 없습니다.
* 관리 ID를 사용하여 작업을 실행하는 경우 *사용자가 할당한* 관리 ID만 허용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* **GitHub 계정** - 아직 계정이 없는 경우 https://github.com 에서 계정을 만듭니다. 
* **포크 샘플 리포지토리** -여기에 표시된 작업 예제의 경우 GitHub UI를 사용하여 다음 샘플 리포지토리를 사용자의 GitHub 계정 https://github.com/Azure-Samples/acr-build-helloworld-node 으로 포크합니다. 이 리포지토리에는 작은 컨테이너 이미지를 빌드하기 위한 샘플 Dockerfile 및 소스 코드가 포함되어 있습니다.

## <a name="example-create-registry-and-queue-task-run"></a>예제: 레지스트리 만들기 및 큐 작업 실행

이 예제에서는 [샘플 템플릿](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild)을 사용하여 컨테이너 레지스트리를 만들고 이미지를 빌드하고 푸시하는 작업 실행을 큐에 대기합니다. 

### <a name="template-parameters"></a>템플릿 매개 변수

이 예제에서는 다음 템플릿 매개 변수에 대해 값을 제공합니다.

|매개 변수  |값  |
|---------|---------|
|registryName     |만든 레지스트리의 고유한 이름         |
|리포지토리     |빌드 작업에 대한 대상 리포지토리        |
|taskRunName     |이미지 태그를 지정하는 작업 실행의 이름 |
|sourceLocation     |빌드 작업에 대한 원격 컨텍스트(예: https://github.com/Azure-Samples/acr-build-helloworld-node )입니다. 리포지토리 루트의 Dockerfile은 작은 Node.js 웹앱에 대한 컨테이너 이미지를 빌드합니다. 원하는 경우 리포지토리의 포크를 빌드 컨텍스트로 사용합니다.         |

### <a name="deploy-the-template"></a>템플릿 배포

[az deployment group create][az-deployment-group-create] 명령을 사용하여 템플릿을 배포합니다. 이 예제에서는 *helloworld-node:testrun* 이미지를 빌드하여 *mycontainerregistry* 라는 레지스트리로 푸시합니다.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

이전 명령은 명령줄에서 매개 변수를 전달합니다. 원하는 경우 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)로 전달합니다.

### <a name="verify-deployment"></a>배포 확인

배포가 성공적으로 완료되면 [az acr repository show 태그][az-acr-repository-show-tags]를 실행하여 이미지가 빌드되었는지 확인합니다.

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

출력:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>실행 로그 보기

작업 실행에 대한 세부 정보를 보려면 실행 로그를 확인합니다.

먼저 [az acr task list][az-acr-task-list-runs]를 사용하여 실행 ID를 가져옵니다.
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

출력은 다음과 비슷합니다.

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

[Az acr task logs][az-acr-task-logs]를 실행하여 실행 ID(이 경우 *c a 1*)에 대한 작업 실행 로그를 확인합니다.

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

출력에는 작업 실행 로그가 표시됩니다.

Azure Portal에서도 작업 실행 로그를 볼 수 있습니다. 

1. 컨테이너 레지스트리로 이동
2. **서비스** 에서 **작업** > **실행** 을 선택합니다.
3. 실행 ID(이 경우 *ca1*)를 선택합니다. 

포털에는 작업 실행 로그가 표시됩니다.

## <a name="example-task-run-with-managed-identity"></a>예: 관리 ID를 사용한 작업 실행

[샘플 템플릿](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity)을 사용하여 사용자가 할당한 관리 ID를 사용하도록 설정하는 작업 실행을 큐에 대기합니다. 작업을 실행하는 동안 ID는 다른 Azure 컨테이너 레지스트리에서 이미지를 끌어오도록 인증합니다. 

이 시나리오는 Azure [관리 ID를 사용한 ACR 작업 내 레지스트리 간 인증](container-registry-tasks-cross-registry-authentication.md)과 유사합니다. 예를 들어 조직은 여러 개발 팀에서 액세스하는 베이스 이미지를 사용하여 중앙 집중형 레지스트리를 유지 관리할 수 있습니다.

### <a name="prepare-base-registry"></a>기본 레지스트리 준비

데모용으로 별도의 컨테이너 레지스트리를 베이스 레지스트리로 만들고 Docker Hub에서 끌어온 Node.js 베이스 이미지를 푸시합니다.

1. 베이스 이미지를 저장하는 두 번째 컨테이너 레지스트리(예: *mybaseregistry*)를 만듭니다.
1. Docker Hub에서 `node:9-alpine` 이미지를 끌어와서 베이스 레지스트리에 대한 태그를 만들고 베이스 레지스트리에 푸시합니다.

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>새 Dockerfile 만들기

베이스 레지스트리에서 베이스 이미지를 가져오는 Dockerfile을 만듭니다. GitHub 리포지토리의 로컬 포크에서 다음 단계를 수행합니다(예: `https://github.com/myGitHubID/acr-build-helloworld-node.git` ).

1. GitHub UI에서 **새 파일 만들기** 를 선택합니다.
1. 파일 이름을 *Dockerfile-test* 로 지정하고 다음 콘텐츠를 붙여넣습니다. *Mybaseregistry* 에 대한 레지스트리 이름을 대체합니다.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. **새 파일 커밋** 을 선택합니다.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>기본 레지스트리에 ID 끌어오기 권한 부여

베이스 레지스트리  *mybaseregistry* 에서 끌어올 수 있는 관리 ID 권한을 부여합니다.

[az acr show][az-acr-show] 명령을 사용하여 기본 레지스트리의 리소스 ID를 가져와서 이를 변수에 저장합니다.

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] 명령을 사용하여 기본 레지스트리에 Acrpull 역할 ID를 할당합니다. 이 역할에는 레지스트리에서 이미지를 끌어올 수 있는 권한만 있습니다.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>템플릿 매개 변수

이 예제에서는 다음 템플릿 매개 변수에 대해 값을 제공합니다.

|매개 변수  |값  |
|---------|---------|
|registryName     |이미지가 빌드되는 레지스트리 이름  |
|리포지토리     |빌드 작업에 대한 대상 리포지토리        |
|taskRunName     |이미지 태그를 지정하는 작업 실행의 이름 |
|userAssignedIdentity |작업에서 사용하도록 설정된 사용자 할당 ID의 리소스 ID|
|customRegistryIdentity | 사용자 지정 레지스트리로 인증하는 데 사용되는 작업에서 사용하도록 설정된 사용자 할당 ID의 클라이언트 ID |
|customRegistry |작업에서 액세스한 사용자 지정 레지스트리의 로그인 서버 이름(예: *mybaseregistry.azurecr.io*)|
|sourceLocation     |빌드 작업에 대한 원격 컨텍스트 (예:  *https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.)* |
|dockerFilePath | 이미지를 빌드하는 데 사용되는 원격 컨텍스트의 Dockerfile에 대한 경로입니다. |

### <a name="deploy-the-template"></a>템플릿 배포

[az deployment group create][az-deployment-group-create] 명령을 사용하여 템플릿을 배포합니다. 이 예제에서는 *helloworld-node:testrun* 이미지를 빌드하여 *mycontainerregistry* 라는 레지스트리로 푸시합니다. 베이스 이미지는 *mybaseregistry.azurecr.io* 에서 끌어옵니다.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

이전 명령은 명령줄에서 매개 변수를 전달합니다. 원하는 경우 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)로 전달합니다.

### <a name="verify-deployment"></a>배포 확인

배포가 성공적으로 완료되면 [az acr repository show 태그][az-acr-repository-show-tags]를 실행하여 이미지가 빌드되었는지 확인합니다.

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

출력:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>실행 로그 보기

실행 로그를 보려면 [이전 섹션](#view-run-log)의 단계를 참조하세요.

## <a name="next-steps"></a>다음 단계

 * [ACR GitHub 리포지토리](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)의 추가 템플릿 예제를 참조하세요.
 * 템플릿 속성에 대한 자세한 내용은 [작업 실행](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) 및 [작업](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)에 대한 템플릿 참조를 참조하세요.


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-logs]: /cli/azure/acr/task#az_acr_task_logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
