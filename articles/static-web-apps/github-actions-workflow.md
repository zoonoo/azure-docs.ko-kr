---
title: Azure Static Web Apps에 대한 GitHub Actions 워크플로
description: GitHub 리포지토리를 사용하여 Azure Static Web Apps에 지속적인 배포를 설정하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: cshoe
ms.openlocfilehash: 57c83a94925e94088085efacf1192416f63e6383
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232038"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Azure Static Web Apps에 대한 GitHub Actions 워크플로 미리 보기

새 Azure Static Web App 리소스를 만들 때 Azure는 GitHub Actions 워크플로를 생성하여 앱의 지속적인 배포를 제어합니다. 워크플로는 YAML 파일에 의해 결정됩니다. 이 문서에서는 워크플로 파일의 구조와 옵션에 대해 자세히 설명합니다.

배포는 개별 [단계](#steps)에서 정의되는 [작업](#jobs)을 실행하는 [트리거](#triggers)에 의해 시작됩니다.

## <a name="file-location"></a>파일 위치

GitHub 리포지토리를 Azure Static Web Apps에 연결하면 워크플로 파일이 리포지토리에 추가됩니다.

생성된 워크플로 파일을 보려면 다음 단계를 수행합니다.

1. GitHub에서 앱의 리포지토리를 엽니다.
1. _코드_ 탭에서 `.github/workflows` 폴더를 클릭합니다.
1. `azure-static-web-apps-<RANDOM_NAME>.yml`과 같은 이름의 파일을 클릭합니다.

리포지토리의 YAML 파일은 다음 예제와 유사합니다.

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: true
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        output_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>트리거

GitHub Actions [트리거](https://help.github.com/actions/reference/events-that-trigger-workflows)는 이벤트 트리거를 기반으로 작업을 실행하도록 GitHub Actions 워크플로에 알립니다. 워크플로 파일에서 `on` 속성을 사용하여 트리거가 나열됩니다.

```yml
on:
  push:
    branches:
    - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - main
```

`on` 속성과 연결된 설정을 통해 작업을 트리거하는 분기를 정의하고, 여러 끌어오기 요청 상태에 대해 발생하는 트리거를 설정할 수 있습니다.

이 예제에서는 _main_ 분기가 변경 될 때 워크플로를 시작 합니다. 워크플로를 시작하는 변경 내용에는 선택한 분기에 대한 밀어넣기 커밋 및 끌어오기 요청 열기가 포함됩니다.

## <a name="jobs"></a>작업

각 이벤트 트리거에는 이벤트 처리기가 필요합니다. [작업](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)은 이벤트를 트리거할 때 수행되는 작업을 정의합니다.

Static Web Apps 워크플로 파일에는 두 개의 사용 가능한 작업이 있습니다.

| 속성  | Description |
|---------|---------|
|`build_and_deploy_job` | 커밋을 푸시하거나 `on` 속성에 나열된 분기에 대해 끌어오기 요청을 열 때 실행됩니다. |
|`close_pull_request_job` | 끌어오기 요청을 닫을 때만 실행 되며, 끌어오기 요청에서 만든 스테이징 환경을 제거 합니다. |

## <a name="steps"></a>단계

단계는 작업의 순차적 작업입니다. 단계는 종속성 설치, 테스트 실행 및 프로덕션 환경에 애플리케이션 배포와 같은 작업을 수행합니다.

워크플로 파일은 다음 단계를 정의합니다.

| 작업  | 단계  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>작업 환경에서 리포지토리를 체크 아웃합니다.<li>리포지토리를 빌드하여 Azure Static Web Apps에 배포합니다.</ol>|
| `close_pull_request_job` | <ol><li>끌어오기 요청이 닫혔음을 Azure Static Web Apps에 알립니다.</ol>|

## <a name="build-and-deploy"></a>빌드 및 배포

`Build and Deploy`라는 단계는 Azure Static Web Apps 인스턴스에 빌드하고 배포합니다. `with` 섹션에서 배포에 대해 다음 값을 사용자 지정할 수 있습니다.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    output_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| 속성 | Description | 필수 |
|---|---|---|
| `app_location` | 애플리케이션 코드의 위치입니다.<br><br>예를 들어 애플리케이션 소스 코드가 리포지토리의 루트에 있으면 `/`를 입력하거나 애플리케이션 코드가 `app`이라는 디렉터리에 있는 경우 `/app`을 입력합니다. | 예 |
| `api_location` | Azure Functions 코드의 위치입니다.<br><br>예를 들어 앱 코드가 `api`라는 폴더에 있는 경우 `/api`를 입력합니다. 폴더에서 Azure Functions 앱이 검색 되지 않으면 빌드에 실패 하 고 워크플로가 원치 않는 API를 가정 합니다. | 예 |
| `output_location` | `app_location`에 상대적인 빌드 출력 디렉터리의 위치입니다.<br><br>예를 들어 애플리케이션 소스 코드를 `/app`에 배치하고, 빌드 스크립트에서 파일을 `/app/build` 폴더에 출력하는 경우 `build`를 `output_location` 값으로 설정합니다. | 예 |

Azure Static Web Apps에서 설정된 `repo_token`, `action` 및 `azure_static_web_apps_api_token` 값은 수동으로 변경하면 안 됩니다.

## <a name="custom-build-commands"></a>사용자 지정 빌드 명령

배포 중에 실행되는 명령을 세부적으로 제어할 수 있습니다. 다음 명령은 작업의 `with` 섹션에서 정의할 수 있습니다.

배포는 항상 사용자 지정 명령 앞에 `npm install`을 호출합니다.

| 명령            | Description |
|---------------------|-------------|
| `app_build_command` | 정적 콘텐츠 애플리케이션을 배포하는 동안 실행할 사용자 지정 명령을 정의합니다.<br><br>예를 들어, 각도 응용 프로그램에 대해 프로덕션 빌드를 구성 하려면를 실행 하도록 라는 npm 스크립트를 만들고를 `build-prod` `ng build --prod` `npm run build-prod` 사용자 지정 명령으로 입력 합니다. 이를 비워 두면 워크플로에서 `npm run build` 또는 `npm run build:azure` 명령을 실행하려고 시도합니다.  |
| `api_build_command` | Azure Functions API 애플리케이션을 배포하는 동안 실행할 사용자 지정 명령을 정의합니다. |

## <a name="route-file-location"></a>경로 파일 위치

리포지토리의 폴더에서 [routes.json](routes.md)을 찾도록 워크플로를 사용자 지정할 수 있습니다. 다음 속성은 작업의 `with` 섹션에서 정의할 수 있습니다.

| 속성            | Description |
|---------------------|-------------|
| `routes_location` | _routes.json_ 파일이 있는 디렉터리 위치를 정의합니다. 이 위치는 리포지토리의 루트를 기준으로 합니다. |

 _routes.json_ 파일의 위치에 대해 명시하는 것은 프런트 엔드 프레임워크 빌드 단계에서 기본적으로 이 파일을 `output_location`으로 이동하지 않는 경우에 특히 중요합니다.

## <a name="environment-variables"></a>환경 변수

작업 구성의 섹션을 통해 빌드에 대 한 환경 변수를 설정할 수 있습니다 `env` .

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: "upload"
          ###### Repository/Build Configurations
          app_location: "/"
          api_location: "api"
          output_location: "public"
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Monorepo 지원

Monorepo는 둘 이상의 응용 프로그램에 대 한 코드를 포함 하는 리포지토리입니다. 기본적으로 정적 Web Apps 워크플로 파일은 리포지토리의 모든 파일을 추적 하지만 단일 앱을 대상으로 조정할 수 있습니다. 따라서 자료의 경우 각 정적 앱에는 리포지토리의 *github/워크플로* 폴더에 나란히 있는 자체 구성 파일이 있습니다.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

단일 앱에 워크플로 파일을 대상으로 지정 하려면 및 섹션에서 경로를 `push` 지정 `pull_request` 합니다.

다음 예제에서는 `paths` `push` `pull_request` _azure-static-web-apps-purple-pond_ 라는 파일의 및 섹션에 노드를 추가 하는 방법을 보여 줍니다.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

이 인스턴스에서 다음 파일에 대 한 변경 내용만 새 빌드를 트리거합니다.

- *App1* 폴더 내의 모든 파일
- *Api1* 폴더 내의 모든 파일
- 앱의 *azure-static-web-apps-purple-pond* 워크플로 파일에 대 한 변경 내용

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사전 프로덕션 환경에서 끌어오기 요청 검토](review-publish-pull-requests.md)
