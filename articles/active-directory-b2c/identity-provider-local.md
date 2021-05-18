---
title: Azure AD B2C 로컬 계정 ID 공급자 설정
titleSuffix: Azure AD B2C
description: 사용자가 Azure Active Directory B2C 테넌트에서 등록 또는 로그인하는 데 사용할 수 있는 ID 유형(이메일, 사용자 이름, 전화 번호)을 정의합니다.
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
ms.openlocfilehash: b2baff33d9e91e1b5259d79eca0a22535c00f419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555236"
---
# <a name="set-up-the-local-account-identity-provider"></a>로컬 계정 ID 공급자 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C는 사용자가 사용자를 인증할 수 있는 다양한 방법을 제공합니다. 사용자는 사용자 이름 및 암호, 전화 확인(암호 없는 인증이라고도 함) 또는 소셜 ID 공급자를 사용하여 로컬 계정에 로그인할 수 있습니다. 이메일 등록은 로컬 계정 ID 공급자 설정에서 기본적으로 사용하도록 설정됩니다. 

이 문서에서는 사용자가 이 Azure AD B2C 테넌트에 로컬 계정을 만드는 방법을 설명합니다. 사용자 ID가 Facebook, Google과 같은 페더레이션 ID 공급자에 의해 관리되는 소셜 또는 엔터프라이즈 ID의 경우 [ID 공급자 추가](add-identity-provider.md)를 참조하세요.

## <a name="email-sign-in"></a>이메일 로그인

이메일 옵션을 사용하면 사용자가 이메일 주소 및 암호를 사용하여 로그인/등록할 수 있습니다.

- **로그인**: 사용자에게 이메일 및 암호를 입력하라는 메시지가 표시됩니다.
- **등록**: 이메일 주소를 묻는 메시지가 표시됩니다. 이 주소는 등록 시 확인되고 로그인 ID로 사용됩니다. 그런 다음 사용자는 표시 이름, 이름, 성 등 등록 페이지에서 요청된 다른 정보를 입력합니다. 그런 다음 계속을 선택하여 계정을 만듭니다.
- **암호 재설정**: 사용자가 이메일을 입력하고 확인해야 하며, 그런 다음 사용자가 암호를 다시 설정할 수 있습니다.

