---
title: B2B의 ID 공급자로 Google 추가 - Azure AD
description: 게스트 사용자가 자신의 Gmail 계정을 사용하여 Azure AD 앱에 로그인할 수 있도록 Google과 페더레이션
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
ms.openlocfilehash: 6dba592417ca07d1858a7942b162b4dfa6a85680
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273996"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가

Google과의 페더레이션을 설정하면 초대된 사용자가 MSA(Microsoft 계정)를 만들지 않고도 자신의 Gmail 계정을 사용하여 공유된 앱 및 리소스에 로그인할 수 있습니다. 

> [!NOTE]
> Google 페더레이션은 Gmail 사용자를 위해 특별히 설계되었습니다. G Suite 도메인과 페더레이션하려면 [직접 페더레이션 기능](direct-federation.md)을 사용합니다.

## <a name="what-is-the-experience-for-the-google-user"></a>Google 사용자를 위한 환경이란?
Google Gmail 사용자에게 초대를 보낼 때 게스트 사용자는 테넌트 컨텍스트를 포함하는 링크를 사용하여 공유된 앱 또는 리소스에 액세스해야 합니다. 환경은 Google에 이미 로그인했는지 여부에 따라 달라집니다.
  - 게스트 사용자가 Google에 로그인하지 않은 경우 Google에 로그인하라는 메시지가 표시됩니다.
  - 게스트 사용자가 Google에 이미 로그인된 경우에는 사용하려는 계정을 선택하라는 메시지가 표시됩니다. 게스트 사용자는 초대에 사용된 계정을 선택해야 합니다.

게스트 사용자에게 “헤더가 너무 김” 오류가 표시되는 경우 자신의 쿠키를 지우거나 프라이빗 또는 시크릿 창을 열고 다시 로그인을 시도해 볼 수 있습니다.

