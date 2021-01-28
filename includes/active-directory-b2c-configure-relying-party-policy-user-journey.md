---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98951535"
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



