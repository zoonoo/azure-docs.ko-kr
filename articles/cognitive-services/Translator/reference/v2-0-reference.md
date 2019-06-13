---
title: Translator Text API V2.0
titleSuffix: Azure Cognitive Services
description: V2.0 Translator Text API에 대한 참조 문서입니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: v-pawal
ms.openlocfilehash: d2ff61908d7901fc464b58ee1ef9b5605b3026a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389838"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> 이 버전의 Translator Text API는 더 이상 사용되지 않습니다. [Translator Text API v3에 대한 설명서를 참조하세요](v3-0-reference.md).

Translator Text API V2는 애플리케이션, 웹 사이트, 도구 또는 기타 솔루션에 원활하게 통합되어 다국어 사용자 환경을 제공할 수 있습니다. 업계 표준을 활용하는 이 API는 모든 하드웨어 플랫폼 및 운영 체제에서 사용되면서 언어 번역은 물론, 텍스트 언어 검색 또는 텍스트 음성 변환과 같은 기타 언어 관련 작업을 수행할 수 있습니다. Microsoft Translator API에 대한 자세한 내용을 보려면 여기를 클릭하세요.

## <a name="getting-started"></a>시작
Translator Text API에 액세스하려면 [Microsoft Azure에 등록](../translator-text-how-to-signup.md)해야 합니다.

## <a name="authorization"></a>권한 부여
Translator Text API에 대한 모든 호출에는 인증을 위한 구독 키가 필요합니다. API 인증의 세 가지 모드를 지원합니다.

