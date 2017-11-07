---
title: "정책: Azure AD SSPR | Microsoft Docs"
description: "Azure AD 셀프 서비스 암호 재설정 정책 옵션"
services: active-directory
keywords: "Active Directory 암호 관리, 암호 관리, Azure AD 셀프 서비스 암호 재설정"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f03c01158bf1a95ec03454b092bbe0314c4ea6be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory에서 암호 정책 및 제한

이 문서에서는 Azure AD 테넌트에 저장된 사용자 계정과 관련된 암호 정책 및 복잡성 요구 사항에 대해 설명합니다.

## <a name="administrator-password-policy-differences"></a>관리자 암호 정책 차이점

Microsoft는 모든 Azure 관리자 역할(예: 전역 관리자, 기술 지원팀 관리자, 암호 관리자 등)에 강력한 기본 암호 재설정 **두 개의 게이트** 정책을 적용합니다.

그러면 관리자가 보안 질문을 사용하지 않도록 설정하고 다음을 적용합니다.

두 가지 인증 데이터(전자 메일 주소 **및** 전화 번호)를 요구하는 두 개의 게이트 정책은 다음과 같은 경우에 적용됩니다.

* 모든 Azure 관리자 역할
  * 기술 지원팀 관리자
  * 서비스 지원 관리자
  * 대금 청구 관리자
  * 파트너 계층1 지원
  * 파트너 계층2 지원
  * Exchange 서비스 관리자
  * Lync 서비스 관리자
  * 사용자 계정 관리자
  * 디렉터리 작성자
  * 전역 관리자/회사 관리자
  * SharePoint 서비스 관리자
  * 규정 준수 관리자
  * 응용 프로그램 관리자
  * 보안 관리자
  * 권한 있는 역할 관리자
  * Intune 서비스 관리자
  * 응용 프로그램 프록시 서비스 관리자
  * CRM 서비스 관리자
  * Power BI 서비스 관리자
  
* 평가판에서 30일 경과 **또는**
* 베니티 도메인이 있는 경우(contoso.com) **또는**
* Azure AD Connect가 온-프레미스 디렉터리에서 ID를 동기화하는 경우

### <a name="exceptions"></a>예외
한 가지 인증 데이터(전자 메일 주소 **또는** 전화 번호)를 요구하는 한 개의 게이트 정책은 다음과 같은 경우에 적용됩니다.

* 첫 번째 30일 평가판 **또는**
* 베니티 도메인이 없는 경우(*.onmicrosoft.com) **및** Azure AD Connect가 ID를 동기화하지 않는 경우


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>모든 사용자 계정에 적용되는 UserPrincipalName 정책

Azure AD에 로그인해야 하는 모든 사용자 계정에는 해당 계정에 연결된 고유한 UPN(사용자 계정 이름) 특성 값이 있어야 합니다. 아래 표에서는 온-프레미스 클라우드로 동기화된 Active Directory 사용자 계정 및 클라우드 전용 사용자 계정에 모두 적용되는 정책을 간략하게 요약하고 있습니다.

| 속성 | UserPrincipalName 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul> <li>A-Z</li> <li>a-z</li><li>0-9</li> <li> 등 4가지 유형의 클러스터가 제공됩니다. - \_ ! \# ^ \~</li></ul> |
| 허용되지 않는 문자 |<ul> <li>도메인에서 사용자 이름을 구분하지 않는 모든 '@' 문자입니다.</li> <li>'@' 기호 바로 앞에는 '.'(마침표) 문자를 사용할 수 없습니다.</li></ul> |
| 길이 제약 조건 |<ul> <li>총 길이는 113자를 초과할 수 없습니다.</li><li>'@' 기호 앞에 64자</li><li>'@' 기호 뒤에 48자</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>클라우드 사용자 계정에만 적용되는 암호 정책

다음 테이블은 Azure AD에서 만들어지고 관리되는 사용자 계정에 적용할 수 있는 사용 가능 암호 정책 설정을 설명합니다.

| 속성 | 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul><li>A-Z</li><li>a-z</li><li>0-9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 허용되지 않는 문자 |<ul><li>유니코드 문자</li><li>공백</li><li> **강력한 암호만**: '@' 기호 바로 앞에 '.'(점) 문자를 사용할 수 없습니다.</li></ul> |
| 암호 제한 |<ul><li>최소 8자 및 최대 16자</li><li>**강력한 암호만**: 다음 4개 중 3개가 필요합니다.<ul><li>소문자</li><li>대문자</li><li>숫자(0-9)</li><li>기호(위 암호 제한 참조)</li></ul></li></ul> |
| 암호 만료 기간 |<ul><li>기본값: **90**일 </li><li>값은 Windows PowerShell용 Azure Active Directory 모듈에서 Set-MsolPasswordPolicy cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료 알림 |<ul><li>기본값: **14**일(암호 만료 이전)</li><li>값은 Set-MsolPasswordPolicy cmdlet을 사용하여 구성 할 수 있습니다.</li></ul> |
| 암호 만료 |<ul><li>기본값: **false**일(사용 가능한 암호 만료임을 나타냄) </li><li>Set-MsolUser cmdlet을 사용하여 개별 사용자 계정에 대한 값을 구성할 수 있습니다. </li></ul> |
| 암호 **변경** 기록 |암호를 **변경**한 경우 마지막 암호는 다시 **사용할 수 없습니다**. |
| 암호 **재설정** 기록 | 잊은 암호를 **다시 설정**하면 마지막 암호를 다시 사용**할 수도** 있습니다. |
| 계정 잠금 |로그인 시도를 10번 실패하면(잘못된 암호) 1분 동안 사용자가 잠기게 됩니다. 잘못된 로그인을 더 시도하면 사용자가 잠기는 시간이 더 늘어납니다. |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Azure Active Directory에서 암호 만료 정책 설정

