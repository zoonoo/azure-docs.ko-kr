---
title: Azure Time Series Insights 단일 페이지 웹앱 만들기 | Microsoft Docs
description: TSI 환경에서 데이터를 쿼리하고 렌더링하는 단일 페이지 웹 응용 프로그램을 만드는 방법을 알아봅니다.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: fccd509d4f16cee86d30feb0e838f1493cae4e0b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275842"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>자습서: Azure Time Series Insights 단일 페이지 웹앱 만들기

이 자습서에서는 [TSI(Time Series Insights) 응용 프로그램 예제](https://insights.timeseries.azure.com/clientsample) 이후에 모델링된 TSI 데이터에 액세스하는 사용자 고유의 SPA(단일 페이지 웹 응용 프로그램)를 만드는 과정을 안내합니다. 이 자습서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 응용 프로그램 설계
> * 응용 프로그램을 Azure AD(Active Directory)에 등록하는 방법
> * 웹 응용 프로그램을 빌드, 게시 및 테스트하는 방법 

## <a name="prerequisites"></a>필수 조건

아직 Azure 구독이 없는 경우 [평가판 Azure 구독](https://azure.microsoft.com/free/)에 등록합니다. 

또한 아직 Visual Studio를 설치하지 않은 경우 설치해야 합니다. 이 자습서에서는 [무료 커뮤니티 버전 또는 평가판을 다운로드/설치](https://www.visualstudio.com/downloads/)할 수 있습니다.

## <a name="application-design-overview"></a>응용 프로그램 설계 개요

앞서 언급했듯이, TSI 응용 프로그램 예제는 이 자습서에 사용되는 설계 및 코드에 대한 기반을 제공합니다. 이 코드에는 TSI 클라이언트 JavaScript 라이브러리가 사용됩니다. TSI 클라이언트 라이브러리는 다음 두 가지 주요 API 범주에 대한 추상화를 제공합니다.

- **TSI 쿼리 API를 호출하는 래퍼 메서드**: JSON 기반 식을 사용하여 TSI 데이터를 쿼리할 수 있게 하는 REST API입니다. 메서드는 라이브러리의 `TsiClient.server` 네임스페이스 아래에 구성됩니다.
- **여러 유형의 차트 작성 컨트롤을 만들고 채우는 메서드**: 웹 페이지에서 TSI 데이터를 시각화하는 데 사용되는 메서드입니다. 메서드는 라이브러리의 `TsiClient.ux` 네임스페이스 아래에 구성됩니다.

이 자습서에서는 응용 프로그램 예제의 TSI 환경에서 제공하는 데이터도 사용합니다. TSI 응용 프로그램 예제의 구조 및 TSI 클라이언트 라이브러리 사용에 대한 자세한 내용은 [Azure Time Series Insights JavaScript 클라이언트 라이브러리 살펴보기](tutorial-explore-js-client-lib.md) 자습서를 참조하세요.

## <a name="register-the-application-with-azure-ad"></a>Azure AD에 응용 프로그램 등록 

응용 프로그램을 빌드하기 전에 응용 프로그램을 Azure AD에 등록해야 합니다. 응용 프로그램을 등록하면 응용 프로그램의 ID가 구성되므로 Single Sign-On에 OAuth 지원을 사용할 수 있습니다. OAuth를 사용하려면 SPA가 "암시적" 권한 부여를 사용해야 하며, 이 부분은 응용 프로그램 매니페스트에서 업데이트하겠습니다. 응용 프로그램 매니페스트는 응용 프로그램 ID 구성의 JSON 표현입니다. 

1. Azure 구독 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.  
1. 왼쪽 창에서 **Azure Active Directory** 리소스를 선택하고, **앱 등록**을 선택하고, **+ 새 응용 프로그램 등록**을 선택합니다.  
   
   ![Azure Portal Azure AD 응용 프로그램 등록](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. **만들기** 페이지에서 필수 매개 변수를 입력합니다.
   
   매개 변수|설명
   ---|---
   **Name** | 의미 있는 등록 이름을 입력합니다.  
   **응용 프로그램 유형** | SPA 웹 응용 프로그램을 빌드하는 것이므로 "웹앱/API"로 둡니다.
   **로그온 URL** | 응용 프로그램의 홈/로그인 페이지 URL을 입력합니다. 응용 프로그램이 Azure App Service에 호스트될 예정이므로(차후에) "https://azurewebsites.net" 도메인 내에서 URL을 사용해야 합니다. 이 예제에서는 이름이 등록 이름을 기반으로 합니다.

   여기까지 마쳤으면 **만들기**를 클릭하여 새 응용 프로그램 등록을 만듭니다.

   ![Azure Portal Azure AD 응용 프로그램 등록 - 만들기](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. 리소스 응용 프로그램은 다른 응용 프로그램에서 사용할 수 있는 REST API를 제공하며, Azure AD에 등록됩니다. API는 "범위"를 노출하여 클라이언트 응용 프로그램에 대한 세밀한/안전한 액세스를 제공합니다. 응용 프로그램에서 "Azure Time Series Insights" API를 호출하므로 런타임에 권한을 요청할/생성할 API 및 범위를 지정해야 합니다. **설정**, **필수 권한**, **+ 추가**를 차례로 선택합니다.

   ![Azure Portal Azure AD 권한 추가](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. **API 액세스 추가** 페이지에서 **1 API 선택**을 클릭하여 TSI API를 지정합니다. **API 선택** 페이지에서 검색 필드에 "azure 시간"을 입력합니다. 결과 목록에서 "Azure Time Series Insights" API를 선택한 다음, **선택**을 클릭합니다. 

   ![Azure Portal Azure AD 권한 추가 - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. 이제 API에 대한 범위를 지정합니다. 다시 **API 액세스 추가** 페이지에서 **2 권한 선택**을 클릭합니다. **액세스 사용** 페이지에서 "Azure Time Series Insights 서비스 액세스" 범위를 선택합니다. **선택**을 클릭하면 **API 액세스 추가** 페이지로 돌아갈 것입니다. **완료**를 클릭합니다.

   ![Azure Portal Azure AD 권한 추가 - 범위](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. **필수 권한** 페이지로 돌아가면 이제 "Azure Time Series Insights" API가 나열됩니다. 또한 모든 사용자의 API 및 범위에 액세스할 수 있도록 응용 프로그램의 권한을 사전 동의해야 합니다. 맨 위에서 **권한 부여** 단추를 클릭하고 **예**를 선택합니다.

   ![Azure Portal Azure AD 필수 권한 - 동의](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. 앞서 언급했듯이, 응용 프로그램 매니페스트도 업데이트해야 합니다. 이동 경로 탐색에서 응용 프로그램 이름을 클릭하여 **등록된 앱** 페이지로 돌아갑니다. **매니페스트**를 선택하고, `oauth2AllowImplicitFlow` 속성을 `true`로 변경한 다음, **저장**을 클릭합니다.

   ![Azure Portal Azure AD 매니페스트 업데이트](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. 마지막으로, 이동 경로 탐색을 클릭하여 다시 **등록된 앱** 페이지로 돌아간 다음, 응용 프로그램의 **홈페이지** URL 및 **응용 프로그램 ID** 속성을 복사합니다. 이러한 속성은 이후 단계에서 사용됩니다.

   ![Azure Portal Azure AD 속성](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>웹 응용 프로그램 빌드 및 게시

1. 응용 프로그램 프로젝트 파일을 저장할 디렉터리를 만듭니다. 다음 각 URL로 이동하고, 페이지 오른쪽 위 영역에서 "원시" 링크를 마우스 오른쪽 단추로 클릭하고, 프로젝트 디렉터리에 "다른 이름으로 저장"합니다.

   > [!NOTE]
   > 브라우저에 따라 파일을 저장하기 전에 파일 확장명을 HTML 또는 CSS로 수정해야 할 수도 있습니다.

   - 페이지의 **index.html** HTML 및 JavaScript https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** CSS 스타일시트(https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css)
    
1. Visual Studio를 시작하고 로그인하여 웹 응용 프로그램에 대한 프로젝트를 만듭니다. **파일** 메뉴에서 **열기**, **웹 사이트** 옵션을 선택합니다. **웹 사이트 열기** 대화 상자에서 HTML 및 CSS 파일을 저장한 작업 디렉터리를 선택한 다음, **열기**를 클릭합니다.

   ![VS - 파일 웹 사이트 열기](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. Visual Studio **보기** 메뉴에서 **솔루션 탐색기**를 엽니다. HTML 및 CSS 파일을 포함하는 웹 사이트 프로젝트(지구 아이콘)가 들어 있는 새 솔루션이 보일 것입니다.

   ![VS - 솔루션 탐색기 새 솔루션](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. 응용 프로그램을 게시하려면 **index.html**에서 JavaScript 코드 부분을 업데이트해야 합니다. 

   a. 첫째, `<head>` 요소에서 JavaScript 및 스타일 시트 파일 참조 경로를 변경합니다. Visual Studio 솔루션에서 **index.html** 파일을 열고 다음 JavaScript 코드 줄을 찾습니다. "프로덕션 리소스 링크" 아래에서 세 줄의 주석 처리를 제거하고, "개발 리소스 링크" 아래에서 세 줄을 주석으로 처리합니다.
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      변경된 코드는 다음 예제와 비슷합니다.

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. 다음으로, 새 Azure AD 응용 프로그램 등록을 사용하도록 액세스 토큰 논리를 변경합니다. [Azure AD에 응용 프로그램 등록](#register-the-application-with-azure-ad) 섹션의 9단계에서 복사한 응용 프로그램 ID 및 홈페이지 URL을 사용하도록 `clientID` 및 `postLogoutRedirectUri` 변수를 각각 변경합니다.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      변경된 코드는 다음 예제와 비슷합니다.

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   다. 편집을 마쳤으면 **index.html**을 저장합니다.

1. 이제 웹 응용 프로그램을 Azure 구독에 Azure App Service로 게시합니다.  

   > [!NOTE]
   > 다음 대화 상자의 여러 필드가 Azure 구독의 데이터로 채워집니다. 따라서 각 대화 상자가 완전히 로드될 때까지 몇 초 정도 걸릴 수 있으며, 그 후 계속 진행할 수 있습니다.  

   a. **솔루션 탐색기**에서 웹 사이트 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **웹앱 게시**를 선택합니다.  

      ![VS - 솔루션 탐색기 웹앱 게시](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. **Microsoft Azure App Service**를 선택하여 게시 대상을 만듭니다.  

      ![VS - 게시 프로필](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   다. 응용 프로그램을 게시하는 데 사용할 구독을 선택한 다음, "새로 만들기"를 클릭합니다. 

      ![VS - 게시 프로필 - App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. **App Service 만들기** 대화 상자의 모든 필드가 로드될 때까지 잠시 기다린 후 다음 필드를 수정합니다.
   
      필드 | 설명
      ---|---
      **앱 이름** | [Azure AD에 응용 프로그램 등록](#register-the-application-with-azure-ad)의 3단계에서 사용한 Azure AD 응용 프로그램 등록 이름으로 변경합니다. 
      **리소스 그룹** | **새로 만들기...** 단추를 사용하여 **앱 이름** 필드와 일치하도록 변경합니다.
      **App Service 계획** | **새로 만들기...** 단추를 사용하여 **앱 이름** 필드와 일치하도록 변경합니다.

      작업을 마쳤으면 **만들기**를 클릭합니다. Azure 리소스가 생성되는 동안 왼쪽 아래의 **내보내기** 단추가 "배포 중:"으로 바뀝니다. 리소스가 생성되면 자동으로 이전 대화 상자로 돌아갑니다. 

      ![VS - 게시 프로필 - 새 App Service 추가](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. **게시** 대화 상자로 돌아오면 **게시 방법**을 "웹 배포"로 설정합니다. 또한 Azure AD 응용 프로그램 등록과 일치하도록, `http` 대신 `https`를 사용하도록 **대상 URL**을 변경합니다. 그런 다음, "게시"를 클릭하여 웹 응용 프로그램을 배포합니다.  

      ![VS - 웹앱 게시 - App Service 게시](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. Visual Studio **출력** 창에 성공한 게시 로그인이 보일 것입니다. 배포가 완료되면 Visual Studio가 브라우저 탭에서 웹 응용 프로그램을 열고 로그인을 요구합니다. 로그인하면 모든 TSI 컨트롤에 데이터가 채워진 것을 볼 수 있습니다.  

      [![VS - 웹앱 게시 - 로그 출력 게시](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![TSI SPA 앱 - 로그인](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>문제 해결  

오류 코드/조건 | 설명
---------------------| -----------
*AADSTS50011: 애플리케이션에 대해 등록된 회신 주소가 없습니다.* | Azure AD 등록에 "회신 URL" 속성이 없습니다. Azure AD 응용 프로그램 등록의 **설정** / **회신 URL** 페이지로 이동합니다. [Azure AD에 응용 프로그램 등록](#register-the-application-with-azure-ad)의 3단계에서 지정한 **로그온** URL이 있는지 확인합니다. 
*AADSTS50011: 요청에 지정된 회신 URL이 애플리케이션에 대해 구성된 회신 URL('<Application ID GUID>')과 일치하지 않습니다.* | [웹 응용 프로그램 빌드 및 게시](#build-and-publish-the-web-application)의 4.b 단계에서 지정한 `postLogoutRedirectUri`는 Azure AD 응용 프로그램 등록의 **설정** / **회신 URL** 속성에서 지정한 값과 일치해야 합니다. 또한 `https`를 사용하도록 **대상 URL**을 변경해야 합니다. 자세한 단계는 [웹 응용 프로그램 빌드 및 게시](#build-and-publish-the-web-application)의 5.e를 참조하세요.
웹 응용 프로그램이 로드되지만, 흰색 배경에 스타일 없이 텍스트로만 구성된 로그인 페이지가 표시됩니다. | [웹 응용 프로그램 빌드 및 게시](#build-and-publish-the-web-application)의 4.a 단계에서 설명한 경로가 올바른지 확인합니다. 웹 응용 프로그램이 .css 파일을 찾을 수 없는 경우 페이지 스타일이 올바르게 지정되지 않습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 여러 실행 중인 Azure 서비스를 만듭니다. 이 자습서 시리즈를 완료할 계획이 없으면 불필요한 비용이 발생하지 않도록 모든 리소스를 삭제하는 것이 좋습니다. 

Azure Portal의 왼쪽 메뉴에서:

1. **리소스 그룹** 아이콘을 클릭한 다음, TSI 환경용으로 만든 리소스 그룹을 선택합니다. 페이지 맨 위에서 **리소스 그룹 삭제**를 클릭하고, 리소스 그룹의 이름을 입력한 다음 **삭제**를 클릭합니다. 
1. **리소스 그룹** 아이콘을 클릭한 다음, 디바이스 시뮬레이션 솔루션 가속기에서 만든 리소스 그룹을 선택합니다. 페이지 맨 위에서 **리소스 그룹 삭제**를 클릭하고, 리소스 그룹의 이름을 입력한 다음, **삭제**를 클릭합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 응용 프로그램 설계
> * 응용 프로그램을 Azure AD(Active Directory)에 등록하는 방법
> * 웹 응용 프로그램을 빌드, 게시 및 테스트하는 방법 

이 자습서에서는 Azure AD와 통합하고, 로그인한 사용자의 ID를 사용하여 액세스 토큰을 획득합니다. 서비스/디먼 응용 프로그램의 ID를 사용하여 TSI API에 액세스하는 방법은 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Time Series Insights API에 대한 인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)