- 액세스 토큰입니다. 9**단계**에 참조된 구독 키를 통해 권한 부여 서비스에 대해 POST 요청을 수행하여 액세스 토큰을 생성합니다. 자세한 내용은 토큰 서비스 설명서를 참조하세요. 권한 부여 헤더를 사용 하 여 Translator 서비스에 액세스 토큰을 전달할 또는 `access_token` 쿼리 매개 변수입니다. 액세스 토큰은 10분 동안 유효합니다. 새 액세스 토큰을 10 분 간격으로 가져오고 유지 동일한 액세스를 사용 하 여 반복 되는 요청에 대 한 토큰이 분 동안.
- 구독 키에 직접 해당 합니다. 구독 키의 값으로 전달 된 `Ocp-Apim-Subscription-Key` Translator API에 요청을 사용 하 여 포함 된 헤더입니다. 이 모드에서는 액세스 토큰을 생성 하는 인증 토큰 서비스를 호출할 필요가 없습니다.
- A [Cognitive Services 다중 서비스 구독](https://azure.microsoft.com/pricing/details/cognitive-services/)합니다. 이 모드에서는 여러 서비스에 대 한 요청을 인증 하는 단일 비밀 키를 사용할 수 있습니다. <br/>
다중 서비스 비밀 키를 사용 하면 요청을 사용 하 여 두 가지 인증 헤더를 포함 해야 합니다. 첫 번째 머리글 비밀 키를 전달합니다. 두 번째 헤더에는 구독과 연결 된 지역을 지정 합니다.
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

지역이 다중 서비스 Text API 구독은 필요 합니다. 선택한 지역이 다중 서비스 등록 키를 사용 하는 경우 텍스트 번역에 사용할 수 있는 유일한 지역 및 Azure portal 통해 다중 서비스 구독에 등록할 때 선택한 동일한 지역에 있어야 합니다.

사용 가능한 지역은 어디 인가요 `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`를 `eastus2`, `japaneast`를 `northeurope`를 `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`를 `westeurope`합니다 `westus`, 및 `westus2`합니다.

구독 키와 액세스 토큰은 보이지 않게 숨겨야 하는 비밀입니다.

## <a name="profanity-handling"></a>욕설 처리
일반적으로 Translator 서비스는 원본에 있는 욕설을 번역에서 유지합니다. 욕설의 정도와 단어가 욕설로 인식되는 상황은 문화권마다 다르므로, 대상 언어에서 욕설의 정도가 증폭되거나 감소할 수 있습니다.

원본 텍스트에 욕설이 있는지 여부에 관계없이 번역에 욕설을 가져오지 않으려면 해당 기능을 지원하는 메서드에 대해 욕설 필터링 옵션을 사용할 수 있습니다. 이 옵션을 사용하면 욕설을 삭제할지, 적절한 태그로 표시할지 또는 아무 작업도 수행하지 않을지를 선택할 수 있습니다. `ProfanityAction`의 허용되는 값은 `NoAction`(기본값), Marked 및 `Deleted`입니다.


|ProfanityAction    |조치 |예제 원본(일본어)  |예제 번역(영어)  |
|:--|:--|:--|:--|
|NoAction   |기본값 옵션을 설정하지 않는 것과 같습니다. 욕설이 원본에서 대상으로 전달됩니다.        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |불쾌 한 단어가 XML 태그로 둘러싸입니다 \<불경 한 언어 > 및 \</profanity >.       |彼はジャッカスです。 |담당 하는 \<불경 한 언어 > jackass\</profanity >.  |
|Deleted    |욕설 단어가 바뀌지 않고 출력에서 제거됩니다.     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>번역에서 콘텐츠 제외
HTML과 같은 태그를 포함하는 콘텐츠(`contentType=text/html`)를 번역할 때, 번역에서 특정 콘텐츠를 제외하는 것이 유용한 경우가 있습니다. 특성 `class=notranslate`를 사용하여 원래 언어로 유지해야 하는 콘텐츠를 지정할 수 있습니다. 다음 예제에서 첫 번째 `div` 요소 내에 포함된 콘텐츠는 번역되지 않지만 두 번째 `div` 요소에 있는 콘텐츠는 번역됩니다.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>구현 참고 사항
한 언어에서 다른 언어로 텍스트 문자열을 번역합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Translate`입니다.

**반환 값:** 번역된 텍스트를 나타내는 문자열입니다.

이전에 `AddTranslation` 또는 `AddTranslationArray`를 사용하여 같은 원본 문장에 대해 등급 5 이상의 번역을 입력한 경우 `Translate`는 시스템에서 사용할 수 있는 최상위 선택 항목만 반환합니다. "같은 원본 문장"은 대/소문자, 공백, 태그 값 및 문장 끝의 문장 부호만을 제외하고 정확히 같은 것을 의미합니다(100% 일치). 등급이 5 이상일 때 등급이 저장되지 않으면 반환되는 결과는 Microsoft Translator에 의한 자동 번역이 됩니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)

문자열

응답 콘텐츠 형식: application/xml 

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명    |매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId  |(empty)    |필수 사항입니다. Authorization 또는 Ocp-Apim-Subscription-Key 헤더를 사용하는 경우 appid 필드를 비워 두고, 사용하지 않는 경우 "Bearer" + " " + "access_token"을 포함하는 문자열을 지정합니다.|쿼리|문자열|
|text|(empty)   |필수 사항입니다. 번역할 텍스트를 나타내는 문자열입니다. 텍스트 크기는 10000자를 초과하지 않아야 합니다.|쿼리|문자열|
|from|(empty)   |선택 사항입니다. 번역 텍스트의 언어 코드를 나타내는 문자열입니다. 예를 들어 영어의 경우는 en입니다.|쿼리|문자열|
|to|(empty) |필수 사항입니다. 텍스트를 번역할 언어 코드를 나타내는 문자열입니다.|쿼리|문자열|
|contentType|(empty)    |선택 사항입니다. 변환되는 텍스트의 형식입니다. 지원되는 형식은 text/plain(기본값) 및 text/html입니다. 모든 HTML은 올바른 형식이 완전한 요소여야 합니다.|쿼리|문자열|
|카테고리|(empty)   |선택 사항입니다. 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 "general"입니다.|쿼리|문자열|
|권한 부여|(empty)  |appid 필드 또는 Ocp-Apim-Subscription-Key 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  “Bearer” + “ ” + “access_token”.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)  |appid 필드 또는 Authorization 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|


### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 X-MS-Trans-Info에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>구현 참고 사항
`TranslateArray` 메서드를 사용하여 여러 원본 텍스트에 대한 번역을 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`입니다.

요청 본문의 형식은 다음과 같습니다.

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

`TranslateArrayRequest` 내의 요소는 다음과 같습니다.


* `appid`: 필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.
* `from`: 선택 사항입니다. 텍스트를 번역할 원본 언어 코드를 나타내는 문자열입니다. 비워 두면 응답에는 언어 자동 검색의 결과가 포함됩니다.
* `options`: 선택 사항입니다. 아래에 나열된 값을 포함하는 `Options` 개체입니다. 모두 선택 사항이며 가장 일반적인 설정이 기본값이 됩니다. 지정된 요소는 사전순으로 나열되어야 합니다.
    - `Category`: 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 `general`입니다.
    - `ContentType`: 변환되는 텍스트의 형식입니다. 지원되는 형식은 `text/plain`(기본값), `text/xml` 및 `text/html`입니다. 모든 HTML은 올바른 형식이 완전한 요소여야 합니다.
    - `ProfanityAction`: 위에서 설명한 것처럼 욕설이 처리되는 방식을 지정합니다. `ProfanityAction`의 허용되는 값은 `NoAction`(기본값), `Marked` 및 `Deleted`입니다.
    - `State`: 요청 및 응답을 상호 연결하게 하는 사용자 상태입니다. 응답에서 동일한 내용이 반환됩니다.
    - `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 기본값: `all`.
    - `User`: 결과를 이 사용자를 기준으로 필터링합니다. 기본값: `all`.
* `texts`: 필수 사항입니다. 번역할 텍스트를 포함하는 배열입니다. 모든 문자열은 동일한 언어여야 합니다. 번역해야 하는 모든 텍스트의 총 수는 10,000자를 넘지 않아야 합니다. 배열 요소의 최대 개수는 2000입니다.
* `to`: 필수 사항입니다. 텍스트를 번역할 언어 코드를 나타내는 문자열입니다.

선택적 요소는 생략할 수 있습니다. TranslateArrayRequest의 직접 자식 요소는 사전순으로 나열되어야 합니다.

TranslateArray 메서드는 `Content-Type`에 대해 `application/xml` 또는 `text/xml`을 수락합니다.

**반환 값:** `TranslateArrayResponse` 배열입니다. 각 `TranslateArrayResponse`에는 다음과 같은 요소가 있습니다.

* `Error`: 오류가 발생한 경우 오류를 나타냅니다. 그렇지 않은 경우, Null로 설정됩니다.
* `OriginalSentenceLengths`: 원본 텍스트에서 각 문장의 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장 수를 나타냅니다.
* `TranslatedText`: 번역된 텍스트입니다.
* `TranslatedSentenceLengths`: 번역된 텍스트에서 각 문장의 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장 수를 나타냅니다.
* `State`: 요청 및 응답을 상호 연결하게 하는 사용자 상태입니다. 요청의 경우와 같은 콘텐츠를 반환합니다.

응답 본문의 형식은 다음과 같습니다.

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
성공적인 응답에는 위에 설명된 형식의 `TranslateArrayResponse` 배열이 포함됩니다.

문자열

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|권한 부여|(empty)) |appid 필드 또는 Ocp-Apim-Subscription-Key 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  “Bearer” + “ ” + “access_token”.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|appid 필드 또는 Authorization 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드   |이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다. 일반적인 오류는 다음과 같습니다. <ul><li>배열 요소는 비워 둘 수 없습니다.</li><li>잘못된 범주</li><li>원본 언어가 올바르지 않습니다.</li><li>대상 언어가 올바르지 않습니다.</li><li>요청에 요소가 너무 많습니다.</li><li>원본 언어는 지원되지 않습니다.</li><li>대상 언어는 지원되지 않습니다.</li><li>번역 요청에 너무 많은 데이터가 있습니다.</li><li>HTML이 올바른 형식이 아닙니다.</li><li>너무 많은 문자열이 번역 요청에 전달되었습니다.</li></ul>|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 X-MS-Trans-Info에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>구현 참고 사항
매개 변수 `languageCodes`로서 전달되고 전달된 로캘 언어를 사용하여 지역화되는 언어에 대한 이름을 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`입니다.

요청 본문에는 해당 이름을 검색하기 위한 ISO 639-1 언어 코드를 나타내는 문자열 배열이 포함됩니다. 예를 들면 다음과 같습니다.

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**반환 값:** Translator Service에서 지원되고 요청된 언어로 지역화되는 언어 이름을 포함하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
Translator Service에서 지원되고 요청된 언어로 지역화되는 언어 이름을 포함하는 문자열 배열입니다.

문자열

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.|쿼리|문자열|
|locale|(empty) |필수 사항입니다. 언어와 연결된 ISO 639의 2자 소문자 문화권 코드와 언어 이름을 지역화하기 위한 ISO 3166의 2자 대문자 문화권 코드 또는 ISO 639 소문자 문화권 코드 자체의 조합을 나타내는 문자열입니다.|쿼리|문자열|
|권한 부여|(empty)  |appid 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)  |appid 필드 또는 `Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 X-MS-Trans-Info에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>구현 참고 사항
Translation Service에서 지원하는 언어를 나타내는 언어 코드의 목록을 가져옵니다.  `Translate` 및 `TranslateArray`는 이러한 임의의 두 언어 간을 번역할 수 있습니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`입니다.

