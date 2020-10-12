---
title: Azure Active Directory B2C의 사용자 프로필 특성
description: Azure AD B2C 디렉터리 사용자 프로필에서 지원하는 사용자 리소스 유형 특성에 대해 알아봅니다. 기본 제공되는 특성 및 확장과 Microsoft Graph에 특성이 매핑되는 방식을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83738560"
---
# <a name="user-profile-attributes"></a>사용자 프로필 특성

Azure AD(Azure Active Directory) B2C 디렉터리 사용자 프로필에는 이름, 성, 도시, 우편 번호, 전화 번호와 같은 기본 제공되는 특성 세트가 함께 제공됩니다. 외부 데이터 저장소 없이 자체 애플리케이션 데이터로 사용자 프로필을 확장할 수 있습니다. Azure AD B2C 사용자 프로필에서 사용할 수 있는 대부분의 특성은 Microsoft Graph에서 지원됩니다. 이 문서에서는 지원되는 Azure AD B2C 사용자 프로필 특성에 대해 설명합니다. 또한, Microsoft Graph에서 지원하지 않는 특성과 Azure AD B2C에서 사용할 수 없는 Microsoft Graph 특성도 안내합니다.

> [!IMPORTANT]
> 기본 제공 특성이나 확장 특성은 계정 자격 증명, 정부 발행 신분증 번호, 카드 소유자 데이터, 금융 계정 데이터, 건강 정보, 중요한 배경 정보와 같은 중요한 개인 정보를 저장하는 데 사용해서는 안 됩니다.

외부 시스템과도 통합해야 합니다. 예를 들어 인증에 Azure AD B2C를 사용하되 외부 CRM(고객 관계 관리) 또는 고객 충성도 데이터베이스를 고객 데이터의 정보 원본으로 위임할 수 있습니다. 자세한 내용은 [원격 프로필](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) 솔루션을 참조하세요.

