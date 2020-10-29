---
title: B2B의 ID 공급자로 Google 추가 - Azure AD
description: Google과 페더레이션 하 여 게스트 사용자가 자신의 Gmail 계정을 사용 하 여 Azure AD 앱에 로그인 할 수 있도록 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926238"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가

Google을 사용 하 여 페더레이션을 설정 하면 초대 된 사용자가 Microsoft 계정을 만들지 않고도 자신의 Gmail 계정을 사용 하 여 공유 앱 및 리소스에 로그인 하도록 허용할 수 있습니다. 

> [!NOTE]
> Google 페더레이션은 Gmail 사용자를 위해 특별히 설계되었습니다. G Suite 도메인과 페더레이션 하려면 [직접 페더레이션을](direct-federation.md)사용 합니다.

## <a name="what-is-the-experience-for-the-google-user"></a>Google 사용자를 위한 환경이란?
Google Gmail 사용자에 게 초대를 보낼 때 게스트 사용자는 테 넌 트 컨텍스트를 포함 하는 링크를 사용 하 여 공유 앱 또는 리소스에 액세스 해야 합니다. 환경은 Google에 이미 로그인했는지 여부에 따라 달라집니다.
  - Google에 로그인 하지 않은 게스트 사용자에 게는이 작업을 수행 하 라는 메시지가 표시 됩니다.
  - Google에 이미 로그인 한 게스트 사용자에 게 사용 하려는 계정을 선택 하 라는 메시지가 표시 됩니다. 게스트 사용자는 초대에 사용된 계정을 선택해야 합니다.

"헤더가 너무 깁니다." 오류가 표시 되는 게스트 사용자는 쿠키를 지우거 나 개인 또는 incognito 창을 열고 다시 로그인을 시도할 수 있습니다.

![Google 로그인 페이지를 보여 주는 스크린샷](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>제한 사항

팀은 모든 디바이스에서 Google 게스트 사용자를 완벽하게 지원합니다. Google 사용자는 `https://teams.microsoft.com`과 같은 일반 엔드포인트에서 팀에 로그인할 수 있습니다.

다른 애플리케이션의 일반 엔드포인트는 Google 사용자를 지원하지 않을 수 있습니다. Google 게스트 사용자는 테 넌 트 정보를 포함 하는 링크를 사용 하 여 로그인 해야 합니다. 예를 들면 다음과 같습니다.
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Google 게스트 사용자가 또는와 같은 링크를 사용 하려고 하면 `https://myapps.microsoft.com` `https://portal.azure.com` 오류가 발생 합니다.

링크가 테 넌 트 정보를 포함 하는 한 응용 프로그램 또는 리소스에 대 한 직접 링크를 Google 게스트 사용자에 게 제공할 수도 있습니다. 예: `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>1단계: Google 개발자 프로젝트 구성
먼저 Google 개발자 콘솔에서 새 프로젝트를 만들어 나중에 Azure Active Directory (Azure AD)에 추가할 수 있는 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 
1. https://console.developers.google.com 에서 Google API로 이동하고, Google 계정으로 로그인합니다. 공유 팀 Google 계정을 사용하는 것이 좋습니다.
2. 이 작업을 수행 하 라는 메시지가 표시 되 면 서비스 약관에 동의 합니다.
3. 새 프로젝트 만들기: 대시보드에서 **프로젝트 만들기** 를 선택 하 고 프로젝트에 이름 (예: **Azure AD B2B** )을 지정한 다음, **만들기** 를 선택 합니다. 
   
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
Google 페더레이션 설치 프로그램을 삭제할 수 있습니다. 이렇게 하면 이미 초대를 받은 Google 게스트 사용자는 로그인 할 수 없습니다. 하지만 디렉터리에서 리소스를 삭제 하 고 다시 초대 하 여 리소스에 대 한 액세스 권한을 다시 부여할 수 있습니다. 
 
**Azure AD 포털에서 Google 페더레이션을 삭제 하려면**
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 
2. **외부 ID** 를 선택합니다.
3. **모든 ID 공급자** 를 선택합니다.
4. **Google** line에서 줄임표 단추 ( **...** )를 선택 하 고 **삭제** 를 선택 합니다. 
   
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