**반환 값:** Translator Service에서 지원하는 언어 코드를 포함하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
Translator Service에서 지원하는 언어 코드를 포함하는 문자열 배열입니다.

문자열

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.|쿼리|문자열|
|권한 부여|(empty)  |`appid` 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|`appid` 필드 또는 `Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 X-MS-Trans-Info에서 포함된 요청 ID를 제공하세요.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>구현 참고 사항
음성 합성에 사용할 수 있는 언어를 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`입니다.

**반환 값:** Translator Service에서 음성 합성을 위해 지원하는 언어 코드를 포함하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
Translator Service에서 음성 합성을 위해 지원하는 언어 코드를 포함하는 문자열 배열입니다.

문자열

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.|쿼리|문자열|
|권한 부여|(empty)|`appid` 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|`appid` 필드 또는 `Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|
 
### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401|잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>구현 참고 사항
원하는 언어로 말하는 전달된 텍스트의 wave 또는 mp3 스트림을 반환합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Speak`입니다.

**반환 값:** 원하는 언어로 말하는 전달된 텍스트의 wave 또는 mp3 스트림입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)

binary

응답 콘텐츠 형식: application/xml 

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.|쿼리|문자열|
|text|(empty)   |필수 사항입니다. wave 스트림에 대해 읽을 지정된 언어의 문장을 포함하는 문자열입니다. 읽을 텍스트 크기는 2,000자를 초과하지 않아야 합니다.|쿼리|문자열|
|언어|(empty)   |필수 사항입니다. 텍스트를 읽을 지원되는 언어 코드를 나타내는 문자열입니다. 이 코드는 메서드 `GetLanguagesForSpeak`에서 반환된 코드 목록에 있어야 합니다.|쿼리|문자열|
|format|(empty)|선택 사항입니다. content-type ID를 지정하는 문자열입니다. 현재 `audio/wav` 및 `audio/mp3`를 사용할 수 있습니다. 기본값은 `audio/wav`입니다.|쿼리|문자열|
|options|(empty)    |<ul><li>선택 사항입니다. 합성한 음성의 속성을 지정하는 문자열입니다.<li>`MaxQuality` 및 `MinSize`는 오디오 신호의 품질을 지정하는 데 사용할 수 있습니다. `MaxQuality`를 사용하면 최고 품질의 음성을 얻을 수 있고, `MinSize`를 사용하면 가장 작은 크기의 음성을 얻을 수 있습니다. 기본값은 `MinSize`입니다.</li><li>`female` 및 `male`은 음성의 원하는 성별을 지정하는 데 사용할 수 있습니다. 기본값은 `female`입니다. 여러 옵션을 포함하려면 세로 막대(<code>\|</code>)를 사용하세요. 예: `MaxQuality|Male`.</li></li></ul> |쿼리|문자열|
|권한 부여|(empty)|`appid` 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)  |`appid` 필드 또는 `Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>구현 참고 사항
`Detect` 메서드를 사용하여 지정된 텍스트의 언어를 식별합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Detect`입니다.

