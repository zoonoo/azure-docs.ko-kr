---
title: Azure Active Directory B2C의 사용자 프로필 특성
description: Azure AD B2C directory 사용자 프로필에서 지 원하는 사용자 리소스 유형 특성에 대해 알아봅니다. 기본 제공 특성, 확장 및 특성을 Microsoft Graph에 매핑하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80057302"
---
# <a name="user-profile-attributes"></a>사용자 프로필 특성

Azure Active Directory (Azure AD) B2C 디렉터리 사용자 프로필에는 지정 된 이름, 성, 도시, 우편 번호, 전화 번호 등의 기본 제공 특성 집합이 제공 됩니다. 외부 데이터 저장소를 요구 하지 않고 사용자 프로필을 사용자 고유의 응용 프로그램 데이터로 확장할 수 있습니다. Azure AD B2C 사용자 프로필과 함께 사용할 수 있는 대부분의 특성은 Microsoft Graph 에서도 지원 됩니다. 이 문서에서는 지원 되는 Azure AD B2C 사용자 프로필 특성을 설명 합니다. 또한 Microsoft Graph에서 지원 하지 않는 특성과 Azure AD B2C 사용 하지 않아야 하는 Microsoft Graph 특성에 대해서도 설명 합니다.

> [!IMPORTANT]
> 계정 자격 증명, 정부 식별 번호, 카드 소유자 데이터, 재무 계정 데이터, 의료 정보 또는 중요 한 배경 정보와 같은 중요 한 개인 데이터를 저장 하기 위해 기본 제공 또는 확장 특성을 사용 하면 안 됩니다.

외부 시스템과 통합할 수도 있습니다. 예를 들어 인증을 위해 Azure AD B2C를 사용할 수 있지만 외부 CRM (고객 관계 관리) 또는 고객 충성도 데이터베이스를 고객 데이터의 신뢰할 수 있는 원본으로 위임할 수 있습니다. 자세한 내용은 [원격 프로필](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) 솔루션 (영문)을 참조 하세요.

