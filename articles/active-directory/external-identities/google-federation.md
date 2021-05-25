---
title: B2B의 ID 공급자로 Google 추가 - Azure AD
description: 게스트 사용자가 자신의 Gmail 계정을 사용하여 Azure AD 앱에 로그인할 수 있도록 Google과 페더레이션합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cb3b3fd2010bf4ad1288b767d62d3d76f7b37e3
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550968"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가

Google을 사용하여 페더레이션을 설정하면 초대된 사용자가 Microsoft 계정을 만들지 않고도 자신의 Gmail 계정을 사용하여 공유 앱 및 리소스에 로그인하도록 허용할 수 있습니다.

애플리케이션의 로그인 옵션 중 하나로 Google을 추가한 다음, **로그인** 페이지에서 단순히 Google에 로그인하는 데 사용하는 이메일을 입력하거나 **로그인 옵션** 을 선택하고 **Google로 로그인** 을 선택할 수 있습니다. 두 경우 모두 인증을 위해 Google 로그인 페이지로 리디렉션됩니다.

![Google 사용자를 위한 로그인 옵션](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google 페더레이션은 Gmail 사용자를 위해 특별히 설계되었습니다. G Suite 도메인과 페더레이션하려면 [직접 페더레이션](direct-federation.md)을 사용합니다.

> [!IMPORTANT]
> **2021년 1월 4일부터** Google은 [WebView 로그인 지원을 중단](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)합니다. Gmail에서 Google 페더레이션 또는 셀프 서비스 등록을 사용하는 경우 [기간 업무 네이티브 애플리케이션의 호환성을 테스트](google-federation.md#deprecation-of-webview-sign-in-support)해야 합니다.

## <a name="what-is-the-experience-for-the-google-user"></a>Google 사용자를 위한 환경이란?

Google 사용자가 초대를 교환할 때 해당 환경은 Google에 이미 로그인했는지 여부에 따라 달라집니다.

- Google에 로그인하지 않은 게스트 사용자에게는 로그인하라는 메시지가 표시됩니다.
- 게스트 사용자가 Google에 이미 로그인한 경우에는 사용하려는 계정을 선택하라는 메시지가 표시됩니다. 게스트 사용자는 초대에 사용된 계정을 선택해야 합니다.

“헤더가 너무 깁니다” 오류가 표시되는 게스트 사용자는 쿠키를 지우거나 개인 또는 incognito 창을 열고 다시 로그인을 시도할 수 있습니다.

![Google 로그인 페이지를 보여주는 스크린샷.](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>로그인 엔드포인트

Google 게스트 사용자는 이제 [공통 엔드포인트](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint)(즉, 테넌트 컨텍스트를 포함하지 않는 일반 앱 URL)를 사용하여 다중 테넌트 또는 Microsoft 자사 앱에 로그인할 수 있습니다. 로그인 프로세스 중에 게스트 사용자는 **로그인 옵션** 을 선택한 다음, **조직에 로그인** 을 선택합니다. 그런 다음, 사용자는 조직의 이름을 입력하고 Google 자격 증명을 사용하여 계속 로그인합니다.

Google 게스트 사용자는 테넌트 정보가 포함된 애플리케이션 엔드포인트를 사용할 수도 있습니다. 예를 들면 다음과 같습니다.

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

테넌트 정보(예: `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`)를 포함하여 Google 게스트 사용자에게 애플리케이션 또는 리소스에 대한 직접 링크를 제공할 수도 있습니다.

## <a name="deprecation-of-webview-sign-in-support"></a>WebView 로그인 지원 중단

2021년 1월 4일부터 Google은 [포함된 WebView 로그인 지원을 중단합니다](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Google 페더레이션 또는 [Gmail로 셀프 서비스 등록](identity-providers.md)을 사용하는 경우 기간 업무 네이티브 애플리케이션의 호환성을 테스트해야 합니다. 앱이 인증을 요구하는 웹 보기 콘텐츠를 포함하는 경우 Google Gmail 사용자는 인증할 수 없습니다. Gmail 사용자에게 영향을 주는 알려진 시나리오는 다음과 같습니다.

- 이전 버전의 Windows에서 포함된 웹 보기 또는 WebAccountManager(WAM)를 사용하는 Windows 앱
- 인증을 위해 포함된 브라우저 프레임워크를 사용하도록 개발된 다른 네이티브 앱

해당 변경 내용은 다음에는 적용되지 않습니다.

- 최신 버전의 Windows에서 포함된 웹 보기 또는 WebAccountManager(WAM)를 사용하는 Windows 앱
- Microsoft iOS 앱
- G Suite ID(예: G Suite와 SAML 기반 [직접 페더레이션](direct-federation.md)을 사용하는 경우)

다양한 플랫폼과 시나리오를 계속해서 테스트하고 있으며, 이에 따라 이 문서를 업데이트합니다.
### <a name="to-test-your-apps-for-compatibility"></a>앱의 호환성을 테스트하려면

1. [Google의 지침](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)에 따라 앱이 영향을 받는지 확인합니다.
2. Fiddler 또는 다른 테스트 도구를 사용하여 로그인하는 동안 헤더를 삽입하고 Google 외부 ID를 사용하여 로그인을 테스트합니다.

   1. accounts.google.com에 요청을 보낼 때 Google-Accounts-Check-OAuth-Login:true를 HTTP 요청 헤더에 추가합니다.
   1. accounts.google.com 로그인 페이지에서 Gmail 주소를 입력하여 앱에 로그인해 봅니다.
   1. 로그인이 실패하고 “이 브라우저 또는 앱이 안전 하지 않을 수 있습니다”와 같은 오류가 표시되면 Google 외부 ID가 로그인 하지 못하도록 차단됩니다.

3. 다음 중 하나를 수행하여 문제를 해결합니다.

   - Windows 앱에서 이전 버전의 Windows에 포함된 웹 보기 또는 WebAccountManager(WAM)를 사용하는 경우 최신 버전의 Windows로 업데이트하세요.
   - 로그인에 시스템 브라우저를 사용하도록 앱을 수정합니다. 자세한 내용은 MSAL.NET 설명서의 [포함된 UI와 시스템 웹 UI 비교](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)를 참조하세요.  


## <a name="step-1-configure-a-google-developer-project"></a>1단계: Google 개발자 프로젝트 구성
먼저 Google 개발자 콘솔에서 새 프로젝트를 만들어 나중에 Azure AD(Azure Active Directory)에 추가할 수 있는 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 
1. https://console.developers.google.com 에서 Google API로 이동하고, Google 계정으로 로그인합니다. 공유 팀 Google 계정을 사용하는 것이 좋습니다.
2. 해당 계정을 사용하라는 메시지가 표시되면 서비스 약관에 동의합니다.
3. 새 프로젝트 만들기: 페이지의 왼쪽 위 모서리에서 프로젝트 목록을 선택한 다음, **프로젝트 선택** 페이지에서 **새 프로젝트** 를 선택합니다.
4. **새 프로젝트** 페이지에서 프로젝트 이름(예: **Azure AD B2B**)을 지정한 다음, **만들기** 를 선택합니다. 
   
   ![새 프로젝트 페이지를 보여주는 스크린샷.](media/google-federation/google-new-project.png)

4. **API 및 서비스** 페이지의 새 프로젝트 아래에서 **보기** 를 선택합니다.

5. API 카드에서 **Go to APIs overview**(API 개요로 이동)를 선택합니다. **OAuth 동의 화면** 을 선택합니다.

6. **외부** 를 선택한 다음, **만들기** 를 선택합니다. 

7. **OAuth 동의 화면** 에서 **애플리케이션 이름** 을 입력합니다.

   ![Google OAuth 동의 화면을 보여주는 스크린샷.](media/google-federation/google-oauth-consent-screen.png)

8. **권한 있는 도메인** 섹션으로 스크롤하고 **microsoftonline.com** 을 입력합니다.

   ![권한 있는 도메인 섹션을 보여주는 스크린샷.](media/google-federation/google-oauth-authorized-domains.PNG)

9. **저장** 을 선택합니다.

10. **자격 증명** 을 선택합니다. **자격 증명 생성** 메뉴에서 **OAuth 클라이언트 ID** 를 선택합니다.

    ![Google API 자격 증명 생성 메뉴를 보여주는 스크린샷.](media/google-federation/google-api-credentials.png)

11. **애플리케이션 형식** 에서 **웹 애플리케이션** 을 선택합니다. **AZURE AD B2B** 와 같이 애플리케이션에 적합한 이름을 지정합니다. **권한 있는 URI** 에서 다음 URI를 입력합니다.
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(여기서 `<tenant ID>`는 테넌트 ID임)
   
    > [!NOTE]
    > 테넌트 ID를 찾으려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **Azure Active Directory** 에서 **속성** 을 선택하고 **테넌트 ID** 를 복사합니다.

    ![권한 있는 리디렉션 URI 섹션을 보여주는 스크린샷.](media/google-federation/google-create-oauth-client-id.png)

12. **만들기** 를 선택합니다. 클라이언트 ID 및 클라이언트 암호를 복사합니다. Azure Portal에서 ID 공급자를 추가할 때 복사한 정보를 사용합니다.

    ![OAuth 클라이언트 ID 및 클라이언트 암호를 보여주는 스크린샷.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2단계: Azure AD에서 Google 페더레이션 구성 
이제 Google 클라이언트 ID와 클라이언트 암호를 설정합니다. Azure Portal 또는 PowerShell을 사용하여 설정할 수 있습니다. 자신을 초대하여 Google 페더레이션 구성을 테스트해야 합니다. Gmail 주소를 사용하고 초대된 Google 계정으로 초대를 사용해 보세요. 

**Azure Portal에서 Google 페더레이션을 구성하려면 다음을 수행합니다.** 
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** 를 선택합니다.
3. **모든 ID 공급자** 를 선택한 다음 **Google** 단추를 선택합니다.
4. 미리 복사해 둔 클라이언트 ID와 클라이언트 암호를 입력합니다. **저장** 을 선택합니다. 

   ![Google ID 공급자 추가 페이지를 보여주는 스크린샷.](media/google-federation/google-identity-provider.png)

**PowerShell을 사용하여 Google 페더레이션을 구성하려면 다음을 수행합니다.**
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 다음 명령을 실행합니다. `Connect-AzureAD`
3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.  
4. 다음 명령 실행: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > “1단계: Google 개발자 프로젝트 구성”에서 만든 앱의 클라이언트 ID 및 클라이언트 암호를 사용합니다. 자세한 내용은 [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true)를 참조하세요. 
 
## <a name="how-do-i-remove-google-federation"></a>Google 페더레이션은 어떻게 제거하나요?
Google 페더레이션 설치 프로그램을 삭제할 수 있습니다. 삭제하면 이미 초대에 응한 Google 게스트 사용자는 로그인할 수 없습니다. 하지만 [해당 상환 상태를 재설정](reset-redemption-status.md)하여 리소스에 대한 액세스 권한을 다시 부여할 수 있습니다.
 
**Azure AD 포털에서 Google 페더레이션을 삭제하려면 다음을 수행합니다.**
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** 를 선택합니다.
3. **모든 ID 공급자** 를 선택합니다.
4. **Google** 줄에서 줄임표 단추( **...** )를 선택한 다음 **삭제** 를 선택합니다. 
   
   ![소셜 ID 공급자의 삭제 단추를 보여주는 스크린샷.](media/google-federation/google-social-identity-providers.png)

1. **예** 를 선택하여 삭제를 확인합니다. 

**PowerShell을 사용하여 Google 페더레이션을 삭제하려면 다음을 수행합니다.** 
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. `Connect-AzureAD`을 실행합니다.  
4. 로그인 프롬프트에서 관리형 전역 관리자 계정으로 로그인합니다.  
5. 다음 명령을 입력합니다.

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 자세한 내용은 [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true)를 참조하세요.
