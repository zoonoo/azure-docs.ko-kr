---
title: Azure AD B2C 로컬 계정 id 공급자 설정
titleSuffix: Azure AD B2C
description: 사용자가 Azure Active Directory B2C 테 넌 트에서 등록 하거나 로그인 (메일, 사용자 이름, 전화 번호) 하는 데 사용할 수 있는 id 유형을 정의 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e6b520a32755379fce5326e25b90f135b0d67755
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98574356"
---
# <a name="set-up-the-local-account-identity-provider"></a>로컬 계정 id 공급자 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C은 사용자가 사용자를 인증할 수 있는 다양 한 방법을 제공 합니다. 사용자는 사용자 이름 및 암호, 전화 확인 (암호 낮은 인증이 라고도 함) 또는 소셜 id 공급자를 사용 하 여 로컬 계정에 로그인 할 수 있습니다. 전자 메일 등록은 로컬 계정 id 공급자 설정에서 기본적으로 사용 하도록 설정 됩니다. 

이 문서에서는 사용자가이 Azure AD B2C 테 넌 트에 로컬 계정을 만드는 방법을 설명 합니다. 사용자의 id가 Facebook 및 Google과 같은 페더레이션된 id 공급자에 의해 관리 되는 소셜 또는 엔터프라이즈 id의 경우 [id 공급자 추가](add-identity-provider.md)를 참조 하세요.

## <a name="email-sign-in"></a>전자 메일 로그인

전자 메일 옵션을 사용 하면 사용자가 전자 메일 주소 및 암호를 사용 하 여 로그인/등록할 수 있습니다.

- **로그인** 하면 사용자에 게 전자 메일 및 암호를 입력 하 라는 메시지가 표시 됩니다.
- **등록 하면 사용자** 에 게 전자 메일 주소를 입력 하 라는 메시지가 표시 됩니다 .이 주소는 등록 (선택 사항)으로 확인 되 고 로그인 ID가 됩니다. 그런 다음 사용자는 표시 이름, 지정 된 이름 및 성 등 등록 페이지에서 요청 된 다른 정보를 입력 합니다. 그런 다음, 계속을 선택 하 여 계정을 만듭니다.
- **암호 재설정** 은 사용자가 메일을 입력 하 고 확인 해야 하며, 그 후에는 사용자가 암호를 다시 설정할 수 있습니다.