**반환 값:** 지정된 텍스트에 대한 2문자 언어 코드를 포함하는 문자열입니다. .

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)

문자열

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)  |필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.|쿼리|문자열|
|text|(empty)|필수 사항입니다. 해당 언어를 식별할 수 있는 텍스트를 포함하는 문자열입니다. 텍스트 크기는 10000자를 초과하지 않아야 합니다.|쿼리| 문자열|
|권한 부여|(empty)|`appid` 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key  |(empty)    |`appid` 필드 또는 `Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>구현 참고 사항
`DetectArray` 메서드를 사용하여 문자열 배열의 언어를 즉시 식별합니다. 각 개별 배열 요소를 독립적으로 검색하고 배열의 각 행에 대한 결과를 반환합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`입니다.

요청 본문의 형식은 다음과 같습니다.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

텍스트 크기는 10000자를 초과하지 않아야 합니다.

**반환 값:** 각 입력 배열 행에 대해 2문자 언어 코드를 포함하는 문자열 배열입니다.

응답 본문의 형식은 다음과 같습니다.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
DetectArray에 성공했습니다. 각 입력 배열 행에 대해 2문자 언어 코드를 포함하는 문자열 배열입니다.

문자열

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.|쿼리|문자열|
|권한 부여|(empty)|`appid` 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|`appid` 필드 또는 Authorization 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 X-MS-Trans-Info에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>구현 참고 사항

