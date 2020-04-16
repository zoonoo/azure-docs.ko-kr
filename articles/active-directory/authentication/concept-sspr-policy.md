---
title: 셀프 서비스 암호 재설정 정책 - Azure Active Directory
description: 다양한 Azure Active Directory 셀프 서비스 암호 재설정 정책 옵션에 대해 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f5987bee82dc22d3742cb5d87040930e5d2c52d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393042"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory에서 셀프 서비스 암호 재설정 정책 및 제한

이 문서에서는 Azure AD(Azure Active Directory) 테넌트의 사용자 계정과 관련된 암호 정책 및 복잡성 요구 사항에 대해 설명합니다.

## <a name="administrator-reset-policy-differences"></a>관리자 재설정 정책의 차이점

**Microsoft는 모든 Azure 관리자 역할에 대해 강력한 기본 *2게이트* 암호 재설정 정책을 적용합니다.** 이 정책은 사용자에 대해 정의한 정책과 다를 수 있으며 이 정책은 변경할 수 없습니다. 항상 Azure 관리자 역할이 할당되지 않은 사용자로 암호 재설정 기능을 테스트해야 합니다.

2게이트 정책을 사용할 경우 **관리자는 보안 질문을 사용할 수 없습니다**.

두 게이트 정책은 *이메일 주소*, *인증자 앱* 또는 *전화 번호*와 같은 두 가지 인증 데이터를 요구합니다. 다음과 같은 경우에 두 게이트 정책이 적용됩니다.

* 다음과 같은 모든 Azure 관리자 역할이 영향을 받습니다.
  * 기술 지원팀 관리자
  * 서비스 지원 관리자
  * 대금 청구 관리자
  * 파트너 계층1 지원
  * 파트너 계층2 지원
  * Exchange 관리자
  * 비즈니스용 Skype 관리자
  * 사용자 관리자
  * 디렉터리 작성자
  * 전역 관리자 또는 회사 관리자
  * SharePoint 관리자
  * 규정 준수 관리자
  * 애플리케이션 관리자
  * 보안 관리자
  * 권한 있는 역할 관리자
  * Intune 관리자
  * 애플리케이션 프록시 서비스 관리자
  * 역학 365 관리자
  * Power BI 서비스 관리자
  * 인증 관리자
  * 권한 있는 인증 관리자

* 평가판 구독에서 30일이 경과한 경우 또는
* *contoso.com*같은 Azure AD 테넌트에 대해 사용자 지정 도메인이 구성되었습니다. 또는
* Azure AD Connect가 온-프레미스 디렉터리에서 ID를 동기화하는 경우

### <a name="exceptions"></a>예외

한 게이트 정책은 전자 메일 주소 또는 전화 번호 등, 한 가지 인증 데이터를 요구합니다. 다음과 같은 경우에 한 게이트 정책이 적용됩니다.

* 평가판 구독의 처음 30일 이내인 경우 또는
* Azure AD 테넌트에 대해 사용자 지정 도메인이 구성되지 않았므로 기본 **.onmicrosoft.com*. 기본 **.onmicrosoft.com* 도메인은 프로덕션 용으로 는 권장되지 않습니다. 및
* Azure AD Connect가 ID를 동기화하지 않는 경우

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>모든 사용자 계정에 적용되는 UserPrincipalName 정책

Azure AD에 로그인해야 하는 모든 사용자 계정에는 해당 계정에 연결된 고유한 UPN(사용자 계정 이름) 특성 값이 있어야 합니다. 다음 표에서는 클라우드와 클라우드 전용 사용자 계정에 동기화되는 온-프레미스 Active Directory 도메인 서비스 사용자 계정에 적용되는 정책을 간략하게 설명합니다.

| 속성 | UserPrincipalName 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul> <li>A-Z</li> <li>a-z</li><li>0-9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| 허용되지 않는 문자 |<ul> <li>도메인에서 사용자 이름을 구분하지 않는 모든 "\@\"" 문자입니다.</li> <li>"\@\"" 기호 바로 앞에는 "."(마침표) 문자를 사용할 수 없습니다.</li></ul> |
| 길이 제약 조건 |<ul> <li>총 길이는 113자를 초과할 수 없습니다.</li><li>"\@\"" 기호 앞에는 최대 64자를 사용할 수 있습니다.</li><li>"\@\"" 기호 뒤에는 최대 48자를 사용할 수 있습니다.</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>클라우드 사용자 계정에만 적용되는 암호 정책

다음 표에서는 Azure AD에서 만들고 관리하는 사용자 계정에 적용되는 암호 정책 설정에 대해 설명합니다.