아래 표에는 Azure AD B2C directory 사용자 프로필에서 지 원하는 [사용자 리소스 유형](https://docs.microsoft.com/graph/api/resources/user) 특성이 나열 되어 있습니다. 각 특성에 대 한 다음 정보를 제공 합니다.

- Azure AD B2C에서 사용 하는 특성 이름 (다른 경우 괄호 안에 Microsoft Graph 이름이 옴)
- Attribute 데이터 형식
- 특성 설명
- 특성을 Azure Portal에서 사용할 수 있으면
- 사용자 흐름에서 특성을 사용할 수 있는 경우
- 사용자 지정 정책 [Azure AD 기술 프로필](active-directory-technical-profile.md) 에서 특성을 사용할 수 있고 해당 섹션 (&lt;&gt;inputclaims, &lt;outputclaims&gt;또는 &lt;persistedclaims)&gt;

|속성     |유형     |Description|Azure portal|사용자 흐름|사용자 지정 정책|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |부울|사용자 계정이 활성화 또는 비활성화 되었는지 여부: 계정이 사용 되 면 **true** 이 고, 그렇지 않으면 **false**입니다.|예|아니요|지속형, 출력|
|연령대        |문자열|사용자의 나이 그룹입니다. 가능한 값: null, 정의 되지 않음, 부, 성인, NotAdult.|예|아니요|지속형, 출력|
|alternativeSecurityId ([id](manage-user-accounts-graph-api.md#identities-property))|문자열|외부 id 공급자의 단일 사용자 id입니다.|아니요|아니요|입력, 지속형, 출력|
|alternativeSecurityIds ([id](manage-user-accounts-graph-api.md#identities-property))|대체 securityId 컬렉션|외부 id 공급자의 사용자 id 컬렉션입니다.|아니요|아니요|지속형, 출력|
|city            |문자열|사용자의 구/군/시입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|consentProvidedForMinor|문자열|주에 대 한 동의가 제공 되었는지 여부입니다. 허용 되는 값: null, 허용, 거부 또는 notRequired.|예|아니요|지속형, 출력|
|country         |문자열|사용자의 국가/지역입니다. 예: "US" 또는 "영국". 최대 길이는 128입니다.|예|예|지속형, 출력|
|createdDateTime|DateTime|사용자 개체가 만들어진 날짜입니다. 읽기 전용입니다.|아니요|아니요|지속형, 출력|
|creationType    |문자열|사용자 계정이 Azure Active Directory B2C 테 넌 트의 로컬 계정으로 생성 된 경우이 값은 LocalAccount 또는 nameCoexistence입니다. 읽기 전용입니다.|아니요|아니요|지속형, 출력|
|dateOfBirth     |Date|생년월일|아니요|아니요|지속형, 출력|
|department      |문자열|사용자의 근무 부서 이름입니다. 최대 길이는 64입니다.|예|아니요|지속형, 출력|
|displayName     |문자열|사용자의 표시 이름입니다. 최대 길이는 256입니다.|예|예|지속형, 출력|
|facsimileTelephoneNumber<sup>1</sup>|문자열|사용자 회사 팩스의 전화 번호입니다.|예|아니요|지속형, 출력|
|givenName       |문자열|사용자의 지정된 이름입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|jobTitle        |문자열|사용자의 직위입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|immutableId     |문자열|온-프레미스 Active Directory에서 마이그레이션되는 사용자에 게 일반적으로 사용 되는 식별자입니다.|아니요|아니요|지속형, 출력|
|legalAgeGroupClassification|문자열|법적 연령 그룹 분류입니다. AgeGroup 및 consentProvidedForMinor 속성을 기반으로 읽기 전용 및 계산 됩니다. 허용 되는 값: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult 및 성인.|예|아니요|지속형, 출력|
|legalCountry<sup>1</sup>  |문자열|법률을 위한 국가.|아니요|아니요|지속형, 출력|
|mail            |문자열|사용자의 SMTP 주소입니다 (예: "bob@contoso.com"). 읽기 전용입니다.|아니요|아니요|지속형, 출력|
|mailNickName    |문자열|사용자의 메일 별칭입니다. 최대 길이는 64입니다.|아니요|아니요|지속형, 출력|
|모바일 (mobilePhone) |문자열|사용자의 기본 휴대 전화 번호입니다. 최대 길이는 64입니다.|예|아니요|지속형, 출력|
|netId           |문자열|Net ID입니다.|아니요|아니요|지속형, 출력|
|objectId        |문자열|사용자의 고유 식별자 인 GUID (globally unique identifier)입니다. 예: 12345678-9abc-9abc-def0-1234-56789abcde). 읽기 전용 이며 변경할 수 없습니다.|읽기 전용|예|입력, 지속형, 출력|
|otherMails      |문자열 컬렉션|사용자에 대한 추가 전자 메일 주소 목록입니다. 예: ["bob@contoso.com", "Robert@fabrikam.com"].|예 (대체 전자 메일)|아니요|지속형, 출력|
|password        |String|사용자를 만드는 동안 로컬 계정에 대 한 암호입니다.|아니요|아니요|지속됨|
|passwordPolicies     |문자열|암호의 정책입니다. 쉼표로 구분 된 다른 정책 이름으로 구성 된 문자열입니다. 즉, "DisablePasswordExpiration, DisableStrongPassword"입니다.|아니요|아니요|지속형, 출력|
|physicalDeliveryOfficeName (위치)|문자열|사용자 근무지의 사무실 위치입니다. 최대 길이는 128입니다.|예|아니요|지속형, 출력|
|postalCode      |문자열|사용자 우편 주소의 우편 번호입니다. 우편 번호는 사용자의 국가/지역마다 다릅니다. 미국의 경우 이 특성은 미국 우편 번호를 포함합니다. 최대 길이는 40입니다.|예|아니요|지속형, 출력|
|preferredLanguage    |문자열|사용자의 기본 설정 언어입니다. ISO 639-1 코드를 따라야 합니다. 예: "en-us".|아니요|아니요|지속형, 출력|
|refreshTokensValidFromDateTime|DateTime|이 시간 이전에 발급 된 새로 고침 토큰은 유효 하지 않으므로 응용 프로그램에서 잘못 된 새로 고침 토큰을 사용 하 여 새 액세스 토큰을 획득할 때 오류가 발생 합니다. 이 경우 응용 프로그램은 권한 부여 끝점에 대 한 요청을 만들어 새로운 새로 고침 토큰을 획득 해야 합니다. 읽기 전용입니다.|아니요|아니요|출력|
|signInNames ([id](manage-user-accounts-graph-api.md#identities-property)) |문자열|디렉터리에 있는 모든 형식의 로컬 계정 사용자에 대 한 고유한 로그인 이름입니다. 이를 사용 하 여 로컬 계정 유형을 지정 하지 않고 로그인 값이 있는 사용자를 가져옵니다.|아니요|아니요|입력|
|signInNames ([id](manage-user-accounts-graph-api.md#identities-property)) |문자열|디렉터리에 있는 로컬 계정 사용자의 고유한 사용자 이름입니다. 이를 사용 하 여 특정 로그인 사용자 이름을 가진 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 PersistedClaims 단독으로이를 지정 하면 다른 유형의 signInNames 제거 됩니다. 새 유형의 signInNames을 추가 하려는 경우에도 기존 signInNames를 유지 해야 합니다.|아니요|아니요|입력, 지속형, 출력|
|signInNames. phoneNumber ([id](manage-user-accounts-graph-api.md#identities-property)) |문자열|디렉터리에 있는 로컬 계정 사용자의 고유한 전화 번호입니다. 이를 사용 하 여 특정 로그인 전화 번호로 사용자를 만들거나 가져옵니다. 패치 작업 중에 PersistedClaims 단독으로이를 지정 하면 다른 유형의 signInNames 제거 됩니다. 새 유형의 signInNames을 추가 하려는 경우에도 기존 signInNames를 유지 해야 합니다.|아니요|아니요|입력, 지속형, 출력|
|signInNames. emailAddress ([id](manage-user-accounts-graph-api.md#identities-property))|문자열|디렉터리에 있는 로컬 계정 사용자의 고유한 전자 메일 주소입니다. 이를 사용 하 여 특정 로그인 전자 메일 주소를 가진 사용자를 만들거나 가져올 수 있습니다. 패치 작업 중에 PersistedClaims 단독으로이를 지정 하면 다른 유형의 signInNames 제거 됩니다. 새 유형의 signInNames을 추가 하려는 경우에도 기존 signInNames를 유지 해야 합니다.|아니요|아니요|입력, 지속형, 출력|
|state           |문자열|사용자 주소의 시/도입니다. 최대 길이는 128입니다.|예|예|지속형, 출력|
|streetAddress   |문자열|사용자 근무지의 주소입니다. 최대 길이는 1024입니다.|예|예|지속형, 출력|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|문자열|Multi-factor authentication에 사용 되는 사용자의 보조 전화 번호입니다.|예|아니요|지속형, 출력|
|strongAuthenticationEmailAddress<sup>1</sup>|문자열|사용자의 SMTP 주소입니다. 예: "bob@contoso.com"이 특성은 사용자 전자 메일 주소를 저장 하는 사용자 이름 정책으로 로그인 하는 데 사용 됩니다. 암호 다시 설정 흐름에서 사용 되는 전자 메일 주소입니다.|예|아니요|지속형, 출력|
|strongAuthenticationPhoneNumber<sup>1</sup>|문자열|Multi-factor authentication에 사용 되는 사용자의 기본 전화 번호입니다.|예|아니요|지속형, 출력|
|surname         |문자열|사용자 성입니다. 최대 길이는 64입니다.|예|예|지속형, 출력|
|telephoneNumber (businessPhones의 첫 번째 항목)|문자열|사용자 근무지의 기본 전화 번호입니다.|예|아니요|지속형, 출력|
|userPrincipalName    |문자열|사용자의 UPN(사용자 계정 이름)입니다. UPN은 인터넷 표준 RFC 822를 기반으로 하는 사용자의 인터넷 스타일 로그인 이름입니다. 도메인은 테 넌 트의 확인 된 도메인 컬렉션에 있어야 합니다. 계정을 만들 때이 속성이 필요 합니다. 변경할 수 없습니다.|아니요|아니요|입력, 지속형, 출력|
|usageLocation   |문자열|국가 내 서비스 사용 가능 여부를 확인해야 하는 법적 요구 사항으로 인해 라이선스를 할당받는 사용자의 경우 필수입니다. Null을 허용하지 않습니다. 두 자로 된 국가 코드(ISO 표준 3166)입니다. 예: "US", "JP" 및 "GB".|예|아니요|지속형, 출력|
|userType        |문자열|디렉터리의 사용자 유형을 분류 하는 데 사용할 수 있는 문자열 값입니다. 값은 멤버 여야 합니다. 읽기 전용입니다.|읽기 전용|아니요|지속형, 출력|
|userState (externalUserState)<sup>2</sup>|문자열|Azure AD B2B 계정에만 해당 초대가 PendingAcceptance 수락 되었는지 여부를 나타냅니다.|아니요|아니요|지속형, 출력|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|UserState 속성의 최신 변경에 대 한 타임 스탬프를 표시 합니다.|아니요|아니요|지속형, 출력|
|<sup>1</sup> Microsoft Graph에서 지원 되지 않음<br><sup>2</sup> Azure AD B2C와 함께 사용 하면 안 됩니다.||||||


## <a name="extension-attributes"></a>확장 특성

다음과 같이 사용자 고유의 특성을 만들어야 하는 경우가 많습니다.

- 고객 지향 응용 프로그램은 **LoyaltyNumber**와 같은 특성을 유지 해야 합니다.
- ID 공급자는 **uniqueUserGUID**와 같이 저장해야 하는 고유한 사용자 ID를 포함합니다.
- 사용자 지정 사용자 경험은 **migrationStatus**와 같은 사용자의 상태를 유지 해야 합니다.

Azure AD B2C는 각 사용자 계정에 저장된 특성 집합을 확장합니다. 확장 특성은 디렉터리에 있는 사용자 개체의 [스키마를 확장](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) 합니다. 확장 특성은 사용자에 대 한 데이터를 포함할 수 있는 경우에도 응용 프로그램 개체에만 등록할 수 있습니다. 확장 특성은 b2c-app 이라는 응용 프로그램에 연결 됩니다. 이 응용 프로그램은 사용자 데이터를 저장 하는 Azure AD B2C에서 사용 되므로 수정 하지 마십시오. 이 응용 프로그램은 Azure Active Directory 앱 등록에서 찾을 수 있습니다.

> [!NOTE]
> - 모든 사용자 계정에 최대 100 개의 확장 특성을 쓸 수 있습니다.
> - B2c 응용 프로그램을 삭제 하는 경우 해당 확장 특성은 포함 된 모든 데이터와 함께 모든 사용자에서 제거 됩니다.
> - 응용 프로그램에서 확장 특성을 삭제 하면 모든 사용자 계정에서 제거 되며 값이 삭제 됩니다.
> - 확장 특성의 기본 이름은 "Extension_" + 응용 프로그램 ID + "_" + 특성 이름 형식으로 생성 됩니다. 예를 들어 LoyaltyNumber 확장 특성을 만들고 b2c 응용 프로그램 ID가 831374b3-bd50-41bf-aa54-263ec9e050fc 인 경우 기본 확장 특성 이름은 extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber 됩니다. Graph API 쿼리를 실행 하 여 사용자 계정을 만들거나 업데이트할 때 기본 이름을 사용 합니다.

스키마 확장에서 속성을 정의할 때 지원 되는 데이터 형식은 다음과 같습니다.

|속성 형식 |설명  |
|--------------|---------|
|부울    | 가능한 값은 **true** 또는 **false**입니다. |
|DateTime   | ISO 8601 형식으로 지정해야 합니다. UTC에 저장됩니다.   |
|정수    | 32비트 값입니다.               |
|문자열     | 최대 256자     |

## <a name="next-steps"></a>다음 단계
확장 특성에 대 한 자세한 정보:
- [스키마 확장](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [사용자 흐름을 사용 하 여 사용자 지정 특성 정의](user-flow-custom-attributes.md)
- [사용자 지정 정책을 사용 하 여 사용자 지정 특성 정의](custom-policy-custom-attributes.md)
