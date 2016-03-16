
<properties
	pageTitle="특성을 사용하여 고급 규칙 만들기| Microsoft Azure"
	description="지원되는 식 규칙 연산자 및 매개 변수를 포함하는 그룹에 대한 고급 규칙 만들기"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# 특성을 사용하여 고급 규칙 만들기
Azure 포털은 Azure Active Directory(Azure AD)에서 고급 규칙을 설정할 수 있는 유연성을 제공하여 Azure AD 그룹에 복잡한 동적 멤버 자격을 사용할 수 있도록 합니다.

**고급 규칙을 만들려면** Azure 포털에 있는 해당 그룹의 **구성** 탭에서 **고급 규칙** 옵션을 선택하고 제공된 텍스트 상자에 고급 규칙을 입력합니다. 다음 정보를 사용하여 고급 규칙을 만들 수 있습니다.

## 고급 규칙 본문 생성
그룹의 동적 멤버 자격에 대해 만들 수 있는 고급 규칙은 기본적으로 세 부분으로 구성되며 true 또는 false 결과를 생성하는 이진 식입니다. 세 부분은 다음과 같습니다.

- 왼쪽 매개 변수
- 이항 연산자
- 오른쪽 상수

고급 규칙의 전체 모양은 (leftParameter binaryOperator "RightConstant")와 유사합니다. 여기서 열기 및 닫기 괄호는 전체 이진 식에 필요하고 큰 따옴표는 오른쪽 상수에 필요하며 왼쪽 매개 변수에 대한 구문은 user.property입니다. 고급 규칙은 -and, -or 및 -not 논리 연산자로 구분된 두 개 이상의 이진 식으로 구성될 수 있습니다. 다음은 제대로 구성된 고급 규칙의 예입니다.

- (user.department -eq "Sales") -or (user.department -eq "Marketing")
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

지원되는 매개 변수 및 식 규칙 연산자의 전체 목록은 아래 섹션을 참조하세요.