Microsoft 클라우드 서비스의 전역 관리자는 Windows PowerShell용 Microsoft Azure Active Directory 모듈을 사용하여 사용자의 암호가 만료되지 않도록 설정할 수 있습니다. 또한 Windows PowerShell cmdlet을 사용하여 만료되지 않는 구성을 제거하거나 어떤 사용자 암호가 만료되지 않도록 설정되어 있는지 확인할 수 있습니다. 이 지침은 ID 및 디렉터리 서비스로 Microsoft Azure Active Directory를 사용하는 Microsoft Intune, Office 365와 같은 다른 공급자에 제공됩니다.

> [!NOTE]
> 디렉터리 동기화를 통해 동기화되지 않는 사용자 계정의 암호만 만료되지 않도록 구성할 수 있습니다. 디렉터리 동기화에 대한 자세한 내용은 [Azure AD와 AD 연결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)을 참조하세요.
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>PowerShell을 사용하여 암호 정책 설정 또는 확인

시작하려면 [Azure AD PowerShell 모듈을 다운로드하고 설치](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)해야 합니다. 설치를 완료한 후에는 아래 단계에 따라서 각 필드를 구성합니다.

### <a name="how-to-check-expiration-policy-for-a-password"></a>암호에 대한 만료 정책 확인 방법
1. 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
2. 다음 중 하나의 명령을 실행합니다.

   * 사용자의 암호가 만료되지 않도록 설정되어 있는지 확인하려면 해당 사용자의 사용자 계정 이름(UPN)(예: aprilr@contoso.onmicrosoft.com) 또는 사용자 ID를 사용하여 `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires` cmdlet을 실행합니다.
   * 모든 사용자에 대한 "암호 만료되지 않음" 설정을 보려면 `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires` cmdlet을 실행합니다.

### <a name="set-a-password-to-expire"></a>암호가 만료되도록 설정

1. 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
2. 다음 중 하나의 명령을 실행합니다.

   * 특정 사용자의 암호가 만료되도록 설정하려면 해당 사용자의 사용자 계정 이름(UPN) 또는 사용자 ID를 사용하여 다음 cmdlet을 실행합니다. `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * 조직의 모든 사용자 암호가 만료되도록 설정하려면 다음 cmdlet을 사용합니다. `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>암호가 만료되지 않도록 설정

1. 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
2. 다음 중 하나의 명령을 실행합니다.

   * 특정 사용자의 암호가 만료되지 않도록 설정하려면 해당 사용자의 사용자 계정 이름(UPN) 또는 사용자 ID를 사용하여 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * 조직의 모든 사용자 암호가 만료되지 않도록 설정하려면 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>다음 단계

다음 링크는 Azure AD를 사용한 암호 재설정에 대한 추가 정보를 제공합니다.

* [**빠른 시작**](active-directory-passwords-getting-started.md) - Azure AD 셀프 서비스 암호 관리를 사용하여 운영 시작 
* [**라이선스**](active-directory-passwords-licensing.md) - Azure AD 라이선스 구성
* [**데이터**](active-directory-passwords-data.md) - 암호 관리에 필요한 데이터 및 사용 방식을 이해
* [**롤아웃**](active-directory-passwords-best-practices.md) - 여기서 제공하는 지침을 사용하여 배포 계획을 세우고 사용자에게 SSPR 배포
* [**사용자 지정**](active-directory-passwords-customize.md) - 회사 SSPR 경험의 모양과 느낌을 사용자 지정.
* [**보고**](active-directory-passwords-reporting.md) - 사용자가 SSPR 기능에 액세스하는 조건, 시간 및 위치 탐색
* [**기술 심층 분석**](active-directory-passwords-how-it-works.md) - 작동 방식을 이해하기 위해 심층 분석
* [**질문과 대답**](active-directory-passwords-faq.md) - 어떤 방식으로? 그 이유는 무엇을? 어디서? 누가? 언제? - 많은 분들이 항상 묻는 질문에 대한 답변입니다.
* [**문제 해결**](active-directory-passwords-troubleshoot.md) - SSPR의 일반적인 문제 해결 방법 알아보기
