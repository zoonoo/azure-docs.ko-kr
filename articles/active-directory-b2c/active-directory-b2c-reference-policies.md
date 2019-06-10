---
title: Azure Active Directory B2C의 사용자 흐름 | Microsoft Docs
description: Azure Active Directory B2C의 확장 가능한 정책 프레임워크 및 다양한 사용자 흐름을 만드는 방법을 자세히 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 54677dc0771f65b7636b4d1cac77f53f9c04a09d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508928"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 흐름

Azure Active Directory(Azure AD) B2C의 확장할 수 있는 정책 프레임워크는 서비스의 핵심 장점입니다. 정책은 가입, 로그인 또는 프로필 편집과 같은 ID 환경을 완벽하게 설명합니다. 가장 일반적인 ID 작업을 설정하는 데 도움을 주기 위해 Azure AD B2C 포털은 **사용자 흐름**이라는 미리 정의되고 구성 가능한 정책을 포함합니다. 

## <a name="what-are-user-flows"></a>사용자 흐름이란?

사용자 흐름을 사용하면 다음 설정을 구성하여 애플리케이션의 동작을 제어할 수 있습니다.

- Facebook 등의 소셜 계정 또는 로컬 계정과 같은 로그인에 사용되는 계정 유형
- 소비자로부터 수집할 특성(예: 이름, 우편 번호 및 신발 크기)
- Azure Multi-Factor Authentication
- 사용자 인터페이스의 사용자 지정
- 애플리케이션이 토큰의 클레임으로 받는 정보 

테넌트에 여러 유형의 많은 사용자 흐름을 만들고 필요에 따라 애플리케이션에서 사용할 수 있습니다. 애플리케이션에 사용자 흐름을 다시 사용할 수 있습니다. 이러한 유연성 덕분에 코드 변경 없이 또는 최소한의 변경으로 ID 환경을 정의하고 수정할 수 있습니다. 애플리케이션은 사용자 흐름 매개 변수를 포함하는 표준 HTTP 인증 요청을 사용하여 사용자 흐름을 트리거합니다. 사용자 지정 [토큰](active-directory-b2c-reference-tokens.md)은 응답으로 수신됩니다. 

다음 예제에서는 사용할 사용자 흐름을 지정하는 “p” 쿼리 문자열 매개 변수를 보여 줍니다.

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>사용자 흐름 버전

Azure Portal에서는 새 [버전의 사용자 흐름](user-flow-versions.md)이 항상 추가되고 있습니다. Azure AD B2C를 시작할 때 테스트된 사용자 흐름을 사용하도록 권장됩니다. 새 사용자 흐름을 만들 때 **권장** 탭에서 필요한 사용자 흐름을 선택합니다.

다음 사용자 흐름이 현재 권장됩니다.

- **가입 및 로그인** - 단일 구성으로 가입 및 로그인 환경을 둘 다 처리합니다. 사용자는 컨텍스트에 따라 올바른 경로로 안내됩니다. **가입** 사용자 흐름 또는 **로그인** 사용자 흐름 대신 이 사용자 흐름을 사용하는 것이 좋습니다.
- **프로필 편집** - 사용자가 해당 프로필 정보를 편집할 수 있습니다.
- **암호 재설정** - 사용자가 암호를 재설정할 수 있는지 여부 및 방법을 구성할 수 있습니다.

## <a name="linking-user-flows"></a>사용자 흐름 연결

로컬 계정을 사용하는 **가입 또는 로그인** 사용자 흐름에서는 환경의 첫 페이지에 **암호 찾기** 링크가 포함됩니다. 이 링크를 클릭해도 암호 재설정 사용자 흐름이 자동으로 트리거되지는 않습니다. 

대신 `AADB2C90118` 오류 코드가 애플리케이션에 반환됩니다. 애플리케이션은 암호를 재설정하는 특정 사용자 흐름을 실행하여 이 오류 코드를 처리해야 합니다. 예제를 보려면 사용자 흐름 연결을 보여 주는 [단순 ASP.NET 샘플](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)을 살펴보세요.

## <a name="email-address-storage"></a>메일 주소 스토리지

사용자 흐름의 일부로 메일 주소가 필요할 수 있습니다. 사용자가 소셜 ID 공급자로 인증하는 경우 메일 주소가 **otherMails** 속성에 저장됩니다. 로컬 계정이 사용자 이름을 기반으로 하는 경우 메일 주소가 강력한 인증 정보 속성에 저장됩니다. 로컬 계정이 메일 주소를 기반으로 하는 경우 메일 주소가 **signInNames** 속성에 저장됩니다.
 
이러한 경우에는 메일 주소가 확인되지 않습니다. 테넌트 관리자는 로컬 계정에 대한 기본 정책에서 메일 확인을 사용하지 않도록 설정할 수 있습니다. 메일 주소 확인이 사용되더라도 소셜 ID 공급자가 제공한 것이며 변경되지 않은 경우에는 주소가 확인되지 않습니다.
 
**otherMails** 및 **signInNames** 속성만 Active Directory Graph API를 통해 노출됩니다. 강력한 인증 정보 속성의 메일 주소를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

권장 사용자 흐름을 만들려면 [자습서: 사용자 흐름 만들기](tutorial-create-user-flows.md)의 지침을 따릅니다.


