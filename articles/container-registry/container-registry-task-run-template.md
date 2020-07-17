---
title: 템플릿으로 빠른 작업 실행
description: Azure Resource Manager 템플릿을 사용 하 여 이미지를 빌드하기 위해 ACR 작업 실행 대기
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 7ad40d2e925d5e1443af9bce4115d45b0e8c06e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82927771"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>리소스 관리자 템플릿을 사용 하 여 ACR 작업 실행

[ACR 작업](container-registry-tasks-overview.md)은 컨테이너 수명 주기에 걸쳐 컨테이너 이미지를 관리하고 수정할 수 있는 Azure Container Registry 내부 기능 모음입니다. 

이 문서에서는 [az acr build][az-acr-build] 명령을 사용 하 여 수동으로 만들 수 있는 것과 마찬가지로 빠른 작업 실행을 큐에 대기 하는 Azure Resource Manager 템플릿 예제를 보여 줍니다.

태스크 실행을 큐에 대기 하는 리소스 관리자 템플릿은 자동화 시나리오에서 유용 하 고의 기능을 확장 `az acr build` 합니다. 예를 들어:

* 템플릿을 사용 하 여 컨테이너 레지스트리를 만들고 작업 실행을 즉시 큐에 대기 하 여 컨테이너 이미지를 빌드하고 푸시합니다.
* Azure 리소스에 대 한 관리 되는 id와 같은 빠른 작업 실행에서 사용할 수 있는 추가 리소스를 만들거나 사용 하도록 설정 합니다.

## <a name="limitations"></a>제한 사항

