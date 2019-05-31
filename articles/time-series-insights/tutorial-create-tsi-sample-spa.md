---
title: '자습서: Azure Time Series Insights 단일 페이지 웹앱 만들기 | Microsoft Docs'
description: Azure Time Series Insights 환경에서 데이터를 쿼리하고 렌더링하는 단일 페이지 웹 애플리케이션을 만드는 방법을 알아봅니다.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 5ee6e9aefb235feb28468798c3bd6b107f8c7c49
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244030"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>자습서: Azure Time Series Insights 단일 페이지 웹앱 만들기

이 자습서에서는 Azure Time Series Insights 데이터에 액세스하는 사용자 고유의 SPA(단일 페이지 웹 애플리케이션)를 만드는 과정을 안내합니다. 

이 자습서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 애플리케이션 설계
> * 애플리케이션을 Azure AD(Azure Active Directory)에 등록하는 방법
> * 웹 애플리케이션을 빌드, 게시 및 테스트하는 방법

> [!NOTE]
> * 이 자습서의 소스 코드는 [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)에 제공됩니다.
> * 이 자습서에 사용되는 전체 앱을 표시하기 위해 Time Series Insights [클라이언트 샘플 앱](https://insights.timeseries.azure.com/clientsample)이 제공됩니다.

## <a name="prerequisites"></a>필수 조건

* 아직 Azure 구독이 없는 경우 [평가판 Azure 구독](https://azure.microsoft.com/free/)에 등록합니다.

* Visual Studio. 시작하려면 [2019 또는 2017 Community 버전](https://www.visualstudio.com/downloads/)을 다운로드합니다.

* Visual Studio용 IIS Express, Web Deploy 및 Azure Cloud Services 핵심 도구 구성 요소. Visual Studio 설치를 수정하여 이러한 구성 요소를 추가합니다.

## <a name="application-design"></a>애플리케이션 설계

Time Series Insights 샘플 SPA는 이 자습서에서 사용되는 디자인 및 코드의 기반입니다. 이 코드는 Time Series Insights JavaScript 클라이언트 라이브러리를 사용합니다. Time Series Insights 클라이언트 라이브러리는 다음 두 가지 주요 API 범주에 대한 추상화를 제공합니다.

- **Time Series Insights 쿼리 API를 호출하는 래퍼 메서드**: JSON 기반 식을 통해 Time Series Insights 데이터를 쿼리하는 데 사용할 수 있는 REST API입니다. 이러한 메서드는 라이브러리의 TsiClient.server 네임스페이스 아래에 구성됩니다.

- **여러 유형의 차트 작성 컨트롤을 만들고 채우는 메서드**: 웹 페이지에서 Time Series Insights 데이터를 시각화하는 데 사용할 수 있는 메서드입니다. 이러한 메서드는 라이브러리의 TsiClient.ux 네임스페이스 아래에 구성됩니다.

이 자습서에서는 샘플 애플리케이션의 Time Series Insights 환경에서 제공하는 데이터도 사용합니다. Time Series Insights 샘플 애플리케이션의 구조 및 Time Series Insights 클라이언트 라이브러리를 사용하는 방법에 대한 자세한 내용은 [Azure Time Series Insights JavaScript 클라이언트 라이브러리 살펴보기](tutorial-explore-js-client-lib.md) 자습서를 참조하세요.

## <a name="register-the-application-with-azure-ad"></a>Azure AD에 애플리케이션 등록

애플리케이션을 빌드하기 전에 Azure AD에 등록해야 합니다. 애플리케이션을 등록하면 ID가 구성되므로 Single Sign-On에 OAuth를 사용할 수 있습니다. 암시적 권한 부여 유형을 사용하려면 OAuth에 SPA가 필요합니다. 애플리케이션 매니페스트에서 권한 부여를 업데이트합니다. 애플리케이션 매니페스트는 애플리케이션 ID 구성의 JSON 표현입니다.

1. Azure 구독 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.  
1. **Azure Active Directory** > **앱 등록** > **새 애플리케이션 등록**을 선택합니다.

   [![Azure Portal - Azure AD 애플리케이션 등록 시작](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. **만들기** 창에서 필수 매개 변수를 입력합니다.

   매개 변수|설명
   ---|---
   **Name** | 의미 있는 등록 이름을 입력합니다.  
   **애플리케이션 유형** | **웹앱/API**를 그대로 둡니다.
   **로그온 URL** | 애플리케이션의 로그인(홈) 페이지 URL을 입력합니다. 향후 애플리케이션이 Azure App Service에 호스트될 예정이므로 https:\//azurewebsites.net 도메인 내에서 URL을 사용해야 합니다. 이 예제에서는 이름이 등록 이름을 기반으로 합니다.

   **만들기**를 선택하여 새 애플리케이션 등록을 만듭니다.

   [![Azure Portal - Azure AD 애플리케이션 등록 창의 만들기 옵션](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. 리소스 애플리케이션은 다른 애플리케이션이 사용할 수 있는 REST API를 제공합니다. API도 Azure AD에 등록됩니다. API는 *범위*를 노출하여 클라이언트 애플리케이션에 대해 보다 세분화된 보안 액세스 권한을 제공합니다. 애플리케이션이 Azure Time Series Insights API를 호출하므로 API 및 범위를 지정해야 합니다. 권한은 런타임에 API 및 범위에 대해 부여됩니다. **설정** > **필수 사용 권한** > **추가**를 선택합니다.

   [![Azure Portal - Azure AD 사용 권한 추가를 위한 추가 옵션](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. **API 액세스 추가** 창에서 **1 API 선택**을 선택하여 Azure Time Series Insights API를 지정합니다. **API 선택** 창의 검색 상자에 **azure 시간**을 입력합니다. 그런 다음, 결과 목록에서 **Azure Time Series Insights**를 선택합니다. **선택**을 선택합니다.

   [![Azure Portal - Azure AD 사용 권한 추가를 위한 검색 옵션](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. API의 범위를 선택하려면 **API 액세스 추가** 창에서 **2 권한 선택**을 선택합니다. **액세스 사용** 창에서 **Azure Time Series Insights 서비스 액세스** 범위를 선택합니다. **선택**을 선택합니다. **API 액세스 추가** 창으로 돌아갑니다. **완료**를 선택합니다.

   [![Azure Portal - Azure AD 사용 권한 추가를 위한 범위 설정](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. 이제 **필요한 권한** 창에 Azure Time Series Insights API가 표시됩니다. 또한 모든 사용자의 API 및 범위에 액세스할 수 있도록 애플리케이션에 대해 사전 동의된 권한을 제공해야 합니다. **사용 권한 부여**를 선택한 다음, **예**를 선택합니다.

   [![Azure Portal - Azure AD의 필요한 권한을 추가하기 위한 권한 부여 옵션](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. 앞에서 설명한 대로 애플리케이션 매니페스트도 업데이트해야 합니다. 창 위쪽에 있는 가로 메뉴(“이동 경로 탐색”)에서 애플리케이션 이름을 선택하여 **등록된 앱** 창으로 돌아갑니다. **매니페스트**를 선택하고, `oauth2AllowImplicitFlow` 속성을 `true`로 변경한 다음, **저장**을 선택합니다.

   [![Azure Portal - Azure AD 매니페스트 업데이트](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. 이동 경로 탐색에서 애플리케이션 이름을 선택하여 **등록된 앱** 창으로 돌아갑니다. 애플리케이션의 **홈페이지** 및 **애플리케이션 ID** 값을 복사합니다. 자습서의 뒷부분에서 이러한 값을 사용합니다.

   [![Azure Portal - 애플리케이션의 홈페이지 URL 및 애플리케이션 ID 값 복사](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>웹 애플리케이션 빌드 및 게시

1. 애플리케이션 프로젝트 파일을 저장할 디렉터리를 만듭니다. 그런 후, 다음의 각 URL로 이동합니다. 페이지의 오른쪽 위 모서리에 있는 **Raw** 링크를 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장**을 선택하여 프로젝트 디렉터리에 파일을 저장합니다.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): 페이지의 HTML 및 JavaScript
   - [*sampleStyles.css*]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): CSS 스타일시트

   > [!NOTE]
   > 브라우저에 따라 파일을 저장하기 전에 파일 확장명을 .html 또는 .css로 변경해야 할 수도 있습니다.

1. Visual Studio에서 필요한 구성 요소가 설치되어 있는지 확인합니다. Visual Studio용 IIS Express, Web Deploy 및 Azure Cloud Services 핵심 도구 구성 요소를 설치해야 합니다.

    [![Visual Studio - 설치된 구성 요소 수정](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Visual Studio 환경은 버전 및 구성 설정에 따라 설명된 예제와 약간 달라질 수 있습니다.

1. Visual Studio를 열고 로그인합니다. 웹 애플리케이션용 프로젝트를 만들려면 **파일** 메뉴에서 **열기** > **웹 사이트**를 선택합니다.

    [![Visual Studio - 새 솔루션 만들기](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. **웹 사이트 열기** 창에서 HTML 및 CSS 파일을 저장한 작업 디렉터리를 선택한 다음, **열기**를 선택합니다.

   [![Visual Studio - 파일 메뉴, 열기 및 웹 사이트 옵션 포함](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Visual Studio의 **보기** 메뉴에서 **솔루션 탐색기**를 선택합니다. 새 솔루션이 열립니다. HTML 및 CSS 파일을 포함하는 웹 사이트 프로젝트(지구본 아이콘)가 포함되어 있습니다.

   [![Visual Studio - 솔루션 탐색기의 새 솔루션](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. 앱을 게시하기 전에 *index.html*에서 구성 설정을 변경해야 합니다.

   1. 주석 `"PROD RESOURCE LINKS"` 아래에 있는 세 줄의 주석 처리를 해제하여 개발 환경에서 프로덕션 환경으로 종속성을 전환합니다. 주석 `"DEV RESOURCE LINKS"` 아래에 있는 세 줄을 주석 처리합니다.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      종속성은 다음 예제와 같이 주석 처리되어야 합니다.

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Azure AD 앱 등록 ID를 사용하도록 앱을 구성하려면 `clientID` 및 `postLogoutRedirectUri` 값을 변경하여 [Azure AD에 애플리케이션 등록](#register-the-application-with-azure-ad)의 9단계에서 복사한 **애플리케이션 ID** 및 **홈페이지**에 대한 값을 사용합니다.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      예: 

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. 수정 작업이 끝나면 *index.html*을 저장합니다.

1. 웹 애플리케이션을 Azure 구독에 Azure App Service로 게시합니다.  

   > [!NOTE]
   > 다음 단계에 표시되는 스크린샷의 몇 가지 옵션은 자동으로 Azure 구독의 데이터로 채워집니다. 각 창이 완전히 로드되는 데 몇 초 정도 걸릴 수 있습니다.  

   1. 솔루션 탐색기에서 웹 사이트 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **웹앱 게시**를 선택합니다.  

      [![Visual Studio - 솔루션 탐색기 웹앱 게시 옵션 선택](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. **시작**을 선택하여 앱 게시를 시작합니다.

      [![Visual Studio - 프로필 게시 창](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. 애플리케이션을 게시하는 데 사용하려는 구독을 선택합니다. **TsiSpaApp** 프로젝트를 선택합니다. 그런 다음 **확인**을 선택합니다.

      [![Visual Studio - 프로필 게시 App Service 창](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. **게시**를 선택하여 웹 애플리케이션을 배포합니다.

      [![Visual Studio - 게시 옵션 및 게시 로그 출력](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Visual Studio **출력** 창에 성공 게시 로그가 표시됩니다. 배포가 완료되면 Visual Studio의 브라우저 탭에서 해당 웹 애플리케이션이 열리고 로그인하라는 메시지가 표시됩니다. 성공적인 로그인 후 Time Series Insights 컨트롤이 데이터로 채워집니다.

## <a name="troubleshoot"></a>문제 해결  

오류 코드/조건 | 설명
---------------------| -----------
*AADSTS50011: 애플리케이션에 대해 등록된 회신 주소가 없습니다.* | Azure AD 등록에 **회신 URL** 속성이 없습니다. Azure AD 애플리케이션 등록의 **설정** > **회신 URL**로 이동합니다. [Azure AD에 애플리케이션 등록](#register-the-application-with-azure-ad)의 3단계에서 지정한 **로그온** URL이 있는지 확인합니다.
*AADSTS50011: 요청에 지정된 회신 URL이 애플리케이션에 대해 구성된 회신 URL과 일치하지 않습니다. '\<애플리케이션 ID GUID>'.* | [웹 애플리케이션 빌드 및 게시](#build-and-publish-the-web-application)의 6단계에서 지정한 `postLogoutRedirectUri`는 Azure AD 애플리케이션 등록의 **설정** > **회신 URL**서 지정한 값과 일치해야 합니다. **대상 URL**의 값을 변경하여 [웹 애플리케이션 빌드 및 게시](#build-and-publish-the-web-application)의 5단계에 따라 *https*를 사용하도록 합니다.
웹 애플리케이션이 로드되지만, 흰색 배경에 스타일 없이 텍스트로만 구성된 로그인 페이지가 표시됩니다. | [웹 애플리케이션 빌드 및 게시](#build-and-publish-the-web-application)의 4단계에서 설명한 경로가 올바른지 확인합니다. 웹 애플리케이션이 .css 파일을 찾을 수 없는 경우 페이지 스타일이 올바르게 지정되지 않습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 여러 실행 중인 Azure 서비스를 만듭니다. 이 자습서 시리즈를 완료할 계획이 없으면 불필요한 비용이 발생하지 않도록 모든 리소스를 삭제하는 것이 좋습니다.

Azure Portal 왼쪽 메뉴:

1. **리소스 그룹**을 선택한 후 Time Series Insights 환경용으로 만든 리소스 그룹을 선택합니다. 페이지 맨 위에서 **리소스 그룹 삭제**를 선택하고, 리소스 그룹의 이름을 입력한 다음, **삭제**를 선택합니다.
1. **리소스 그룹**을 선택하고 디바이스 시뮬레이션 솔루션 가속기에서 만든 리소스 그룹을 선택합니다. 페이지 맨 위에서 **리소스 그룹 삭제**를 선택하고, 리소스 그룹의 이름을 입력한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * 애플리케이션 설계
> * Azure AD에 애플리케이션을 등록하는 방법
> * 웹 애플리케이션을 빌드, 게시 및 테스트하는 방법

이 자습서에서는 Azure AD와 통합하고, 로그인한 사용자의 ID를 사용하여 액세스 토큰을 획득합니다. 서비스 또는 디먼 애플리케이션의 ID를 사용하여 Time Series Insights API에 액세스하는 방법을 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Time Series Insights API에 대한 인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)
