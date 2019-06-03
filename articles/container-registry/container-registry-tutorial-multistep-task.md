---
title: 자습서 - 다단계 컨테이너 작업 - Azure Container Registry 작업
description: 이 자습서에서는 소스 코드를 Git 리포지토리에 커밋할 때 클라우드에서 컨테이너 이미지를 빌드, 실행 및 푸시하는 다단계 워크플로를 자동으로 트리거하도록 Azure Container Registry 작업을 구성하는 방법을 알아봅니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 09b8e5d31bc6a4ec24633889920e2768bb7ce538
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546557"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>자습서: 소스 코드를 커밋할 때 클라우드에서 다단계 컨테이너 워크플로 실행

ACR 작업은 [빠른 작업](container-registry-tutorial-quick-task.md) 외에도 소스 코드를 Git 리포지토리에 커밋할 때 자동으로 트리거할 수 있는 다단계 다중 컨테이너 기반 워크플로를 지원합니다. 

이 자습서에서는 YAML 파일 예제를 사용하여 소스 코드를 커밋할 때 레지스트리에서 하나 이상의 컨테이너 이미지를 빌드, 실행 및 푸시하는 다단계 작업을 정의하는 방법에 대해 알아봅니다. 코드를 커밋할 때 단일 이미지 빌드만 자동화하는 작업을 만들려면 [자습서: 소스 코드를 커밋할 때 클라우드에서 컨테이너 이미지 빌드 자동화](container-registry-tutorial-build-task.md)를 참조하세요. ACR 작업에 대한 개요는 [ACR 작업을 사용하여 OS 및 프레임워크 패치 자동화](container-registry-tasks-overview.md)를 참조하세요.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * YAML 파일을 사용하여 다단계 작업 정의
> * 작업을 만듭니다.
> * 필요에 따라 자격 증명을 작업에 추가하여 다른 레지스트리에 액세스할 수 있도록 설정
> * 작업 테스트
> * 태스크 상태 보기
> * 코드 커밋을 사용하여 작업 트리거

