---
title: "Azure Active Directory B2C: 언어 사용자 지정 사용 | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: sama
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.contentlocale: ko-kr
ms.lasthandoff: 08/08/2017

---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Azure Active Directory B2C: 언어 사용자 지정 사용

>[!NOTE] 
>이 기능은 공개 미리 보기 상태입니다.  이 기능을 사용할 때 테스트 테넌트를 사용하는 것이 좋습니다.  미리 보기에서 일반 공급 릴리스로 주요 변경 사항은 계획하지 않지만 기능 개선을 위해 이러한 변경 작업을 수행할 권리를 보유합니다.  기능을 사용해 볼 기회가 있는 경우 경험에 대한 피드백을 제공하고 더 나은 결과를 얻을 수 있는 방법을 제공해주세요.  Azure Portal을 통해 오른쪽 상단의 웃는 얼굴 도구로 피드백을 제공할 수 있습니다.   미리 보기 단계에서 이 기능을 라이브로 전환하여 사용해야 하는 비즈니스 요구 사항이 있는 경우 시나리오를 알려 주시면 적절한 안내와 지원을 제공할 수 있습니다.  [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com)으로 문의할 수 있습니다.

언어 사용자 지정을 통해 고객 요구에 맞게 사용자 경험을 다른 언어로 변경할 수 있습니다.  36개 언어에 대한 번역이 제공됩니다([추가 정보](#additional-information) 참조).  단일 언어로만 환경이 제공되더라도 사용자 요구에 맞게 페이지에 있는 텍스트를 사용자 지정할 수 있습니다.  

## <a name="how-does-language-customization-work"></a>언어 사용자 지정이 작동하는 방식
언어 사용자 지정을 통해 사용자 경험이 제공되는 언어를 선택할 수 있습니다.  해당 기능을 사용하도록 설정하면 사용자 응용 프로그램에서 쿼리 문자열 매개 변수 ui_locales를 제공할 수 있습니다.  Azure AD B2C를 호출하면 페이지가 표시된 로케일로 번역됩니다.  구성 유형을 사용하면 사용자 경험의 언어를 완전히 제어할 수 있고 고객의 브라우저 언어 설정이 무시됩니다. 또는 고객이 볼 수 있는 언어에 대한 제어 수준이 필요하지 않을 수 있습니다.  ui_locales 매개 변수를 제공하지 않으면 고객의 환경이 브라우저 설정으로 결정됩니다.  언어를 지원 언어로 추가하여 사용자 경험이 번역된 언어를 계속 제어할 수 있습니다.  고객의 브라우저가 지원하지 않으려는 언어를 표시하도록 설정된 경우 지원되는 문화에서 기본값으로 선택한 언어가 대신 표시됩니다.

1. **ui-locales 지정된 언어** - 언어 사용자 지정을 사용하도록 설정한 경우 사용자 경험은 여기에 지정된 언어로 번역됩니다.
2. **브라우저 요청 언어** - ui-locales가 지정되지 않았고 **지원되는 언어에 포함된 경우** 브라우저 요청 언어로 번역됩니다.
3. **정책 기본 언어** - 브라우저에서 언어를 지정하지 않거나 지원되지 않는 언어를 지정하면 정책 기본 언어로 번역됩니다.

>[!NOTE]
>사용자 지정 사용자 특성을 사용하는 경우 사용자 고유의 번역을 제공해야 합니다. 자세한 내용은 '[문자열 사용자 지정](#customize-your-strings)'을 참조하세요.
>

## <a name="support-uilocales-requested-languages"></a>ui_locales 요청 언어 지원 
정책에서 '언어 사용자 지정'을 사용하도록 설정하면 ui_locales 매개 변수를 추가하여 사용자 경험의 언어를 제어할 수 있습니다.
1. [다음 단계에 따라 Azure Portal의 B2C 기능 블레이드로 이동합니다.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. 번역에 사용할 정책으로 이동합니다.
3. **언어 사용자 지정**을 클릭합니다.
4. 경고를 주의깊게 읽습니다.  사용하도록 설정된 후에는 '언어 사용자 지정'을 해제할 수 없습니다.
5. 기능을 설정하고 **확인**을 클릭합니다.
6. **정책 편집** 블레이드의 왼쪽 맨 위 모퉁이에서 정책을 저장합니다.

## <a name="select-which-languages-your-user-journey-supports"></a>사용자 경험에서 지원하는 언어 선택 
ui_locales 매개 변수가 제공되지 않을 때 번역할 사용자 경험에 대해 허용되는 언어 목록을 만듭니다.
1. 이전 지침에서 정책에 '언어 사용자 지정'이 설정되었는지 확인합니다.
2. **정책 편집** 블레이드에서 **언어 사용자 지정**을 선택합니다.
3. **지원되는 언어** 블레이드로 이동합니다.  여기에서 **언어 추가**를 선택할 수 있습니다.
4. 지원하려는 모든 언어를 선택합니다.  

>[!NOTE]
>ui_locales 매개 변수가 제공되지 않으면 페이지가 고객의 브라우저 언어(목록에 있는 경우)로만 번역됩니다.
>

5. 아래쪽에서 **확인**을 클릭합니다.
6. **언어 사용자 지정** 블레이드를 닫고 정책을 **저장**합니다.

## <a name="customize-your-strings"></a>문자열 사용자 지정
'언어 사용자 지정'을 통해 사용자 경험에서 모든 문자열을 사용자 지정할 수 있습니다.
1. 이전 지침에서 정책에 '언어 사용자 지정'이 설정되었는지 확인합니다.
2. **정책 편집** 블레이드에서 **언어 사용자 지정**을 선택합니다.
3. 왼쪽 탐색 메뉴에서 **콘텐츠 다운로드**를 선택합니다.
4. 편집하려는 페이지를 선택합니다.
5. 드롭다운에서 편집할 언어를 선택합니다.
6. **다운로드**를 클릭합니다. 

이러한 단계는 문자열 편집을 시작하는 데 사용할 수 있는 JSON 파일을 제공합니다.

### <a name="changing-any-string-on-the-page"></a>페이지에서 문자열 변경
1. 이전 지침에서 다운로드한 JSON 파일을 JSON 편집기에서 엽니다.
2. 변경할 요소를 찾습니다.  찾으려는 문자열의 `StringId`를 찾거나 변경할 `Value`를 찾을 수 있습니다.
3. `Value` 특성을 표시하기를 원하는 항목으로 업데이트합니다.
4. 파일을 저장하고 변경 내용을 업로드합니다.

### <a name="changing-extension-attributes"></a>확장 특성 변경
사용자 지정 사용자 특성의 문자열을 변경하려는 경우 또는 JSON에 문자열을 추가하려는 경우 다음 형식을 사용합니다.
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>문자열을 재정의해야 하는 경우 `Override` 값을 `true`로 설정해야 합니다.  값이 변경되지 않으면 항목이 무시됩니다. 
>

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
      "Override": false,
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
>[!IMPORTANT]
>문자열을 재정의해야 하는 경우 `Override` 값을 `true`로 설정해야 합니다.  값이 변경되지 않으면 항목이 무시됩니다. 
>

* `ElementId`는 이 `LocalizedCollections`가 응답인 사용자 특성입니다.
* `Name`은 사용자에게 표시된 값입니다.
* `Value`는 이 옵션이 선택될 때 클레임에 반환된 항목입니다.

### <a name="upload-your-changes"></a>변경 내용 업로드
1. JSON 파일에 대한 변경을 완료했으면 B2C 테넌트로 다시 이동합니다.
2. **정책 편집** 블레이드에서 **언어 사용자 지정**을 선택합니다.
3. 왼쪽 탐색 메뉴에서 **콘텐츠 업로드**를 선택합니다.
4. 변경 내용을 업로드하려는 페이지를 선택합니다.
5. 이전에 JSON을 제공한 언어를 업로드하려면 이전 항목을 삭제해야 합니다.  해당 언어 오른쪽에서 `...`를 클릭하고 **삭제**를 선택하여 삭제할 수 있습니다.
6. 왼쪽 위에서 **추가**를 클릭합니다.
7. JSON 파일의 언어를 선택합니다.
8. 오른쪽에서 폴더 단추를 클릭하고 JSON 파일을 찾아봅니다.
9. 블레이드 맨 아래에 있는 **업로드** 단추를 클릭합니다.
10. **정책 편집** 블레이드로 다시 이동하여 **저장**을 클릭합니다.

## <a name="additional-information"></a>추가 정보
### <a name="recommendations-for-language-customization"></a>'언어 사용자 지정'에 대한 권장 사항
명시적으로 바꾸려는 문자열에 대한 언어 리소스에 항목을 배치하는 것이 좋습니다.  모든 JSON 변환에서 컴파일된 파일에 크기 제한을 적용합니다.  파일이 너무 큰 경우 사용자 경험의 성능에 영향을 줍니다.
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>'언어 사용자 지정'을 사용하면 페이지 UI 사용자 지정 레이블이 제거됩니다.
'언어 사용자 지정'을 사용하도록 설정하면 사용자 지정 사용자 특성을 제외하고, 페이지 UI 사용자 지정을 사용한 레이블에 대한 이전 편집이 제거됩니다.  이 변경은 문자열을 편집할 수 있는 곳에서 충돌을 피하기 위해 수행됩니다.  '언어 사용자 지정'에서 언어 리소스를 업로드하여 레이블 및 기타 문자열을 계속해서 변경할 수 있습니다.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>Microsoft는 사용자가 사용할 수 있는 가장 최신의 번역을 제공하기 위해 최선을 다하고 있습니다.
지속적으로 번역을 개선하고 사용자에 맞게 유지할 예정입니다.  버그 및 글로벌 용어의 변화를 파악하고 사용자 경험에서 원활하게 작동하도록 업데이트할 예정입니다.
### <a name="support-for-right-to-left-languages"></a>오른쪽에서 왼쪽 언어 지원
오른쪽에서 왼쪽 언어에 대한 지원은 없습니다. 이 기능이 필요하면 [Azure 피드백](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)에서 이 기능에 대해 투표하세요.
### <a name="social-identity-provider-translations"></a>소셜 ID 공급자 변환
소셜 로그인에 ui_locales OIDC 매개 변수를 제공하지만 Facebook 및 Google을 포함한 일부 소셜 ID 공급자에는 이 매개 변수가 적용되지 않습니다. 
### <a name="browser-behavior"></a>브라우저 동작
Chrome 및 Firefox는 모두 설정된 언어를 요청하며 지원되는 언어인 경우 기본값보다 먼저 표시됩니다.  에지에서는 현재 언어를 요청하지 않으며 기본 언어로 바로 이동합니다.
### <a name="known-issues"></a>알려진 문제
* 프로필 편집 정책에서 MFA 페이지에 대한 언어 리소스 업로드는 현재 제공되지 않습니다.
* 응답 유형에 필요한 경우 값에 대한 `LocalizedCollections`는 생성되지 않습니다.
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>지원되지 않는 언어가 필요하다면 어떻게 하나요?
JSON 리소스를 '사용자 지정 언어'로 업로드할 수 있도록 이 기능의 확장을 제공할 계획입니다.  이 기능을 사용하면 언어에 대한 이름과 언어 코드를 지정하고 해당 언어에 대한 *모든* 번역을 제공할 수 있습니다.  이 기능이 필요한 경우 [aadb2cpreview@microsoft.com](mailto:aadb2cpreview@microsoft.com)으로 시나리오를 보내 주세요.  

### <a name="what-languages-are-supported"></a>어떤 언어가 지원되나요?

| language              | 언어 코드 |
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

