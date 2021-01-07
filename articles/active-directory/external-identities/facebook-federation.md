---
title: Facebook을 ID 공급자로 추가 - Azure AD
description: Facebook과 페더레이션하여 외부 사용자(게스트)가 자신의 Facebook 계정을 사용하여 Azure AD 앱에 로그인할 수 있도록 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 547b455dc776b7304e413b3b6f1330e7cedcf2a2
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442000"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Facebook을 외부 ID에 대한 ID 공급자로 추가

사용자가 자신의 Facebook 계정을 사용하여 애플리케이션에 로그인할 수 있도록 셀프 서비스 등록 사용자 흐름(미리 보기)에 Facebook을 추가할 수 있습니다. 사용자가 Facebook을 사용하여 로그인할 수 있도록 하려면 먼저 테넌트에 대해 [셀프 서비스 등록을 사용하도록 설정](self-service-sign-up-user-flow.md)해야 합니다. Facebook을 ID 공급자로 추가한 후에는 애플리케이션에 대한 사용자 흐름을 설정하고 로그인 옵션 중 하나로 Facebook을 선택합니다.

> [!NOTE]
> 사용자는 자신의 Facebook 계정을 사용하여 셀프 서비스 가입 및 사용자 흐름에 따라 앱에 가입할 수만 있습니다. 사용자는 Facebook 계정을 사용하여 초대를 받고 초대권을 사용할 수 없습니다.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Facebook 개발자 콘솔에서 앱 만들기

[ID 공급자](identity-providers.md)로 Facebook 계정을 사용하려면 Facebook 개발자 콘솔에서 애플리케이션을 만들어야 합니다. Facebook 계정이 없는 경우 [https://www.facebook.com/](https://www.facebook.com)에서 등록할 수 있습니다.

> [!NOTE]  
> 아래의 9 및 16단계에서 다음 URL을 사용합니다.
> - **사이트 URL** 에 응용 프로그램의 주소를 입력 합니다 (예:) `https://contoso.com` .
> - **유효한 OAuth 리디렉션 URI**에 `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp`를 입력합니다. Azure Active Directory 개요 블레이드에서 `<tenant-ID>`를 찾을 수 있습니다.


1. Facebook 계정 자격 증명으로 [개발자용 Facebook](https://developers.facebook.com/)에 로그인합니다.
2. 아직 등록하지 않은 경우 Facebook 개발자로 등록해야 합니다. 이를 수행하려면 페이지의 오른쪽 위에서 **시작**을 선택하고 Facebook의 정책에 동의한 후 등록 단계를 완료합니다.
3. **내 앱**을 선택한 다음, **앱 만들기**를 선택합니다.
4. **표시 이름** 및 유효한 **연락처 전자 메일**을 제공합니다.
5. **앱 ID 만들기**를 선택합니다. Facebook 플랫폼 정책을 수용하고 온라인 보안 검사를 완료해야 합니다.
6. **설정** > **기본**을 선택합니다.
7. 비즈니스 및 페이지와 같은 **범주**를 선택합니다. 이 값은 Facebook의 경우 필수이지만 Azure AD에서는 사용되지 않습니다.
8. 페이지의 맨 아래에서 **플랫폼 추가**를 선택한 후 **웹 사이트**를 선택합니다.
9. **사이트 URL**에서 위에 명시된 적절한 URL을 입력합니다.
10. **개인 정보 취급 방침 URL**에서 애플리케이션에 대한 개인 정보를 유지 관리하는 페이지의 URL을 입력합니다(예: `http://www.contoso.com`).
11. **변경 내용 저장**을 선택합니다.
12. 페이지의 맨 위에서 **앱 ID** 값을 복사합니다.
13. **표시**를 선택하고 **앱 비밀** 값을 복사합니다. 테넌트에서 Facebook을 ID 공급자로 구성하려면 둘 다 사용합니다. **앱 암호** 는 중요한 보안 자격 증명입니다.
14. **제품** 옆에 있는 더하기 기호를 선택한 다음, **Facebook 로그인**에 있는 **설정**을 선택합니다.
15. **Facebook 로그인**에서 **설정**을 선택합니다.
16. **유효한 OAuth 리디렉션 URI**에서 해당 URL(위에서 언급됨)을 입력합니다.
17. 페이지 아래쪽에 있는 **변경 내용 저장**을 선택합니다.
18. Azure AD에서 Facebook 애플리케이션을 사용할 수 있도록 하려면 페이지 오른쪽 위에 있는 상태 선택기를 선택한 후, **켜기**로 바꾸어 애플리케이션을 공용으로 만들고 **모드 전환**을 선택합니다. 이때 상태가 **개발**에서 **라이브**로 변경됩니다.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook 계정을 ID 공급자로 구성
이제 Azure AD 포털에 입력하거나 PowerShell을 사용하여 Facebook 클라이언트 ID 및 클라이언트 암호를 설정합니다. 셀프 서비스 가입이 설정된 앱에서 사용자 흐름을 통해 가입하여 Facebook 구성을 테스트할 수 있습니다.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 Facebook 페더레이션을 구성하려면 다음을 수행합니다.
1. Azure AD 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services**에서 **Azure Active Directory**를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID**를 선택합니다.
4. **전체 ID 공급자**를 선택한 다음, **Facebook**을 선택합니다.
5. **Client ID**에 대해 이전에 만든 Facebook 애플리케이션의 **앱 ID**를 입력합니다.
6. **클라이언트 암호**에는 기록한 **앱 비밀**을 입력합니다.

   ![소셜 ID 공급자 추가 페이지를 보여 주는 스크린샷](media/facebook-federation/add-social-identity-provider-page.png)

7. **저장**을 선택합니다.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>PowerShell을 사용하여 Facebook 페더레이션을 구성하려면 다음을 수행합니다.
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 명령 `Connect-AzureAD`를 실행합니다.
3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.  
4. 다음 명령 실행: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Facebook 개발자 콘솔에서 위에서 만든 앱의 클라이언트 ID 및 클라이언트 암호를 사용합니다. 자세한 내용은 [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 문서를 참조하세요. 

## <a name="how-do-i-remove-facebook-federation"></a>Facebook 페더레이션을 제거하려면 어떻게 해야 하나요?
Facebook 페더레이션 설치 프로그램을 삭제할 수 있습니다. 이렇게 하면 Facebook 계정으로 사용자 흐름을 통해 가입한 모든 사용자가 더 이상 로그인할 수 없습니다. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 Facebook 페더레이션을 삭제하려면 다음을 수행합니다. 
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **외부 ID**를 선택합니다.
3. **모든 ID 공급자**를 선택합니다.
4. **Facebook** 줄에서 바로 가기 메뉴( **...** )를 선택한 다음, **삭제**를 선택합니다. 
5. **예**를 선택하여 삭제를 확인합니다.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>PowerShell을 사용하여 Facebook 페더레이션을 삭제하려면 다음을 수행합니다. 
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. `Connect-AzureAD`을 실행합니다.  
4. 로그인 프롬프트에서 관리형 전역 관리자 계정으로 로그인합니다.  
5. 다음 명령을 입력합니다.

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > 자세한 내용은 [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [앱에 셀프 서비스 등록 추가](self-service-sign-up-user-flow.md)