> [!IMPORTANT]
> **사용 중단 알림:** 2018년 1월 31일 이후부터 이 메서드는 새 문장 제출을 수락하지 않으며 오류 메시지가 나타납니다. 공동 작업 번역 함수의 변경에 대한 이 알림을 참조하세요.

번역 메모리에 번역을 추가합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)

문자열

응답 콘텐츠 형식: application: xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식   |
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.|쿼리|문자열|
|originalText|(empty)|필수 사항입니다. 번역할 원본 텍스트를 포함하는 문자열입니다. 이 문자열의 최대 길이는 1,000자입니다.|쿼리|문자열|
|translatedText|(empty) |필수 사항입니다. 대상 언어로 번역된 텍스트를 포함하는 문자열입니다. 이 문자열의 최대 길이는 2,000자입니다.|쿼리|문자열|
|from|(empty)   |필수 사항입니다. 번역 텍스트의 언어 코드를 나타내는 문자열입니다. en = english, de = german 등|쿼리|문자열|
|to|(empty)|필수 사항입니다. 텍스트를 번역할 언어 코드를 나타내는 문자열입니다.|쿼리|문자열|
|rating|(empty) |선택 사항입니다. 이 문자열의 품질 등급을 나타내는 정수입니다. 값은 -10에서 10 사이입니다. 기본값은 1입니다.|쿼리|정수|
|contentType|(empty)    |선택 사항입니다. 변환되는 텍스트의 형식입니다. 지원되는 형식은 "text/plain" 및 "text/html"입니다. 모든 HTML은 올바른 형식이 완전한 요소여야 합니다.   |쿼리|문자열|
|카테고리|(empty)|선택 사항입니다. 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 "general"입니다.|쿼리|문자열|
|사용자|(empty)|필수 사항입니다. 제출의 보낸 사람을 추적하는 데 사용되는 문자열입니다.|쿼리|문자열|
|uri|(empty)|선택 사항입니다. 이 번역의 콘텐츠 위치를 포함하는 문자열입니다.|쿼리|문자열|
|권한 부여|(empty)|appid 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.    |머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|`appid` 필드 또는 `Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|410|AddTranslation은 더 이상 지원되지 않습니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 X-MS-Trans-Info에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>구현 참고 사항

> [!IMPORTANT]
> **사용 중단 알림:** 2018년 1월 31일 이후부터 이 메서드는 새 문장 제출을 수락하지 않으며 오류 메시지가 나타납니다. 공동 작업 번역 함수의 변경에 대한 이 알림을 참조하세요.

번역 메모리에 추가할 번역의 배열입니다. `AddTranslation`의 배열 버전입니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`입니다.

요청 본문의 형식은 다음과 같습니다.

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

AddtranslationsRequest 요소 내의 요소는 다음과 같습니다.

* `AppId`: 필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.
* `From`: 필수 사항입니다. 원본 언어의 언어 코드를 포함하는 문자열입니다. `GetLanguagesForTranslate` 메서드에서 반환되는 언어 중 하나여야 합니다.
* `To`: 필수 사항입니다. 대상 언어의 언어 코드를 포함하는 문자열입니다. `GetLanguagesForTranslate` 메서드에서 반환되는 언어 중 하나여야 합니다.
* `Translations`: 필수 사항입니다. 번역 메모리에 추가할 번역의 배열입니다. 각 번역은 originalText, translatedText 및 rating을 포함해야 합니다. 각 originalText 및 translatedText의 크기는 1,000자로 제한됩니다. 모든 originalText 및 translatedText의 총합은 10,000자를 넘지 않아야 합니다. 배열 요소의 최대 개수는 100입니다.
* `Options`: 필수 사항입니다. Category, ContentType, Uri 및 User를 포함하는 옵션 집합입니다. User는 필수입니다. Category, ContentType 및 Uri는 선택 사항입니다. 지정된 요소는 사전순으로 나열되어야 합니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
AddTranslationArray 메서드가 성공했습니다. 2018년 1월 31일 이후부터 문장 제출이 수락되지 않습니다. 이 서비스는 오류 코드 410으로 응답합니다.

