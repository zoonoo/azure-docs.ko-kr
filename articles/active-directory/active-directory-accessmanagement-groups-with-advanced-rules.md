---
title: "Azure Active Directory에서 개체 특성에 따른 동적 그룹 채우기 | Microsoft Docs"
description: "그룹 멤버 자격에 대해 지원되는 식 규칙 연산자 및 매개 변수를 포함한 고급 규칙을 만드는 방법입니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal
ms.openlocfilehash: 168829494de33a21df68d3fc5e2a174f435c5ad5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="populate-groups-dynamically-based-on-object-attributes"></a>개체 특성에 따른 동적 그룹 채우기
Azure 클래식 포털에서는 Azure AD(Azure Active Directory) 그룹에 대해 더 복잡한 특성 기반의 동적 멤버 자격을 사용하도록 설정할 수 있는 기능을 제공합니다.  

사용자 또는 장치의 특성이 변경될 때 변경 내용이 그룹 추가 또는 제거를 트리거할지를 확인하기 위해 시스템은 디렉터리에서 모든 동적 그룹 규칙을 평가합니다. 사용자 또는 장치가 그룹에 대한 규칙을 만족하면 해당 그룹의 멤버로 추가됩니다. 규칙을 더 이상 만족하지 않는 경우 제거됩니다.

> [!NOTE]
> - 보안 그룹 또는 Office 365 그룹에서 동적 멤버 자격에 대한 규칙을 설정할 수 있습니다.
>
> - 이 기능은 하나 이상의 동적 그룹에 추가된 각 사용자 멤버에 대해 Azure AD Premium P1 라이선스가 필요합니다.
>
> - 장치 또는 사용자에 대한 동적 그룹을 만들 수 있지만 사용자 및 장치 개체를 모두 포함하는 규칙을 만들 수는 없습니다.

> - 현재 소유 사용자의 특성에 따라 장치 그룹을 만들 수 없습니다. 장치 멤버 자격 규칙은 디렉터리에 있는 장치 개체의 즉각적인 특성만 참조할 수 있습니다.

## <a name="to-create-an-advanced-rule"></a>고급 규칙을 만들려면
1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory**를 선택한 다음 조직의 디렉터리를 엽니다.
2. **그룹** 탭을 선택하고 편집할 그룹을 엽니다.
3. **구성** 탭, **고급 규칙** 옵션을 선택한 다음 텍스트 상자에 고급 규칙을 입력합니다.

## <a name="constructing-the-body-of-an-advanced-rule"></a>고급 규칙 본문 생성
그룹의 동적 멤버 자격에 대해 만들 수 있는 고급 규칙은 기본적으로 세 부분으로 구성되며 true 또는 false 결과를 생성하는 이진 식입니다. 세 부분은 다음과 같습니다.

* 왼쪽 매개 변수
* 이항 연산자
* 오른쪽 상수

고급 규칙의 전체 모양은 (leftParameter binaryOperator "RightConstant")와 유사합니다. 여기서 열기 및 닫기 괄호는 전체 이진 식에 필요하고 큰 따옴표는 오른쪽 상수에 필요하며 왼쪽 매개 변수에 대한 구문은 user.property입니다. 고급 규칙은 -and, -or 및 -not 논리 연산자로 구분된 두 개 이상의 이진 식으로 구성될 수 있습니다.
다음은 제대로 구성된 고급 규칙의 예입니다.

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

지원되는 매개 변수 및 식 규칙 연산자의 전체 목록은 아래 섹션을 참조하세요.


속성에는 올바른 개체 형식 접두어(user 또는 device)가 있어야 합니다.
mail -ne null 규칙은 유효성 검사에 실패합니다.

올바른 규칙은 다음과 같습니다.

user.mail-ne null

고급 규칙 본문의 총 길이는 2048자를 초과할 수 없습니다.

