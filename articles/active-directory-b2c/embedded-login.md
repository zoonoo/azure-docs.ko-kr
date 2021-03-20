---
title: 사용자 지정 정책을 사용 하 여 앱에 Azure Active Directory B2C 사용자 인터페이스 포함
titleSuffix: Azure AD B2C
description: 사용자 지정 정책을 사용 하 여 앱에 Azure Active Directory B2C 사용자 인터페이스를 포함 하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1255c4962de1fce19efa9c0b0e1d28fc348463ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580150"
---
# <a name="embedded-sign-in-experience"></a>포함 된 로그인 환경

더 간단한 로그인 환경을 위해 사용자를 별도의 로그인 페이지로 리디렉션하거나 팝업 창을 생성 하지 않도록 방지할 수 있습니다. 인라인 프레임 요소를 사용 하 여 `<iframe>` Azure AD B2C 로그인 사용자 인터페이스를 웹 응용 프로그램에 직접 포함할 수 있습니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>웹 응용 프로그램 embedded 로그인

인라인 프레임 요소는 `<iframe>` HTML5 웹 페이지에 문서를 포함 하는 데 사용 됩니다. 다음 예제에 표시 된 것 처럼 iframe 요소를 사용 하 여 Azure AD B2C 로그인 사용자 인터페이스를 웹 응용 프로그램에 직접 포함할 수 있습니다.

![가리키기 DIV 환경으로 로그인](media/embedded-login/login-hovering.png)

Iframe을 사용 하는 경우 다음 사항을 고려 합니다.

- 포함 된 로그인은 로컬 계정만 지원 합니다. 대부분의 소셜 id 공급자 (예: Google 및 Facebook)는 로그인 페이지가 인라인 프레임에서 렌더링 되지 않도록 차단 합니다.
- Iframe 내의 Azure AD B2C 세션 쿠키는 타사 쿠키로 간주 되기 때문에 특정 브라우저 (예: Safari 또는 incognito 모드의 Chrome)는 이러한 쿠키를 차단 하거나 지워 원치 않는 사용자 환경을 생성 합니다. 이 문제를 방지 하려면 응용 프로그램 도메인 이름과 Azure AD B2C 도메인의 *원본이 동일한* 지 확인 합니다. 동일한 원본을 사용 하려면 Azure AD B2C 테 넌 트에 대해 [사용자 지정 도메인을 사용 하도록 설정한](custom-domain.md) 다음 동일한 원본으로 웹 앱을 구성 합니다. 예를 들어에서 호스팅된 응용 프로그램은 https://app.contoso.com 에서 실행 되는 Azure AD B2C와 동일한 원본을 갖습니다 https://login.contoso.com .

## <a name="prerequisites"></a>필수 구성 요소

* [Active Directory B2C에서 사용자 지정 정책을 사용하여 시작하기](custom-policy-get-started.md)에 있는 단계를 완료합니다.
* 정책에 대해 [사용자 지정 도메인을 사용 하도록 설정](custom-domain.md) 합니다.

## <a name="configure-your-policy"></a>정책 구성

Azure AD B2C 사용자 인터페이스를 iframe에 포함할 수 있도록 하려면 `Content-Security-Policy` `X-Frame-Options` Azure AD B2C HTTP 응답 헤더에 콘텐츠 보안 정책 및 프레임 옵션을 포함 해야 합니다. 이러한 헤더를 사용 하면 Azure AD B2C 사용자 인터페이스가 응용 프로그램 도메인 이름으로 실행 될 수 있습니다.

[RelyingParty](relyingparty.md) 요소 내에 **JourneyFraming** 요소를 추가 합니다.  **UserJourneyBehaviors** 요소는 **defaultuserjourney** 을 따라야 합니다. **UserJourneyBehaviors** 요소는 다음 예제와 같이 표시 됩니다.

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

**Sources** 특성에는 웹 응용 프로그램의 URI가 포함 됩니다. Uri 사이에 공백을 추가 합니다. 각 URI는 다음 요구 사항을 충족 해야 합니다.

- 응용 프로그램에서 URI를 신뢰 하 고 소유 해야 합니다.
- URI는 https 체계를 사용 해야 합니다.  
- 웹 앱의 전체 URI를 지정 해야 합니다. 와일드카드는 지원되지 않습니다.

또한 응용 프로그램 페이지에서 각각 콘텐츠-보안 정책 및 X 프레임 옵션 헤더를 설정 하 여 자체 도메인 이름이 iframe에 포함 되지 않도록 차단 하는 것이 좋습니다. 이렇게 하면 iframe의 중첩 된 포함과 관련 된 이전 브라우저의 보안 문제를 완화할 수 있습니다.

## <a name="adjust-policy-user-interface"></a>정책 사용자 인터페이스 조정