이 자습서에서는 [이전 자습서](container-registry-tutorial-quick-task.md)의 단계를 이미 완료했다고 가정합니다. 아직 완료하지 않은 경우 계속 진행하기 전에 이전 자습서의 [필수 조건](container-registry-tutorial-quick-task.md#prerequisites) 섹션에 있는 단계를 완료하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI를 로컬로 사용하려면 Azure CLI 버전 **2.0.62** 이상이 설치되어 있고, [az login][az-login]을 사용하여 로그인해야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>다단계 작업 만들기

이제 ACR 작업에서 커밋 상태를 읽고 웹후크를 리포지토리에 만들 수 있도록 설정하는 데 필요한 단계를 완료했으므로 컨테이너 이미지의 빌드, 실행 및 푸시를 트리거하는 작업을 만듭니다.

### <a name="yaml-file"></a>YAML 파일

[YAML 파일](container-registry-tasks-reference-yaml.md)에는 다단계 작업에 대한 단계가 정의됩니다. 이 자습서의 첫 번째 다단계 작업은 복제한 GitHub 리포지토리의 루트에 있는 `taskmulti.yaml` 파일에 정의되어 있습니다.

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

이 다단계 작업은 다음을 수행합니다.

1. `build` 단계를 실행하여 작업 디렉터리의 Dockerfile에서 이미지를 빌드합니다. 이미지는 작업이 실행되는 레지스트리인 `Run.Registry`를 대상으로 하며 고유한 ACR 작업 실행 ID가 있는 태그가 지정됩니다. 
1. `cmd` 단계를 실행하여 임시 컨테이너에서 이미지를 실행합니다. 이 예제에서는 백그라운드에서 장기 실행 컨테이너를 시작하고, 컨테이너 ID를 반환한 다음, 컨테이너를 중지합니다. 실제 시나리오에서는 실행 중인 컨테이너가 올바르게 실행되는지 테스트하는 단계가 포함될 수 있습니다.
1. `push` 단계에서 실행 레지스트리에 빌드된 이미지를 푸시합니다.

### <a name="task-command"></a>작업 명령

먼저 이러한 셸 환경 변수를 환경에 적합한 값으로 채웁니다. 이 단계는 반드시 필요한 것이 아니지만, 여러 줄로 된 Azure CLI 명령을 이 자습서에서 좀 더 쉽게 실행할 수 있게 합니다. 이러한 환경 변수를 채우지 않는 경우 명령 예제에 나타나는 각 값을 수동으로 바꿔야 합니다.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

이제 다음 [az acr task create][az-acr-task-create] 명령을 실행하여 작업을 만듭니다.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

이 작업은 모든 시간 코드를 `--context`에 지정된 리포지토리의 *마스터* 분기에 커밋하도록 지정하고, ACR 작업은 해당 분기의 코드에서 다단계 작업을 실행합니다. 리포지토리 루트의 `--file`에 지정된 YAML 파일에서 단계를 정의합니다. 

성공적인 [az acr task create][az-acr-task-create] 명령의 출력은 다음과 비슷합니다.

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>다단계 워크플로 테스트

다단계 작업을 테스트하려면 [az acr task run][az-acr-task-run] 명령을 실행하여 이 작업을 수동으로 트리거합니다.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

기본적으로 `az acr task run` 명령이 실행되면 로그 출력이 콘솔에 스트림됩니다. 출력에는 실행하는 각 작업 단계에 대한 진행률이 표시됩니다. 아래 출력에서는 주요 단계를 요약된 형식으로 보여 주고 있습니다.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>커밋을 사용하여 빌드 트리거

이제 작업을 수동으로 실행하여 테스트했으므로 소스 코드가 변경되면 해당 작업을 자동으로 트리거합니다.

먼저 [리포지토리][sample-repo]의 로컬 복제본이 포함된 디렉터리에 있는지 확인합니다.

```azurecli-interactive
cd acr-build-helloworld-node
```

다음으로, 다음 명령을 실행하여 새 파일을 만들고, 커밋하고, GitHub에 있는 리포지토리의 포크에 푸시합니다.

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

`git push` 명령을 실행할 때 GitHub 자격 증명을 제공하라는 메시지가 표시될 수 있습니다. GitHub 사용자 이름을 제공하고, 이전에 암호에 대해 만든 PAT(개인용 액세스 토큰)를 입력합니다.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

커밋이 리포지토리에 푸시되면 ACR 작업에서 만든 웹후크가 실행되어 Azure Container Registry에서 작업을 시작합니다. 현재 실행 중인 작업에 대한 로그를 표시하여 빌드 진행 상황을 확인하고 모니터링합니다.

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

출력은 다음과 비슷하며, 현재 실행 중이거나 마지막으로 실행된 작업을 보여 줍니다.

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>빌드 나열

ACR 작업에서 레지스트리에 대해 완료한 작업 실행 목록을 보려면 [az acr task list-runs][az-acr-task-list-runs] 명령을 실행합니다.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

이 명령의 출력은 다음과 비슷하게 표시됩니다. ACR 작업에서 실행한 실행이 표시되고, 가장 최근 작업에 대한 TRIGGER 열에 "Git Commit"(Git 커밋)이 표시됩니다.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>다중 레지스트리 다단계 작업 만들기

ACR 작업에는 기본적으로 작업이 실행되는 레지스트리에서 이미지를 푸시하거나 풀할 수 있는 권한이 있습니다. 실행 레지스트리 외에도 하나 이상의 레지스트리를 대상으로 하는 다단계 작업을 실행할 수도 있습니다. 예를 들어 하나의 레지스트리에 이미지를 빌드하고 프로덕션 시스템에서 액세스하는 두 번째 레지스트리에 다른 태그가 있는 이미지를 저장해야 할 수 있습니다. 다음 예제에서는 이러한 작업을 만들고 다른 레지스트리에 대한 자격 증명을 제공하는 방법을 보여 줍니다.

두 번째 레지스트리가 아직 없는 경우 이 예제에 사용할 레지스트리를 만듭니다. 레지스트리가 필요한 경우 [이전 자습서](container-registry-tutorial-quick-task.md) 또는 [빠른 시작: Azure CLI를 사용하여 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조하세요.

작업을 만들려면 *mycontainerregistrydate.azurecr.io* 형식(모두 소문자)의 레지스트리 로그인 서버 이름이 필요합니다. 이 예제에서는 두 번째 레지스트리를 사용하여 빌드 날짜별로 태그가 지정된 이미지를 저장합니다.

### <a name="yaml-file"></a>YAML 파일

이 자습서의 두 번째 다단계 작업은 복제한 GitHub 리포지토리의 루트에 있는 `taskmulti-multiregistry.yaml` 파일에 정의되어 있습니다.

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

이 다단계 작업은 다음을 수행합니다.

1. 두 개의 `build` 단계를 실행하여 작업 디렉터리의 Dockerfile에서 이미지를 빌드합니다.
    * 첫 번째 단계는 작업이 실행되고 ACR 작업 실행 ID가 있는 태그가 지정된 `Run.Registry` 레지스트리를 대상으로 합니다. 
    * 두 번째 단계는 작업을 만들 때 설정하거나 `az acr task create`에 전달된 외부 `values.yaml` 파일을 통해 제공한 `regDate`의 값으로 식별되는 레지스트리를 대상으로 합니다. 이 이미지는 실행 날짜가 있는 태그로 지정됩니다.
1. `cmd` 단계를 실행하여 빌드된 컨테이너 중 하나를 실행합니다. 이 예제에서는 백그라운드에서 장기 실행 컨테이너를 시작하고, 컨테이너 ID를 반환한 다음, 컨테이너를 중지합니다. 실제 시나리오에서는 실행 중인 컨테이너를 테스트하여 올바르게 실행되는지 확인할 수 있습니다.
1. `push` 단계에서 빌드된 이미지 중 첫 번째는 실행 레지스트리로 푸시하고, 두 번째는 `regDate`로 식별되는 레지스트리로 푸시합니다.

### <a name="task-command"></a>작업 명령

이전에 정의한 셸 환경 변수를 사용하여 다음 [az acr task create][az-acr-task-create] 명령을 통해 작업을 만듭니다. *mycontainerregistrydate*를 사용자 고유의 레지스트리 이름으로 바꿉니다.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti-image.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>작업 자격 증명 추가

이미지를 `regDate` 값으로 식별된 레지스트리로 푸시하려면 [az acr task credential add][az-acr-task-credential-add] 명령을 사용하여 해당 레지스트리에 대한 로그인 자격 증명을 작업에 추가합니다.

다음 예제에서는 *AcrPush* 역할로 범위가 지정된 레지스트리에 액세스할 수 있는 [ 서비스 주체](container-registry-auth-service-principal.md)를 만드는 것이 좋습니다. 서비스 주체를 만들려면 이 [Azure CLI 스크립트](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)를 참조하세요.

다음 `az acr task credential add` 명령에서 서비스 주체 애플리케이션 ID와 암호를 전달합니다.

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

CLI에서 추가한 레지스트리 로그인 서버의 이름을 반환합니다.

### <a name="test-the-multi-step-workflow"></a>다단계 워크플로 테스트

앞의 예제에서와 같이 다단계 작업을 테스트하려면 [az acr task run][az-acr-task-run] 명령을 실행하여 이 작업을 수동으로 트리거합니다. Git 리포지토리에 대한 커밋을 사용하여 작업을 트리거하려면 [커밋을 사용하여 빌드 트리거](#trigger-a-build-with-a-commit) 섹션을 참조하세요.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

기본적으로 `az acr task run` 명령이 실행되면 로그 출력이 콘솔에 스트림됩니다. 이전과 마찬가지로 출력에는 실행하는 각 작업 단계에 대한 진행률이 표시됩니다. 출력에서는 주요 단계를 요약된 형식으로 보여 주고 있습니다.

출력:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 소스 코드를 Git 리포지토리에 커밋할 때 자동으로 트리거되는 다단계 다중 컨테이너 기반 작업을 만드는 방법을 알아보았습니다. 병렬 및 종속 단계 실행을 포함한 다단계 작업의 고급 기능은 [ACR 작업 YAML 참조](container-registry-tasks-reference-yaml.md)를 참조하세요. 컨테이너 이미지의 기본 이미지가 업데이트될 때 빌드를 트리거하는 작업을 만드는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [기본 이미지 업데이트 시 빌드 자동화](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
