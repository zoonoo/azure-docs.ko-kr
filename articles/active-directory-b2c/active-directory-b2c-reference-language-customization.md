---
title: 언어 사용자 지정 사용 - Azure AD B2C | Microsoft Docs
description: 언어 환경을 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.openlocfilehash: 7c72c1d43d9a5fa541c72a8ba7a5ccedeafdaaff
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: 언어 사용자 지정 사용

>[!NOTE]
>이 기능은 공개 미리 보기 상태입니다.
>

언어 사용자 지정을 사용하면 고객 요구에 적합하도록 정책을 다른 언어로 맞출 수 있습니다.  Microsoft는 36개 언어([추가 정보](#additional-information) 참조)에 대한 번역을 제공하지만 사용자가 언어에 대한 고유한 번역을 제공할 수 있습니다.  단일 언어로만 환경이 제공되더라도 페이지에 있는 텍스트를 사용자 지정할 수 있습니다.  

## <a name="how-does-language-customization-work"></a>언어 사용자 지정이 작동하는 방식
언어 사용자 지정을 통해 사용자 경험이 제공되는 언어를 선택할 수 있습니다.  해당 기능을 사용하도록 설정하면 사용자 응용 프로그램에서 쿼리 문자열 매개 변수 ui_locales를 제공할 수 있습니다.  Azure AD B2C를 호출하면 페이지가 표시된 로케일로 번역됩니다.  이 구성 형식을 통해 사용자 경험의 언어를 완전히 제어하고 고객의 브라우저 언어 설정을 무시합니다. 또는 고객이 볼 수 있는 언어에 대한 제어 수준이 필요하지 않을 수 있습니다.  ui_locales 매개 변수를 제공하지 않으면 고객의 환경이 브라우저 설정으로 결정됩니다.  언어를 지원 언어로 추가하여 사용자 경험이 번역된 언어를 계속 제어할 수 있습니다.  고객의 브라우저가 지원하지 않으려는 언어를 표시하도록 설정된 경우 지원되는 문화에서 기본값으로 선택한 언어가 대신 표시됩니다.

1. **ui-locales 지정된 언어** - 언어 사용자 지정을 사용하도록 설정한 경우 사용자 경험은 여기에 지정된 언어로 번역됩니다.
2. **브라우저 요청 언어** - ui-locales가 지정되지 않았고 **지원되는 언어에 포함된 경우** 브라우저 요청 언어로 번역됩니다.
3. **정책 기본 언어** - 브라우저에서 언어를 지정하지 않거나 지원되지 않는 언어를 지정하면 정책 기본 언어로 번역됩니다.

>[!NOTE]
>사용자 지정 사용자 특성을 사용하는 경우 사용자 고유의 번역을 제공해야 합니다. 자세한 내용은 '[문자열 사용자 지정](#customize-your-strings)'을 참조하세요.
>

## <a name="support-uilocales-requested-languages"></a>ui_locales 요청 언어 지원 
언어 사용자 지정의 일반 공급 릴리스가 먼저 이 기능을 사용하도록 설정하기 전에 생성된 정책입니다.  이후에 만든 정책은 기본적으로 언어 사용자 지정을 사용해야 합니다.  정책에서 '언어 사용자 지정'을 사용하도록 설정하면 ui_locales 매개 변수를 추가하여 사용자 경험의 언어를 제어할 수 있습니다.
1. [다음 단계에 따라 Azure Portal의 B2C 기능 페이지로 이동합니다.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. 번역에 사용할 정책으로 이동합니다.
3. **언어 사용자 지정**을 클릭합니다.  
4. 위쪽에서 **언어 사용자 지정 사용**을 클릭합니다.
5. 대화 상자를 읽고 '예'를 클릭합니다.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>사용할 사용자 경험의 언어를 선택합니다. 
ui_locales 매개 변수가 제공되지 않을 때 사용자 경험의 언어 목록을 번역할 수 있도록 합니다.
1. 이전 지침에서 정책에 '언어 사용자 지정'이 설정되었는지 확인합니다.
2. **정책 편집** 페이지에서 **언어 사용자 지정**을 선택합니다.
3. 지원하려는 언어를 선택합니다.
4. 속성 창에서 **사용됨**을 '예'로 전환합니다.  
5. 화면 위쪽에서 **저장**을 클릭합니다.

>[!NOTE]
>ui_locales 매개 변수가 제공되지 않으면 페이지가 고객의 브라우저 언어(사용하는 경우에만)로 번역됩니다.
>

## <a name="customize-your-strings"></a>문자열 사용자 지정
'언어 사용자 지정'을 통해 사용자 경험에서 모든 문자열을 사용자 지정할 수 있습니다.
1. 이전 지침에서 정책에 '언어 사용자 지정'이 설정되었는지 확인합니다.
2. **정책 편집** 페이지에서 **언어 사용자 지정**을 선택합니다.
3. 사용자 지정하려는 언어를 선택합니다.
4. 편집하려는 페이지를 선택합니다.
5. **기본값 다운로드**(또는 이전에 이 언어를 편집한 경우 **재정의 다운로드**)를 클릭합니다. 

이러한 단계는 문자열 편집을 시작하는 데 사용할 수 있는 JSON 파일을 제공합니다.

### <a name="changing-any-string-on-the-page"></a>페이지에서 문자열 변경
1. 이전 지침에서 다운로드한 JSON 파일을 JSON 편집기에서 엽니다.
2. 변경할 요소를 찾습니다.  찾으려는 문자열의 `StringId`를 찾거나 변경할 `Value`를 찾을 수 있습니다.
3. `Value` 특성을 표시하기를 원하는 항목으로 업데이트합니다.
4. 변경하려는 모든 문자열의 경우 `Override`를 **True**로 전환합니다.
5. 파일을 저장하고 변경 내용을 업로드합니다.(JSON 파일을 다운로드하는 곳과 동일한 위치에서 업로드 컨트롤을 찾을 수 있습니다.) 

>[!IMPORTANT]
>문자열을 재정의해야 하는 경우 `Override` 값을 `true`로 설정해야 합니다.  값이 변경되지 않으면 항목이 무시됩니다. 
>

### <a name="changing-extension-attributes"></a>확장 특성 변경
사용자 지정 사용자 특성의 문자열을 변경하려는 경우 또는 JSON에 문자열을 추가하려는 경우 다음 형식을 사용합니다.
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

`<ExtensionAttribute>`를 사용자 지정 사용자 특성 이름으로 바꿉니다.  

`<ExtensionAttributeValue>`를 표시할 새 문자열로 바꿉니다.

### <a name="using-localizedcollections"></a>LocalizedCollections 사용
응답에 대한 값 목록 집합을 지정하려면 `LocalizedCollections`를 만들어야 합니다.  `LocalizedCollections`는 `Name` 및 `Value` 쌍의 배열입니다.  `Name`은 표시되는 항목이고 `Value`는 클레임에 반환된 항목입니다.  `LocalizedCollections`를 추가하기 위해 다음 형식을 포함합니다.

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId`는 이 `LocalizedCollections`가 응답인 사용자 특성입니다.
* `Name`은 사용자에게 표시된 값입니다.
* `Value`는 이 옵션이 선택될 때 클레임에 반환된 항목입니다.

### <a name="upload-your-changes"></a>변경 내용 업로드
1. JSON 파일에 대한 변경을 완료했으면 B2C 테넌트로 다시 이동합니다.
2. **정책 편집** 페이지에서 **언어 사용자 지정**을 선택합니다.
3. 번역을 제공하려는 언어를 선택합니다.
4. 번역을 제공하려는 페이지를 선택합니다.
5. 폴더 아이콘을 클릭하고 업로드할 JSON 파일을 선택합니다.
6. 변경 내용이 자동으로 정책에 저장됩니다.

## <a name="using-page-ui-customization-with-language-customization"></a>언어 사용자 지정과 페이지 UI 사용자 지정 사용

HTML 콘텐츠를 지역화하는 방법은 두 가지가 있습니다.  ['언어 사용자 지정'](active-directory-b2c-reference-language-customization.md)을 설정하는 방법입니다.  이 기능을 사용하도록 설정하면 Azure AD B2C가 Open ID Connect 매개 변수 `ui-locales`를 끝점으로 전달할 수 있습니다.  콘텐츠 서버는 이 매개 변수를 사용하여 언어 관련 사용자 지정된 HTML 페이지를 제공할 수 있습니다.

또는 사용 중인 로캘을 기반으로 하는 다른 위치에서 콘텐츠를 끌어올 수 있습니다.  CORS 사용 끝점에서 특정 언어에 대한 콘텐츠를 호스트하도록 폴더 구조를 설정할 수 있습니다. 또한 와일드 카드 값 `{Culture:RFC5646}`을 설정한 경우 적합한 구조를 호출합니다.  예를 들어, 내 사용자 지정 페이지 URI로 설정한 경우:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
내 페이지를 `fr`에 로드할 수 있고 html 및 css 콘텐츠를 끌어올 때 다음에서 끌어옵니다.
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>사용자 지정 로캘

또한 Microsoft가 현재 번역을 제공하지 않는 언어를 추가할 수 있습니다.  정책에서 모든 문자열에 대한 번역을 제공해야 합니다.

1. **정책 편집** 페이지에서 **언어 사용자 지정**을 선택합니다.
2. 페이지의 위쪽에서 **사용자 지정 언어 추가**를 선택합니다.
3. 열린 컨텍스트 창에서 유효한 로캘 코드를 입력하여 번역을 제공하는 언어를 식별합니다.
4. 각 페이지에서 영어에 대한 재정의의 집합을 다운로드하고 번역에 사용할 수 있습니다.
5. JSON 파일을 완료하면 각 페이지에 업로드할 수 있습니다.
6. **사용**을 선택하면 정책이 사용자에게 이 언어를 표시합니다.
7. 언어를 사용하도록 설정하면 저장해야 합니다.

## <a name="additional-information"></a>추가 정보

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>'언어 사용자 지정'을 사용하도록 설정하면 페이지 UI 사용자 지정 레이블이 첫 번째 재정의 집합으로 유지됩니다.
'언어 사용자 지정'을 사용하도록 설정하면 페이지 UI 사용자 지정을 사용한 레이블에 대한 이전 편집이 영어(en)용 JSON 파일에서 유지됩니다.  '언어 사용자 지정'에서 언어 리소스를 업로드하여 레이블 및 기타 문자열을 계속해서 변경할 수 있습니다.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft는 사용자가 사용할 수 있는 가장 최신의 번역을 제공하기 위해 최선을 다하고 있습니다.
지속적으로 번역을 개선하고 사용자에 맞게 유지할 예정입니다.  버그 및 글로벌 용어의 변화를 파악하고 사용자 경험에서 원활하게 작동하도록 업데이트할 예정입니다.
### <a name="support-for-right-to-left-languages"></a>오른쪽에서 왼쪽 언어 지원
현재 오른쪽에서 왼쪽 언어에 대한 지원을 제공하지 않습니다. 이 기능이 필요하면 [Azure 피드백](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)에서 이 기능에 대해 투표하세요.
### <a name="social-identity-provider-translations"></a>소셜 ID 공급자 변환
소셜 로그인에 ui_locales OIDC 매개 변수를 제공하지만 Facebook 및 Google을 포함한 일부 소셜 ID 공급자에는 이 매개 변수가 적용되지 않습니다. 
### <a name="browser-behavior"></a>브라우저 동작
Chrome 및 Firefox는 모두 설정된 언어를 요청하며 지원되는 언어인 경우 기본값보다 먼저 표시됩니다.  에지에서는 현재 언어를 요청하지 않으며 기본 언어로 바로 이동합니다.

### <a name="what-languages-are-supported"></a>어떤 언어가 지원되나요?

| 언어              | 언어 코드 |
|-----------------------|---------------|
| 벵골어                | bn            |
| 체코어                 | cs            |
| 덴마크어                | da            |
| 독일어                | de            |
| 그리스어                 | el            |
| 영어               | en            |
| 스페인어               | es            |
| 핀란드어               | fi            |
| 프랑스어                | fr            |
| 구자라트어              | gu            |
| 힌디어                 | hi            |
| 크로아티아어              | hr            |
| 헝가리어             | hu            |
| 이탈리아어               | it            |
| 일본어              | ja            |
| 카나다어               | kn            |
| 한국어                | ko            |
| 말라얄람어             | ml            |
| 마라티어               | mr            |
| 말레이어                 | ms            |
| 노르웨이어 복말      | nb            |
| 네덜란드어                 | nl            |
| 펀잡어               | pa            |
| 폴란드어                | pl            |
| 포르투갈어 - 브라질   | pt-br         |
| 포르투갈어 - 포르투갈 | pt-pt         |
| 루마니아어              | ro            |
| 러시아어               | ru            |
| 슬로바키아어                | sk            |
| 스웨덴어               | sv            |
| 타밀어                 | ta            |
| 텔루구어                | te            |
| 태국어                  | th            |
| 터키어               | tr            |
| 중국어 - 간체  | zh-hans       |
| 중국어 - 번체 | zh-hant       |
