---
title: Azure Active Directory B2C의 사용자 프로필 특성
description: Azure AD B2C 디렉터리 사용자 프로필에서 지원하는 사용자 리소스 유형 특성에 대해 알아봅니다. 기본 제공되는 특성 및 확장과 Microsoft Graph에 특성이 매핑되는 방식을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500532"
---
# <a name="user-profile-attributes"></a>사용자 프로필 특성

Azure AD(Azure Active Directory) B2C 디렉터리 사용자 프로필에는 이름, 성, 도시, 우편 번호, 전화 번호와 같은 기본 제공되는 특성 세트가 함께 제공됩니다. 외부 데이터 저장소 없이 자체 애플리케이션 데이터로 사용자 프로필을 확장할 수 있습니다. 

Azure AD B2C 사용자 프로필에서 사용할 수 있는 대부분의 특성은 Microsoft Graph에서 지원됩니다. 이 문서에서는 지원되는 Azure AD B2C 사용자 프로필 특성에 대해 설명합니다. 또한, Microsoft Graph에서 지원하지 않는 특성과 Azure AD B2C에서 사용할 수 없는 Microsoft Graph 특성도 안내합니다.

> [!IMPORTANT]
> 기본 제공 특성이나 확장 특성은 계정 자격 증명, 정부 발행 신분증 번호, 카드 소유자 데이터, 금융 계정 데이터, 건강 정보, 중요한 배경 정보와 같은 중요한 개인 정보를 저장하는 데 사용해서는 안 됩니다.

외부 시스템과도 통합해야 합니다. 예를 들어 인증에 Azure AD B2C를 사용하되 외부 CRM(고객 관계 관리) 또는 고객 충성도 데이터베이스를 고객 데이터의 정보 원본으로 위임할 수 있습니다. 자세한 내용은 [원격 프로필](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) 솔루션을 참조하세요.

## <a name="azure-ad-user-resource-type"></a>Azure AD 사용자 리소스 유형

아래 표에는 Azure AD B2C 디렉터리 사용자 프로필에서 지원하는 [사용자 리소스 유형](/graph/api/resources/user) 특성이 나와 있습니다. 표에서는 각 특성에 대해 다음 정보를 제공합니다.

- Azure AD B2C에서 사용하는 특성 이름(Microsoft Graph 이름이 다른 경우 그 뒤에 Microsoft Graph 이름)
- 특성 데이터 형식
- 특성 설명
- 특성을 Azure Portal에서 사용할 수 있는지 여부
- 사용자 흐름에서 특성을 사용할 수 있는지 여부
- 사용자 지정 정책 [Azure AD 기술 프로필](active-directory-technical-profile.md)에서 특성을 사용할 수 있는지 여부와 사용할 수 있는 섹션(&lt;InputClaims&gt;, &lt;OutputClaims&gt; 또는 &lt;PersistedClaims&gt;)