![전자 메일 등록 또는 로그인 환경](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>사용자 이름 로그인

사용자 옵션을 사용 하 여 사용자는 사용자 이름 및 암호를 사용 하 여 로그인/등록할 수 있습니다.

- **로그인**: 사용자에 게 사용자 이름과 암호를 입력 하 라는 메시지가 표시 됩니다.
- **등록**: 사용자에 게 로그인 ID가 될 사용자 이름을 입력 하 라는 메시지가 표시 됩니다. 또한 사용자에 게 전자 메일 주소를 입력 하 라는 메시지가 표시 됩니다 .이 주소는 등록 시 확인 됩니다. 전자 메일 주소는 암호 다시 설정 흐름 중에 사용 됩니다. 사용자가 등록 페이지에서 요청 된 다른 정보 (예: 표시 이름, 지정 된 이름 및 성)를 입력 합니다. 그런 다음 사용자가 계속 해 서 계정을 만듭니다 .를 선택 합니다.
- **암호 재설정**: 사용자는 사용자 이름 및 연결 된 전자 메일 주소를 입력 해야 합니다. 전자 메일 주소를 확인 해야 하며, 그 후에는 사용자가 암호를 다시 설정할 수 있습니다.

![사용자 이름 등록 또는 로그인 환경](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>휴대폰 로그인 (미리 보기)

Passwordless authentication은 사용자가 암호를 사용 하 여 로그인 하지 않아도 되는 인증 유형입니다. 전화 등록 및 로그인을 사용 하 여 사용자는 기본 로그인 식별자로 전화 번호를 사용 하 여 앱에 등록할 수 있습니다. 사용자는 등록 및 로그인 시 다음과 같은 환경을 제공 합니다.

- **로그인**: 사용자에 게 id로 전화 번호가 있는 기존 계정이 있는 경우 사용자는 전화 번호를 입력 하 고 *로그인* 을 선택 합니다. 사용자는 *계속* 을 선택 하 여 국가 및 전화 번호를 확인 하 고, 한 번의 확인 코드를 휴대폰으로 보냅니다. 사용자가 확인 코드를 입력 하 고 *계속* 로그인을 선택 합니다.
- **등록**: 사용자에 게 응용 프로그램에 대 한 계정이 아직 없는 경우 *지금 등록* 링크를 클릭 하 여 만들 수 있습니다. 
    1. 등록 페이지가 표시 되 고 사용자가 해당 *국가* 를 선택 하 고 전화 번호를 입력 하 고 *코드 보내기* 를 선택 합니다. 
    1. 일회성 확인 코드가 사용자의 전화 번호로 전송 됩니다. 사용자가 등록 페이지에 *확인 코드* 를 입력 하 고 *코드 확인* 을 선택 합니다. 사용자가 코드를 검색할 수 없는 경우 *새 코드 보내기* 를 선택할 수 있습니다. 
    1. 사용자가 등록 페이지에서 요청 된 다른 정보 (예: 표시 이름, 지정 된 이름 및 성)를 입력 합니다. 그런 다음, 계속을 선택합니다.
    1. 그런 다음 사용자에 게 **복구 전자 메일** 을 제공 하 라는 메시지가 표시 됩니다. 사용자가 전자 메일 주소를 입력 하 고 *확인 코드 보내기* 를 선택 합니다. 사용자의 전자 메일 받은 편지함에 코드가 전송 되 고,이를 검색 하 여 확인 코드 상자에 입력할 수 있습니다. 그런 다음 사용자가 코드 확인을 선택 합니다.
    1. 코드를 확인 한 후 사용자는 *만들기* 를 선택 하 여 계정을 만듭니다. 

![전화 등록 또는 로그인 환경](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>가격 책정

SMS 문자 메시지를 사용 하 여 일회용 암호를 사용자에 게 보냅니다. 모바일 네트워크 운영자에 따라 전송 되는 각 메시지에 대해 요금이 청구 될 수 있습니다. 가격 책정 정보는 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)의 **별도 요금** 섹션을 참조 하세요.

> [!NOTE]
> 전화 등록으로 사용자 흐름을 구성 하는 경우 MFA (multi-factor authentication)는 기본적으로 사용 되지 않습니다. 전화 등록을 사용 하 여 사용자 흐름에서 MFA를 사용 하도록 설정할 수 있지만 전화 번호가 기본 식별자로 사용 되기 때문에 두 번째 인증 요소에 사용할 수 있는 유일한 옵션은 전자 메일 일회용 암호입니다.

### <a name="phone-recovery"></a>전화 복구

사용자 흐름에 대해 전화 등록 및 로그인을 사용 하도록 설정 하면 복구 전자 메일 기능을 사용 하도록 설정 하는 것도 좋습니다. 이 기능을 사용 하면 사용자가 휴대폰을 사용 하지 않을 때 계정을 복구 하는 데 사용할 수 있는 전자 메일 주소를 제공할 수 있습니다. 이 메일 주소는 계정 복구에만 사용 됩니다. 이 파일은 로그인 하는 데 사용할 수 없습니다.

- 복구 전자 메일 메시지가 표시 되 면 처음 등록 하는 사용자에 게 백업 전자 메일을 확인 하 라는 메시지가 **표시 됩니다.** 이전에 복구 전자 메일을 제공 하지 않은 사용자는 다음에 로그인 하는 동안 백업 전자 메일을 확인 하 라는 메시지가 표시 됩니다.

- 복구 전자 메일이 **꺼져** 있는 경우 사용자가 등록 하거나 로그인 하면 복구 전자 메일 프롬프트가 표시 되지 않습니다.
 
다음 스크린샷은 전화 복구 흐름을 보여 줍니다.

![전화 복구 사용자 흐름](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>전화 또는 전자 메일 로그인 (미리 보기)

[휴대폰 로그인](#phone-sign-in-preview)과 [전자 메일 로그인](#email-sign-in)을 결합 하도록 선택할 수 있습니다. 등록 또는 로그인 페이지에서 사용자는 전화 번호 또는 전자 메일 주소를 입력할 수 있습니다. 사용자 입력에 따라 사용자가 해당 흐름으로 이동 Azure AD B2C. 

![전화 또는 전자 메일 등록 또는 로그인 환경](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>로컬 계정 id 공급자 설정 구성

공급자 (전자 메일, 사용자 이름 또는 전화 번호)를 사용 하거나 사용 하지 않도록 설정 하 여 사용자 흐름 내에서 사용할 수 있는 로컬 id 공급자를 구성할 수 있습니다.  테 넌 트 수준에서 두 개 이상의 로컬 id 공급자를 사용할 수 있습니다.

사용자 흐름은 한 번에 로컬 계정 id 공급자 중 하나를 사용 하도록 구성할 수 있습니다. 테 넌 트 수준에서 사용 하도록 설정 된 경우 각 사용자 흐름은 다른 로컬 계정 id 공급자를 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **관리** 아래에서 **ID 공급자** 를 선택합니다.
1. ID 공급자 목록에서 **로컬 계정** 을 선택합니다.
1. **로컬 IDP 구성** 페이지에서 소비자가 Azure AD B2C 테 넌 트에서 로컬 계정을 만드는 데 사용할 수 있는 허용 가능한 id 유형 중 하나 이상을 선택 합니다.
1. **저장** 을 선택합니다.

## <a name="configure-your-user-flow"></a>사용자 흐름 구성

1. Azure Portal 왼쪽 메뉴에서 **Azure AD B2C** 를 선택 합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 등록 및 로그인 환경을 구성 하려는 사용자 흐름을 선택 합니다.
1. **Id 공급자** 선택
1. **로컬 계정** 에서 **전자 메일 등록**, **사용자 ID 등록**, **전화 등록**, **전화/이메일 등록** 또는 **없음** 중 하나를 선택 합니다.

### <a name="enable-the-recovery-email-prompt"></a>복구 전자 메일 프롬프트 사용

**전화 등록**, **전화/전자 메일 등록** 옵션을 선택 하는 경우 복구 전자 메일 프롬프트를 사용 하도록 설정 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. Azure AD B2C의 **정책** 에서 **사용자 흐름** 을 선택 합니다.
1. 목록에서 사용자 흐름을 선택 합니다.
1. **설정** 아래에서 **속성** 을 선택합니다.
1. **전화 번호 등록 및 로그인 (미리 보기)에 대 한 복구 전자 메일 확인을 사용** 하려면 다음을 선택 합니다.
   - **에서** 등록 및 로그인 중에 복구 전자 메일 메시지를 표시 합니다.
   - 을 (를) **해제** 하 여 복구 전자 메일 메시지를 숨깁니다.
1. **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>시작 팩 가져오기

사용자 지정 정책은 사용자 경험를 정의 하기 위해 Azure AD B2C 테 넌 트에 업로드 하는 XML 파일 집합입니다. 몇 가지 미리 작성 된 정책을 사용 하 여 시작 팩을 제공 합니다. 관련 스타터 팩을 다운로드 합니다. 

- [전자 메일 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [사용자 이름 로그인](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [휴대폰 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 신뢰 당사자 정책을 선택 합니다. 
- [전화 또는 전자 메일 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 신뢰 당사자 정책을 선택 합니다.

시작 팩을 다운로드 한 후

1. 각 파일에서 문자열을 `yourtenant` Azure AD B2C 테 넌 트의 이름으로 바꿉니다. 예를 들어 B2C 테 넌 트의 이름이 *contosob2c* 인 경우의 모든 인스턴스는가 됩니다 `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .

1. [Azure Active Directory B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)의 [사용자 지정 정책에 응용 프로그램 id 추가](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) 섹션에 설명 된 단계를 완료 합니다. 예를 들어 `/phone-number-passwordless/` **`Phone_Email_Base.xml`** 필수 구성 요소 *IdentityExperienceFramework* 및 *ProxyIdentityExperienceFramework* 를 완료할 때 등록 한 두 응용 프로그램의 **응용 프로그램 (클라이언트) id** 로 업데이트 합니다.
1. 정책 파일을 업로드 합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

- [외부 id 공급자 추가](tutorial-add-identity-providers.md)
- [사용자 흐름 만들기](tutorial-create-user-flows.md)