![Google 로그인 페이지를 보여주는 스크린샷](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>제한 사항

팀은 모든 디바이스에서 Google 게스트 사용자를 완벽하게 지원합니다. Google 사용자는 `https://teams.microsoft.com`과 같은 일반 엔드포인트에서 팀에 로그인할 수 있습니다.

다른 애플리케이션의 일반 엔드포인트는 Google 사용자를 지원하지 않을 수 있습니다. Google 게스트 사용자는 테넌트 정보가 포함된 링크를 사용하여 로그인해야 합니다. 예제는 다음과 같습니다.
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Google 게스트 사용자가 `https://myapps.microsoft.com` 또는 `https://portal.azure.com`과 같은 링크를 사용하려고 하면 오류가 발생합니다.

이 링크에 테넌트 정보(예: `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`)가 포함되어 있는 한 Google 게스트 사용자에게 애플리케이션 또는 리소스에 대한 직접 링크를 제공할 수도 있습니다. 

## <a name="step-1-configure-a-google-developer-project"></a>1단계: Google 개발자 프로젝트 구성
먼저 Google 개발자 콘솔에서 새 프로젝트를 만들어 나중에 Azure AD에 추가할 수 있는 클라이언트 ID 및 클라이언트 비밀을 가져옵니다. 
1. https://console.developers.google.com 에서 Google API로 이동하고, Google 계정으로 로그인합니다. 공유 팀 Google 계정을 사용하는 것이 좋습니다.
2. 메시지가 표시 되 면 서비스 약관에 동의 합니다.
3. 새 프로젝트 만들기: 대시보드에서 **프로젝트 만들기**를 선택 하 고 프로젝트 이름을 지정한 다음 (예: "AZURE AD B2B") **만들기**를 선택 합니다. 
   
   ![Google의 새 프로젝트 페이지를 보여주는 스크린샷](media/google-federation/google-new-project.png)

4. 이제 제공 되는 **api & 서비스** 페이지에서 새 프로젝트 아래에 있는 **보기** 를 클릭 합니다.

5. Api 카드에서 **api 개요로 이동을** 클릭 합니다. **OAuth 동의 화면**을 선택 합니다.

6. **외부**를 선택한 다음, **만들기**를 선택합니다. 

7. **OAuth 동의 화면**에서 **애플리케이션 이름**을 입력합니다. 

   ![Google OAuth 동의 화면 옵션을 보여주는 스크린샷](media/google-federation/google-oauth-consent-screen.png)

8. **권한 있는 도메인** 섹션으로 스크롤하고 microsoftonline.com을 입력합니다.

   ![권한 있는 도메인 섹션을 보여주는 스크린샷](media/google-federation/google-oauth-authorized-domains.PNG)

9. **저장**을 선택합니다.

10. **자격 증명**을 선택합니다. **자격 증명 만들기** 메뉴에서 **OAuth 클라이언트 ID**를 선택합니다.

    ![Google API 자격 증명 만들기 옵션을 보여주는 스크린샷](media/google-federation/google-api-credentials.png)

11. **응용 프로그램 종류**아래에서 **웹 응용 프로그램** 을 선택 하 고 응용 프로그램에 적합 한 이름을 지정 하 고 (예: "Azure AD B2B"), **권한 있는 리디렉션 uri**아래에 다음 uri를 입력 합니다.
    - `https://login.microsoftonline.com` 
    - `https://login.microsoftonline.com/te/<tenant id>/oauth2/authresp` <br>(여기서 `<tenant id>` 은 테 넌 트 ID)
   
    > [!NOTE]
    > 테 넌 트 ID를 찾으려면로 이동 하 여 https://portal.azure.com **Azure Active Directory**에서 **속성** 을 선택 하 고 **테 넌 트 id**를 복사 합니다.

    ![권한 있는 리디렉션 URI 섹션을 보여주는 스크린샷](media/google-federation/google-create-oauth-client-id.png)

12. **만들기**를 선택합니다. 클라이언트 ID 및 클라이언트 비밀을 복사합니다. Azure AD 포털에서 ID 공급자를 추가할 때 사용하게 됩니다.

    ![OAuth 클라이언트 ID 및 클라이언트 암호를 보여주는 스크린샷](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2단계: Azure AD에서 Google 페더레이션 구성 
이제 Azure AD 포털에 입력하거나 PowerShell을 사용하여 Google 클라이언트 ID 및 클라이언트 비밀을 설정합니다. Gmail 주소를 사용하고 자신을 초대하고, 초대된 Google 계정을 사용하여 초대 사용을 시도해봄으로써 Google 페더레이션 구성을 테스트해야 합니다. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 Google 페더레이션을 구성하려면 다음을 수행합니다. 
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **외부 ID**를 선택합니다.
3. **모든 ID 공급자**를 선택한 다음, **Google** 단추를 클릭합니다.
4. 그런 다음, 이전에 가져온 클라이언트 ID 및 클라이언트 비밀을 입력합니다. **저장**을 선택합니다. 

   ![Google ID 공급자 추가 페이지를 보여주는 스크린샷](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>PowerShell을 사용하여 Google 페더레이션을 구성하려면 다음을 수행합니다.
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 명령 `Connect-AzureAD`를 실행합니다.
3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.  
4. 다음 명령 실행: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > “1단계: Google 개발자 프로젝트 구성”에서 만든 앱의 클라이언트 ID 및 클라이언트 암호를 사용합니다. 자세한 내용은 [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 문서를 참조하세요. 
 
## <a name="how-do-i-remove-google-federation"></a>Google 페더레이션은 어떻게 제거하나요?
Google 페더레이션 설치 프로그램을 삭제할 수 있습니다. 이렇게 하면 이미 초대를 사용한 Google 게스트 사용자는 로그인할 수 없게 되지만, 디렉터리에서 해당 사용자를 삭제하고 다시 초대하여 리소스에 대한 액세스 권한을 다시 부여할 수 있습니다. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 Google 페더레이션을 삭제하려면 다음을 수행합니다. 
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **외부 ID**를 선택합니다.
3. **모든 ID 공급자**를 선택합니다.
4. **Google** 줄에서 바로 가기 메뉴( **...** )를 선택한 다음, **삭제**를 선택합니다. 
   
   ![소셜 ID 공급자의 삭제 옵션을 보여주는 스크린샷](media/google-federation/google-social-identity-providers.png)

1. **예**를 선택하여 삭제를 확인합니다. 

### <a name="to-delete-google-federation-by-using-powershell"></a>PowerShell을 사용하여 Google 페더레이션을 삭제하려면 다음을 수행합니다. 
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. `Connect-AzureAD`을 실행합니다.  
4. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.  
5. 다음 명령을 입력합니다.

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 자세한 내용은 [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)를 참조하세요. 
