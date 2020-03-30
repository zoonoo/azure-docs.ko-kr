---
title: Azure Active 디렉터리 B2C의 사용자 프로필 특성
description: Azure AD B2C 디렉터리 사용자 프로필에서 지원하는 사용자 리소스 유형 특성에 대해 알아봅니다. 기본 제공 속성, 확장 및 특성이 Microsoft 그래프에 매핑하는 방법에 대해 알아보십시오.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057302"
---
# <a name="user-profile-attributes"></a>사용자 프로필 특성

Azure Active Directory(Azure AD) B2C 디렉터리 사용자 프로필에는 지정된 이름, 성, 도시, 우편 번호 및 전화 번호와 같은 기본 제공 속성 집합이 함께 제공됩니다. 외부 데이터 저장소없이 사용자 프로필을 사용자 고유의 응용 프로그램 데이터로 확장할 수 있습니다. Azure AD B2C 사용자 프로필과 함께 사용할 수 있는 대부분의 특성도 Microsoft 그래프에서 지원됩니다. 이 문서에서는 지원되는 Azure AD B2C 사용자 프로필 특성에 대해 설명합니다. 또한 Microsoft 그래프에서 지원되지 않는 속성과 Azure AD B2C에서 사용하지 않아야 하는 Microsoft 그래프 특성도 기록하고 있습니다.

> [!IMPORTANT]
> 계정 자격 증명, 정부 ID 번호, 카드 소지자 데이터, 금융 계정 데이터, 의료 정보 또는 민감한 배경 정보와 같은 중요한 개인 데이터를 저장하기 위해 기본 제공 또는 확장 특성을 사용해서는 안 됩니다.

외부 시스템과 통합할 수도 있습니다. 예를 들어 인증에 Azure AD B2C를 사용할 수 있지만 외부 CRM(고객 관계 관리) 또는 고객 충성도 데이터베이스를 고객 데이터의 신뢰할 수 있는 원본으로 위임할 수 있습니다. 자세한 내용은 원격 [프로필](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) 솔루션을 참조하십시오.