[사용자 인터페이스 사용자 지정](customize-ui.md)을 Azure AD B2C 사용 하면 사용자에 게 제공 되는 HTML 및 CSS 콘텐츠를 거의 완전히 제어할 수 있습니다. 콘텐츠 정의를 사용 하 여 HTML 페이지를 사용자 지정 하는 단계를 수행 합니다. Azure AD B2C 사용자 인터페이스를 iframe 크기에 맞추려면 배경 및 추가 공간 없이 완전 한 HTML 페이지를 제공 합니다.  

다음 CSS 코드는 Azure AD B2C HTML 요소를 숨기고 iframe을 채우도록 패널의 크기를 조정 합니다.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

경우에 따라 현재 표시 되는 Azure AD B2C 페이지를 응용 프로그램에 알릴 수 있습니다. 예를 들어 사용자가 등록 옵션을 선택 하면 소셜 계정으로 로그인 하거나 iframe 크기를 조정 하기 위한 링크를 숨겨 응용 프로그램이 응답 하도록 할 수 있습니다.

응용 프로그램에 현재 Azure AD B2C 페이지를 알리려면 [JavaScript에 대 한 정책을 사용 하도록 설정한](./javascript-and-page-layout.md)다음 HTML5 post 메시지를 사용 합니다. 다음 JavaScript 코드는를 사용 하 여 게시 메시지를 앱에 보냅니다 `signUp` .

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>웹 응용 프로그램 구성

사용자가 로그인 단추를 선택 하면 [웹 앱](code-samples.md#web-apps-and-apis) 은 사용자에 게 로그인 환경을 Azure AD B2C 하는 권한 부여 요청을 생성 합니다. 로그인이 완료 되 면 Azure AD B2C는 응용 프로그램 내의 구성 된 리디렉션 URI에 ID 토큰 또는 권한 부여 코드를 반환 합니다.

포함 된 로그인을 지원 하기 위해 iframe **src** 속성은 `/account/SignUpSignIn` 권한 부여 요청을 생성 하 고 사용자를 Azure AD B2C 정책으로 리디렉션하는 등의 로그인 컨트롤러를 가리킵니다.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

응용 프로그램에서 ID 토큰을 받고 유효성을 검사 한 후에는 권한 부여 흐름이 완료 되 고 응용 프로그램에서 사용자를 인식 하 고 신뢰 합니다. 권한 부여 흐름이 iframe 내에서 발생 하기 때문에 기본 페이지를 다시 로드 해야 합니다. 페이지가 다시 로드 되 면 로그인 단추가 "로그 아웃"으로 바뀌고 사용자 이름은 UI에 표시 됩니다.  

다음은 로그인 리디렉션 URI가 기본 페이지를 새로 고칠 수 있는 방법을 보여 주는 예제입니다.

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>소셜 계정으로 로그인을 웹 앱에 추가

소셜 id 공급자는 인라인 프레임에서의 로그인 페이지가 렌더링 되지 않도록 차단 합니다. 소셜 계정에 대해 별도의 정책을 사용할 수도 있고, 로컬 및 소셜 계정으로 로그인 및 등록에 모두 단일 정책을 사용할 수도 있습니다. 그런 다음 `domain_hint` 쿼리 문자열 매개 변수를 사용할 수 있습니다. 도메인 힌트 매개 변수는 사용자가 소셜 id 공급자의 로그인 페이지로 직접 이동 합니다.

응용 프로그램에서 소셜 계정으로 로그인 단추를 추가 합니다. 사용자가 소셜 계정 단추 중 하나를 클릭 하면 컨트롤에서 정책 이름을 변경 하거나 도메인 힌트 매개 변수를 설정 해야 합니다.

<!-- TBD: add a diagram -->

리디렉션 URI는 iframe에서 사용 되는 것과 동일한 리디렉션 URI 일 수 있습니다. 페이지 다시 로드를 건너뛸 수 있습니다.

## <a name="configure-a-single-page-application"></a>단일 페이지 응용 프로그램 구성

단일 페이지 응용 프로그램의 경우 iframe에 로드 되는 두 번째 "로그인" HTML 페이지도 필요 합니다. 이 로그인 페이지는 인증 코드를 생성 하는 인증 라이브러리 코드를 호스팅하고 토큰을 반환 합니다.

단일 페이지 응용 프로그램에 액세스 토큰이 필요한 경우 JavaScript 코드를 사용 하 여이 토큰이 포함 된 iframe 및 개체에서 액세스 토큰을 가져옵니다.

> [!NOTE]
> 현재 iframe에서 MSAL 2.0을 실행 하는 것은 지원 되지 않습니다.

다음 코드는 기본 페이지에서 실행 되며 iframe의 JavaScript 코드를 호출 하는 예제입니다.

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조 하세요.

- [사용자 인터페이스 사용자 지정](customize-ui.md)
- [RelyingParty](relyingparty.md) 요소 참조
- [JavaScript에 대 한 정책 사용](./javascript-and-page-layout.md)
- [코드 샘플](code-samples.md)