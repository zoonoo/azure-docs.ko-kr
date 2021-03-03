---
title: B2B의 ID 공급자로 Google 추가 - Azure AD
description: Google과 페더레이션 하 여 게스트 사용자가 자신의 Gmail 계정을 사용 하 여 Azure AD 앱에 로그인 할 수 있도록 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9e7ec5569dd0de3b0535c3b0e3b3304848a5207
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653325"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가

Google을 사용 하 여 페더레이션을 설정 하면 초대 된 사용자가 Microsoft 계정을 만들지 않고도 자신의 Gmail 계정을 사용 하 여 공유 앱 및 리소스에 로그인 하도록 허용할 수 있습니다.

응용 프로그램 로그인 옵션 중 하나로 Google을 추가한 후 **로그인** 페이지에서 사용자는 google에 로그인 하는 데 사용 하는 전자 메일을 간단히 입력 하거나 **로그인 옵션** 을 선택 하 고 **Google을 사용 하 여 로그인** 을 선택할 수 있습니다. 두 경우 모두 인증을 위해 Google 로그인 페이지로 리디렉션됩니다.

![Google 사용자에 대 한 로그인 옵션](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google 페더레이션은 Gmail 사용자를 위해 특별히 설계되었습니다. G Suite 도메인과 페더레이션 하려면 [직접 페더레이션을](direct-federation.md)사용 합니다.

> [!IMPORTANT]
> **2021년 1월 4일부터** Google은 [WebView 로그인 지원을 중단](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)합니다. Gmail에서 Google 페더레이션 또는 셀프 서비스 등록을 사용하는 경우 [기간 업무 네이티브 애플리케이션의 호환성을 테스트](google-federation.md#deprecation-of-webview-sign-in-support)해야 합니다.

## <a name="what-is-the-experience-for-the-google-user"></a>Google 사용자를 위한 환경이란?

Google 사용자가 초대를 교환 때 해당 환경은 Google에 이미 로그인 했는지 여부에 따라 달라 집니다.

- Google에 로그인 하지 않은 게스트 사용자에 게는이 작업을 수행 하 라는 메시지가 표시 됩니다.
- Google에 이미 로그인 한 게스트 사용자에 게 사용 하려는 계정을 선택 하 라는 메시지가 표시 됩니다. 게스트 사용자는 초대에 사용된 계정을 선택해야 합니다.

"헤더가 너무 깁니다." 오류가 표시 되는 게스트 사용자는 쿠키를 지우거 나 개인 또는 incognito 창을 열고 다시 로그인을 시도할 수 있습니다.

![Google 로그인 페이지를 보여 주는 스크린샷](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>로그인 끝점

이제 Google 게스트 사용자는 [공통 끝점](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (즉, 테 넌 트 컨텍스트를 포함 하지 않는 일반 앱 URL)을 사용 하 여 다중 테 넌 트 또는 Microsoft 자사 앱에 로그인 할 수 있습니다. 일반적인 끝점의 예는 다음과 같습니다.

- `https://teams.microsoft.com`
- `https://myapps.microsoft.com`
- `https://portal.azure.com`

로그인 프로세스 중에 게스트 사용자가 **로그인 옵션** 을 선택 하 고 **조직에 로그인** 을 선택 합니다. 그런 다음 사용자는 조직의 이름을 입력 하 고 Google 자격 증명을 사용 하 여 계속 로그인 합니다.

Google 게스트 사용자는 테 넌 트 정보를 포함 하는 응용 프로그램 엔드포인트를 사용할 수도 있습니다. 예를 들면 다음과 같습니다.

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Google 게스트 사용자에 게 테 넌 트 정보 (예:)를 포함 하 여 응용 프로그램 또는 리소스에 대 한 직접 링크를 제공할 수도 있습니다 `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="deprecation-of-webview-sign-in-support"></a>웹 보기 로그인 지원 중단

Google은 2021 년 1 월 4 일부 터 [사용 중단 Embedded 웹 보기 로그인을 지원](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)합니다. Gmail을 사용 하 여 Google 페더레이션 또는 [셀프 서비스 등록](identity-providers.md)을 사용 하는 경우 lob (기간 업무) 네이티브 응용 프로그램의 호환성을 테스트 해야 합니다. 앱이 인증을 요구 하는 웹 보기 콘텐츠를 포함 하는 경우 Google Gmail 사용자는 인증할 수 없습니다. Gmail 사용자에 게 영향을 주는 알려진 시나리오는 다음과 같습니다.

- 이전 버전의 Windows에서 포함 된 웹 보기 또는 WebAccountManager (WAM)를 사용 하는 windows 앱입니다.
- 인증을 위해 포함 된 브라우저 프레임 워크를 사용 하는 다른 네이티브 앱을 개발 했습니다.

이 변경 내용은 영향을 주지 않습니다.

- 최신 버전의 Windows에서 포함 된 웹 보기 또는 WebAccountManager (WAM)를 사용 하는 windows 앱
- Microsoft iOS 앱
- G suite id (예: G Suite와 SAML 기반 [직접 페더레이션을](direct-federation.md) 사용 하는 경우)

Microsoft는 다양 한 플랫폼과 시나리오를 계속 해 서 테스트 하 고 있으며 그에 따라이 문서를 업데이트 합니다.
### <a name="to-test-your-apps-for-compatibility"></a>응용 프로그램의 호환성을 테스트 하려면

1. [Google의 지침](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) 에 따라 앱이 영향을 받는지 확인 합니다.
2. Fiddler 또는 다른 테스트 도구를 사용 하 여 로그인 하는 동안 헤더를 삽입 하 고 Google external identity를 사용 하 여 로그인을 테스트 합니다.

   1. Accounts.google.com에 요청을 보낼 때 Google-Check-OAuth-Login: true를 HTTP 요청 헤더에 추가 합니다.
   1. Accounts.google.com 로그인 페이지에서 Gmail 주소를 입력 하 여 앱에 로그인을 시도 합니다.
   1. 로그인이 실패 하 고 "이 브라우저 또는 앱이 안전 하지 않을 수 있습니다."와 같은 오류가 표시 되 면 Google external id가 로그인 하지 못하도록 차단 됩니다.

3. 다음 중 하나를 수행 하 여 문제를 해결 합니다.

   - Windows 앱에서 이전 버전의 Windows에 포함 된 웹 보기 또는 WebAccountManager (WAM)를 사용 하는 경우 최신 버전의 Windows로 업데이트 합니다.
   - 로그인에 시스템 브라우저를 사용 하도록 앱을 수정 합니다. 자세한 내용은 MSAL.NET 설명서의 [Embedded Vs System WEB UI](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) 를 참조 하십시오.  


## <a name="step-1-configure-a-google-developer-project"></a>1단계: Google 개발자 프로젝트 구성
먼저 Google 개발자 콘솔에서 새 프로젝트를 만들어 나중에 Azure Active Directory (Azure AD)에 추가할 수 있는 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 
1. https://console.developers.google.com 에서 Google API로 이동하고, Google 계정으로 로그인합니다. 공유 팀 Google 계정을 사용하는 것이 좋습니다.
2. 이 작업을 수행 하 라는 메시지가 표시 되 면 서비스 약관에 동의 합니다.
3. 새 프로젝트 만들기: 페이지의 왼쪽 위 모서리에서 프로젝트 목록을 선택한 다음 **프로젝트 선택** 페이지에서 **새 프로젝트** 를 선택 합니다.
4. **새 프로젝트** 페이지에서 프로젝트 이름 (예: **Azure AD B2B**)을 지정 하 고 **만들기** 를 선택 합니다. 
   
   ![새 프로젝트 페이지를 보여 주는 스크린샷](media/google-federation/google-new-project.png)

4. **Api & 서비스** 페이지의 새 프로젝트 아래에서 **보기** 를 선택 합니다.

5. Api 카드에서 **api 개요로 이동을** 선택 합니다. **OAuth 동의 화면** 을 선택 합니다.

6. **외부** 를 선택한 다음, **만들기** 를 선택합니다. 

7. **OAuth 동의 화면** 에서 **응용 프로그램 이름을** 입력 합니다.

   ![Google OAuth 동의 화면을 보여 주는 스크린샷](media/google-federation/google-oauth-consent-screen.png)

8. **권한 있는 도메인** 섹션으로 스크롤하고 **microsoftonline.com** 를 입력 합니다.

   ![승인 된 도메인 섹션을 보여 주는 스크린샷](media/google-federation/google-oauth-authorized-domains.PNG)

9. **저장** 을 선택합니다.

10. **자격 증명** 을 선택합니다. **자격 증명 만들기** 메뉴에서 **OAuth 클라이언트 ID** 를 선택 합니다.

    ![Google Api 자격 증명 만들기 메뉴를 보여 주는 스크린샷](media/google-federation/google-api-credentials.png)

11. **애플리케이션 형식** 에서 **웹 애플리케이션** 을 선택합니다. **AZURE AD B2B** 와 같이 응용 프로그램에 적합 한 이름을 지정 합니다. **권한 있는 리디렉션 uri** 아래에 다음 uri를 입력 합니다.
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(여기서 `<tenant ID>` 은 테 넌 트 ID)
   
    > [!NOTE]
    > 테 넌 트 ID를 찾으려면 [Azure Portal](https://portal.azure.com)로 이동 합니다. **Azure Active Directory** 에서 **속성** 을 선택 하 고 **테 넌 트 ID** 를 복사 합니다.

    ![권한 있는 리디렉션 Uri 섹션을 보여 주는 스크린샷](media/google-federation/google-create-oauth-client-id.png)

12. **만들기** 를 선택합니다. 클라이언트 ID 및 클라이언트 암호를 복사 합니다. Azure Portal에서 id 공급자를 추가할 때이를 사용 합니다.

    ![OAuth 클라이언트 ID 및 클라이언트 암호를 보여 주는 스크린샷](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2단계: Azure AD에서 Google 페더레이션 구성 
이제 Google 클라이언트 ID 및 클라이언트 암호를 설정 합니다. Azure Portal 또는 PowerShell을 사용 하 여이 작업을 수행할 수 있습니다. 사용자를 초대 하 여 Google federation 구성을 테스트 해야 합니다. Gmail 주소를 사용 하 고 초대 된 Google 계정으로 초대를 사용해 보세요. 

**Azure Portal에서 Google 페더레이션을 구성 하려면** 
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** 를 선택합니다.
3. **모든 id 공급자** 를 선택한 다음 **Google** 단추를 선택 합니다.
4. 이전에 얻은 클라이언트 ID 및 클라이언트 암호를 입력 합니다. **저장** 을 선택 합니다. 

   ![Google id 공급자 추가 페이지를 보여 주는 스크린샷](media/google-federation/google-identity-provider.png)

**PowerShell을 사용하여 Google 페더레이션을 구성하려면 다음을 수행합니다.**
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 다음 명령을 실행합니다. `Connect-AzureAD`
3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.  
4. 다음 명령 실행: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > "1 단계: Google developer 프로젝트 구성"에서 만든 앱의 클라이언트 ID 및 클라이언트 암호를 사용 합니다. 자세한 내용은 [AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview)를 참조 하세요. 
 
## <a name="how-do-i-remove-google-federation"></a>Google 페더레이션은 어떻게 제거하나요?
Google 페더레이션 설치 프로그램을 삭제할 수 있습니다. 이렇게 하면 이미 초대를 받은 Google 게스트 사용자는 로그인 할 수 없습니다. 그러나 디렉터리에서 리소스를 삭제하고 다시 초대하여 리소스에 대한 액세스 권한을 다시 부여할 수 있습니다. 
 
**Azure AD 포털에서 Google 페더레이션을 삭제 하려면**
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** 를 선택합니다.
3. **모든 ID 공급자** 를 선택합니다.
4. **Google** line에서 줄임표 단추 (**...**)를 선택 하 고 **삭제** 를 선택 합니다. 
   
   ![소셜 id 공급자에 대 한 삭제 단추를 보여 주는 스크린샷](media/google-federation/google-social-identity-providers.png)

1. **예** 를 선택 하 여 삭제를 확인 합니다. 

**PowerShell을 사용 하 여 Google 페더레이션을 삭제 하려면** 
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. `Connect-AzureAD`을 실행합니다.  
4. 로그인 프롬프트에서 관리형 전역 관리자 계정으로 로그인합니다.  
5. 다음 명령을 입력합니다.

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 자세한 내용은 [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)를 참조하세요.
