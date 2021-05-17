---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98951535"
---
## <a name="configure-the-relying-party-policy"></a>신뢰 당사자 정책 구성

신뢰 당사자 정책(예: [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml))은 Azure AD B2C에서 실행할 사용자 경험을 지정합니다. [신뢰 당사자](../articles/active-directory-b2c/relyingparty.md) 내에서 **DefaultUserJourney** 요소를 찾습니다. ID 공급자를 추가한 사용자 경험 ID와 일치하도록  **ReferenceId** 를 업데이트합니다. 

다음 예제에서는 `CustomSignUpOrSignIn` 사용자 경험에 대해 **ReferenceId** 가 `CustomSignUpOrSignIn`으로 설정됩니다.

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
1. **사용자 지정 정책 업로드** 를 선택한 다음, 변경한 두 정책 파일을 확장 정책(예: `TrustFrameworkExtensions.xml`), 신뢰 당사자 정책(예: `SignUpSignIn.xml`) 순으로 업로드합니다.



