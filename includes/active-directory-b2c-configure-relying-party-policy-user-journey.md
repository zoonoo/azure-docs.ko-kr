---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674253"
---
## <a name="configure-the-relying-party-policy"></a>신뢰 당사자 정책 구성

[SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)와 같은 신뢰 당사자 정책은 Azure AD B2C 실행 될 사용자 경험을 지정 합니다. [신뢰](../articles/active-directory-b2c/relyingparty.md)당사자 내에서 **defaultuserjourney** 요소를 찾습니다. Id 공급자를 추가한 사용자 경험 ID와 일치 하도록  **ReferenceId** 을 업데이트 합니다. 

다음 예에서는 사용자 경험에 대해 `CustomSignUpOrSignIn` **ReferenceId** 가로 설정 됩니다 `CustomSignUpOrSignIn` .

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>사용자 지정 정책 업로드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택 하 고 변경한 두 정책 파일을 다음 순서로 업로드 합니다. 예 `TrustFrameworkExtensions.xml` 를 들어와 같은 신뢰 당사자 정책 `SignUpSignIn.xml` 입니다.

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책을 선택 합니다 (예:). `B2C_1A_signup_signin`
1. **응용 프로그램** 의 경우 이전에 등록 한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.

로그인 프로세스가 성공 하면 브라우저가로 리디렉션되 며 `https://jwt.ms` ,이는 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