문자열

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|권한 부여|(empty)|appid 필드 또는 Ocp-Apim-Subscription-Key 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  “Bearer” + “ ” + “access_token”.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|appid 필드 또는 Authorization 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|410    |AddTranslation은 더 이상 지원되지 않습니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>구현 참고 사항
텍스트를 여러 문장으로 분리하고 각 문장의 길이를 포함하는 배열을 반환합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`입니다.

**반환 값:** 문장 길이를 나타내는 정수의 배열입니다. 배열의 길이는 문장의 수이고 값은 각 문장의 길이입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
문장 길이를 나타내는 정수의 배열입니다. 배열의 길이는 문장의 수이고 값은 각 문장의 길이입니다.

정수

응답 콘텐츠 형식: application/xml 

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)  |필수 사항입니다. Authorization 또는 Ocp-Apim-Subscription-Key 헤더를 사용하는 경우 appid 필드를 비워 두고, 사용하지 않는 경우 "Bearer" + " " + "access_token"을 포함하는 문자열을 지정합니다.|쿼리| 문자열|
|text|(empty)   |필수 사항입니다. 문장으로 분할할 텍스트를 나타내는 문자열입니다. 텍스트 크기는 10000자를 초과하지 않아야 합니다.|쿼리|문자열|
|언어   |(empty)    |필수 사항입니다. 입력 텍스트의 언어 코드를 나타내는 문자열입니다.|쿼리|문자열|
|권한 부여|(empty)|appid 필드 또는 Ocp-Apim-Subscription-Key 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  “Bearer” + “ ” + “access_token”.    |머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|appid 필드 또는 Authorization 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401|잘못된 자격 증명|
|500|서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 X-MS-Trans-Info에서 포함된 요청 ID를 제공하세요.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>구현 참고 사항
저장소 및 MT 엔진에서 지정된 언어 쌍에 대한 번역 배열을 검색합니다. GetTranslations는 사용 가능한 모든 번역을 반환하므로 Translate와 다릅니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`입니다.

요청 본문에는 다음과 같은 형식의 선택적 TranslationOptions 개체가 포함되어 있습니다.

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions` 개체는 아래에 나열된 값을 포함합니다. 모두 선택 사항이며 가장 일반적인 설정이 기본값이 됩니다. 지정된 요소는 사전순으로 나열되어야 합니다.

* `Category`: 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 "general"입니다.
* `ContentType`: 유일하게 지원되는 기본 옵션은 “text/plain”입니다.
* `IncludeMultipleMTAlternatives`: 둘 이상의 대안이 MT 엔진에서 반환되어야 할지 여부를 결정하는 부울 플래그입니다. 유효한 값은 true 및 false(대/소문자 구분)입니다. 기본값이 False이고 1개의 대안만 포함합니다. 이 플래그를 true로 설정하면 CTF(공동 작업 번역 프레임워크)와 완벽하게 통합되는 인위적인 번역 대안을 생성할 수 있습니다. 이 기능을 사용하면 디코더의 n-best 목록에 있는 인위적인 대안을 추가하여 CTF에 대안이 없는 문장의 대안을 반환할 수 있습니다.
    - 등급: 등급은 다음과 같이 적용됩니다. 1) 최고의 자동 번역 등급은 5입니다. 2) CTF의 대안은 검토자의 권한인 -10 ~ +10을 반영합니다. 3) 자동으로 생성된(n-best) 번역 대안의 등급은 0이며, 일치 정도는 100입니다.
    - 대안 수: 반환된 대안 수는 최대 maxTranslations이지만 더 작을 수도 있습니다.
    - 언어 쌍: 이 기능은 중국어 간체 및 번체 간 양방향 번역에 사용할 수 없습니다. Microsoft Translator의 다른 모든 지원되는 언어 쌍에 대해 사용할 수 있습니다.
* `State`: 요청 및 응답을 상호 연결하게 하는 사용자 상태입니다. 응답에서 동일한 내용이 반환됩니다.
* `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 값을 설정하지 않으면 기본값은 all입니다.
* `User`: 결과를 이 사용자를 기준으로 필터링합니다. 값을 설정하지 않으면 기본값은 all입니다.

요청 `Content-Type`은 `text/xml`이어야 합니다.

**반환 값:** 응답의 형식은 다음과 같습니다.

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

여기에는 다음 값을 포함하는 `GetTranslationsResponse` 요소가 포함되어 있습니다.

