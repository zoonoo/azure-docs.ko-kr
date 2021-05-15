---
title: '자습서: Azure Static Web Apps에 Jekyll 사이트 게시'
description: Azure Static Web Apps에 Jekyll 애플리케이션을 배포하는 방법에 대해 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: cshoe
ms.openlocfilehash: 0f572d49867fe9149416664a405309253dd01af2
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202943"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>자습서: Azure Static Web Apps 미리 보기에 Jekyll 사이트 게시

이 문서에서는 [Azure Static Web Apps](overview.md)에 [Jekyll](https://jekyllrb.com/) 웹 애플리케이션을 만들고 배포하는 방법을 보여줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Jekyll 웹 사이트 만들기
> - Azure Static Web Apps 리소스 설정
> - Azure에 Jekyll 앱 배포

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [Jekyll](https://jekyllrb.com/docs/installation/) 설치
  - Linux용 Windows 하위 시스템을 사용하고, 필요한 경우 Ubuntu 지침을 따를 수 있습니다.
- 활성 구독이 있는 Azure 계정. 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.
- GitHub 계정. 계정이 없는 경우 [체험 계정을 만들](https://github.com/join) 수 있습니다.

## <a name="create-jekyll-app"></a>Jekyll 앱 만들기

Jekyll CLI(명령줄 인터페이스)를 사용하여 Jekyll 앱을 만듭니다.

1. 터미널에서 Jekyll CLI를 실행하여 새 앱을 만듭니다.

   ```bash
   jekyll new static-app
   ```

1. 새로 만든 앱으로 이동합니다.

   ```bash
   cd static-app
   ```

1. 새 Git 리포지토리를 초기화합니다.

   ```bash
    git init
   ```

1. 변경 내용을 커밋합니다.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>GitHub에 애플리케이션 푸시

Azure Static Web Apps는 GitHub를 사용하여 웹 사이트를 게시합니다. 다음 단계에서는 GitHub 리포지토리를 만드는 방법을 보여줍니다.

1. **jekyll-azure-static** 이라는 [https://github.com/new](https://github.com/new)에서 빈 GitHub 리포지토리(추가 정보를 만들지 않음)를 만듭니다.

1. 로컬 리포지토리에 GitHub 리포지토리를 원격으로 추가합니다. 다음 명령에서 `<YOUR_USER_NAME>` 자리 표시자 대신 GitHub 사용자 이름을 추가해야 합니다.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. 로컬 리포지토리를 GitHub로 푸시합니다.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > git 분기의 이름은 `main`과 다를 수 있습니다. 이 명령의 `main`을 올바른 값으로 바꿉니다.

## <a name="deploy-your-web-app"></a>웹앱 배포

다음 단계에서는 새 정적 사이트 앱을 만들어 프로덕션 환경에 배포하는 방법을 보여줍니다.

### <a name="create-the-application"></a>애플리케이션 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. **리소스 만들기** 를 클릭합니다.

1. **Static Web Apps** 를 검색합니다.

1. **Static Web Apps(미리 보기)** 를 클릭합니다.

1. **만들기** 를 클릭합니다.

1. **구독** 의 경우 나열된 구독을 수락하거나 드롭다운 목록에서 새 구독을 선택합니다.

1. _리소스 그룹_ 에서 **새로 만들기** 를 선택합니다. _새 리소스 그룹 이름_ 에서 **jekyll-static-app** 을 입력하고 **확인** 을 선택합니다.

1. 그런 다음, _이름_ 상자에 있는 앱 이름을 입력합니다. 유효한 문자에는 `a-z`, `A-Z`, `0-9` 및 `-`가 포함됩니다.

1. _지역_ 에 대해 가까운 사용 가능한 지역을 선택합니다.

1. _SKU_ 에 대해 **무료** 를 선택합니다.

1. _배포 세부 정보_ 에서 _원본_ 에 대해 **GitHub** 를 선택합니다.

1. **GitHub로 로그인** 단추를 클릭합니다.

1. 리포지토리를 만든 **조직** 을 선택합니다.

1. **jekyll-static-app** 을 _리포지토리_ 로 선택합니다.

1. _분기_ 에 대해 **기본** 을 선택합니다.

### <a name="build"></a>빌드

그런 다음, 빌드 프로세스에서 앱을 빌드하는 데 사용하는 구성 설정을 추가합니다. 다음 설정은 GitHub Action 워크플로 파일을 구성합니다.

1. _빌드 사전 설정_ 에 대해 **사용자 지정** 을 선택합니다.

1. _앱 위치_ 를 **/** 로 설정합니다.

1. _출력 위치_ 를 **_site** 로 설정합니다.

   현재 API를 배포하지 않으므로 _API 위치_ 에 대한 값이 필요하지 않습니다.

   :::image type="content" source="./media/publish-jekyll/github-actions-inputs.png" alt-text="GitHub Actions 입력":::

### <a name="review-and-create"></a>검토 및 만들기

1. **검토 + 만들기** 단추를 클릭하여 세부 정보가 모두 올바른지 확인합니다.

1. **만들기** 를 클릭하여 Azure Static Web Apps 만들기를 시작하고 배포를 위한 GitHub Action을 프로비저닝합니다.

1. GitHub Action이 완료될 때까지 기다립니다.

1. 새로 만든 Azure Static Web Apps 리소스의 Azure Portal _개요_ 창에서 _URL_ 링크를 클릭하여 배포된 애플리케이션을 엽니다.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="배포된 애플리케이션":::

#### <a name="custom-jekyll-settings"></a>사용자 지정 Jekyll 설정

정적 웹앱을 생성하면 애플리케이션에 대한 게시 구성 설정이 포함된 [워크플로 파일](./github-actions-workflow.md)이 생성됩니다.

`JEKYLL_ENV`와 같은 환경 변수를 구성하려면 워크플로의 Azure Static Web Apps GitHub Action에 `env` 섹션을 추가합니다.

```yaml
- name: Build And Deploy
   id: builddeploy
   uses: Azure/static-web-apps-deploy@v0.0.1-preview
   with:
      azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
      repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
      action: "upload"
      ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
      # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
      app_location: "/" # App source code path
      api_location: "" # Api source code path - optional
      output_location: "_site_" # Built app content directory - optional
      ###### End of Repository/Build Configurations ######
   env:
      JEKYLL_ENV: production
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](custom-domain.md)