|속성     |Type     |설명|Azure portal|사용자 흐름|사용자 지정 정책|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |부울|사용자 계정이 사용하도록 설정되었는지 여부: **true** - 계정이 사용하도록 설정됨, **false** - 계정이 사용하도록 설정되지 않음.|예|예|지속형, 출력|
|ageGroup        |String|사용자의 나이 그룹입니다. 가능한 값: Null, Undefined, Minor, Adult, NotAdult.|예|예|지속형, 출력|
|alternativeSecurityId([Identities](#identities-attribute))|String|외부 ID 공급자에서 가져온 단일 사용자 ID.|예|예|입력, 지속형, 출력|
|alternativeSecurityIds([Identities](#identities-attribute))|대체 securityId 컬렉션|외부 ID 공급자에서 가져온 사용자 ID 컬렉션.|예|예|지속형, 출력|
|city            |String|사용자가 위치한 구/군입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|consentProvidedForMinor|String|미성년에 대한 동의가 제공되었는지 여부입니다. Null, granted, denied 또는 notRequired가 허용되는 값입니다.|예|예|지속형, 출력|
|country         |String|사용자가 위치한 국가/지역입니다. 예제: “US” 또는 “UK”. 최대 길이는 128입니다.|예|예|지속형, 출력|
|createdDateTime|DateTime|사용자 개체가 만들어진 날짜입니다. 읽기 전용입니다.|예|예|지속형, 출력|
|creationType    |String|사용자 계정이 Azure Active Directory B2C 테넌트의 로컬 계정으로 만들어진 경우, 값은 LocalAccount 또는 nameCoexistence입니다. 읽기 전용입니다.|예|예|지속형, 출력|
|dateOfBirth     |Date|생년월일입니다.|예|예|지속형, 출력|
|department      |String|사용자가 근무하는 부서의 이름입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|displayName     |String|사용자의 표시 이름입니다. 최대 길이는 256입니다.|예|예|지속형, 출력|
|facsimileTelephoneNumber<sup>1</sup>|String|사용자의 회사 팩스 전화 번호입니다.|예|예|지속형, 출력|
|givenName       |String|사용자의 이름(성 제외)입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|jobTitle        |String|사용자의 직위입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|immutableId     |String|온-프레미스 Active Directory에서 마이그레이션되는 사용자에 게 일반적으로 사용 되는 식별자입니다.|아니요|예|지속형, 출력|
|legalAgeGroupClassification|String|법적 연령 그룹 분류입니다. ageGroup 및 consentProvidedForMinor 속성을 기준으로 계산되는 읽기 전용 값입니다. 허용 되는 값: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult 및 성인.|예|예|지속형, 출력|
|legalCountry<sup>1</sup>  |String|법적 목적을 위한 국가/지역입니다.|예|예|지속형, 출력|
|mail            |String|사용자의 SMTP 주소입니다(예: “bob@contoso.com”). 읽기 전용입니다.|예|예|지속형, 출력|
|mailNickName    |String|사용자의 메일 별칭입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|모바일(mobilePhone) |String|사용자의 기본 휴대폰 번호입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|netId           |String|Net ID입니다.|예|예|지속형, 출력|
|objectId        |String|사용자의 고유 식별자인 GUID(Globally Unique Identifier)입니다. 예제: 12345678-9abc-def0-1234-56789abcde. 읽기 전용, 변경 불가.|읽기 전용|예|입력, 지속형, 출력|
|otherMails      |문자열 컬렉션|사용자의 다른 전자 메일 주소 목록입니다. 예: [“bob@contoso.com”, “Robert@fabrikam.com”].|예(대체 메일)|예|지속형, 출력|
|password        |String|사용자자 생성 중 만든 로컬 계정의 암호입니다.|예|예|지속됨|
|passwordPolicies     |String|암호의 정책입니다. 쉼표로 구분된 여러 정책 이름으로 구성된 문자열입니다. 예를 들면 "DisablePasswordExpiration, DisableStrongPassword"입니다.|아니요|예|지속형, 출력|
|physicalDeliveryOfficeName(officeLocation)|String|사용자 사업장의 사무실 위치입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|postalCode      |String|사용자의 우편 주소에 대한 우편 번호입니다. 우편 번호는 사용자의 해당 국가/지역에 따라 다릅니다. 미국의 경우 이 특성은 우편 번호를 포함합니다. 최대 길이는 40입니다.|예|예|지속형, 출력|
|preferredLanguage    |String|사용자의 기본 설정 언어입니다. 기본 언어 형식은 RFC 4646을 기반으로 합니다. 이 이름은 해당 언어와 연결 된 ISO 639 2 문자 소문자 문화권 코드와 해당 국가 또는 지역과 연결 된 ISO 3166 2 문자 대문자 하위 문화권 코드의 조합입니다. 예: "en-us" 또는 "es ES".|아니요|예|지속형, 출력|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|이 시간 이전에 발급된 모든 새로 고침 토큰은 유효하지 않으며, 유효하지 않은 새로 고침 토큰을 사용하여 새 액세스 토큰을 가져오면 애플리케이션에서 오류가 발생합니다. 이 경우 애플리케이션이 권한 부여 엔드포인트에 요청을 제출하여 새 새로 고침 토큰을 가져와야 합니다. 읽기 전용입니다.|예|예|출력|
|signInNames([Identities](#identities-attribute)) |String|디렉터리에 있는 임의 유형의 로컬 계정 사용자의 고유한 로그인 이름입니다. 이 특성을 사용 하 여 로컬 계정 유형을 지정 하지 않고 로그인 값이 있는 사용자를 가져옵니다.|아니요|예|입력|
|signInNames.userName([Identities](#identities-attribute)) |String|디렉터리에 있는 로컬 계정 사용자의 고유한 사용자 이름입니다. 이 특성을 사용 하 여 특정 로그인 사용자 이름을 가진 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 이 특성을 PersistedClaims에 단독으로 지정하면 다른 유형의 signInNames가 제거됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다.|예|예|입력, 지속형, 출력|
|signInNames.phoneNumber([Identities](#identities-attribute)) |String|디렉터리에 있는 로컬 계정 사용자의 고유한 전화 번호입니다. 이 특성을 사용 하 여 특정 로그인 전화 번호로 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 PersistedClaims 단독으로이 특성을 지정 하면 다른 유형의 signInNames 제거 됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다.|예|예|입력, 지속형, 출력|
|signInNames.emailAddress([Identities](#identities-attribute))|String|디렉터리에 있는 로컬 계정 사용자의 고유한 메일 주소입니다. 이 특성을 사용하여 특정 로그인 메일 주소를 갖는 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 PersistedClaims 단독으로이 특성을 지정 하면 다른 유형의 signInNames 제거 됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다.|예|예|입력, 지속형, 출력|
|state           |String|사용자 주소의 시/도입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|streetAddress   |String|사용자 사업장의 주소입니다. 최대 길이는 1024입니다.|예|예|지속형, 출력|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|사용자의 보조 전화 번호로, 다단계 인증에 사용됩니다.|예|예|지속형, 출력|
|strongAuthenticationEmailAddress<sup>1</sup>|String|사용자의 SMTP 주소입니다. 예: “bob@contoso.com” 이 특성은 사용자 이름 정책에서 사용자 주소 메일을 저장하는 데 로그인에 사용됩니다. 메일 주소는 암호 재설정 흐름에서도 사용됩니다.|예|예|지속형, 출력|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|사용자의 기본 전화 번호로, 다단계 인증에 사용됩니다.|예|예|지속형, 출력|
|surname         |String|사용자의 성입니다(이름 제외). 최대 길이는 64입니다.|예|예|지속형, 출력|
|telephoneNumber(businessPhones의 첫 번째 항목)|String|사용자 사업장의 기본 전화 번호입니다.|예|예|지속형, 출력|
|userPrincipalName    |String|사용자의 UPN(사용자 계정 이름)입니다. UPN은 인터넷 표준 RFC 822를 기반으로 하는 사용자의 인터넷 스타일 로그인 이름입니다. 도메인은 테넌트의 확인된 도메인 컬렉션에 있어야 합니다. 이 속성은 계정을 만들 때 필요합니다. 변경 불가|예|예|입력, 지속형, 출력|
|usageLocation   |String|국가/지역의 서비스 가용성을 확인해야 하는 법적 요구 사항으로 인해 라이선스를 할당받을 사용자에게 필요합니다. Null을 허용하지 않습니다. 두 문자로 된 국가/지역 코드(ISO 표준 3166)입니다. 예제: “US”, “JP”, “GB”.|예|예|지속형, 출력|
|userType        |String|등 디렉터리의 사용자 유형을 분류하는 데 사용할 수 있는 문자열 값입니다. 값은 멤버여야 합니다. 읽기 전용입니다.|읽기 전용|예|지속형, 출력|
|userState (externalUserState)<sup>3</sup>|String|Azure AD B2B 계정에서만, 초대가 PendingAcceptance 또는 Accepted인지 나타냅니다.|예|예|지속형, 출력|
|userStateChangedOn(externalUserStateChangeDateTime)<sup>2</sup>|DateTime|UserState 속성의 최신 변경에 대한 타임스탬프를 표시합니다.|예|예|지속형, 출력|

<sup>1 </sup>Microsoft Graph에서 지원하지 않음<br><sup>2</sup> 자세한 내용은 [MFA 전화 번호 특성](#mfa-phone-number-attribute) 을 참조 하세요.<br><sup>3 </sup> Azure AD B2C와 함께 사용 하면 안 됩니다.

## <a name="display-name-attribute"></a>표시 이름 특성

는 `displayName` 사용자에 대 한 Azure Portal 사용자 관리에 표시 되는 이름이 며, 액세스 토큰 Azure AD B2C 응용 프로그램으로 돌아갑니다. 이 속성은 필수입니다.

## <a name="identities-attribute"></a>Id 특성

소비자, 파트너 또는 시민 일 수 있는 고객 계정은 다음과 같은 id 형식에 연결할 수 있습니다.

- **로컬** id-사용자 이름 및 암호는 Azure AD B2C 디렉터리에 로컬로 저장 됩니다. 이러한 ID는 "로컬 계정"으로 참조하는 경우가 많습니다.
- **페더레이션된** id- *소셜* 계정 또는 *엔터프라이즈* 계정이 라고도 하며, 사용자의 ID는 Facebook, Microsoft, ADFS 또는 Salesforce와 같은 페더레이션된 id 공급자를 통해 관리 됩니다.

고객 계정을 가진 사용자는 여러 id를 사용 하 여 로그인 할 수 있습니다. 예: 사용자 이름, 전자 메일, 직원 ID, 정부 ID 및 기타. 단일 계정에는 동일한 암호를 사용 하 여 로컬 및 소셜의 id가 여러 개 있을 수 있습니다. 

Microsoft Graph API에서 로컬 및 페더레이션 id는 모두 `identities` [objectIdentity](/graph/api/resources/objectidentity)형식의 user 특성에 저장 됩니다. `identities`컬렉션은 사용자 계정에 로그인 하는 데 사용 되는 id 집합을 나타냅니다. 이 컬렉션을 통해 사용자는 연결 된 id를 사용 하 여 사용자 계정에 로그인 할 수 있습니다. Id 특성에는 최대 10 개의 [objectIdentity](/graph/api/resources/objectidentity) 개체가 포함 될 수 있습니다. 각 개체에는 다음 속성이 포함 되어 있습니다.

| Name   | Type |설명|
|:---------------|:--------|:----------|
|signInType|문자열| 디렉터리에서 사용자 로그인 유형을 지정 합니다. 로컬 계정:,,,,  `emailAddress` `emailAddress1` 또는 원하는 `emailAddress2` `emailAddress3`  `userName` 다른 모든 형식 소셜 계정은로 설정 되어야 합니다  `federated` .|
|발급자|문자열|Id의 발급자를 지정 합니다. 로컬 계정 (여기서 **signInType** 가이 아닌 경우)의 경우 `federated` 이 속성은 로컬 B2C 테 넌 트 기본 도메인 이름입니다 (예:) `contoso.onmicrosoft.com` . 소셜 id (여기서 **signInType** 는)의 경우  `federated` 값은 발급자의 이름입니다 (예:). `facebook.com`|
|issuerAssignedId|문자열|발급자에 의해 사용자에 게 할당 된 고유 식별자를 지정 합니다. **발급자** 와 **issuerAssignedId** 의 조합은 테 넌 트 내에서 고유 해야 합니다. 로컬 계정의 경우 **signInType** 가 또는로 설정 되 `emailAddress` 면 `userName` 사용자의 로그인 이름을 나타냅니다.<br>**SignInType** 가로 설정 된 경우: <ul><li>`emailAddress` (또는 like로 `emailAddress` 시작 `emailAddress1` ) **issuerAssignedId** 는 유효한 전자 메일 주소 여야 합니다.</li><li>`userName`(또는 다른 값) **issuerAssignedId** 는 [전자 메일 주소의 유효한 로컬 부분](https://tools.ietf.org/html/rfc3696#section-3) 이어야 합니다.</li><li>`federated`, **issuerAssignedId** 는 페더레이션된 계정 고유 식별자를 나타냅니다.</li></ul>|

로그인 이름이 있는 로컬 계정 id, 로그인으로 전자 메일 주소, 소셜 id를 사용 하는 다음 **id** 특성입니다. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

페더레이션 id의 경우 id 공급자에 따라 **issuerAssignedId** 는 응용 프로그램 또는 개발 계정 별로 지정 된 사용자에 대 한 고유한 값입니다. 이전에 소셜 공급자나 동일한 개발 계정 내의 다른 응용 프로그램에서 할당 한 것과 동일한 응용 프로그램 ID를 사용 하 여 Azure AD B2C 정책을 구성 합니다. 

## <a name="password-profile-property"></a>암호 프로필 속성

로컬 id의 경우 **Passwordprofile** 특성이 필요 하며, 사용자의 암호를 포함 합니다. `forceChangePasswordNextSignIn`사용자가 다음 로그인 시 암호를 다시 설정 해야 하는지 여부를 나타내는 특성입니다. 강제 암호 재설정을 처리 하려면 [강제 암호 재설정 흐름을 설정](force-password-reset.md)합니다.

페더레이션된 (소셜) id의 경우 **Passwordprofile** 특성이 필요 하지 않습니다.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>암호 정책 특성

로컬 계정에 대 한 Azure AD B2C 암호 정책은 Azure Active Directory [강력한 암호 수준](../active-directory/authentication/concept-sspr-policy.md) 정책을 기반으로 합니다. Azure AD B2C 등록 또는 로그인 및 암호 재설정 정책에는 강력한 암호 강도가 필요 하며 암호는 만료 되지 않습니다.

사용자 마이그레이션 시나리오에서 마이그레이션하려는 계정이 Azure AD B2C에 의해 적용 되는 [강력한 암호](../active-directory/authentication/concept-sspr-policy.md) 강도 보다 약한 암호 강도의 경우 강력한 암호 요구 사항을 사용 하지 않도록 설정할 수 있습니다. 기본 암호 정책을 변경 하려면 특성을로 설정 `passwordPolicies` `DisableStrongPassword` 합니다. 예를 들어 다음과 같이 사용자 만들기 요청을 수정할 수 있습니다.

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>MFA 전화 번호 특성

MFA (multi-factor authentication)에 전화를 사용 하는 경우 휴대폰을 사용 하 여 사용자 id를 확인 합니다. 새 전화 번호를 프로그래밍 방식으로 [추가](/graph/api/authentication-post-phonemethods) 하려면 전화 번호를 [업데이트](/graph/api/b2cauthenticationmethodspolicy-update), [가져오기](/graph/api/b2cauthenticationmethodspolicy-get)또는 [삭제](/graph/api/phoneauthenticationmethod-delete) 하려면 MS Graph API [전화 인증 방법을](/graph/api/resources/phoneauthenticationmethod)사용 합니다.

[사용자 지정 정책](custom-policy-overview.md)Azure AD B2C에서 전화 번호는 클레임 유형을 통해 사용할 수 있습니다 `strongAuthenticationPhoneNumber` .

## <a name="extension-attributes"></a>확장 특성

모든 고객 지향 응용 프로그램에는 수집할 정보에 대 한 고유한 요구 사항이 있습니다. Azure AD B2C 테 넌 트에는 지정 된 이름, 성 및 우편 번호와 같은 속성에 저장 되는 기본 제공 정보 집합이 제공 됩니다. Azure AD B2C를 사용 하 여 각 고객 계정에 저장 된 속성 집합을 확장할 수 있습니다. 자세한 내용은 [Azure Active Directory B2C에서 사용자 특성 추가 및 사용자 입력 사용자 지정](configure-user-input.md) 을 참조 하세요.

확장 특성은 디렉터리에서 사용자 개체의 [스키마를 확장](/graph/extensibility-overview#schema-extensions)합니다. 확장 특성은 사용자에 대한 데이터를 포함할 수 있더라도 애플리케이션 개체에만 등록할 수 있습니다. 확장 특성은 라는 응용 프로그램에 연결 됩니다 `b2c-extensions-app` . 이 애플리케이션은 사용자 데이터 저장을 위해 Azure AD B2C에서 사용하므로 수정하지 않습니다. 이 애플리케이션은 Azure Active Directory App 등록 아래에서 확인할 수 있습니다.

> [!NOTE]
> - 사용자 계정에 최대 100개의 확장 특성을 쓸 수 있습니다.
> - b2c-extensions-app 애플리케이션을 삭제할 경우, 확장 특성은 포함된 데이터와 함께 모든 사용자에게서 제거됩니다.
> - 확장 특성이 애플리케이션에 의해 삭제될 경우, 모든 사용자 계정에서 제거되고 값이 삭제됩니다.

Graph API의 확장 특성은 규칙을 사용 하 여 이름이 지정 됩니다 `extension_ApplicationClientID_AttributeName` . 여기서는 응용 프로그램의 응용 프로그램 `ApplicationClientID` **(클라이언트) ID** 입니다 `b2c-extensions-app` (   >  Azure Portal의 **모든 응용** 프로그램 앱 등록에 있음). 확장 특성 이름에 표시 되는 **응용 프로그램 (클라이언트) ID** 에는 하이픈이 포함 되지 않습니다. 예를 들어:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

스키마 확장에서 특성을 정의할 때 지원 되는 데이터 형식은 다음과 같습니다.

|Type |설명  |
|--------------|---------|
|부울    | 가능한 값: **true** 또는 **false**. |
|DateTime   | ISO 8601 형식으로 지정해야 합니다. UTC로 저장됩니다.   |
|정수    | 32비트 값.               |
|String     | 최대 256자.     |

## <a name="next-steps"></a>다음 단계

확장 특성에 대한 자세한 정보:

- [스키마 확장](/graph/extensibility-overview#schema-extensions)
- [사용자 지정 특성 정의](user-flow-custom-attributes.md)
