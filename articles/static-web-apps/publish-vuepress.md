---
title: '자습서: Azure Static Web Apps에 VuePress 사이트 게시'
description: 이 자습서에서는 Azure Static Web Apps에 VuePress 애플리케이션을 배포하는 방법을 보여줍니다.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 8b681816feb9f28b90c71e924681a7e787d52594
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814391"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps"></a>자습서: Azure Static Web Apps에 VuePress 사이트 게시

이 문서에서는 [Azure Static Web Apps](overview.md)에 [VuePress](https://vuepress.vuejs.org/) 웹 애플리케이션을 만들고 배포하는 방법을 보여줍니다. 최종 결과는 앱이 빌드되고 게시되는 방식을 제어하는 관련 GitHub Actions를 포함하는 새로운 Azure Static Web Apps 애플리케이션입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - VuePress 앱 만들기
> - Azure Static Web Apps 설정
> - VuePress 앱을 Azure에 배포

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.
- GitHub 계정. 계정이 없는 경우 [체험 계정을 만들](https://github.com/join) 수 있습니다.
- 설치된 [Node.js](https://nodejs.org).

## <a name="create-a-vuepress-app"></a>VuePress 앱 만들기

CLI(명령줄 인터페이스)에서 VuePress 앱을 만듭니다.

1. VuePress 앱에 대한 새 폴더를 만듭니다.

   ```bash
   mkdir static-site
   ```

1. 폴더에 _README.md_ 파일을 추가합니다.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. _package.json_ 파일을 초기화합니다.

   ```bash
   npm init -y
   ```

1. VuePress를 `devDependency`로 추가합니다.

   ```bash
   npm install --save-dev vuepress
   ```

1. 텍스트 편집기에서 _package.json_ 파일을 열고 [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) 섹션에 빌드 명령을 추가합니다.

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. _.gitignore_ 파일을 만들어 _노드\_모듈_ 폴더를 제외합니다.

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Git 리포지토리를 초기화합니다.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>GitHub에 애플리케이션 푸시

Azure Static Web Apps에 연결하려면 GitHub의 리포지토리가 필요합니다. 다음 단계에서는 사이트용 리포지토리를 만드는 방법을 보여줍니다.

1. **vuepress-static-app** 이라는 [https://github.com/new](https://github.com/new)에서 빈 GitHub 리포지토리(추가 정보를 만들지 않음)를 만듭니다.

1. 로컬 리포지토리에 GitHub 리포지토리를 원격으로 추가합니다. 다음 명령에서 `<YOUR_USER_NAME>` 자리 표시자 대신 GitHub 사용자 이름을 추가해야 합니다.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. 로컬 리포지토리를 GitHub로 푸시합니다.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>웹앱 배포

다음 단계에서는 새 정적 사이트 앱을 만들어 프로덕션 환경에 배포하는 방법을 보여줍니다.

### <a name="create-the-application"></a>애플리케이션 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기** 선택
1. **Static Web Apps** 를 검색합니다.
1. **Static Web Apps** 를 선택합니다.
1. **만들기** 를 선택합니다.
1. _기본_ 탭에서 다음 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _구독_ | Azure 구독 이름. |
    | _리소스 그룹_ | **my-vuepress-group**  |
    | _이름_ | **vuepress-static-app** |
    | _플랜 유형_ | **Free** |
    | _Azure Functions API 및 스테이징 환경을 위한 지역_ | 가장 가까운 지역을 선택합니다. |
    | _원본_ | **GitHub** |

1. **GitHub로 로그인** 을 선택하고 GitHub로 인증합니다.

1. 다음 GitHub 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _조직_ | 원하는 GitHub 조직을 선택합니다. |
    | _리포지토리_ | **vuepress-static-app** 을 선택합니다. |
    | _분기_ | **기본** 을 선택합니다. |

1. _빌드 세부 정보_ 섹션의 _빌드 사전 설정_ 드롭다운에서 **VuePress** 를 선택하고 기본값을 유지합니다.

### <a name="review-and-create"></a>검토 후 만들기

1. **검토 + 만들기** 단추를 선택하여 세부 정보가 모두 올바른지 확인합니다.

1. **만들기** 를 선택하여 App Service Static Web App 만들기를 시작하고 배포를 위한 GitHub Action을 프로비전합니다.

1. 배포가 완료되면 **리소스로 이동** 을 클릭합니다.

1. 리소스 화면에서 _URL_ 링크를 클릭하여 배포된 애플리케이션을 엽니다. GitHub Action이 완료되려면 1~2분 정도 기다려야 할 수 있습니다.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="배포된 애플리케이션":::

### <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](custom-domain.md)
