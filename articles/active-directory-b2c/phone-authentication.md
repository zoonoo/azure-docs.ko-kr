---
title: 전화 등록 및 사용자 지정 정책으로 로그인(미리 보기)
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 응용 프로그램 사용자의 휴대폰에 문자 메시지의 OTP(일회성 암호)를 보냅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183961"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Azure AD B2C에서 사용자 지정 정책으로 전화 등록 및 로그인 설정(미리 보기)

Azure Active Directory B2C(Azure AD B2C)에서 전화 등록 및 로그인을 사용하면 사용자가 휴대폰으로 문자 메시지로 전송되는 OTP(일회성 암호)를 사용하여 응용 프로그램에 등록하고 로그인할 수 있습니다. 일회성 암호는 사용자가 암호를 잊어내거나 손상될 위험을 최소화하는 데 도움이 될 수 있습니다.

이 문서의 단계에 따라 사용자 지정 정책을 사용하여 고객이 휴대폰으로 전송된 일회용 암호를 사용하여 응용 프로그램에 등록하고 로그인할 수 있도록 합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>가격 책정

SMS 문자 메시지를 사용하여 일회성 암호가 사용자에게 전송되며 전송된 각 메시지에 대해 요금이 부과될 수 있습니다. 가격 정보는 [Azure Active Directory B2C 가격](https://azure.microsoft.com/pricing/details/active-directory-b2c/)책정의 별도 **요금** 섹션을 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

OTP를 설정하기 전에 다음 리소스가 필요합니다.

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* 테넌트에 [등록된 웹 응용 프로그램](tutorial-register-applications.md)
* 테넌트에 업로드된 [사용자 지정 정책](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>전화 등록 & 등록 스타터 팩 받기

Azure AD B2C 테넌트와 함께 작동하도록 전화 등록 및 로그인 사용자 지정 정책 파일을 업데이트하여 시작합니다.

다음 단계에서는 [필수 구성 조건을](#prerequisites) 완료하고 사용자 지정 정책 시작 [팩][starter-pack] 리포지토리를 로컬 컴퓨터에 이미 복제했다고 가정합니다.

1. 시작 팩 리포지토리의 로컬 복제에서 [전화 등록 및 로그인 사용자 지정 정책 파일을][starter-pack-phone] 찾거나 직접 다운로드합니다. XML 정책 파일은 다음 디렉터리에 있습니다.

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 각 파일에서 문자열을 `yourtenant` Azure AD B2C 테넌트의 이름으로 바꿉니다. 예를 들어 B2C 테넌트의 이름이 *contosob2c인*경우 `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com`의 모든 인스턴스가 됩니다.

1. [Azure Active Directory B2C의 사용자 지정 정책 시작하기의](custom-policy-get-started.md)사용자 지정 정책 섹션에 [응용 프로그램 아이디 추가](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) 단계 완료. 이 경우 필수 `/phone-number-passwordless/` **`Phone_Email_Base.xml`** 구성 요소인 *IdentityExperienceFramework* 및 *프록시IdentityExperienceFramework를*완료할 때 등록한 두 응용 프로그램의 **응용 프로그램(클라이언트) ID로** 업데이트합니다.

## <a name="upload-the-policy-files"></a>정책 파일 업로드

1. [Azure 포털에](https://portal.azure.com) 로그인하고 Azure AD B2C 테넌트로 이동합니다.
1. **정책에서** **ID 환경 프레임워크를**선택합니다.
1. **사용자 지정 정책 업로드를 선택합니다.**
1. 다음 순서로 정책 파일을 업로드합니다.
    1. *Phone_Email_Base.xml*
    1. *가입자로그인폰.xml*
    1. *가입자 로그인전화또는이메일로.xml*
    1. *프로파일편집폰만.xml*
    1. *프로필편집폰이메일.xml*
    1. *변경 전화 번호.xml*
    1. *암호 재설정이메일.xml*

각 파일을 업로드할 때 Azure는 `B2C_1A_`접두사를 추가합니다.

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. **사용자 지정 정책에서** **B2C_1A_SignUpOrSignInWithPhone**선택합니다.
1. **선택 응용 프로그램에서**필수 구성 조건을 완료할 때 등록한 *webapp1* 응용 프로그램을 선택합니다.
1. **회신 URL 선택에서**를 선택합니다. `https://jwt.ms`
1. **지금 실행을** 선택하고 이메일 주소 또는 전화 번호를 사용하여 등록합니다.
1. **지금 실행을** 다시 한 번 선택하고 동일한 계정으로 로그인하여 올바른 구성이 있는지 확인합니다.

## <a name="get-user-account-by-phone-number"></a>전화 번호로 사용자 계정 받기

전화 번호로 등록하지만 복구 전자 메일 주소를 제공하지 않는 사용자는 Azure AD B2C 디렉터리에 전화 번호를 로그인 이름으로 기록합니다. 사용자가 전화 번호를 변경하려면 헬프 데스크 또는 지원 팀에서 먼저 계정을 찾은 다음 전화 번호를 업데이트해야 합니다.

[Microsoft 그래프를](manage-user-accounts-graph-api.md)사용하여 사용자의 전화 번호(로그인 이름)로 사용자를 찾을 수 있습니다.

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

예를 들어:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>다음 단계

GitHub에서 전화 등록 및 로그인 사용자 지정 정책 시작 팩(및 기타 스타터 팩)을 찾을 수 있습니다.

[Azure-샘플/활성 디렉토리-b2c-사용자 지정 정책-시작 팩/시나리오/전화 번호 암호 없는][starter-pack-phone]

스타터 팩 정책 파일은 다단계 인증 기술 프로필 및 전화 번호 클레임 변환을 사용합니다.

* [Azure 다단계 인증 기술 프로필 정의](multi-factor-auth-technical-profile.md)
* [전화 번호 클레임 변환 정의](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