| 속성 | 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul><li>A-Z</li><li>a-z</li><li>0-9</li> <li>@ # $ % ^ & * - _ ! + = = = = { = = = = = [ = = = = = = = = = = \ &#124; = \ \ \ \ \ \ : ' . ? / \`~ " ( ) ;</li> <li>빈 공간</li></ul> |
| 허용되지 않는 문자 | 유니코드 문자 |
| 암호 제한 |<ul><li>최소 8자, 최대 256자입니다.</li><li>다음 4개 중 3개가 필요합니다.<ul><li>소문자</li><li>대문자</li><li>숫자(0-9)</li><li>기호(이전 암호 제한 참조)</li></ul></li></ul> |
| 암호 만료 기간(최대 암호 사용 기간) |<ul><li>기본값: **90**일</li><li>값은 Windows PowerShell용 Azure Active Directory 모듈에서 `Set-MsolPasswordPolicy` cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료 알림(사용자에게 암호 만료 알림이 있는 경우) |<ul><li>기본값: **14**일(암호 만료 이전)</li><li>값은 `Set-MsolPasswordPolicy` cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료(암호가 만료되지 않도록 허용) |<ul><li>기본값: **false** false(암호의 만료 날짜가 있음을 나타냅니다).</li><li>`Set-MsolUser` cmdlet을 사용하여 개별 사용자 계정에 대한 값을 구성할 수 있습니다.</li></ul> |
| 암호 변경 기록 | 사용자 암호를 변경할 때 마지막 암호를 다시 사용할 수 *없습니다*. |
| 암호 재설정 기록 | 사용자가 잊어버린 암호를 재설정할 때 마지막 암호를 다시 사용할 수 *있습니다*. |
| 계정 잠금 | 로그인 시도를 10번 실패하면(잘못된 암호) 1분 동안 사용자가 잠기게 됩니다. 잘못된 로그인을 더 시도하면 사용자가 잠기는 시간이 더 늘어납니다. [스마트 잠금은](howto-password-smart-lockout.md) 마지막 세 가지 잘못된 암호 해시를 추적하여 동일한 암호에 대한 잠금 카운터를 증가하지 않도록 합니다. 다른 사용자가 동일한 잘못된 암호를 여러 번 입력해도 계정이 잠기지 않습니다. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Azure AD에서 암호 만료 정책 설정

Microsoft 클라우드 서비스의 *글로벌 관리자* 또는 *사용자 관리자는* *Windows PowerShell용 Microsoft Azure AD 모듈을* 사용하여 사용자 암호가 만료되지 않도록 설정할 수 있습니다. 또한 Windows PowerShell cmdlet을 사용하여 만료되지 않는 구성을 제거하거나 어떤 사용자 암호가 만료되지 않도록 설정되어 있는지 확인할 수 있습니다.

이 지침은 ID 및 디렉터리 서비스로 Azure AD를 사용하는 Intune, Office 365와 같은 다른 공급자에 제공됩니다. 암호 만료는 정책에서 변경할 수 있는 유일한 부분입니다.

> [!NOTE]
> 디렉터리 동기화를 통해 동기화되지 않는 사용자 계정의 암호만 만료되지 않도록 구성할 수 있습니다. 디렉터리 동기화에 대한 자세한 내용은 [Azure AD와 AD 연결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)을 참조하세요.

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>PowerShell을 사용하여 암호 정책 설정 또는 확인

시작하려면 [Azure AD PowerShell 모듈을 다운로드하여 설치합니다.](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0) 모듈을 설치한 후 다음 단계를 사용하여 각 필드를 구성합니다.

### <a name="check-the-expiration-policy-for-a-password"></a>암호에 대한 만료 정책 확인

1. 사용자 관리자 또는 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
1. 다음 명령 중 하나를 실행합니다.

   * 단일 사용자의 암호가 만료되지 않도록 설정되어 있는지 확인하려면 UPN(예: *\@aprilr contoso.onmicrosoft.com)* 또는 확인하려는 사용자의 사용자 ID를 사용하여 다음 cmdlet을 실행합니다.

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * 모든 사용자에 대한 **암호 사용 기간 제한 없음** 설정을 보려면  cmdlet을 실행합니다.

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>암호가 만료되도록 설정

1. 사용자 관리자 또는 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
1. 다음 중 하나의 명령을 실행합니다.

   * 특정 사용자의 암호가 만료되도록 설정하려면 해당 사용자의 UPN 또는 사용자 ID를 사용하여  cmdlet을 실행합니다.

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * 조직의 모든 사용자 암호가 만료되도록 설정하려면 다음 cmdlet을 사용합니다. 

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>암호가 만료되지 않도록 설정

1. 사용자 관리자 또는 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
1. 다음 중 하나의 명령을 실행합니다.

   * 특정 사용자의 암호가 만료되지 않도록 설정하려면 해당 사용자의 UPN 또는 사용자 ID를 사용하여  cmdlet을 실행합니다.

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * 조직 내 모든 사용자의 암호를 기간 제한 없음으로 설정하려면 다음 cmdlet을 실행합니다.

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > `-PasswordPolicies DisablePasswordExpiration`로 설정된 암호는 계속해서 `pwdLastSet` 특성에 따라 사용 기간이 계산됩니다. `pwdLastSet` 특성에 따라, 만료를 `-PasswordPolicies None`로 변경한 경우 `pwdLastSet`이 90일보다 오래된 모든 암호는 다음 번에 로그인할 때 변경해야 합니다. 이 변경으로 많은 사용자가 영향을 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계

SSPR을 시작하려면 [자습서: 사용자가 Azure Active Directory 셀프 서비스 암호 재설정을 사용하여 계정 잠금을 해제하거나 암호를 재설정할 수 있도록 설정합니다.](tutorial-enable-sspr.md)

사용자 또는 사용자가 SSPR에 문제가 있는 경우 [셀프 서비스 암호 재설정 문제를](active-directory-passwords-troubleshoot.md) 참조하십시오.