* `Translations`: 발견된 일치 항목의 배열로, TranslationMatch(아래 참조) 개체에 저장됩니다. 번역에는 원본 텍스트가 약간 변형된 형태(유사 일치)가 포함될 수 있습니다. 번역은 100% 일치부터 유사 일치 순으로 정렬됩니다.
* `From`: 메서드가 From 언어를 지정하지 않을 경우 자동 언어 감지의 결과가 됩니다. 그렇지 않은 경우 지정된 From 언어입니다.
* `State`: 요청 및 응답을 상호 연결하게 하는 사용자 상태입니다. TranslateOptions 매개 변수에 지정된 것과 동일한 값을 포함합니다.

TranslationMatch 개체는 다음으로 구성됩니다.

* `Error`: 특정 입력 문자열에서 오류가 발생하는 경우 오류 코드에 저장됩니다. 그렇지 않으면 이 필드가 비어 있습니다.
* `MatchDegree`: 시스템은 입력 문장과 일치하는 문장을 저장소에서 찾습니다. 정확하지 않은 일치도 검색됩니다.  MatchDegree는 입력 텍스트가 저장소의 원본 텍스트와 얼마나 일치하는지를 나타냅니다. 반환되는 값은 0부터 100까지로, 0은 유사성이 없는 것이고 100은 대/소문자까지 정확하게 일치하는 것입니다.
MatchedOriginalText: 이 결과에서 일치된 원본 텍스트입니다. 일치된 원본 텍스트가 입력 텍스트와 다른 경우에만 반환됩니다. 유사 일치의 원본 텍스트를 반환하는 데 사용됩니다. Microsoft Translator 결과에 대해서는 반환되지 않습니다.
* `Rating`: 품질 결정 담당자의 권한을 나타냅니다. Machine Translation 결과의 등급은 5입니다. 익명으로 제공되는 번역은 등급이 1~4이지만, 정식으로 제공된 번역은 등급이 6~10입니다.
* `Count`: 이 등급의 현재 번역이 선택된 횟수입니다. 자동으로 번역된 응답의 경우 이 값이 0입니다.
* `TranslatedText`: 번역된 텍스트입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
위에 설명된 형식의 `GetTranslationsResponse` 개체입니다.

문자열

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.|쿼리|문자열|
|text|(empty)|필수 사항입니다. 번역할 텍스트를 나타내는 문자열입니다. 텍스트 크기는 10000자를 초과하지 않아야 합니다.|쿼리|문자열|
|from|(empty)|필수 사항입니다. 번역 텍스트의 언어 코드를 나타내는 문자열입니다.|쿼리|문자열|
|to |(empty)    |필수 사항입니다. 텍스트를 번역할 언어 코드를 나타내는 문자열입니다.|쿼리|문자열|
|maxTranslations|(empty)|필수 사항입니다. 반환할 번역의 최대 수를 나타내는 정수입니다.|쿼리|정수|
|권한 부여| (empty)|`appid` 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|문자열| 머리글|
|Ocp-Apim-Subscription-Key|(empty)  |`appid` 필드 또는 `Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>구현 참고 사항
`GetTranslationsArray` 메서드를 사용하여 여러 원본 텍스트에 대한 여러 번역 후보를 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`입니다.

요청 본문의 형식은 다음과 같습니다.

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest`에는 다음과 같은 요소가 포함되어 있습니다.

* `AppId`: 필수 사항입니다. Authorization 헤더가 사용되는 경우 appid 필드를 비워 두고, 사용되지 않는 경우 `"Bearer" + " " + "access_token"`을 포함하는 문자열을 포함합니다.
* `From`: 필수 사항입니다. 번역 텍스트의 언어 코드를 나타내는 문자열입니다.
* `MaxTranslations`: 필수 사항입니다. 반환할 번역의 최대 수를 나타내는 정수입니다.
* `Options`: 선택 사항입니다. 아래에 나열된 값을 포함하는 Options 개체입니다. 모두 선택 사항이며 가장 일반적인 설정이 기본값이 됩니다. 지정된 요소는 사전순으로 나열되어야 합니다.
    - Category: 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 general입니다.
    - `ContentType`: 유일하게 지원되는 기본 옵션은 text/plain입니다.
    - `IncludeMultipleMTAlternatives`: 둘 이상의 대안이 MT 엔진에서 반환되어야 할지 여부를 결정하는 부울 플래그입니다. 유효한 값은 true 및 false(대/소문자 구분)입니다. 기본값이 False이고 1개의 대안만 포함합니다. 이 플래그를 true로 설정하면 CTF(공동 작업 번역 프레임워크)와 완벽하게 통합되는 인위적인 번역 대안을 생성할 수 있습니다. 이 기능을 사용하면 디코더의 n-best 목록에 있는 인위적인 대안을 추가하여 CTF에 대안이 없는 문장의 대안을 반환할 수 있습니다.
        - 등급: 등급은 다음과 같이 적용됩니다. 1) 최고의 자동 번역 등급은 5입니다. 2) CTF의 대안은 검토자의 권한인 -10 ~ +10을 반영합니다. 3) 자동으로 생성된(n-best) 번역 대안의 등급은 0이며, 일치 정도는 100입니다.
        - 대안 수: 반환된 대안 수는 최대 maxTranslations이지만 더 작을 수도 있습니다.
        - 언어 쌍: 이 기능은 중국어 간체 및 번체 간 양방향 번역에 사용할 수 없습니다. Microsoft Translator의 다른 모든 지원되는 언어 쌍에 대해 사용할 수 있습니다.
* `State`: 요청 및 응답을 상호 연결하게 하는 사용자 상태입니다. 응답에서 동일한 내용이 반환됩니다.
* `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 값을 설정하지 않으면 기본값은 all입니다.
* `User`: 결과를 이 사용자를 기준으로 필터링합니다. 값을 설정하지 않으면 기본값은 all입니다.
* `Texts`: 필수 사항입니다. 번역할 텍스트를 포함하는 배열입니다. 모든 문자열은 동일한 언어여야 합니다. 번역해야 하는 모든 텍스트의 총 수는 10,000자를 넘지 않아야 합니다. 배열 요소의 최대 개수는 10입니다.
* `To`: 필수 사항입니다. 텍스트를 번역할 언어 코드를 나타내는 문자열입니다.