고급 규칙 본문의 총 길이는 2048자를 초과할 수 없습니다.
> [AZURE.NOTE]
문자열 및 regex 연산은 대/소문자를 구분합니다. $null을 상수로 사용하여 Null 확인을 수행할 수도 있습니다(예: user.department -eq $null). 따옴표(")를 포함하는 문자열은 ' 문자를 사용하여 이스케이프해야 합니다(예: user.department -eq "Sa`"les").

##지원되는 식 규칙 연산자
다음 표에는 지원되는 모든 식 규칙 연산자와 고급 규칙 본문에 사용할 수 있는 해당 구문이 나와 있습니다.

| 연산자 | 구문 |
|-----------------|----------------|
| 같지 않음 | -ne |
| 같음 | -eq |
| 다음으로 시작 안 함 | -notStartsWith |
| 시작 | -startsWith |
| 포함하지 않음 | -notContains |
| 포함 | -contains |
| 일치하지 않음 | -notMatch |
| 일치 | -match |


| 쿼리 구문 분석 오류 | 잘못된 사용법 | 수정된 사용법 |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 오류: 특성이 지원되지 않습니다. | (user.invalidProperty -eq "Value") | (user.department -eq "value")속성은 위의 지원되는 속성 목록 중 하나의 항목과 일치해야 합니다. |
| 오류: 특성에서는 연산자가 지원되지 않습니다. | (user.accountEnabled -contains true) | (user.accountEnabled -eq true)속성은 부울 형식입니다. 위의 목록에서 부울 형식에 지원되는 연산자(-eq 또는 -ne)를 사용합니다. |
| 오류: 쿼리 컴파일 오류입니다. | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")논리 연산자는 위의 지원되는 특성 목록 중 하나의 항목과 일치해야 합니다.(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")정규식 오류입니다. |
| 오류: 이진 식 형식이 올바르지 않습니다. | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")쿼리에 오류가 여러 개 있습니다. 괄호 위치가 적절하지 않습니다. |
| 오류: 동적 멤버 자격을 설정하는 동안 알 수 없는 오류가 발생했습니다. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")쿼리에 오류가 여러 개 있습니다. 괄호 위치가 적절하지 않습니다. |

##지원되는 매개 변수
다음은 고급 규칙에 사용할 수 있는 모든 사용자 속성입니다.

**부울 형식의 속성**

허용되는 연산자

* -eq


* -ne


| 속성 | 허용되는 값 | 사용 현황 |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**문자열 형식의 속성**

허용되는 연산자

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| 속성 | 허용되는 값 | 사용 현황 |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | 임의의 문자열 값 또는 $null입니다. | (user.city -eq "value") |
| country | 임의의 문자열 값 또는 $null입니다. | (user.country -eq "value") |
| department | 임의의 문자열 값 또는 $null입니다. | (user.department -eq "value") |
| displayName | 임의의 문자열 값입니다. | (user.displayName -eq "value") |
| facsimileTelephoneNumber | 임의의 문자열 값 또는 $null입니다. | (user.facsimileTelephoneNumber -eq "value") |
| givenName | 임의의 문자열 값 또는 $null입니다. | (user.givenName -eq "value") |
| jobTitle | 임의의 문자열 값 또는 $null입니다. | (user.jobTitle -eq "value") |
| mail | 임의의 문자열 값 또는 $null입니다. 사용자의 SMTP 주소입니다. | (user.mail -eq "value") |
| mailNickName | 임의의 문자열 값입니다. 사용자의 메일 별칭입니다. | (user.mailNickName -eq "value") |
| mobile | 임의의 문자열 값 또는 $null입니다. | (user.mobile -eq "value") |
| objectId | 사용자 개체의 GUID입니다. | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | 임의의 문자열 값 또는 $null입니다. | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | 임의의 문자열 값 또는 $null입니다. | (user.postalCode -eq "value") |
| preferredLanguage | ISO 639-1 코드 | (user.preferredLanguage -eq "ko-KR") |
| sipProxyAddress | 임의의 문자열 값 또는 $null입니다. | (user.sipProxyAddress -eq "value") |
| state | 임의의 문자열 값 또는 $null입니다. | (user.state -eq "value") |
| streetAddress | 임의의 문자열 값 또는 $null입니다. | (user.streetAddress -eq "value") |
| surname | 임의의 문자열 값 또는 $null입니다. | (user.surname-eq "value") |
| telephoneNumber | 임의의 문자열 값 또는 $null입니다. | (user.telephoneNumber -eq "value") |
| usageLocation | 두 자로 된 국가 코드 | (user.usageLocation -eq "US") |
| userPrincipalName | 임의의 문자열 값입니다. | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (user.userType -eq "Member") |

**문자열 컬렉션 형식의 속성**

허용되는 연산자

* -contains


* -notContains

| 속성 | 허용되는 값 | 사용 현황 |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | 임의의 문자열 값입니다. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## 확장 특성 및 사용자 지정 특성
확장 특성 및 사용자 지정 특성은 동적 멤버 자격 규칙에서 지원됩니다.

확장 특성은 온-프레미스 Windows Server AD에서 동기화되고 "ExtensionAttributeX" 형식을 사용하며 여기서 X는 1 - 15입니다. 확장 특성을 사용하는 규칙의 예는 다음과 같습니다.

(user.extensionAttribute15 -eq "Marketing")

사용자 지정 특성은 온-프레미스 Windows Server AD 또는 연결된 SaaS 응용 프로그램에서 동기화되고 "user.extension\_[GUID]\_\_[Attribute]" 형식입니다. 여기서 [GUID]는 AAD에서 특성을 만든 응용 프로그램에 대한 AAD의 고유한 식별자이고 [Attribute]는 만들어진 특성 이름입니다. 사용자 지정 특성을 사용하는 규칙의 예는 다음과 같습니다.

user.extension\_c272a57b722d4eb29bfe327874ae79cb\_\_OfficeNumber

사용자 지정 특성 이름은 Graph Explorer를 사용하여 사용자의 특성을 쿼리하거나 특성 이름을 검색하여 디렉터리에서 찾을 수 있습니다.

## 직접 보고 규칙
이제 사용자의 관리자 특성에 따라 그룹의 멤버를 채울 수 있습니다.
"관리자" 그룹으로 그룹을 구성하려면
--------------------------------------------------------------------------------
1. 관리자 포털에서 **구성**탭을 클릭하고 **고급 규칙**을 선택합니다.
2. 다음 구문을 사용하여 규칙을 입력합니다.*{UserID\_of\_manager}에 대한 직접 보고*를 위한 직접 보고 직접 보고에 대해 유효한 규칙의 예는 다음과 같습니다.

"62e19b97-8b3d-4d4a-a106-4ce66896a863"에 대한 직접 보고

여기서 "62e19b97-8b3d-4d4a-a106-4ce66896a863"은 관리자의 objectID입니다. 개체 ID는 관리자인 사용자의 사용자 페이지의 프로필 탭에 있는 AAD 관리 포털에서 찾을 수 있습니다.

3. 이 규칙을 저장하면 규칙을 만족하는 모든 사용자가 그룹의 구성원으로 가입됩니다. 그룹을 처음 채울 때는 몇 분 정도 걸릴 수 있습니다.


## 추가 정보
이러한 문서는 Azure Active Directory에 추가 정보를 제공합니다.

* [그룹의 동적 멤버 자격 문제 해결](active-directory-accessmanagement-troubleshooting.md)

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)

* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

* [Azure Active Directory란?](active-directory-whatis.md)

* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->