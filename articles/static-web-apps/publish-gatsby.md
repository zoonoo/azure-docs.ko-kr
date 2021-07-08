---
title: '자습서: Azure Static Web Apps에 Gatsby 사이트 게시'
description: 이 자습서에서는 Azure Static Web Apps에 Gatsby 애플리케이션을 배포하는 방법을 보여줍니다.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4c6a68b8db40aa07c251cabab28217143105aab1
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814517"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps"></a>자습서: Azure Static Web Apps에 Gatsby 사이트 게시

이 문서에서는 [Azure Static Web Apps](overview.md)에 [Gatsby](https://gatsbyjs.org) 웹 애플리케이션을 만들고 배포하는 방법을 보여줍니다. 최종 결과는 앱이 빌드되고 게시되는 방식을 제어하는 새로운 Static Web Apps(관련 GitHub Actions 포함)입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Gatsby 앱 만들기
> - Azure Static Web Apps 사이트 설정
> - Azure에 Gatsby 앱 배포

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.
- GitHub 계정. 계정이 없는 경우 [체험 계정을 만들](https://github.com/join) 수 있습니다.
- 설치된 [Node.js](https://nodejs.org).

## <a name="create-a-gatsby-app"></a>Gatsby 앱 만들기

Gatsby CLI(명령줄 인터페이스)를 사용하여 Gatsby 앱을 만듭니다.

1. 터미널 열기
1. [npx](https://www.npmjs.com/package/npx) 도구를 사용하여 Gatsby CLI로 새 앱을 만듭니다. 몇 분이 걸릴 수 있습니다.

   ```bash
   npx gatsby new static-web-app
   ```

1. 새로 만든 앱으로 이동

   ```bash
   cd static-web-app
   ```

1. Git 리포지토리 초기화

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>GitHub에 애플리케이션 푸시

새 Azure Static Web Apps 리소스를 만들려면 GitHub에 리포지토리가 있어야 합니다.

1. **gatsby-static-web-app** 이라는 [https://github.com/new](https://github.com/new)에서 빈 GitHub 리포지토리(추가 정보를 만들지 않음)를 만듭니다.

1. 그런 다음, 방금 만든 GitHub 리포지토리를 로컬 리포지토리에 원격으로 추가합니다. 다음 명령에서 `<YOUR_USER_NAME>` 자리 표시자 대신 GitHub 사용자 이름을 추가해야 합니다.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
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
    | _리소스 그룹_ | **my-gatsby-group**  |
    | _이름_ | **my-gatsby-app** |
    | _플랜 유형_ | **Free** |
    | _Azure Functions API 및 스테이징 환경을 위한 영역_ | 가장 가까운 지역을 선택합니다. |
    | _원본_ | **GitHub** |

1. **GitHub로 로그인** 을 선택하고 GitHub로 인증합니다.

1. 다음 GitHub 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _조직_ | 원하는 GitHub 조직을 선택합니다. |
    | _리포지토리_ | **gatsby-static-web-app** 을 선택합니다. |
    | _분기_ | **기본** 을 선택합니다. |

1. _빌드 세부 정보_ 섹션의 _빌드 사전 설정_ 드롭다운에서 **Gatsby** 를 선택하고 기본값을 유지합니다.

### <a name="review-and-create"></a>검토 후 만들기

1. **검토 + 만들기** 단추를 선택하여 세부 정보가 모두 올바른지 확인합니다.

1. **만들기** 를 선택하여 App Service Static Web App 만들기를 시작하고 배포를 위한 GitHub Action을 프로비전합니다.

1. 배포가 완료되면 **리소스로 이동** 을 클릭합니다.

1. 리소스 화면에서 _URL_ 링크를 클릭하여 배포된 애플리케이션을 엽니다. GitHub Action이 완료되려면 1~2분 정도 기다려야 할 수 있습니다.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="배포된 애플리케이션":::

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](custom-domain.md)