선택적 요소는 생략할 수 있습니다. `GetTranslationsArrayRequest`의 직접 자식 요소는 사전순으로 나열되어야 합니다.

요청 `Content-Type`은 `text/xml`이어야 합니다.

**반환 값:** 응답의 형식은 다음과 같습니다.

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

각 `GetTranslationsResponse` 요소는 다음 값을 갖습니다.

* `Translations`: 발견된 일치 항목의 배열로, `TranslationMatch`(아래 참조) 개체에 저장됩니다. 번역에는 원본 텍스트가 약간 변형된 형태(유사 일치)가 포함될 수 있습니다. 번역은 100% 일치부터 유사 일치 순으로 정렬됩니다.
* `From`: 메서드가 `From` 언어를 지정하지 않을 경우 자동 언어 감지의 결과가 됩니다. 그렇지 않은 경우 지정된 From 언어입니다.
* `State`: 요청 및 응답을 상호 연결하게 하는 사용자 상태입니다. `TranslateOptions` 매개 변수에 지정된 것과 동일한 값을 포함합니다.

`TranslationMatch` 개체는 다음으로 구성됩니다.
* `Error`: 특정 입력 문자열에서 오류가 발생하는 경우 오류 코드에 저장됩니다. 그렇지 않으면 이 필드가 비어 있습니다.
* `MatchDegree`: 시스템은 입력 문장과 일치하는 문장을 저장소에서 찾습니다. 정확하지 않은 일치도 검색됩니다.  `MatchDegree`는 입력 텍스트가 저장소의 원본 텍스트와 얼마나 일치하는지를 나타냅니다. 반환되는 값은 0부터 100까지로, 0은 유사성이 없는 것이고 100은 대/소문자까지 정확하게 일치하는 것입니다.
* `MatchedOriginalText`: 이 결과에서 일치된 원본 텍스트입니다. 일치된 원본 텍스트가 입력 텍스트와 다른 경우에만 반환됩니다. 유사 일치의 원본 텍스트를 반환하는 데 사용됩니다. Microsoft Translator 결과에 대해서는 반환되지 않습니다.
* `Rating`: 품질 결정 담당자의 권한을 나타냅니다. Machine Translation 결과의 등급은 5입니다. 익명으로 제공되는 번역은 등급이 1~4이지만, 정식으로 제공된 번역은 등급이 6~10입니다.
* `Count`: 이 등급의 현재 번역이 선택된 횟수입니다. 자동으로 번역된 응답의 경우 이 값이 0입니다.
* `TranslatedText`: 번역된 텍스트입니다.


### <a name="response-class-status-200"></a>응답 클래스(상태: 200)

문자열

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|권한 부여  |(empty)    |`appid` 필드 또는 `Ocp-Apim-Subscription-Key` 헤더를 지정하지 않은 경우 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)  |`appid` 필드 또는 `Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [v3 Translator Text API로 마이그레이션](../migrate-to-v3.md)