아래 표에는 Azure AD B2C 디렉터리 사용자 프로필에서 지원하는 [사용자 리소스 유형](https://docs.microsoft.com/graph/api/resources/user) 특성이 나와 있습니다. 표에서는 각 특성에 대해 다음 정보를 제공합니다.

- Azure AD B2C에서 사용하는 특성 이름(Microsoft Graph 이름이 다른 경우 그 뒤에 Microsoft Graph 이름)
- 특성 데이터 형식
- 특성 설명
- 특성을 Azure Portal에서 사용할 수 있는지 여부
- 사용자 흐름에서 특성을 사용할 수 있는지 여부
- 사용자 지정 정책 [Azure AD 기술 프로필](active-directory-technical-profile.md)에서 특성을 사용할 수 있는지 여부와 사용할 수 있는 섹션(&lt;InputClaims&gt;, &lt;OutputClaims&gt; 또는 &lt;PersistedClaims&gt;)

|속성     |Type     |Description|Azure portal|사용자 흐름|사용자 지정 정책|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |부울|사용자 계정이 사용하도록 설정되었는지 여부: **true** - 계정이 사용하도록 설정됨, **false** - 계정이 사용하도록 설정되지 않음.|예|예|지속형, 출력|
|ageGroup        |String|사용자의 나이 그룹입니다. 가능한 값: Null, Undefined, Minor, Adult, NotAdult.|예|예|지속형, 출력|
|alternativeSecurityId([Identities](manage-user-accounts-graph-api.md#identities-property))|String|외부 ID 공급자에서 가져온 단일 사용자 ID.|예|예|입력, 지속형, 출력|
|alternativeSecurityIds([Identities](manage-user-accounts-graph-api.md#identities-property))|대체 securityId 컬렉션|외부 ID 공급자에서 가져온 사용자 ID 컬렉션.|예|예|지속형, 출력|
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
|immutableId     |String|일반적으로 온-프레미스 Active Directory에서 마이그레이션된 사용자에게 사용되는 식별자입니다.|예|예|지속형, 출력|
|legalAgeGroupClassification|String|법적 연령 그룹 분류입니다. ageGroup 및 consentProvidedForMinor 속성을 기준으로 계산되는 읽기 전용 값입니다. 허용되는 값: Null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult 및 adult.|예|예|지속형, 출력|
|legalCountry<sup>1</sup>  |String|법적 목적을 위한 국가/지역입니다.|예|예|지속형, 출력|
|mail            |String|사용자의 SMTP 주소입니다(예: “bob@contoso.com”). 읽기 전용입니다.|예|예|지속형, 출력|
|mailNickName    |String|사용자의 메일 별칭입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|모바일(mobilePhone) |String|사용자의 기본 휴대폰 번호입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|netId           |String|Net ID입니다.|예|예|지속형, 출력|
|objectId        |String|사용자의 고유 식별자인 GUID(Globally Unique Identifier)입니다. 예제: 12345678-9abc-def0-1234-56789abcde. 읽기 전용, 변경 불가.|읽기 전용|예|입력, 지속형, 출력|
|otherMails      |문자열 컬렉션|사용자의 추가 메일 주소 목록입니다. 예: [“bob@contoso.com”, “Robert@fabrikam.com”].|예(대체 메일)|예|지속형, 출력|
|password        |String|사용자자 생성 중 만든 로컬 계정의 암호입니다.|예|예|지속됨|
|passwordPolicies     |String|암호의 정책입니다. 쉼표로 구분된 여러 정책 이름으로 구성된 문자열입니다. 즉, “DisablePasswordExpiration, DisableStrongPassword”.|예|예|지속형, 출력|
|physicalDeliveryOfficeName(officeLocation)|String|사용자 사업장의 사무실 위치입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|postalCode      |String|사용자의 우편 주소에 대한 우편 번호입니다. 우편 번호는 사용자의 해당 국가/지역에 따라 다릅니다. 미국의 경우 이 특성은 우편 번호를 포함합니다. 최대 길이는 40입니다.|예|예|지속형, 출력|
|preferredLanguage    |String|사용자의 기본 설정 언어입니다. ISO 639-1 코드를 따라야 합니다. 예: “en-US”.|예|예|지속형, 출력|
|refreshTokensValidFromDateTime|DateTime|이 시간 이전에 발급된 모든 새로 고침 토큰은 유효하지 않으며, 유효하지 않은 새로 고침 토큰을 사용하여 새 액세스 토큰을 가져오면 애플리케이션에서 오류가 발생합니다. 이 경우 애플리케이션이 권한 부여 엔드포인트에 요청을 제출하여 새 새로 고침 토큰을 가져와야 합니다. 읽기 전용입니다.|예|예|출력|
|signInNames([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|디렉터리에 있는 임의 유형의 로컬 계정 사용자의 고유한 로그인 이름입니다. 이 특성을 사용하여 로컬 계정 유형을 지정하지 않고 로그인 값으로 사용자를 가져올 수 있습니다.|예|예|입력|
|signInNames.userName([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|디렉터리에 있는 로컬 계정 사용자의 고유한 사용자 이름입니다. 이 특성을 사용하여 특정 로그인 사용자 이름을 갖는 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 이 특성을 PersistedClaims에 단독으로 지정하면 다른 유형의 signInNames가 제거됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다.|예|예|입력, 지속형, 출력|
|signInNames.phoneNumber([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|디렉터리에 있는 로컬 계정 사용자의 고유한 전화 번호입니다. 이 특성을 사용하여 특정 로그인 전화 번호를 갖는 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 이 특성을 PersistedClaims에 단독으로 지정하면 다른 유형의 signInNames가 제거됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다.|예|예|입력, 지속형, 출력|
|signInNames.emailAddress([Identities](manage-user-accounts-graph-api.md#identities-property))|String|디렉터리에 있는 로컬 계정 사용자의 고유한 메일 주소입니다. 이 특성을 사용하여 특정 로그인 메일 주소를 갖는 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 이 특성을 PersistedClaims에 단독으로 지정하면 다른 유형의 signInNames가 제거됩니다. 새로운 유형의 signInNames를 추가하려면 기존 signInNames를 지속 처리해야 합니다.|예|예|입력, 지속형, 출력|
|state           |String|사용자 주소의 시/도입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|streetAddress   |String|사용자 사업장의 주소입니다. 최대 길이는 1024입니다.|예|예|지속형, 출력|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|사용자의 보조 전화 번호로, 다단계 인증에 사용됩니다.|예|예|지속형, 출력|
|strongAuthenticationEmailAddress<sup>1</sup>|String|사용자의 SMTP 주소입니다. 예: “bob@contoso.com” 이 특성은 사용자 이름 정책에서 사용자 주소 메일을 저장하는 데 로그인에 사용됩니다. 메일 주소는 암호 재설정 흐름에서도 사용됩니다.|예|예|지속형, 출력|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|사용자의 기본 전화 번호로, 다단계 인증에 사용됩니다.|예|예|지속형, 출력|
|surname         |String|사용자의 성입니다(이름 제외). 최대 길이는 64입니다.|예|예|지속형, 출력|
|telephoneNumber(businessPhones의 첫 번째 항목)|String|사용자 사업장의 기본 전화 번호입니다.|예|예|지속형, 출력|
|userPrincipalName    |String|사용자의 UPN(사용자 계정 이름)입니다. UPN은 인터넷 표준 RFC 822를 기반으로 하는 사용자의 인터넷 스타일 로그인 이름입니다. 도메인은 테넌트의 확인된 도메인 컬렉션에 있어야 합니다. 이 속성은 계정을 만들 때 필요합니다. 변경 불가|예|예|입력, 지속형, 출력|
|usageLocation   |String|국가/지역의 서비스 가용성을 확인해야 하는 법적 요구 사항으로 인해 라이선스를 할당받을 사용자에게 필요합니다. Null을 허용하지 않습니다. 두 문자로 된 국가/지역 코드(ISO 표준 3166)입니다. 예제: “US”, “JP”, “GB”.|예|예|지속형, 출력|
|userType        |String|등 디렉터리의 사용자 유형을 분류하는 데 사용할 수 있는 문자열 값입니다. 값은 멤버여야 합니다. 읽기 전용입니다.|읽기 전용|예|지속형, 출력|
|userState (externalUserState)<sup>2</sup>|String|Azure AD B2B 계정에서만, 초대가 PendingAcceptance 또는 Accepted인지 나타냅니다.|예|예|지속형, 출력|
|userStateChangedOn(externalUserStateChangeDateTime)<sup>2</sup>|DateTime|UserState 속성의 최신 변경에 대한 타임스탬프를 표시합니다.|예|예|지속형, 출력|
|<sup>1 </sup>Microsoft Graph에서 지원하지 않음<br><sup>2 </sup>Azure AD B2C와 함께 사용할 수 없음||||||


## <a name="extension-attributes"></a>확장 특성

다음과 같이 사용자 고유의 특성을 만들어야 하는 경우가 있습니다.

- 고객 대면 애플리케이션은 **LoyaltyNumber**와 같은 특성을 위해 유지해야 합니다.
- ID 공급자는 **uniqueUserGUID**와 같이 저장해야 하는 고유한 사용자 ID를 포함합니다.
- 사용자 지정 사용자 경험은 **migrationStatus**와 같은 사용자 상태를 위해 유지해야 합니다.

Azure AD B2C는 각 사용자 계정에 저장된 특성 집합을 확장합니다. 확장 특성은 디렉터리에서 사용자 개체의 [스키마를 확장](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)합니다. 확장 특성은 사용자에 대한 데이터를 포함할 수 있더라도 애플리케이션 개체에만 등록할 수 있습니다. 확장 특성은 b2c-extensions-app이라는 애플리케이션에 연결됩니다. 이 애플리케이션은 사용자 데이터 저장을 위해 Azure AD B2C에서 사용하므로 수정하지 않습니다. 이 애플리케이션은 Azure Active Directory App 등록 아래에서 확인할 수 있습니다.

> [!NOTE]
> - 사용자 계정에 최대 100개의 확장 특성을 쓸 수 있습니다.
> - b2c-extensions-app 애플리케이션을 삭제할 경우, 확장 특성은 포함된 데이터와 함께 모든 사용자에게서 제거됩니다.
> - 확장 특성이 애플리케이션에 의해 삭제될 경우, 모든 사용자 계정에서 제거되고 값이 삭제됩니다.
> - 확장 특성의 기본 이름은 “Extension_” + Application ID + “_” + Attribute name 형식으로 생성됩니다. 예를 들어, 확장 특성 LoyaltyNumber를 만들었고 b2c-extensions-app 애플리케이션 ID가 831374b3-bd50-41bf-aa54-263ec9e050fc라면 기본 확장 속성 이름은 extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber가 됩니다. Graph API 쿼리를 실행하여 사용자 계정을 만들거나 업데이트할 때 기본 이름을 사용합니다.

스키마 확장에서 속성을 정의할 때 다음과 같은 데이터 형식이 지원됩니다.

|속성 형식 |설명  |
|--------------|---------|
|부울    | 가능한 값: **true** 또는 **false**. |
|DateTime   | ISO 8601 형식으로 지정해야 합니다. UTC로 저장됩니다.   |
|정수    | 32비트 값.               |
|String     | 최대 256자.     |

## <a name="next-steps"></a>다음 단계
확장 특성에 대한 자세한 정보:
- [스키마 확장](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [사용자 흐름으로 사용자 지정 특성 정의](user-flow-custom-attributes.md)
- [사용자 지정 정책으로 사용자 지정 특성 정의](custom-policy-custom-attributes.md)