* 작업 실행의 [원본 위치로](container-registry-tasks-overview.md#context-locations) GitHub 리포지토리와 같은 원격 컨텍스트를 지정 해야 합니다. 로컬 소스 컨텍스트는 사용할 수 없습니다.
* 관리 id를 사용 하 여 작업을 실행 하는 경우에는 *사용자 할당* 관리 id만 허용 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* **GitHub 계정** -아직 계정이 없는 경우에 계정을 만듭니다 https://github.com . 
* **포크 샘플 리포지토리** -여기에 표시 된 작업 예제에 대해서는 github UI를 사용 하 여 다음 샘플 리포지토리를 github 계정으로 분기 https://github.com/Azure-Samples/acr-build-helloworld-node 합니다. 이 리포지토리에는 작은 컨테이너 이미지를 빌드하기 위한 샘플 Dockerfiles 및 소스 코드가 포함 되어 있습니다.

## <a name="example-create-registry-and-queue-task-run"></a>예: 레지스트리 만들기 및 큐 작업 실행

이 예제에서는 [샘플 템플릿을](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) 사용 하 여 컨테이너 레지스트리를 만들고 이미지를 빌드하고 푸시하는 작업 실행을 큐에 대기 합니다. 

### <a name="template-parameters"></a>템플릿 매개 변수

이 예에서는 다음 템플릿 매개 변수에 대 한 값을 제공 합니다.

|매개 변수  |값  |
|---------|---------|
|registryName     |만든 레지스트리의 고유 이름         |
|리포지토리     |빌드 작업에 대 한 대상 리포지토리        |
|taskRunName     |이미지 태그를 지정 하는 작업 실행의 이름입니다. |
|sourceLocation     |빌드 작업에 대 한 원격 컨텍스트 (예:) https://github.com/Azure-Samples/acr-build-helloworld-node 입니다. 리포지토리 루트의 Dockerfile은 작은 Node.js 웹 앱에 대 한 컨테이너 이미지를 작성 합니다. 원하는 경우 빌드 컨텍스트로 리포지토리의 포크를 사용 합니다.         |

### <a name="deploy-the-template"></a>템플릿 배포

[Az deployment group create][az-deployment-group-create] 명령을 사용 하 여 템플릿을 배포 합니다. 이 예제에서는 *helloworld-node: testrun* 이미지를 빌드하고 *mycontainerregistry*라는 레지스트리에 푸시합니다.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git
 ```

이전 명령은 명령줄에서 매개 변수를 전달 합니다. 원하는 경우 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)에 전달 합니다.

### <a name="verify-deployment"></a>배포 확인

배포가 성공적으로 완료 되 면 [az acr repository show 태그][az-acr-repository-show-tags]를 실행 하 여 이미지가 빌드 되었는지 확인 합니다.

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

작업 실행에 대 한 세부 정보를 보려면 실행 로그를 확인 합니다.

먼저 [az acr task list][az-acr-task-list-runs] 를 사용 하 여 실행 ID를 가져옵니다.
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

[Az acr task logs][az-acr-task-logs] 를 실행 하 여 실행 ID (이 경우 *c a 1*)에 대 한 태스크 실행 로그를 확인 합니다.

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

출력에는 태스크 실행 로그가 표시 됩니다.

Azure Portal에서 작업 실행 로그를 볼 수도 있습니다. 

1. 컨테이너 레지스트리로 이동 합니다.
2. **서비스**에서 **작업**  >  **실행**을 선택 합니다.
3. 실행 ID (이 경우 *c a 1*)를 선택 합니다. 

포털에는 태스크 실행 로그가 표시 됩니다.

## <a name="example-task-run-with-managed-identity"></a>예: 관리 id를 사용 하 여 작업 실행

[샘플 템플릿을](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) 사용 하 여 사용자 할당 관리 id를 사용 하도록 설정 하는 태스크 실행을 큐에 대기 합니다. 작업을 실행 하는 동안 id는 다른 Azure container registry에서 이미지를 가져오도록 인증 합니다. 

이 시나리오는 Azure에서 [관리 하는 id를 사용 하 여 ACR 작업에서 크로스 레지스트리 인증과](container-registry-tasks-cross-registry-authentication.md)유사 합니다. 예를 들어 조직은 여러 개발 팀에서 액세스 하는 기본 이미지를 사용 하 여 중앙 집중식 레지스트리를 유지 관리할 수 있습니다.

### <a name="prepare-base-registry"></a>기본 레지스트리 준비

데모용으로 별도의 컨테이너 레지스트리를 기본 레지스트리로 만들고 Docker 허브에서 끌어온 Node.js 기본 이미지를 푸시합니다.

1. 기본 이미지를 저장 하는 두 번째 컨테이너 레지스트리 (예: *mybaseregistry*)를 만듭니다.
1. `node:9-alpine`Docker 허브에서 이미지를 가져와서 기본 레지스트리에 대 한 태그를 만들고 기본 레지스트리에 푸시합니다.

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>새 Dockerfile 만들기

기본 레지스트리에서 기본 이미지를 가져오는 Dockerfile을 만듭니다. GitHub 리포지토리의 로컬 포크에서 다음 단계를 수행 합니다 (예:) `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. GitHub UI에서 **새 파일 만들기**를 선택 합니다.
1. 파일 이름을 *Dockerfile-test로* 하 고 다음 내용을 붙여넣습니다. *Mybaseregistry*에 대 한 레지스트리 이름을 대체 합니다.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. **새 파일 커밋**을 선택 합니다.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>기본 레지스트리에 ID 끌어오기 권한 부여

기본 레지스트리 *mybaseregistry*에서 가져올 관리 id 권한을 제공 합니다.

[az acr show][az-acr-show] 명령을 사용하여 기본 레지스트리의 리소스 ID를 가져와서 이를 변수에 저장합니다.

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

[Az role assign create][az-role-assignment-create] 명령을 사용 하 여 id를 기본 레지스트리에 Acrpull 역할을 할당 합니다. 이 역할에는 레지스트리에서 이미지를 끌어올 수 있는 권한만 있습니다.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>템플릿 매개 변수

이 예에서는 다음 템플릿 매개 변수에 대 한 값을 제공 합니다.

|매개 변수  |값  |
|---------|---------|
|registryName     |이미지가 빌드되는 레지스트리 이름  |
|리포지토리     |빌드 작업에 대 한 대상 리포지토리        |
|taskRunName     |이미지 태그를 지정 하는 작업 실행의 이름입니다. |
|userAssignedIdentity |태스크에서 사용 하도록 설정 된 사용자 할당 id의 리소스 ID|
|customRegistryIdentity | 사용자 지정 레지스트리를 사용 하 여 인증 하는 데 사용 되는 작업에서 사용 하도록 설정 된 사용자 할당 id의 클라이언트 ID |
|customRegistry |작업에서 액세스 한 사용자 지정 레지스트리의 로그인 서버 이름 (예: *mybaseregistry.azurecr.io* )|
|sourceLocation     |빌드 작업에 대 한 원격 컨텍스트 (예: * https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.* ) |
|dockerFilePath | 이미지를 빌드하는 데 사용 되는 원격 컨텍스트의 Dockerfile에 대 한 경로입니다. |

### <a name="deploy-the-template"></a>템플릿 배포

[Az deployment group create][az-deployment-group-create] 명령을 사용 하 여 템플릿을 배포 합니다. 이 예제에서는 *helloworld-node: testrun* 이미지를 빌드하고 *mycontainerregistry*라는 레지스트리에 푸시합니다. 기본 이미지는 *mybaseregistry.azurecr.io*에서 가져옵니다.

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
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

이전 명령은 명령줄에서 매개 변수를 전달 합니다. 원하는 경우 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)에 전달 합니다.

### <a name="verify-deployment"></a>배포 확인

배포가 성공적으로 완료 되 면 [az acr repository show 태그][az-acr-repository-show-tags]를 실행 하 여 이미지가 빌드 되었는지 확인 합니다.

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

실행 로그를 보려면 [이전 섹션](#view-run-log)의 단계를 참조 하세요.

## <a name="next-steps"></a>다음 단계

 * [ACR GitHub 리포지토리의](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)추가 템플릿 예제를 참조 하세요.
 * 템플릿 속성에 대 한 자세한 내용은 [작업 실행](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) 및 태스크에 대 한 템플릿 참조를 [참조 하세요.](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
