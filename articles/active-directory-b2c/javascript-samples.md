---
title: Azure Active Directory B2C에서 사용하기 위한 JavaScript 샘플 | Microsoft Docs
description: Azure Active Directory B2C에서 JavaScript를 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ea1b4bd904a9936c401f6b0ea6ac9663c20bc92b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400039"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용하기 위한 JavaScript 샘플

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD(Azure Active Directory) B2C 애플리케이션에 사용자 고유의 JavaScript 클라이언트 쪽 코드를 추가할 수 있습니다. 이 문서에서는 스크립트 실행이 가능하도록 [사용자 지정 정책](active-directory-b2c-overview-custom.md) 또는 [사용자 흐름](user-flow-javascript-overview.md)을 변경하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

애플리케이션의 사용자 인터페이스 요소에 대해 [페이지 계약](page-contract.md)을 선택합니다. JavaScript를 사용하려면 사용자 흐름 또는 사용자 지정 정책의 모든 콘텐츠 정의에 대해 페이지 계약 버전을 정의해야 합니다.

## <a name="add-the-scriptexecution-element"></a>ScriptExecution 요소 추가

[RelyingParty](relyingparty.md) 요소에 **ScriptExecution** 요소를 추가하여 스크립트 실행을 가능하게 합니다.

1. 사용자 지정 정책 파일(예: *SignUpOrSignin.xml*)을 엽니다.
2. **RelyingParty**의 **UserJourneyBehaviors** 요소에 **ScriptExecution** 요소를 추가합니다.

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. 파일을 저장하고 업로드합니다.

## <a name="guidelines-for-using-javascript"></a>JavaScript 사용 지침

JavaScript를 사용하여 애플리케이션의 인터페이스를 사용자 지정하는 경우 다음 지침을 따르세요.

- `<a>` HTML 요소에 클릭 이벤트를 바인딩하지 않습니다.
- Azure AD B2C 코드 또는 주석에 대한 종속성을 사용하지 않습니다.
- Azure AD B2C HTML 요소의 순서나 계층 구조를 변경하지 않습니다. Azure AD B2C 정책을 사용하여 UI 요소의 순서를 제어합니다.
- 다음 사항을 고려하여 RESTful 서비스를 호출할 수 있습니다.
    - 클라이언트 쪽 HTTP 호출을 허용하도록 RESTful 서비스 CORS를 설정해야 할 수 있습니다.
    - RESTful 서비스가 안전하고 HTTPS 프로토콜만 사용하는지 확인합니다.
    - JavaScript를 사용하여 Azure AD B2C 엔드포인트를 직접 호출하지 않습니다.
- JavaScript를 포함하거나 외부 JavaScript 파일에 연결할 수 있습니다. 외부 JavaScript 파일을 사용하는 경우 상대 URL이 아닌 절대 URL을 사용해야 합니다.
- JavaScript 프레임워크:
    - Azure AD B2C에서는 특정 버전의 jQuery를 사용합니다. 다른 버전의 jQuery를 포함하지 않습니다. 동일한 페이지에 둘 이상의 버전을 사용하면 문제가 발생합니다.
    - RequireJS 사용은 지원되지 않습니다.
    - 대부분의 JavaScript 프레임워크는 Azure AD B2C에서 지원되지 않습니다.
- `window.SETTINGS`, `window.CONTENT` 개체를 호출하여 현재 UI 언어와 같은 Azure AD B2C 설정을 읽을 수 있습니다. 이러한 개체의 값을 변경하지 않습니다.
- Azure AD B2C 오류 메시지를 사용자 지정하려면 정책의 지역화를 사용합니다.
- 정책을 통해 수행할 수 있는 작업이 있다면 일반적으로 정책을 사용하는 것이 좋습니다.

## <a name="javascript-samples"></a>JavaScript 샘플

### <a name="show-or-hide-a-password"></a>암호 표시 또는 숨기기

고객이 가입에 성공하도록 지원하는 일반적인 방법은 고객이 암호로 입력한 내용을 확인할 수 있도록 하는 것입니다. 이 옵션은 사용자가 필요한 경우 쉽게 암호를 보고 수정할 수 있도록 하여 가입을 지원합니다. 암호 유형의 필드에는 **암호 표시** 레이블이 지정된 확인란이 있습니다.  사용자는 이 확인란을 통해 암호를 일반 텍스트로 볼 수 있습니다. 자체 어설션된 페이지의 가입 또는 로그인 템플릿에 다음 코드 조각을 포함합니다.

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>사용 약관 추가

**사용 약관** 확인란을 포함하려는 페이지에 다음 코드를 포함합니다. 이 확인란은 일반적으로 로컬 계정 가입 및 소셜 계정 가입 페이지에 필요합니다.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

코드에서 `terms-of-use-url`을 해당 사용 약관 링크로 바꿉니다. 디렉터리의 경우 **termsOfUse**라는 새 사용자 특성을 만든 다음, 사용자 흐름에 대한 사용자 특성으로 **termsOfUse**를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C의 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](active-directory-b2c-ui-customization-custom.md)에서 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대한 자세한 정보를 확인합니다.
