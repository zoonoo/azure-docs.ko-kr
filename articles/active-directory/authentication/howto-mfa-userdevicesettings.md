---
title: Azure Multi-Factor Authentication에 대 한 인증 방법 관리-Azure Active Directory
description: Azure에 대 한 사용자 설정 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다 Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6309ef6793858051ceaf3c3b33edb9f830b26710
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378048"
---
# <a name="manage-user-authentication-methods-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 대 한 사용자 인증 방법 관리

Azure AD의 사용자에 게는 두 가지 연락처 정보 집합이 있습니다.  

- 사용자 프로필에서 관리 되 고 조직의 구성원에 게 표시 되는 공개 프로필 연락처 정보입니다. 온-프레미스 Active Directory에서 동기화 된 사용자의 경우이 정보는 온-프레미스 Windows Server Active Directory Domain Services에서 관리 됩니다.
- 항상 비공개로 유지 되 고 MFA (multi-factor authentication)를 비롯 한 인증에만 사용 되는 인증 방법입니다. 관리자는 사용자의 인증 방법 블레이드에서 이러한 메서드를 관리할 수 있으며, 사용자는 MyAccount의 보안 정보 페이지에서 해당 메서드를 관리할 수 있습니다.

사용자에 대 한 Azure Multi-Factor Authentication 메서드를 관리 하는 경우 인증 관리자는 다음을 수행할 수 있습니다. 

1. MFA에 사용 되는 전화 번호를 포함 하 여 특정 사용자에 대 한 인증 방법을 추가 합니다.
1. 사용자 암호를 다시 설정합니다.
1. 사용자가 MFA를 다시 등록 해야 합니다.
1. 기존 MFA 세션을 취소 합니다.
1. 사용자의 기존 앱 암호 삭제  

## <a name="add-authentication-methods-for-a-user"></a>사용자에 대 한 인증 방법 추가 

Azure Portal 또는 Microsoft Graph를 통해 사용자에 대 한 인증 방법을 추가할 수 있습니다.  

> [!NOTE]
> 보안상의 이유로 공용 사용자 연락처 정보 필드는 MFA를 수행 하는 데 사용 하면 안 됩니다. 대신 사용자는 MFA에 사용할 인증 방법 번호를 채워야 합니다.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Azure Portal에서 인증 방법 추가":::

Azure Portal를 통해 사용자에 대 한 인증 방법을 추가 하려면:  

1. **Azure Portal** 에 로그인합니다. 
1. 사용자 **Azure Active Directory**  >  **Users**  >  **모든 사용자** 로 이동 합니다. 
1. 인증 방법을 추가 하려는 사용자를 선택 하 고 **인증 방법** 을 선택 합니다.  
1. 창 맨 위에서 **+ 인증 방법 추가** 를 선택 합니다.
   1. 방법 (전화 번호 또는 전자 메일)을 선택 합니다. 전자 메일을 암호 재설정에 사용할 수 있지만 인증은 사용할 수 없습니다. 전화 번호를 추가할 때 전화 유형을 선택 하 고 올바른 형식의 전화 번호 (예: + 1 4255551234)를 입력 합니다.
   1. **추가** 를 선택합니다.

> [!NOTE]
> 관리자는 미리 보기 환경을 통해 사용자에 게 사용 가능한 인증 방법을 추가할 수 있으며, 원래 환경에서는 휴대폰 및 대체 전화 방법의 업데이트만 허용 합니다.

### <a name="manage-methods-using-powershell"></a>PowerShell을 사용 하 여 메서드 관리:  

다음 명령을 사용 하 여 Microsoft Graph. Id PowerShell 모듈을 설치 합니다. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

특정 사용자에 대 한 전화 기반 인증 방법을 나열 합니다.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

특정 사용자에 대 한 휴대폰 인증 방법을 만듭니다.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType “mobile” -phoneNumber "+1 7748933135"
```

사용자에 대 한 특정 전화 메서드 제거

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Microsoft Graph Api를 사용 하 여 인증 방법을 관리할 수도 있습니다. 자세한 내용은 [AZURE AD 인증 방법 API 개요](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true) 문서에서 찾을 수 있습니다.

## <a name="manage-user-authentication-options"></a>사용자 인증 옵션 관리

*인증 관리자* 역할이 할당 된 경우 사용자에 게 암호를 재설정 하 고 MFA를 다시 등록 하거나 사용자 개체에서 기존 MFA 세션을 취소 하도록 요구할 수 있습니다. 사용자 설정을 관리 하려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 선택합니다.
1. 작업을 수행 하려는 사용자를 선택 하 고 **인증 방법** 을 선택 합니다. 창의 맨 위에서 사용자에 대 한 다음 옵션 중 하나를 선택 합니다.
   - **암호 다시 설정** 사용자의 암호를 다시 설정 하 고 다음 로그인 시 변경 해야 할 임시 암호를 할당 합니다.
   - **Mfa를 다시 등록 해야** 합니다. 그러면 사용자가 다음 번에 로그인 할 때 새 mfa 인증 방법을 설정 하도록 요청 됩니다.
   
      > [!NOTE]
      > 관리자가 MFA를 다시 등록 해야 하는 경우 사용자의 현재 등록 된 인증 방법이 삭제 되지 않습니다. 사용자가 MFA를 다시 등록 한 후에는 보안 정보를 검토 하 고 더 이상 사용할 수 없는 이전에 등록 된 인증 방법을 삭제 하는 것이 좋습니다.
   
   - **Mfa 세션 철회** 사용자의 기억 된 mfa 세션을 지우고 장치에서 정책에 따라 다음에 필요할 때 mfa를 수행 하도록 요구 합니다.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Azure Portal에서 인증 방법 관리":::

## <a name="delete-users-existing-app-passwords"></a>사용자의 기존 앱 암호 삭제

앱 암호를 정의한 사용자의 경우 관리자는 이러한 암호를 삭제 하도록 선택 하 여 해당 응용 프로그램에서 레거시 인증이 실패 하도록 할 수도 있습니다. 이러한 작업은 사용자에 게 지원을 제공 하거나 인증 방법을 다시 설정 해야 하는 경우에 필요할 수 있습니다. 이러한 앱 암호와 연결 된 비 브라우저 앱은 새 앱 암호를 만들 때까지 작동을 중지 합니다. 

사용자의 앱 암호를 삭제 하려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory**  >  **사용자**  >  **모든 사용자** 를 선택 합니다.
1. **Multi-Factor Authentication** 을 선택합니다. 이 메뉴 옵션을 보려면 오른쪽으로 스크롤해야 할 수 있습니다. 다음 스크린샷 예를 선택 하 여 전체 Azure Portal 창 및 메뉴 위치를 확인 합니다. [ ![ Azure AD의 사용자 창에서 Multi-Factor Authentication를 선택 합니다.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 오른쪽에 빠른 단계 옵션 목록이 표시 됩니다.
1. **사용자 설정 관리** 를 선택 하 고 다음 예제와 같이 모든 기존 앱 암호 삭제의 확인란을 선택 하 여 **선택한 사용자가 생성 한 모든 기존 앱 암호를 삭제** 합니다. 확인란을 선택 합니다. ![](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. **저장** , **닫기** 를 차례로 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 개별 사용자 설정을 구성 하는 방법을 살펴보았습니다. 전체 Azure Multi-Factor Authentication 서비스 설정을 구성 하려면 [azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md)을 참조 하세요.

사용자에 게 도움이 필요한 경우 [Azure Multi-Factor Authentication에 대 한 사용자 가이드](../user-help/multi-factor-authentication-end-user-first-time.md)를 참조 하세요.
