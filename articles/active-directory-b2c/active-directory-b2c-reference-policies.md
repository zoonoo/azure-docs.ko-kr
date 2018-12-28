---
title: Azure Active Directory B2C의 사용자 흐름 | Microsoft Docs
description: Azure Active Directory B2C의 확장 가능한 정책 프레임워크 및 다양한 사용자 흐름 형식을 만드는 방법에 대한 항목입니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d4a93b04b8ad86a6a6d36a5bdaf3209b49e7a9dc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877085"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Azure Active Directory B2C: 사용자 흐름


Azure Active Directory(Azure AD) B2C의 확장할 수 있는 정책 프레임워크는 서비스의 핵심 장점입니다. 정책은 등록, 로그인 또는 프로필 편집과 같은 소비자 ID 환경을 완벽하게 설명합니다. 가장 일반적인 ID 작업을 설정하는 데 도움을 주기 위해 Azure AD B2C 포털은 **사용자 흐름**이라는 미리 정의되고 구성 가능한 정책을 포함합니다. 예를 들어 가입 사용자 흐름을 사용하면 다음 설정을 구성하여 동작을 제어할 수 있습니다.

* 소비자가 응용 프로그램에 등록하는 데 사용할 수 있는 계정 유형(Facebook과 같은 소셜 계정 또는 이메일 주소와 같은 로컬 계정)
* 등록 시 소비자로부터 수집할 특성(예: 이름, 우편 번호 및 신발 크기)
* Azure Multi-Factor Authentication 사용
* 모든 등록 페이지의 모양과 느낌
* 사용자 흐름 실행이 완료될 때 애플리케이션에서 받는 정보(토큰에서 클레임으로 매니페스트됨)

테넌트에 다른 형식의 여러 사용자 흐름을 만들고 필요에 따라 애플리케이션에서 사용할 수 있습니다. 애플리케이션에 사용자 흐름을 다시 사용할 수 있습니다. 이러한 유연성을 통해 개발자가 코드를 변경하지 않거나 최소로 변경하여 소비자 ID 환경을 정의하고 수정할 수 있습니다.

간단한 개발자 인터페이스를 통해 사용자 흐름을 사용할 수 있습니다. 애플리케이션은 표준 HTTP 인증 요청을 사용하여 사용자 흐름을 트리거하고(요청에 사용자 흐름 매개 변수 전달) 사용자 지정 토큰을 응답으로 받습니다. 예를 들어 가입 사용자 흐름을 호출하는 요청과 로그인 사용자 흐름을 호출하는 요청 간의 유일한 차이점은 "p" 쿼리 문자열 매개 변수에 사용되는 사용자 흐름 이름에 있습니다.

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

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>가입 또는 로그인 사용자 흐름 만들기

이 사용자 흐름은 단일 구성으로 가입 및 로그인 환경을 모두 처리합니다. 소비자는 컨텍스트에 따라 올바른 경로(등록 또는 로그인)를 진행합니다. 또한 성공적인 등록 또는 로그인 시 응용 프로그램에서 수신할 토큰 내용도 설명합니다.  **가입 또는 로그인** 사용자 흐름에 대한 코드 샘플은 [여기에서 사용할 수 있습니다](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  **가입** 사용자 흐름 및 **로그인** 사용자 흐름 대신 이 사용자 흐름을 사용하는 것이 좋습니다.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>가입 사용자 흐름 만들기

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>로그인 사용자 흐름 만들기

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>프로필 편집 사용자 흐름 만들기

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>암호 재설정 사용자 흐름 만들기

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>사용자 흐름 미리 보기

새 기능을 릴리스할 때 일부 기능은 기존 정책 또는 사용자 흐름에 사용할 수 없습니다.  이러한 사용자 흐름이 GA에 돌입하면 기존 버전을 같은 종류의 최신 버전으로 교체할 계획입니다.  기존 정책 또는 사용자 흐름은 변경되지 않으며, 새 기능을 사용하려면 새 사용자 흐름을 만들어야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>가입 또는 로그인 사용자 흐름을 암호 재설정 사용자 흐름과 연결하려면 어떻게 하나요?
**가입 또는 로그인** 사용자 흐름을 만드는 경우(로컬 계정 사용) 환경의 첫 번째 페이지에서 **암호 찾기** 링크가 표시됩니다. 이 링크를 클릭해도 암호 재설정 사용자 흐름이 자동으로 트리거되지는 않습니다. 

대신 **`AADB2C90118`** 오류 코드가 앱에 반환됩니다. 앱에서 특정 암호 재설정 사용자 흐름을 호출하여 이 오류 코드를 처리해야 합니다. 자세한 내용은 [사용자 흐름을 연결하는 방법을 보여주는 샘플](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)을 참조하세요.

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>가입 또는 로그인 사용자 흐름 또는 가입 사용자 흐름 및 로그인 사용자 흐름을 사용해야 하나요?
**가입** 사용자 흐름 및 **로그인** 사용자 흐름을 통해 **가입 또는 로그인** 사용자 흐름을 사용하는 것이 좋습니다.  

**등록 또는 로그인** 사용자 흐름에는 **로그인** 사용자 흐름보다 많은 기능이 있습니다. 또한 페이지 UI 사용자 지정 기능을 사용할 수 있으며 지역화를 더 효율적으로 지원합니다. 

사용자 흐름을 지역화할 필요가 없고, 브랜딩에 대한 약간의 사용자 지정 기능만 필요하고, 암호 재설정을 기본 제공하려는 경우에는 **로그인** 사용자 흐름을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [토큰, 세션 및 Single Sign-On 구성](active-directory-b2c-token-session-sso.md)
* [소비자를 등록하는 동안 전자 메일 확인 사용 안 함](active-directory-b2c-reference-disable-ev.md)

