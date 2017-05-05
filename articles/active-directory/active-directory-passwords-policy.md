---
title: "Azure Active Directory에서 암호 정책 및 제한 | Microsoft 문서"
description: "허용되는 문자, 길이 및 만료를 포함하여 Azure Active Directory에서 암호에 적용되는 정책을 설명합니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e7cae5cf-e0ee-467b-9a90-db6fdf56cd52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: fc73b667112fe35473872c33bc98423a424c5c65
ms.openlocfilehash: d6bee5afea6afcd756b53f41baef5dd984423c0a
ms.lasthandoff: 02/17/2017


---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory에서 암호 정책 및 제한
이 문서에서는 Azure AD 디렉터리에 저장된 사용자 계정과 관련된 암호 정책 및 복잡성 요구 사항에 대해 설명합니다.

> [!IMPORTANT]
> **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).
>
>

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>모든 사용자 계정에 적용되는 UserPrincipalName 정책
Azure AD 인증 시스템에 로그인해야 하는 모든 사용자 계정에는 해당 계정에 연결된 고유한 UPN(사용자 계정 이름) 특성 값이 있어야 합니다. 다음 표에서는 온-프레미스 Active Directory 기반 사용자 계정(클라우드로 동기화됨) 및 클라우드 전용 사용자 계정에 모두 적용되는 정책을 간략하게 요약하고 있습니다.

| 자산 | UserPrincipalName 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul> <li>A-Z</li> <li>a-z</li><li>0-9</li> <li> 을 참조하세요. - \_ ! \# ^ \~</li></ul> |
| 허용되지 않는 문자 |<ul> <li>도메인에서 사용자 이름을 구분하지 않는 모든 '@' 문자입니다.</li> <li>'@' 기호 바로 앞에는 '.'(마침표) 문자를 사용할 수 없습니다.</li></ul> |
| 길이 제약 조건 |<ul> <li>총 길이는 113자를 초과할 수 없습니다.</li><li>‘@’ 기호 앞에&64;자</li><li>‘@’ 기호 뒤에&48;자</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>클라우드 사용자 계정에만 적용되는 암호 정책
다음 표에서는 Azure AD에서 만들어지고 관리되는 사용자 계정에 적용할 수 있는 사용 가능 암호 정책 설정을 설명합니다.

| 자산 | 요구 사항 |
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

## <a name="next-steps"></a>다음 단계
* **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).
* [어디에서나 암호 관리](active-directory-passwords.md)
* [암호 관리의 작동 원리](active-directory-passwords-how-it-works.md)
* [암호 관리 시작](active-directory-passwords-getting-started.md)
* [암호 관리 사용자 지정](active-directory-passwords-customize.md)
* [암호 관리 모범 사례](active-directory-passwords-best-practices.md)
* [암호 관리 보고서와 함께 Operational Insights를 얻는 방법](active-directory-passwords-get-insights.md)
* [암호 관리 FAQ](active-directory-passwords-faq.md)
* [암호 관리 문제 해결](active-directory-passwords-troubleshoot.md)
* [자세한 정보](active-directory-passwords-learn-more.md)

