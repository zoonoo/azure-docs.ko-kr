<properties
	pageTitle="Azure Active Directory에서 암호 정책 및 제한 | Microsoft Azure"
	description="허용되는 문자, 길이 및 만료를 포함하여 Azure Active Directory에서 암호에 적용되는 정책을 설명합니다."
  services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="curtand"/>


# Azure Active Directory에서 암호 정책 및 제한

이 문서에서는 Azure AD 디렉터리에 저장된 사용자 계정과 관련된 암호 정책 및 복잡성 요구 사항을 설명합니다.

> [AZURE.IMPORTANT] **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md).

## 모든 사용자 계정에 적용되는 UserPrincipalName 정책

Azure AD 인증 시스템에 로그인해야 하는 모든 사용자 계정에는 해당 계정에 연결된 고유한 사용자 계정 이름(UPN) 특성 값이 있어야 합니다. 다음 테이블은 온-프레미스 Active Directory 소싱 사용자 계정(클라우드로 동기화됨) 및 클라우드 전용 사용자 계정에 모두 적용되는 정책을 간략하게 설명합니다.

| 속성 | UserPrincipalName 요구 사항 |
|   ----------------------- |   ----------------------- |
| 허용되는 문자 | <ul> <li>A – Z</li> <li>a -z </li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| 허용되지 않는 문자 | <ul> <li>사용자 이름을 도메인으로부터 구분하지 않는 모든 '@' 문자.</li> <li>'@' 기호 바로 앞에 마침표 문자 '.'를 포함할 수 없습니다.</li></ul> |
| 길이 제약 조건 | <ul> <li>총 길이는 113 자를 초과할 수 없습니다</li><li>'@' 기호 앞에 64자</li><li>'@' 기호 뒤에 48자</li></ul>

## 클라우드 사용자 계정에만 적용되는 암호 정책

다음 테이블은 Azure AD에서 만들어지고 관리되는 사용자 계정에 적용할 수 있는 사용 가능 암호 정책 설정을 설명합니다.

| 속성 | 요구 사항 |
|   ----------------------- |   ----------------------- |
| 허용되는 문자 | <ul><li>A – Z</li><li>a -z </li><li>0 – 9</li> <li>@ # $ % ^ & * - \_ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 허용되지 않는 문자 | <ul><li>유니코드 문자</li><li>공백</li><li> **강력한 암호만**: '@' 기호 바로 앞에 점 문자'.'를 포함할 수 없습니다.</li></ul> |
| 암호 제한 | <ul><li>최소 8자 및 최대 16자</li><li>**강력한 암호만**: 다음의 4가지 중에서 3가지가 필요합니다.<ul><li>소문자</li><li>대문자</li><li>숫자(0-9)</li><li>기호(위의 암호 제한 참조)</li></ul></li></ul> |
| 암호 만료 기간 | <ul><li>기본값: **90**일 </li><li>값은 Windows PowerShell용 Azure Active Directory 모듈에서 Set-MsolPasswordPolicy cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료 알림 | <ul><li>기본값: **14**일(암호가 만료되기 전에)</li><li>값은 Set-MsolPasswordPolicy cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료 | <ul><li>기본값: **false**일 (암호 만료가 사용됨을 나타냄) </li><li>Set-MsolUser cmdlet을 사용하여 개별 사용자 계정에 대한 값을 구성할 수 있습니다. </li></ul> |
| 암호 기록 | 마지막 암호를 다시 사용할 수 없습니다. |
| 암호 기록 기간 | 영구 |
| 계정 잠금 | 로그인 시도를 10번 실패하면(잘못된 암호) 1분 동안 사용자가 잠기게 됩니다. 잘못된 로그인을 더 시도하면 사용자가 잠기는 시간이 더 늘어납니다. |


## 다음 단계

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

<!---HONumber=AcomDC_0713_2016-->