> [!NOTE]
> 문자열 및 regex 연산은 대/소문자를 구분합니다.
> 따옴표(")를 포함하는 문자열은 ' 문자를 사용하여 이스케이프해야 합니다(예: user.department -eq \`"Sales").
> 문자열 형식 값에 대해서만 따옴표를 사용하고, 영어 따옴표만 사용합니다.
>
>

## <a name="supported-expression-rule-operators"></a>지원되는 식 규칙 연산자
다음 표에는 지원되는 모든 식 규칙 연산자와 고급 규칙 본문에 사용할 수 있는 해당 구문이 나와 있습니다.

| 연산자 | 구문 |
| --- | --- |
| 같지 않음 |-ne |
| 같음 |-eq |
| 다음으로 시작 안 함 |-notStartsWith |
| 시작 |-startsWith |
| 포함하지 않음 |-notContains |
| 포함 |-contains |
| 일치하지 않음 |-notMatch |
| 일치 |-match |
| 내용 | -in |
| 속하지 않음 | -notIn |

## <a name="operator-precedence"></a>연산자 우선 순위

모든 연산자는 낮은 우선 순위에서 높은 우선 순위까지 나열되며(-any -all -or -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn), 같은 줄의 연산자는 동등한 우선 순위를 가집니다.

모든 연산자는 하이픈(-) 접두사를 사용하거나 사용하지 않을 수 있습니다.

괄호는 항상 필요한 것이 아니지만, 우선 순위가 요구 사항을 충족하지 못할 경우 괄호를 추가하면 됩니다. 예를 들면 다음과 같습니다.

   user.department –eq "Marketing" –and user.country –eq "US"

이는 다음과 동등합니다.

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="using-the--in-and--notin-operators"></a>-In 및 -notIn 연산자 사용

사용자 특성의 값을 다양한 값과 비교하려면 -In 또는 -notIn 연산자를 사용할 수 있습니다. 다음은 -In 연산자를 사용하는 예제입니다.

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

값 목록의 처음과 끝에 "[" 및 "]"를 사용합니다. user.department 값이 목록의 값 중 하나와 같으면 이 조건에서 True로 평가합니다.

## <a name="query-error-remediation"></a>쿼리 오류 수정
다음 표에서는 잠재적인 오류와 오류가 발생할 경우 이를 수정하는 방법을 나열합니다.

| 쿼리 구문 분석 오류 | 잘못된 사용법 | 수정된 사용법 |
| --- | --- | --- |
| 오류: 특성이 지원되지 않습니다. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>속성은 [지원되는 속성 목록](#supported-properties)를 선택합니다. |
| 오류: 특성에서는 연산자가 지원되지 않습니다. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/>속성은 부울 형식입니다. 위의 목록에서 부울 형식에 지원되는 연산자(-eq 또는 -ne)를 사용합니다. |
| 오류: 쿼리 컴파일 오류입니다. |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>논리 연산자는 위의 지원되는 특성 목록 중 하나의 항목과 일치해야 합니다.(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")정규식 오류입니다. |
| 오류: 이진 식 형식이 올바르지 않습니다. |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>쿼리에 오류가 여러 개 있습니다. 괄호 위치가 적절하지 않습니다. |
| 오류: 동적 멤버 자격을 설정하는 동안 알 수 없는 오류가 발생했습니다. |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>쿼리에 오류가 여러 개 있습니다. 괄호 위치가 적절하지 않습니다. |

## <a name="supported-properties"></a>지원되는 속성
다음은 고급 규칙에 사용할 수 있는 모든 사용자 속성입니다.

### <a name="properties-of-type-boolean"></a>부울 형식의 속성
허용되는 연산자

* -eq
* -ne

| 속성 | 허용되는 값 | 사용 현황 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>문자열 형식의 속성
허용되는 연산자

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| 속성 | 허용되는 값 | 사용 현황 |
| --- | --- | --- |
| city |임의의 문자열 값 또는 $null입니다. |(user.city -eq "value") |
| country |임의의 문자열 값 또는 $null입니다. |(user.country -eq "value") |
| companyName | 임의의 문자열 값 또는 $null입니다. | (user.companyName -eq "value") |
| department |임의의 문자열 값 또는 $null입니다. |(user.department -eq "value") |
| displayName |임의의 문자열 값입니다. |(user.displayName -eq "value") |
| facsimileTelephoneNumber |임의의 문자열 값 또는 $null입니다. |(user.facsimileTelephoneNumber -eq "value") |
| givenName |임의의 문자열 값 또는 $null입니다. |(user.givenName -eq "value") |
| jobTitle |임의의 문자열 값 또는 $null입니다. |(user.jobTitle -eq "value") |
| mail |임의의 문자열 값 또는 $null(사용자의 SMTP 주소)입니다. |(user.mail -eq "value") |
| mailNickName |임의의 문자열 값(사용자의 메일 별칭) |(user.mailNickName -eq "value") |
| mobile |임의의 문자열 값 또는 $null입니다. |(user.mobile -eq "value") |
| objectId |사용자 개체의 GUID입니다. |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | 온-프레미스에서 클라우드로 동기화된 사용자의 온-프레미스 SID(보안 식별자)입니다. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |임의의 문자열 값 또는 $null입니다. |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |임의의 문자열 값 또는 $null입니다. |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 코드 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |임의의 문자열 값 또는 $null입니다. |(user.sipProxyAddress -eq "value") |
| state |임의의 문자열 값 또는 $null입니다. |(user.state -eq "value") |
| streetAddress |임의의 문자열 값 또는 $null입니다. |(user.streetAddress -eq "value") |
| surname |임의의 문자열 값 또는 $null입니다. |(user.surname-eq "value") |
| telephoneNumber |임의의 문자열 값 또는 $null입니다. |(user.telephoneNumber -eq "value") |
| usageLocation |두 자로 된 국가 코드 |(user.usageLocation -eq "US") |
| userPrincipalName |임의의 문자열 값입니다. |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>문자열 컬렉션 형식의 속성
허용되는 연산자

* -contains
* -notContains

| 속성 | 허용되는 값 | 사용 현황 |
| --- | --- | --- |
| otherMails |임의의 문자열 값입니다. |(user.otherMails -contains "alias@domain") |

| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>다중 값 속성
허용되는 연산자

* -any(컬렉션에서 적어도 하나의 항목이 조건과 일치하는 경우 충족)
* -all(컬렉션에서 모든 항목이 조건과 일치하는 경우 충족)

| 속성 | 값 | 사용 현황 |
| --- | --- | --- |
| assignedPlans |컬렉션에 있는 각 개체는 다음 문자열 속성을 표시합니다. capabilityStatus, service, servicePlanId |user.assignedPlans -any(assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

다중 값 속성은 동일한 유형인 개체의 컬렉션입니다. -any 및 -all 연산자를 사용하여 각각 컬렉션의 항목 중 하나 또는 모두에 조건을 적용할 수 있습니다. 예:

assignedPlans는 사용자에게 할당된 모든 서비스 계획을 나열하는 다중 값 속성입니다. 아래 식은 사용 상태인 Exchange Online(계획 2) 서비스 계획을 가진 사용자를 선택합니다.

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Guid 식별자는 Exchange Online(계획 2) 서비스 계획을 식별합니다.)

> [!NOTE]
> 예를 들어 특정 정책 집합이 포함된 대상으로 지정하는 데 Office 365(또는 기타 Microsoft 온라인 서비스) 기능을 사용하기 위해 모든 사용자를 식별하려는 경우에 유용합니다.

다음 식은 Intune 서비스("SCO" 서비스 이름으로 식별)와 연결된 서비스 계획이 있는 모든 사용자를 선택합니다.
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Null 값 사용

규칙에 null 값을 지정하려면 "null" 또는 $null을 사용하면 됩니다. 예제:

   user.mail -ne null은 user.mail –ne $null과 동등합니다.

## <a name="extension-attributes-and-custom-attributes"></a>확장 특성 및 사용자 지정 특성
확장 특성 및 사용자 지정 특성은 동적 멤버 자격 규칙에서 지원됩니다.

확장 특성은 온-프레미스 Windows Server AD에서 동기화되고 "ExtensionAttributeX" 형식을 사용하며 여기서 X는 1 - 15입니다.
확장 특성을 사용하는 규칙의 예는 다음과 같습니다.

(user.extensionAttribute15 -eq "Marketing")

사용자 지정 특성은 온-프레미스 Windows Server AD 또는 연결된 SaaS 응용 프로그램에서 동기화되고 "user.extension_[GUID]\__[Attribute]" 형식입니다. 여기서 [GUID]는 AAD에서 특성을 만든 응용 프로그램에 대한 AAD의 고유한 식별자이고 [Attribute]는 만들어진 특성 이름입니다.
사용자 지정 특성을 사용하는 규칙의 예는 다음과 같습니다.

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

사용자 지정 특성 이름은 Graph Explorer를 사용하여 사용자의 특성을 쿼리하거나 특성 이름을 검색하여 디렉터리에서 찾을 수 있습니다.
현재 온-프레미스 Active Directory에서 동기화되는 다중 값 특성을 지원하지 않습니다.

## <a name="direct-reports-rule"></a>"직접 보고" 규칙
관리자의 직접 보고서를 모두 포함하는 그룹을 만들 수 있습니다. 관리자의 직접 보고서가 나중에 변경될 경우 그룹의 멤버 자격은 자동으로 조정됩니다.

> [!NOTE]
> 1. 규칙이 작동하려면 테넌트의 사용자에 대해 **관리자 ID** 속성이 올바르게 설정되어야 합니다. 해당 **프로필 탭**에서 사용자의 현재 값을 확인할 수 있습니다.
> 2. 이 규칙은 **직접** 보고서만을 지원합니다. 예를 들어 현재는 직접 보고서 및 해당 보고서를 포함하는 그룹과 같이 중첩된 계층에 그룹을 만들 수 없습니다.

**그룹을 구성하려면**

1. [고급 규칙을 만들려면](#to-create-the-advanced-rule) 섹션의 1~5단계에 따르고 **동적 사용자**의 **멤버 자격 형식**을 선택합니다.
2. **동적 멤버 자격 규칙** 블레이드에서 다음 구문을 사용하여 규칙을 입력합니다.

    *"{obectID_of_manager}"의 직접 보고서*

    올바른 규칙의 예제:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. 규칙을 저장한 후에 지정한 관리자 ID 값을 가진 모든 사용자가 그룹에 추가됩니다.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>특성을 사용하여 장치 개체에 대한 규칙 만들기
또한 그룹의 멤버 자격에 대한 장치 개체를 선택하는 규칙을 만들 수 있습니다. 다음과 같은 장치 특성을 사용할 수 있습니다.

| 속성              | 허용되는 값                     | 사용 현황                                                       |
|-------------------------|------------------------------------|-------------------------------------------------------------|
| accountEnabled          | true false                         | (device.accountEnabled -eq true)                            |
| displayName             | 임의의 문자열 값입니다.                   | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | 임의의 문자열 값입니다.                   | (device.deviceOSType -eq "Android")                         |
| deviceOSVersion         | 임의의 문자열 값입니다.                   | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | 유효한 장치 범주 이름       | (device.deviceCategory -eq "BYOD")                          |
| deviceManufacturer      | 임의의 문자열 값입니다.                   | (device.deviceManufacturer -eq "Samsung")                   |
| deviceModel             | 임의의 문자열 값입니다.                   | (device.deviceModel -eq "iPad Air”)                         |
| deviceOwnership         | 개인, 회사                  | (device.deviceOwnership -eq "Company")                      |
| domainName              | 임의의 문자열 값입니다.                   | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Apple 장치 등록 프로필 이름    | (device.enrollmentProfileName -eq "DEP iPhones")       |
| isRooted                | true false                         | (device.isRooted -eq true)                              |
| managementType          | 모바일 장치의 경우 "MDM", Intune PC 에이전트를 통해 관리되는 컴퓨터의 경우 "PC"    | (device.managementType -eq "MDM")                  |
| organizationalUnit      | 온-프레미스 Active Directory에 의해 설정된 OU의 이름과 일치하는 임의의 문자열 값 | (device.organizationalUnit -eq "US PCs")      |
| deviceId                | 유효한 Intune deviceId                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | 유효한 Azure AD objectId            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> Azure 클래식 포털에서 "간단한 규칙" 드롭다운을 사용하여 장치 규칙을 만들 수 없습니다.
>
>

## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>동적 멤버 자격을 정적으로 또는 그 반대로 변경
그룹에서 멤버 자격을 관리하는 방식을 변경할 수 있습니다. 이것은 시스템에 동일한 그룹 이름과 ID를 유지하려는 경우에 유용하므로 그룹에 대한 기존 참조는 여전히 유효합니다. 새 그룹을 만들면 해당 참조를 업데이트해야 합니다.

> [!WARNING]
> 기존 정적 그룹을 동적 그룹으로 변경할 경우 기존의 모든 멤버가 그룹에서 제거된 후 멤버 자격 규칙이 처리되어 새 멤버가 추가됩니다. 그룹이 앱 또는 리소스에 대한 액세스를 제어하는 데 사용되는 경우 멤버 자격 규칙이 완전히 처리될 때까지는 원래 멤버가 액세스하지 못할 수 있습니다.
>
> 그룹의 새 멤버 자격이 예상대로 유지되는지 확인하기 위해 새 멤버 자격 규칙을 미리 테스트하는 것이 좋습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 그룹을 엽니다.
2. 동적 멤버 자격의 현재 상태를 보려면 **구성** 탭을 선택합니다.
3. 그룹을 정적으로 만들려면 **동적 멤버 자격 사용** 설정을 **아니요**로 간단히 토글합니다. 아래 도구 모음에서 **저장** 단추를 클릭하여 확인합니다. 기존 멤버는 그룹에 보관되고 지금부터는 멤버 자격 규칙이 처리되지 않습니다.
4. 그룹을 동적으로 만들려면 설정을 **예**로 토글하고 원하는 멤버 자격 규칙을 지정하고 **저장**을 클릭합니다. 기존 멤버가 제거되고 새 규칙 처리가 시작되어 새 멤버를 추가합니다.

## <a name="next-steps"></a>다음 단계
이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [그룹의 동적 멤버 자격 문제 해결](active-directory-accessmanagement-troubleshooting.md)
* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [그룹 설정을 구성하는 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