아래 표에는 Azure AD B2C 디렉터리 사용자 프로필에서 지원하는 [사용자 리소스 유형](https://docs.microsoft.com/graph/api/resources/user) 특성이 나열되어 있습니다. 각 특성에 대한 다음 정보를 제공합니다.

- Azure AD B2C에서 사용하는 특성 이름(괄호 안에 Microsoft 그래프 이름(다른 경우) 뒤에 있음)
- 특성 데이터 형식
- 특성 설명
- Azure 포털에서 특성을 사용할 수 있는 경우
- 사용자 흐름에서 특성을 사용할 수 있는 경우
- 사용자 지정 정책 [Azure AD 기술 프로필에서](active-directory-technical-profile.md) 특성을 사용할&lt;수&gt; &lt;있는&gt;경우(입력 클레임, OutputClaims 또는 &lt;지속 클레임)&gt;

|이름     |Type     |Description|Azure portal|사용자 흐름|사용자 지정 정책|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |부울|사용자 계정이 사용 설정되었는지 사용 중지되었는지 여부: 계정이 활성화된 경우 **true(** 그렇지 않으면 **false)**.|yes|예|지속됨, 출력|
|연령대        |String|사용자의 연령 그룹입니다. 가능한 값 : null, 정의되지 않은, 마이너, 성인, NotAdult.|yes|예|지속됨, 출력|
|대안보안Id[(아이덴티티)](manage-user-accounts-graph-api.md#identities-property)|String|외부 ID 공급자의 단일 사용자 ID입니다.|예|예|입력, 지속, 출력|
|대안보안ID[(아이덴티티)](manage-user-accounts-graph-api.md#identities-property)|대체 보안Id 컬렉션|외부 ID 공급자의 사용자 ID 컬렉션입니다.|예|예|지속됨, 출력|
|city            |String|사용자의 구/군/시입니다. 최대 길이 128.|yes|yes|지속됨, 출력|
|동의제공포마이너|String|미성년자에 대한 동의가 제공되었는지 여부. 허용된 값: null, 부여됨, 거부됨 또는 NotRequired.|yes|예|지속됨, 출력|
|country         |String|사용자의 국가/지역입니다. 예: "미국" 또는 "영국". 최대 길이 128.|yes|yes|지속됨, 출력|
|createdDateTime|DateTime|사용자 개체가 만들어진 날짜입니다. 읽기 전용입니다.|예|예|지속됨, 출력|
|creationType    |String|Azure Active Directory B2C 테넌트의 로컬 계정으로 사용자 계정을 만든 경우 값은 LocalAccount 또는 nameCo존재입니다. 읽기 전용입니다.|예|예|지속됨, 출력|
|dateOfBirth     |Date|생년월일|예|예|지속됨, 출력|
|department      |String|사용자의 근무 부서 이름입니다. 최대 길이 64.|yes|예|지속됨, 출력|
|displayName     |String|사용자의 표시 이름입니다. 최대 길이 256.|yes|yes|지속됨, 출력|
|팩스전화 번호<sup>1</sup>|String|사용자 회사 팩스의 전화 번호입니다.|yes|예|지속됨, 출력|
|givenName       |String|사용자의 지정된 이름입니다. 최대 길이 64.|yes|yes|지속됨, 출력|
|jobTitle        |String|사용자의 직위입니다. 최대 길이 128.|yes|yes|지속됨, 출력|
|immutableId     |String|일반적으로 온-프레미스 Active Directory에서 마이그레이션된 사용자에 대 한 사용 되는 식별자입니다.|예|예|지속됨, 출력|
|법적연령그룹분류|String|법적 연령 그룹 분류. 읽기 전용 및 연령에 따라 계산그룹 및 동의제공ForMinor 속성. 허용 된 값 : null, minorWithOut부모동의, 미성년자부모동의, 미성년자NoParental동의요구, 성인및 성인이 아닙니다.|yes|예|지속됨, 출력|
|법적국가<sup>1</sup>  |String|법적 목적을 위한 국가.|예|예|지속됨, 출력|
|mail            |String|사용자의 SMTP 주소(예: "bob@contoso.com"). 읽기 전용입니다.|예|예|지속됨, 출력|
|mailNickName    |String|사용자의 메일 별칭입니다. 최대 길이 64.|예|예|지속됨, 출력|
|모바일(휴대폰) |String|사용자의 기본 휴대 전화 번호입니다. 최대 길이 64.|yes|예|지속됨, 출력|
|네티드           |String|순 ID.|예|예|지속됨, 출력|
|objectId        |String|사용자에 대 한 고유 식별자는 전역적으로 고유 한 식별자 (GUID) 입니다. 예: 12345678-9abc-def0-1234-56789abcde. 만 읽기, 변경할 수 없습니다.|읽기 전용|yes|입력, 지속, 출력|
|otherMails      |문자열 컬렉션|사용자에 대한 추가 전자 메일 주소 목록입니다. 예: [" ""bob@contoso.com" "Robert@fabrikam.com|예(대체 이메일)|예|지속됨, 출력|
|password        |String|사용자를 만드는 동안 로컬 계정의 암호입니다.|예|예|지속됨|
|passwordPolicies     |String|암호의 정책입니다. 쉼표로 구분된 다른 정책 이름으로 구성된 문자열입니다. 즉, "사용 안 함암호 만료, 강력한 암호 비활성화".|예|예|지속됨, 출력|
|물리적 배달사무실이름(사무실위치)|String|사용자 근무지의 사무실 위치입니다. 최대 길이 128.|yes|예|지속됨, 출력|
|postalCode      |String|사용자 우편 주소의 우편 번호입니다. 우편 번호는 사용자의 국가/지역마다 다릅니다. 미국의 경우 이 특성은 미국 우편 번호를 포함합니다. 최대 길이 40.|yes|예|지속됨, 출력|
|preferredLanguage    |String|사용자의 기본 설정 언어입니다. ISO 639-1 코드를 따라야 합니다. 예: "en-US".|예|예|지속됨, 출력|
|새로 고침토큰유효한유효FromDateTime|DateTime|이 시간 이전에 발급된 새로 고침 토큰은 유효하지 않으며 응용 프로그램에서 새 액세스 토큰을 획득하기 위해 잘못된 새로 고침 토큰을 사용할 때 오류가 발생합니다. 이 경우 응용 프로그램은 권한 부여 끝점에 대한 요청을 하여 새 새로 고침 토큰을 획득해야 합니다. 읽기 전용입니다.|예|예|출력|
|로그인 이름[(ID)](manage-user-accounts-graph-api.md#identities-property)) |String|디렉터리에서 모든 유형의 로컬 계정 사용자의 고유한 로그인 이름입니다. 로컬 계정 유형을 지정하지 않고 로그인 값을 가진 사용자를 얻으려면 이 방법을 사용합니다.|예|예|입력|
|signInNames.userName[(ID)](manage-user-accounts-graph-api.md#identities-property)) ) ) ) |String|디렉터리에서 로컬 계정 사용자의 고유한 사용자 이름입니다. 이 것을 사용하여 특정 로그인 사용자 이름을 가진 사용자를 만들거나 가져옵니다. 패치 작업 중에 지속 클레임에서 이 것을 지정하면 다른 유형의 signInName이 제거됩니다. 새 유형의 signInName을 추가하려면 기존 signInName도 유지해야 합니다.|예|예|입력, 지속, 출력|
|signInNames.phone번호[(ID)](manage-user-accounts-graph-api.md#identities-property) |String|디렉터리에서 로컬 계정 사용자의 고유 전화 번호입니다. 이 것을 사용하여 특정 로그인 전화 번호가 있는 사용자를 만들거나 가져옵니다. 패치 작업 중에 지속 클레임에서 이 것을 지정하면 다른 유형의 signInName이 제거됩니다. 새 유형의 signInName을 추가하려면 기존 signInName도 유지해야 합니다.|예|예|입력, 지속, 출력|
|signInNames.emailAddress[(ID)](manage-user-accounts-graph-api.md#identities-property)|String|디렉터리에서 로컬 계정 사용자의 고유한 전자 메일 주소입니다. 이 방법을 사용하여 특정 로그인 이메일 주소를 사용하여 사용자를 만들거나 가져옵니다. 패치 작업 중에 지속 클레임에서 이 것을 지정하면 다른 유형의 signInName이 제거됩니다. 새 유형의 signInName을 추가하려면 기존 signInName도 유지해야 합니다.|예|예|입력, 지속, 출력|
|state           |String|사용자 주소의 시/도입니다. 최대 길이 128.|yes|yes|지속됨, 출력|
|streetAddress   |String|사용자 근무지의 주소입니다. 최대 길이 1024.|yes|yes|지속됨, 출력|
|강한 인증 대안전화<sup>번호 1</sup>|String|다단계 인증에 사용되는 사용자의 보조 전화 번호입니다.|yes|예|지속됨, 출력|
|강력한 인증이메일 주소<sup>1</sup>|String|사용자의 SMTP 주소입니다. 예:bob@contoso.com" "이 특성은 사용자 이름 정책으로 로그인하여 사용자 전자 메일 주소를 저장하는 데 사용됩니다. 그런 다음 암호 재설정 흐름에 사용되는 전자 메일 주소입니다.|yes|예|지속됨, 출력|
|강한 인증전화<sup>번호 1</sup>|String|다단계 인증에 사용되는 사용자의 기본 전화 번호입니다.|yes|예|지속됨, 출력|
|surname         |String|사용자 성입니다. 최대 길이 64.|yes|yes|지속됨, 출력|
|전화 번호 (비즈니스의 첫 번째 항목전화)|String|사용자 근무지의 기본 전화 번호입니다.|yes|예|지속됨, 출력|
|userPrincipalName    |String|사용자의 UPN(사용자 계정 이름)입니다. UPN은 인터넷 표준 RFC 822를 기반으로 하는 사용자의 인터넷 스타일 로그인 이름입니다. 도메인은 테넌트의 확인된 도메인 컬렉션에 있어야 합니다. 이 속성은 계정을 만들 때 필요합니다. 변경할 수 없습니다.|예|예|입력, 지속, 출력|
|usageLocation   |String|국가 내 서비스 사용 가능 여부를 확인해야 하는 법적 요구 사항으로 인해 라이선스를 할당받는 사용자의 경우 필수입니다. Null을 허용하지 않습니다. 두 자로 된 국가 코드(ISO 표준 3166)입니다. 예: "US", "JP" 및 "GB".|yes|예|지속됨, 출력|
|userType        |String|디렉터리에서 사용자 형식을 분류하는 데 사용할 수 있는 문자열 값입니다. 값은 멤버여야 합니다. 읽기 전용입니다.|읽기 전용|예|지속됨, 출력|
|사용자 상태(외부 사용자 상태)<sup>2</sup>|String|Azure AD B2B 계정의 경우 초대가 수락 보류 중인지 수락중인지 여부를 나타냅니다.|예|예|지속됨, 출력|
|사용자 상태 변경 온 (외부 사용자 상태변경 날짜 시간)<sup>2</sup>|DateTime|UserState 속성에 대한 최신 변경에 대한 타임스탬프를 표시합니다.|예|예|지속됨, 출력|
|<sup>1개</sup> 마이크로 소프트 그래프에서 지원되지 않음<br><sup>2개</sup> Azure AD B2C와 함께 사용해서는 안 됩니다.||||||


## <a name="extension-attributes"></a>확장 특성

다음과 같은 경우와 같이 사용자 고유의 특성을 만들어야 하는 경우가 많습니다.

- 고객 대면 응용 프로그램은 **LoyaltyNumber**와 같은 특성에 대해 유지되어야 합니다.
- ID 공급자는 **uniqueUserGUID**와 같이 저장해야 하는 고유한 사용자 ID를 포함합니다.
- 사용자 지정 사용자 이동은 **마이그레이션Status**와 같은 사용자 상태에 대해 유지되어야 합니다.

Azure AD B2C는 각 사용자 계정에 저장된 특성 집합을 확장합니다. 확장 특성은 디렉터리에서 사용자 개체의 [스키마를 확장합니다.](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) 확장 특성은 사용자에 대한 데이터를 포함할 수 있더라도 응용 프로그램 개체에만 등록할 수 있습니다. 확장 특성은 b2c 확장 앱이라는 응용 프로그램에 첨부됩니다. Azure AD B2C에서 사용자 데이터를 저장하는 데 사용되므로 이 응용 프로그램을 수정하지 마십시오. Azure Active Directory 앱 등록에서 이 응용 프로그램을 찾을 수 있습니다.

> [!NOTE]
> - 모든 사용자 계정에 최대 100개의 확장 특성을 기록할 수 있습니다.
> - b2c 확장 앱 응용 프로그램이 삭제되면 해당 확장 특성이 포함된 모든 데이터와 함께 모든 사용자로부터 제거됩니다.
> - 응용 프로그램에서 확장 특성이 삭제되면 모든 사용자 계정에서 제거되고 값이 삭제됩니다.
> - 확장 특성의 기본 이름은 "Extension_" + 응용 프로그램 ID + "_" + 특성 이름으로 생성됩니다. 예를 들어 확장 속성 LoyaltyNumber를 만들고 b2c 확장 응용 프로그램 응용 프로그램 ID가 831374b3-bd50-aa54-263ec9e050fc인 경우 기본 확장 특성 이름은 다음과 extension_831374b3bd5041bfaa54263ec9e050fc_ 충성도 번호. 그래프 API 쿼리를 실행하여 사용자 계정을 만들거나 업데이트할 때 기본 이름을 사용합니다.

스키마 확장에서 속성을 정의할 때 다음 데이터 형식이 지원됩니다.

|속성 형식 |설명  |
|--------------|---------|
|부울    | 가능한 값: **true** 또는 **false**. |
|DateTime   | ISO 8601 형식으로 지정해야 합니다. UTC에 저장됩니다.   |
|정수    | 32비트 값입니다.               |
|String     | 최대 256자     |

## <a name="next-steps"></a>다음 단계
확장 특성에 대해 자세히 알아보세요.
- [스키마 확장](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [사용자 흐름을 통해 사용자 지정 특성 정의](user-flow-custom-attributes.md)
- [사용자 지정 정책으로 사용자 지정 특성 정의](custom-policy-custom-attributes.md)