![이메일 등록 또는 로그인 환경](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>사용자 이름 로그인

사용자 옵션을 사용하여 사용자는 사용자 이름 및 암호를 사용하여 로그인/등록할 수 있습니다.

- **로그인**: 사용자에게 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다.
- **등록**: 사용자에게 로그인 ID로 사용될 사용자 이름을 입력하라는 메시지가 표시됩니다. 또한 사용자에게 이메일 주소를 입력하라는 메시지가 표시됩니다. 이 주소는 등록 시 확인됩니다. 이메일 주소는 암호 재설정 흐름에서도 사용됩니다. 사용자는 등록 페이지에서 요청된 다른 정보(예: 표시 이름, 이름 및 성)를 입력합니다. 그런 다음 계속을 선택하여 계정을 만듭니다.
- **암호 재설정**: 사용자가 사용자 이름 및 연결된 이메일 주소를 입력해야 합니다. 이메일 주소를 확인해야 하며, 그런 다음 사용자가 암호를 다시 설정할 수 있습니다.

![사용자 이름 등록 또는 로그인 환경](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>전화 로그인(미리 보기)

암호 없는 인증은 사용자가 암호를 사용하여 로그인하지 않아도 되는 인증 유형입니다. 전화 등록 및 로그인을 사용하여 사용자는 전화 번호를 기본 로그인 식별자로 사용하여 앱에 등록할 수 있습니다. 사용자가 등록 및 로그인할 때 다음과 같은 환경이 제공됩니다.

- **로그인**: 사용자에게 ID로 전화 번호를 사용하는 기존 계정이 있는 경우 사용자가 전화 번호를 입력하고 로그인을 선택합니다. 사용자가 계속을 선택하여 국가 및 전화 번호를 확인하고 1회성 확인 코드가 휴대폰으로 전송됩니다. 사용자가 확인 코드를 입력하고 계속을 선택하여 로그인합니다.
- **등록**: 사용자가 애플리케이션에 대한 계정이 아직 없는 경우 지금 등록 링크를 클릭하여 계정을 만들 수 있습니다. 
    1. 등록 페이지가 표시되면 사용자가 국가를 선택하고, 전화 번호를 입력하고, 코드 보내기를 선택합니다. 
    1. 일회성 확인 코드가 사용자의 전화 번호로 전송됩니다. 사용자가 등록 페이지에 확인 코드를 입력하고 코드 확인을 선택합니다. (사용자가 코드를 검색할 수 없는 경우 새 코드 보내기를 선택할 수 있습니다.) 
    1. 사용자는 등록 페이지에서 요청된 다른 정보(예: 표시 이름, 이름 및 성)를 입력합니다. 그런 다음, 계속을 선택합니다.
    1. 다음으로 사용자에게 **복구 메일** 을 제공하라는 메시지가 표시됩니다. 사용자가 이메일 주소를 입력하고 확인 코드 보내기를 선택합니다. 사용자의 이메일 받은 편지함에 코드가 전송되고 사용자가 이를 검색하여 확인 코드 상자에 입력할 수 있습니다. 그런 다음 사용자가 코드 확인을 선택합니다.
    1. 코드가 확인되면 사용자가 만들기를 선택하여 계정을 만듭니다. 

![전화 등록 또는 로그인 환경](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>가격 책정

일회용 암호는 SMS 문자 메시지를 사용하여 사용자에게 전송됩니다. 모바일 네트워크 사업자에 따라 전송되는 각 메시지에 대해 요금이 청구될 수 있습니다. 가격 책정 정보는 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)의 **별도 요금** 섹션을 참조하세요.

> [!NOTE]
> 전화 등록으로 사용자 흐름을 구성하는 경우 MFA(다단계 인증)는 기본적으로 사용되지 않습니다. 전화 등록을 통해 사용자 흐름에서 MFA를 사용할 수 있으나, 여기에는 전화 번호가 기본 식별자로 사용되기 때문에 일회용 이메일 암호가 두 번째 인증 요소에 사용할 수 있는 유일한 옵션이 됩니다.

### <a name="phone-recovery"></a>전화 복구

사용자 흐름에서 전화 등록 및 로그인을 사용하도록 설정할 때 복구 메일 기능도 사용하도록 설정하는 것이 좋습니다. 해당 기능은 사용자가 휴대폰을 사용하지 않을 경우 계정을 복구하는 데 사용할 수 있는 이메일 주소를 제공합니다. 해당 이메일 주소는 계정 복구에만 사용되며, 로그인에 사용할 수 없습니다.

- 복구 메일 프롬프트가 **On** 인 경우 처음 등록하는 사용자에게 백업 메일을 확인하라는 메시지가 표시됩니다. 이전에 복구 메일을 제공하지 않은 사용자는 다음 로그인 시 백업 메일을 확인하라는 메시지가 표시됩니다.

- 복구 메일이 **Off** 인 경우 사용자가 등록하거나 로그인하면 복구 메일 프롬프트가 표시되지 않습니다.
 
다음 스크린샷에서는 전화 복구 흐름을 보여 줍니다.

![전화 복구 사용자 흐름](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>전화 또는 이메일 로그인(미리 보기)

[전화 로그인](#phone-sign-in-preview)과 [이메일 로그인](#email-sign-in)을 결합하도록 선택할 수 있습니다. 등록 또는 로그인 페이지에서 사용자가 전화 번호 또는 이메일 주소를 입력할 수 있습니다. 사용자 입력에 따라 Azure AD B2C가 사용자를 해당 흐름으로 이동합니다. 

![전화 또는 이메일 등록 또는 로그인 환경](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>로컬 계정 ID 공급자 설정 구성

공급자(이메일, 사용자 이름 또는 전화 번호)를 사용하거나 사용하지 않도록 설정하여 사용자 흐름 내에서 사용할 수 있는 로컬 ID 공급자를 구성할 수 있습니다.  테넌트 수준에서 두 개 이상의 로컬 ID 공급자를 사용할 수 있습니다.

사용자 흐름은 한 번에 로컬 계정 ID 공급자 중 하나를 사용하도록 구성할 수 있습니다. 테넌트 수준에서 여러 로컬 계정 ID 공급자가 사용하도록 설정된 경우 각 사용자 흐름마다 다른 공급자를 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, Azure AD 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **관리** 아래에서 **ID 공급자** 를 선택합니다.
1. ID 공급자 목록에서 **로컬 계정** 을 선택합니다.
1. **로컬 IDP 구성** 페이지에서 소비자가 Azure AD B2C 테넌트의 로컬 계정을 만드는 데 사용할 수 있는 허용 가능한 ID 유형을 하나 이상 선택합니다.
1. **저장** 을 선택합니다.

## <a name="configure-your-user-flow"></a>사용자 흐름 구성

1. Azure Portal 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 등록 및 로그인 환경을 구성하려는 사용자 흐름을 선택합니다.
1. **ID 공급자** 를 선택합니다.
1. **로컬 계정** 에서 **이메일 등록**, **사용자 ID 등록**, **전화 등록**, **전화/이메일 등록** 또는 **없음** 중 하나를 선택합니다.

### <a name="enable-the-recovery-email-prompt"></a>복구 메일 프롬프트를 사용하도록 설정

**전화 등록**, **전화/이메일 등록** 옵션을 선택하는 경우 복구 메일 프롬프트를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. Azure AD B2C의 **정책** 에서 **사용자 흐름** 을 선택합니다.
1. 목록에서 사용자 흐름을 선택합니다.
1. **설정** 아래에서 **속성** 을 선택합니다.
1. **전화 번호 등록 및 로그인에 복구 메일 프롬프트를 사용하도록 설정(미리 보기)** 에서 다음을 선택합니다.
   - **On** 은 등록 및 로그인 시 복구 메일 프롬프트를 표시합니다.
   - **Off** 는 복구 메일 프롬프트를 숨깁니다.
1. **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>시작 팩 가져오기

사용자 지정 정책은 사용자 경험을 정의하기 위해 Azure AD B2C 테넌트에 업로드하는 XML 파일 집합입니다. 몇 가지 미리 작성된 정책이 포함된 시작 팩이 제공됩니다. 관련 스타터 팩을 다운로드합니다. 

- [이메일 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [사용자 이름 로그인](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [전화 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 신뢰 당사자 정책을 선택합니다. 
- [전화 또는 이메일 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 신뢰 당사자 정책을 선택합니다.

시작 팩을 다운로드한 후

1. 각 파일에서 `yourtenant` 문자열을 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예를 들어 B2C 테넌트의 이름이 *contosob2c* 인 경우 모든 `yourtenant.onmicrosoft.com` 인스턴스는 `contosob2c.onmicrosoft.com`이 됩니다.

1. [Azure Active Directory B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)의 [사용자 지정 정책에 애플리케이션 ID 추가](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) 섹션에 설명된 단계를 수행합니다. 예를 들어 필수 구성 요소 *IdentityExperienceFramework* 및 *ProxyIdentityExperienceFramework* 를 완료할 때 등록한 두 애플리케이션의 **애플리케이션(클라이언트) ID** 로 `/phone-number-passwordless/`**`Phone_Email_Base.xml`** 을 업데이트합니다.
1. 정책 파일을 업로드합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

- [외부 ID 공급자 추가](add-identity-provider.md)
- [사용자 흐름 만들기](tutorial-create-user-flows.md)
