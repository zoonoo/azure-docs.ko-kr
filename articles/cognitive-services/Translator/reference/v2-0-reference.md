---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: 번역기 텍스트 API v2.0에 대한 참조 문서입니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242492"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> 이 버전의 Translator Text API는 더 이상 사용되지 않습니다. [번역기 텍스트 API의 버전 3에 대한 설명서를 봅니다.](v3-0-reference.md)

번역기 텍스트 API의 버전 2는 앱, 웹 사이트, 도구 또는 기타 솔루션에 원활하게 통합되어 다국어 사용자 환경을 제공할 수 있습니다. 모든 하드웨어 플랫폼및 운영 체제에서 사용할 수 있으며, 업계 표준에 따라 텍스트 언어 검색 및 텍스트 음성 변환과 같은 언어 번역 및 기타 언어 관련 작업을 수행할 수 있습니다. 자세한 내용은 [번역기 텍스트 API를](../translator-info-overview.md)참조하십시오.

## <a name="getting-started"></a>시작
번역기 텍스트 API에 액세스하려면 Microsoft Azure 에 [등록해야](../translator-text-how-to-signup.md)합니다.

## <a name="authentication"></a>인증 
번역기 텍스트 API에 대한 모든 호출에는 인증을 위한 구독 키가 필요합니다. API는 세 가지 인증 방법을 지원합니다.

- 액세스 토큰입니다. 구독 키를 사용하여 인증 서비스에 POST 요청을 하여 액세스 토큰을 만듭니다. 자세한 내용은 토큰 서비스 설명서를 참조하세요. 헤더 또는 쿼리 매개 변수를 `Authorization` 사용하여 액세스 `access_token` 토큰을 Translator 서비스에 전달합니다. 액세스 토큰은 10분 동안 유효합니다. 10분마다 새 액세스 토큰을 가져오고 10분 동안 반복되는 요청에 대해 동일한 액세스 토큰을 계속 사용하십시오.
- 직접 사용되는 구독 키입니다. 요청과 함께 포함된 헤더의 `Ocp-Apim-Subscription-Key` 값으로 구독 키를 번역기 텍스트 API에 전달합니다. 구독 키를 직접 사용하는 경우 액세스 토큰을 만들기 위해 토큰 인증 서비스를 호출할 필요가 없습니다.
- [Azure 코그너티브 서비스 다중 서비스 구독.](https://azure.microsoft.com/pricing/details/cognitive-services/) 이 방법을 사용하면 단일 비밀 키를 사용하여 여러 서비스에 대한 요청을 인증할 수 있습니다.
다중 서비스 비밀 키를 사용하는 경우 요청에 두 개의 인증 헤더를 포함해야 합니다. 첫 번째 헤더는 비밀 키를 전달합니다. 두 번째 헤더는 구독과 연결된 지역을 지정합니다.
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

이 지역은 다중 서비스 텍스트 API 구독에 필요합니다. 선택한 지역은 다중 서비스 구독 키를 사용할 때 텍스트 번역에 사용할 수 있는 유일한 지역입니다. Azure Portal에서 다중 서비스 구독에 등록할 때 선택한 지역과 동일한 지역이어야 합니다.

사용 가능한 `australiaeast` `brazilsouth`영역은 " `centraluseuap` `eastasia`, `eastus` `eastus2` `japaneast` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2`" , " , " , , , , , `canadacentral` `centralindia`

구독 키 및 액세스 토큰은 보기에서 숨김해야 하는 암호입니다.

## <a name="profanity-handling"></a>욕설 처리
일반적으로 번역기 서비스는 원본에 있는 욕설을 유지합니다. 욕설의 정도와 욕설을 하는 문맥은 문화에 따라 다릅니다. 따라서 대상 언어의 욕설 의 정도가 증가하거나 감소 될 수 있습니다.

원본 텍스트에 있는 경우에도 번역에서 욕설을 방지하려면 해당 텍스트를 지원하는 메서드에 욕설 필터링 옵션을 사용할 수 있습니다. 이 옵션을 사용하면 욕설이 삭제되거나 적절한 태그로 표시되도록 할지 또는 대상에서 욕설을 허용할지 여부를 선택할 수 있습니다. 허용된 `ProfanityAction` 값은 `NoAction` (기본값) `Marked`및 `Deleted`.


|ProfanityAction    |작업 |예제 소스(일본어)  |예제 번역(영어)  |
|:--|:--|:--|:--|
|NoAction   |기본값 옵션을 설정하지 않는 것과 같습니다. 욕설이 원본에서 대상으로 전달됩니다.        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |욕설은> XML 태그와 \< \<욕설> 둘러싸입니다.       |彼はジャッカスです。 |\<그는> 재커스 /욕설을\<>욕설입니다.  |
|Deleted    |욕설 단어가 바뀌지 않고 출력에서 제거됩니다.     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>번역에서 콘텐츠 제외
HTML(예:`contentType=text/html`태그)으로 콘텐츠를 번역할 때 특정 콘텐츠를 번역에서 제외하는 것이 유용한 경우도 있습니다. 특성 `class=notranslate`를 사용하여 원래 언어로 유지해야 하는 콘텐츠를 지정할 수 있습니다. 다음 예제에서는 첫 번째 `div` 요소의 콘텐츠가 번역되지 않지만 두 번째 `div` 요소의 콘텐츠는 번역됩니다.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>구현 참고 사항
한 언어에서 다른 언어로 텍스트 문자열을 번역합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Translate`입니다.

**반환 값:** 번역된 텍스트를 나타내는 문자열입니다.

이전에 동일한 `AddTranslation` 소스 `AddTranslationArray` 문장에 대해 5 등급 이상인 번역을 사용했거나 입력한 경우 시스템에서 사용할 수 있는 최상위 선택 항목만 `Translate` 반환합니다. "동일한 소스 문장"은 대문자, 공백, 태그 값 및 문장 부호를 제외하고 문장 끝에 정확히 동일 (100 % 일치)을 의미합니다. 등급이 5 이상인 등급이 저장되지 않으면 반환된 결과는 Microsoft 번역기에 의한 자동 번역입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)

문자열

응답 내용 유형: 응용 프로그램/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명    |매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId  |(empty)    |필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|text|(empty)   |필수 사항입니다. 번역할 텍스트를 나타내는 문자열입니다. 텍스트에는 10,000자를 초과할 수 없습니다.|Query|문자열|
|원본|(empty)   |(선택 사항) 번역되는 텍스트의 언어 코드를 나타내는 문자열입니다. 예를 들어 영어의 경우는 en입니다.|Query|문자열|
|to|(empty) |필수 사항입니다. 텍스트를 번역하는 언어 코드를 나타내는 문자열입니다.|Query|문자열|
|contentType|(empty)    |(선택 사항) 변환되는 텍스트의 형식입니다. 지원되는 형식은 `text/plain` (기본값) 및 `text/html`. 모든 HTML 요소는 잘 형성되고 완전한 요소가 어야 합니다.|Query|문자열|
|category|(empty)   |(선택 사항) 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 `general`입니다.|Query|문자열|
|권한 부여|(empty)  |필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)  |필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|


### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>구현 참고 사항
여러 원본 텍스트에 대한 번역을 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`입니다.

요청 본문 형식은 다음과 같습니다.

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

이러한 요소는 `TranslateArrayRequest`다음과 같습니다.


* `AppId`: 필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `AppId` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.
* `From`: 선택 사항입니다. 번역되는 텍스트의 언어 코드를 나타내는 문자열입니다. 이 필드가 비어 있으면 응답에 자동 언어 검색 결과가 포함됩니다.
* `Options`: 선택 사항입니다. 다음 `Options` 값을 포함하는 개체입니다. 모두 선택 사항이며 가장 일반적인 설정으로 기본값입니다. 지정된 요소는 사전순으로 나열되어야 합니다.
    - `Category`: 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 `general`입니다.
    - `ContentType`: 변환되는 텍스트의 형식입니다. 지원되는 형식은 `text/plain` `text/xml`(기본값) `text/html`및 . 모든 HTML 요소는 잘 형성되고 완전한 요소가 어야 합니다.
    - `ProfanityAction`: 앞서 설명한 대로 욕설을 처리하는 방법을 지정합니다. 허용된 `NoAction` 값은 `Marked`(기본값) 및 `Deleted`은
    - `State`: 요청 및 응답의 상관 관계를 파악하는 데 도움이 되는 사용자 상태입니다. 응답에서 동일한 콘텐츠가 반환됩니다.
    - `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 기본값: `all`.
    - `User`: 결과를 이 사용자를 기준으로 필터링합니다. 기본값: `all`.
* `Texts`: 필수 사항입니다. 번역텍스트가 포함된 배열입니다. 모든 문자열은 동일한 언어여야 합니다. 번역할 모든 텍스트의 합계는 10,000자를 초과할 수 없습니다. 배열 요소의 최대 수는 2,000개입니다.
* `To`: 필수 사항입니다. 텍스트를 번역하는 언어 코드를 나타내는 문자열입니다.

선택적 요소를 생략할 수 있습니다. 의 직접 자식요소는 알파벳 순으로 `TranslateArrayRequest` 나열되어야 합니다.

메서드는 `TranslateArray` 또는 `application/xml` `text/xml` 에 `Content-Type`대해 허용합니다.

**반환 값:**`TranslateArrayResponse` 배열입니다. 각각에는 `TranslateArrayResponse` 다음 요소가 있습니다.

* `Error`: 오류가 발생하면 오류를 나타냅니다. 그렇지 않은 경우, Null로 설정됩니다.
* `OriginalSentenceLengths`: 원본 텍스트에서 각 문장의 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장 수를 나타냅니다.
* `TranslatedText`: 번역된 텍스트입니다.
* `TranslatedSentenceLengths`: 번역된 텍스트에서 각 문장의 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장 수를 나타냅니다.
* `State`: 요청 및 응답의 상관 관계를 파악하는 데 도움이 되는 사용자 상태입니다. 요청과 동일한 콘텐츠를 반환합니다.

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

### <a name="response-class-status-200"></a>응답 클래스(상태 200)
성공적인 응답에는 앞에서 `TranslateArrayResponse` 설명한 형식의 배열이 포함됩니다.

문자열

응답 내용 유형: 응용 프로그램/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|권한 부여|(empty)  |필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드   |이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다. 일반적인 오류는 다음과 같습니다. <ul><li>배열 요소는 비어 있을 수 없습니다.</li><li>범주가 잘못되었습니다.</li><li>언어에서 유효하지 않습니다.</li><li>언어에 유효하지 않습니다.</li><li>요청에 요소가 너무 많습니다.</li><li>From 언어는 지원되지 않습니다.</li><li>To 언어는 지원되지 않습니다.</li><li>번역 요청에 데이터가 너무 많습니다.</li><li>HTML이 올바른 형식이 아닙니다.</li><li>번역 요청에 너무 많은 문자열이 전달되었습니다.</li></ul>|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>구현 참고 사항
매개 변수로 `languageCodes`전달 된 언어에 대 한 친숙 한 `locale` 이름을 검색 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`입니다.

요청 본문에는 친숙한 이름을 검색하는 ISO 639-1 언어 코드를 나타내는 문자열 배열이 포함되어 있습니다. 예를 들면 다음과 같습니다.

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**반환 값:** Translator 서비스에서 지원하는 언어 이름을 포함하는 문자열 배열로 요청된 언어로 지역화됩니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)
Translator 서비스에서 지원하는 언어 이름을 포함하는 문자열 배열로 요청된 언어로 지역화됩니다.

문자열

응답 내용 유형: 응용 프로그램/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|locale|(empty) |필수 사항입니다. 언어 이름을 지역화하는 데 사용되는 다음 중 하나를 나타내는 문자열입니다. <ul><li>언어와 연결된 ISO 639 2자 소문자 문화권 코드와 ISO 3166 두 문자 대문자 하위 문화 코드의 조합입니다. <li>ISO 639 소문자 문화 코드 자체입니다.|Query|문자열|
|권한 부여|(empty)  |필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)  |필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>구현 참고 사항
번역 서비스에서 지원하는 언어를 나타내는 언어 코드 목록을 가져옵니다.  `Translate` 및 `TranslateArray`는 이러한 임의의 두 언어 간을 번역할 수 있습니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`입니다.

**반환 값:** Translator 서비스에서 지원하는 언어 코드를 포함하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)
Translator 서비스에서 지원하는 언어 코드를 포함하는 문자열 배열입니다.

문자열

응답 내용 유형: 응용 프로그램/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|권한 부여|(empty)  |필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>구현 참고 사항
음성 합성에 사용할 수 있는 언어를 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`입니다.

**반환 값:** 번역기 서비스에서 음성 합성에 지원되는 언어 코드를 포함하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)
번역기 서비스에서 음성 합성에 지원되는 언어 코드를 포함하는 문자열 배열입니다.

문자열

응답 내용 유형: 응용 프로그램/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|권한 부여|(empty)|필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|
 
### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401|잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>구현 참고 사항
원하는 언어로 사용되는 전달된 텍스트의 WAV 또는 MP3 스트림을 반환합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Speak`입니다.

**반환 값:** 원하는 언어로 사용되는 전달된 텍스트의 WAV 또는 MP3 스트림입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)

binary

응답 내용 유형: 응용 프로그램/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|text|(empty)   |필수 사항입니다. 스트림에 대해 지정된 언어로 사용할 하나 이상의 문장이 포함된 문자열입니다. 텍스트가 2,000자를 초과해서는 안 됩니다.|Query|문자열|
|언어|(empty)   |필수 사항입니다. 텍스트를 말할 언어의 지원되는 언어 코드를 나타내는 문자열입니다. 코드는 메서드에서 `GetLanguagesForSpeak`반환되는 코드 중 하나여야 합니다.|Query|문자열|
|format|(empty)|(선택 사항) 콘텐츠 유형 ID를 지정하는 문자열입니다. 현재 `audio/wav` 및 `audio/mp3`를 사용할 수 있습니다. 기본값은 `audio/wav`입니다.|Query|문자열|
|옵션|(empty)    |(선택 사항) 합성된 음성의 속성을 지정하는 문자열:<ul><li>`MaxQuality`오디오 `MinSize` 신호의 품질을 지정합니다. `MaxQuality`최고 품질을 제공합니다. `MinSize`파일 크기를 가장 작게 제공합니다. 기본값은 `MinSize`.</li><li>`female`원하는 `male` 음성 성별을 지정합니다. 기본값은 `female`입니다. 세로 막대 ()<code>\|</code>를 사용하여 여러 옵션을 포함합니다. 예: `MaxQuality|Male`.</li></li></ul>  |Query|문자열|
|권한 부여|(empty)|필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)  |필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>구현 참고 사항
텍스트 섹션의 언어를 식별합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Detect`입니다.

**반환 값:** 텍스트에 대한 두 문자 언어 코드가 포함된 문자열입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)

문자열

응답 내용 유형: 응용 프로그램/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)  |필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|text|(empty)|필수 사항입니다. 언어를 식별할 텍스트가 포함된 문자열입니다. 텍스트가 10,000자를 초과해서는 안 됩니다.|Query|  문자열|
|권한 부여|(empty)|필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key  |(empty)    |필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>구현 참고 사항

문자열 배열의 언어를 식별합니다. 각 개별 배열 요소의 언어를 독립적으로 검색하고 배열의 각 행에 대한 결과를 반환합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`입니다.

요청 본문 형식은 다음과 같습니다.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

텍스트는 10,000자를 초과할 수 없습니다.

**반환 값:** 입력 배열의 각 행에 대해 두 문자 언어 코드를 포함하는 문자열 배열입니다.

응답 본문의 형식은 다음과 같습니다.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>응답 클래스(상태 200)
`DetectArray`성공적이었습니다. 입력 배열의 각 행에 대해 두 문자 언어 코드가 포함된 문자열 배열을 반환합니다.

문자열

응답 내용 유형: 응용 프로그램/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|권한 부여|(empty)|필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>구현 참고 사항

> [!IMPORTANT]
> **사용 중단 참고 사항:** 2018년 1월 31일 이후에는 이 방법은 새 문장 제출을 허용하지 않습니다. 오류 메시지가 표시됩니다. 공동 번역 프레임워크(CTF)의 변경 사항에 대한 공지를 참조하십시오.

번역 메모리에 번역을 추가합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)

문자열

응답 내용 유형: 응용 프로그램: xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식   |
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|originalText|(empty)|필수 사항입니다. 번역할 텍스트가 포함된 문자열입니다. 문자열의 최대 길이는 1,000자입니다.|Query|문자열|
|translatedText|(empty) |필수 사항입니다. 대상 언어로 번역된 텍스트가 포함된 문자열입니다. 문자열의 최대 길이는 2,000자입니다.|Query|문자열|
|원본|(empty)   |필수 사항입니다. 텍스트의 원래 언어의 언어 코드를 나타내는 문자열입니다. 예를 들어 영어의 경우 en, 독일어의 경우 de입니다.|Query|문자열|
|to|(empty)|필수 사항입니다. 텍스트를 번역하는 언어의 언어 코드를 나타내는 문자열입니다.|Query|문자열|
|rating|(empty) |(선택 사항) 문자열의 품질 등급을 나타내는 정수입니다. 값은 -10에서 10 사이입니다. 기본값은 1입니다.|Query|integer|
|contentType|(empty)    |(선택 사항) 변환되는 텍스트의 형식입니다. 지원되는 형식은 `text/plain` `text/html`및 입니다. 모든 HTML 요소는 잘 형성되고 완전한 요소가 어야 합니다.    |Query|문자열|
|category|(empty)|(선택 사항) 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 `general`입니다.|Query|문자열|
|사용자|(empty)|필수 사항입니다. 제출의 작성기를 추적하는 데 사용되는 문자열입니다.|Query|문자열|
|uri|(empty)|(선택 사항) 번역의 콘텐츠 위치를 포함하는 문자열입니다.|Query|문자열|
|권한 부여|(empty)|필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.  |머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|410|`AddTranslation`은 더 이상 지원되지 않습니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>구현 참고 사항

> [!IMPORTANT]
> **사용 중단 참고 사항:** 2018년 1월 31일 이후에는 이 방법은 새 문장 제출을 허용하지 않습니다. 오류 메시지가 표시됩니다. 공동 번역 프레임워크(CTF)의 변경 사항에 대한 공지를 참조하십시오.

번역 메모리에 번역 배열을 추가합니다. 이 메서드는 의 `AddTranslation`배열 버전입니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`입니다.

요청 본문 형식은 다음과 같습니다.

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

이러한 요소는 `AddtranslationsRequest`다음과 같습니다.

* `AppId`: 필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `AppId` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.
* `From`: 필수 사항입니다. 원본 언어의 언어 코드를 포함하는 문자열입니다. `GetLanguagesForTranslate` 메서드에서 반환되는 언어 중 하나여야 합니다.
* `To`: 필수 사항입니다. 대상 언어의 언어 코드를 포함하는 문자열입니다. `GetLanguagesForTranslate` 메서드에서 반환되는 언어 중 하나여야 합니다.
* `Translations`: 필수 사항입니다. 번역 메모리에 추가할 번역의 배열입니다. 각 번역에는 `OriginalText` `TranslatedText`에 `Rating`의한 및 을 포함해야 합니다. 각 `OriginalText` 자의 최대 `TranslatedText` 크기는 1,000자입니다. 모든 `OriginalText` 요소와 `TranslatedText` 요소의 합계는 10,000자를 초과할 수 없습니다. 배열 요소의 최대 개수는 100입니다.
* `Options`: 필수 사항입니다. 을 `Category` `ContentType`비롯한 옵션 집합입니다. `User` `Uri` `User`은 필수입니다. `Category`및 `ContentType` `Uri` 선택 사항입니다. 지정된 요소는 사전순으로 나열되어야 합니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)
`AddTranslationArray`메서드가 성공했습니다. 

2018년 1월 31일 이후에는 문장 제출이 허용되지 않습니다. 이 서비스는 오류 코드 410으로 응답합니다.

문자열

응답 내용 유형: 응용 프로그램/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|권한 부여|(empty)|필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|410    |`AddTranslation`은 더 이상 지원되지 않습니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>구현 참고 사항
텍스트 섹션을 문장으로 나누고 각 문장의 길이가 포함된 배열을 반환합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`입니다.

**반환 값:** 문장의 길이를 나타내는 정수 의 배열입니다. 배열의 길이는 문장 수를 나타냅니다. 값은 각 문장의 길이를 나타냅니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)
문장의 길이를 나타내는 정수 의 배열입니다. 배열의 길이는 문장 수를 나타냅니다. 값은 각 문장의 길이를 나타냅니다.

integer

응답 내용 유형: 응용 프로그램/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)  |필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query| 문자열|
|text|(empty)   |필수 사항입니다. 문장으로 분할할 텍스트를 나타내는 문자열입니다. 텍스트의 최대 크기는 10,000자입니다.|Query|문자열|
|언어   |(empty)    |필수 사항입니다. 입력 텍스트의 언어 코드를 나타내는 문자열입니다.|Query|문자열|
|권한 부여|(empty)|필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.   |머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)|필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401|잘못된 자격 증명입니다.|
|500|서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>구현 참고 사항
저장소 및 MT 엔진에서 지정된 언어 쌍에 대한 번역 배열을 검색합니다. `GetTranslations``Translate` 사용 가능한 모든 번역을 반환한다는 점에서 다릅니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`입니다.

요청본문에는 다음 형식의 선택적 `TranslationOptions` 개체가 포함됩니다.

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

개체에는 `TranslateOptions` 다음 목록의 값이 포함되어 있습니다. 모두 선택 사항이며 가장 일반적인 설정으로 기본값입니다. 지정된 요소는 사전순으로 나열되어야 합니다.

* `Category`: 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 `general`입니다.
* `ContentType`: 지원되는 유일한 옵션과 기본값은 입니다. `text/plain`
* `IncludeMultipleMTAlternatives`: MT 엔진에서 두 개 이상의 대체 대체 를 반환할지 여부를 지정하는 부울 플래그입니다. 유효한 `true` 값은 `false` 및(대/소문자 구분)입니다. 기본값은 `false`하나의 대안만 반환하는 것입니다. 공동 번역 `true` 프레임워크(CTF)와 완전히 통합된 인공 대안을 만들 수 있도록 플래그를 설정합니다. 이 기능을 사용하면 *n-best*디코더 목록에서 인공 대안을 추가하여 CTF에 번역이 없는 문장에 대한 대안을 반환할 수 있습니다.
    - 등급. 등급은 다음과 같이 적용됩니다. 
         - 최고 품질의 자동 번역은 5등급입니다.
       - CTF의 대안은 검토자의 권한을 반영합니다. 범위는 -10에서 +10사이입니다.
       - 자동*생성(n-best)* 번역 대안의 등급은 0이고 일치 정도는 100입니다.
    - 대안 의 수입니다. 반환된 대안의 수는 `maxTranslations`에 지정된 값만큼 높을 수 있지만 더 낮을 수 있습니다.
    - 언어 쌍입니다. 이 기능은 중국어 간체와 중국어 번체 간 번역을 어느 방향으로든 사용할 수 없습니다. Microsoft 번역기에서 지원하는 다른 모든 언어 쌍에 사용할 수 있습니다.
* `State`: 요청 및 응답의 상관 관계를 파악하는 데 도움이 되는 사용자 상태입니다. 응답에서 동일한 콘텐츠가 반환됩니다.
* `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 값이 설정되지 않은 경우 `all`기본값은 입니다.
* `User`: 결과를 이 사용자를 기준으로 필터링합니다. 값이 설정되지 않은 경우 `all`기본값은 입니다.

요청 `Content-Type`은 `text/xml`이어야 합니다.

**반환 값:** 응답 의 형식은 다음과 같습니다.

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

이 응답에는 `GetTranslationsResponse` 다음 값이 포함된 요소가 포함됩니다.

* `Translations`: 개체에 `TranslationMatch` 저장된 일치하는 항목의 배열입니다(다음 섹션에 설명됨). 번역에는 원본 텍스트의 약간의 변형(퍼지 일치)이 포함될 수 있습니다. 번역은 정렬됩니다 : 100 % 먼저 일치, 퍼지 일치 다음.
* `From`: 메서드가 `From` 언어를 지정하지 않으면 이 값은 자동 언어 검색에서 비롯됩니다. 그렇지 않으면 지정된 `From` 언어가 됩니다.
* `State`: 요청 및 응답의 상관 관계를 파악하는 데 도움이 되는 사용자 상태입니다. 매개 변수에 제공된 `TranslateOptions` 값을 포함합니다.

개체는 `TranslationMatch` 다음 값으로 구성됩니다.

* `Error`: 특정 입력 문자열에 대한 오류가 발생하는 경우 오류 코드입니다. 그렇지 않으면 이 필드가 비어 있습니다.
* `MatchDegree`: 입력 텍스트가 저장소에 있는 원본 텍스트와 얼마나 가깝게 일치하는지를 나타냅니다. 시스템은 입력 문장과 일치하는 문장을 저장소에서 찾습니다. 정확하지 않은 일치도 검색됩니다. 반환된 값의 범위는 0에서 100사이이며, 여기서 0은 유사성이 없고 100은 대/소문자를 구분하는 정확한 일치항목입니다.
* `MatchedOriginalText`: 이 결과에서 일치된 원본 텍스트입니다. 이 값은 일치하는 원본 텍스트가 입력 텍스트와 다른 경우에만 반환됩니다. 퍼지 일치의 소스 텍스트를 반환하는 데 사용됩니다. 이 값은 Microsoft 번역기 결과에 반환되지 않습니다.
* `Rating`: 품질을 결정하는 사용자의 권한을 나타냅니다. 기계 번역 결과의 등급은 5입니다. 익명으로 제공된 번역은 일반적으로 1에서 4까지의 등급을 가합니다. 정식으로 제공된 번역은 일반적으로 6에서 10까지의 등급을 갖습니다.
* `Count`: 이 등급을 값는 이 번역이 선택된 횟수입니다. 자동으로 변환된 응답의 값은 0입니다.
* `TranslatedText`: 번역된 텍스트입니다.

### <a name="response-class-status-200"></a>응답 클래스(상태 200)
앞에서 `GetTranslationsResponse` 설명한 형식의 개체입니다.

문자열

응답 내용 유형: 응용 프로그램/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|appId|(empty)|필수 사항입니다. 또는 `Authorization` `Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.|Query|문자열|
|text|(empty)|필수 사항입니다. 번역할 텍스트를 나타내는 문자열입니다. 텍스트의 최대 크기는 10,000자입니다.|Query|문자열|
|원본|(empty)|필수 사항입니다. 번역되는 텍스트의 언어 코드를 나타내는 문자열입니다.|Query|문자열|
|to |(empty)    |필수 사항입니다. 텍스트를 번역하는 언어의 언어 코드를 나타내는 문자열입니다.|Query|문자열|
|maxTranslations|(empty)|필수 사항입니다. 반환할 최대 번역 수를 나타내는 정수입니다.|Query|integer|
|권한 부여| (empty)|필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|문자열|  머리글|
|Ocp-Apim-Subscription-Key|(empty)  |필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청 & 대략적인 날짜와 응답 헤더에 `X-MS-Trans-Info`포함된 요청 ID를 입력해 주십시오.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>구현 참고 사항
여러 원본 텍스트에 대해 여러 번역 후보를 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`입니다.

요청 본문 형식은 다음과 같습니다.

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

`GetTranslationsArrayRequest`다음 요소가 포함됩니다.

* `AppId`: 필수 사항입니다. 헤더를 `Authorization` 사용하는 경우 `AppId` 필드를 비워 둡니다. 그렇지 않으면 을 포함하는 `"Bearer" + " " + "access_token"`문자열을 포함합니다.
* `From`: 필수 사항입니다. 번역되는 텍스트의 언어 코드를 나타내는 문자열입니다.
* `MaxTranslations`: 필수 사항입니다. 반환할 최대 번역 수를 나타내는 정수입니다.
* `Options`: 선택 사항입니다. 다음 `Options` 값을 포함하는 개체입니다. 모두 선택 사항이며 가장 일반적인 설정으로 기본값입니다. 지정된 요소는 사전순으로 나열되어야 합니다.
    - `Category`: 번역의 범주(도메인)를 포함하는 문자열입니다. 기본값은 `general`입니다.
    - `ContentType`: 지원되는 유일한 옵션과 기본값은 입니다. `text/plain`
    - `IncludeMultipleMTAlternatives`: MT 엔진에서 두 개 이상의 대체 대체 를 반환할지 여부를 지정하는 부울 플래그입니다. 유효한 `true` 값은 `false` 및(대/소문자 구분)입니다. 기본값은 `false`하나의 대안만 반환하는 것입니다. 공동 번역 `true` 프레임워크(CTF)와 완전히 통합된 번역에서 인공 대안을 생성할 수 있도록 플래그를 설정합니다. 이 기능을 사용하면 *n-best*디코더 목록에서 인공 대안을 추가하여 CTF에 대안이 없는 문장에 대한 대안을 반환할 수 있습니다.
        - 등급 등급은 다음과 같이 적용됩니다.
          - 최고 품질의 자동 번역은 5등급입니다.
          - CTF의 대안은 검토자의 권한을 반영합니다. 범위는 -10에서 +10사이입니다.
          - 자동*생성(n-best)* 번역 대안의 등급은 0이고 일치 정도는 100입니다.
        - 대안 의 수입니다. 반환된 대안의 수는 `maxTranslations`에 지정된 값만큼 높을 수 있지만 더 낮을 수 있습니다.
        - 언어 쌍입니다. 이 기능은 중국어 간체와 중국어 번체 간 번역을 어느 방향으로든 사용할 수 없습니다. Microsoft 번역기에서 지원하는 다른 모든 언어 쌍에 사용할 수 있습니다.
* `State`: 요청 및 응답의 상관 관계를 파악하는 데 도움이 되는 사용자 상태입니다. 응답에서 동일한 콘텐츠가 반환됩니다.
* `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 값이 설정되지 않은 경우 `all`기본값은 입니다.
* `User`: 결과를 이 사용자를 기준으로 필터링합니다. 값이 설정되지 않은 경우 `all`기본값은 입니다.
* `Texts`: 필수 사항입니다. 번역텍스트가 포함된 배열입니다. 모든 문자열은 동일한 언어여야 합니다. 번역할 모든 텍스트의 합계는 10,000자를 초과할 수 없습니다. 배열 요소의 최대 개수는 10입니다.
* `To`: 필수 사항입니다. 텍스트를 번역하는 언어의 언어 코드를 나타내는 문자열입니다.

선택적 요소를 생략할 수 있습니다. 의 직접 자식요소는 알파벳 순으로 `GetTranslationsArrayRequest` 나열되어야 합니다.

요청 `Content-Type`은 `text/xml`이어야 합니다.

**반환 값:** 응답 의 형식은 다음과 같습니다.

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

각 `GetTranslationsResponse` 요소에는 다음 값이 포함됩니다.

* `Translations`: 개체에 `TranslationMatch` 저장된 일치하는 항목의 배열입니다(다음 섹션에 설명됨). 번역에는 원본 텍스트의 약간의 변형(퍼지 일치)이 포함될 수 있습니다. 번역은 정렬됩니다 : 100 % 먼저 일치, 퍼지 일치 다음.
* `From`: 메서드가 `From` 언어를 지정하지 않으면 이 값은 자동 언어 검색에서 비롯됩니다. 그렇지 않으면 지정된 `From` 언어가 됩니다.
* `State`: 요청 및 응답의 상관 관계를 파악하는 데 도움이 되는 사용자 상태입니다. 매개 변수에 제공된 `TranslateOptions` 값을 포함합니다.

개체에 `TranslationMatch` 다음 값이 포함되어 있습니다.
* `Error`: 특정 입력 문자열에 대한 오류가 발생하는 경우 오류 코드입니다. 그렇지 않으면 이 필드가 비어 있습니다.
* `MatchDegree`: 입력 텍스트가 저장소에 있는 원본 텍스트와 얼마나 가깝게 일치하는지를 나타냅니다. 시스템은 입력 문장과 일치하는 문장을 저장소에서 찾습니다. 정확하지 않은 일치도 검색됩니다. 반환된 값의 범위는 0에서 100사이이며, 여기서 0은 유사성이 없고 100은 대/소문자를 구분하는 정확한 일치항목입니다.
* `MatchedOriginalText`: 이 결과에서 일치된 원본 텍스트입니다. 이 값은 일치하는 원본 텍스트가 입력 텍스트와 다른 경우에만 반환됩니다. 퍼지 일치의 소스 텍스트를 반환하는 데 사용됩니다. 이 값은 Microsoft 번역기 결과에 반환되지 않습니다.
* `Rating`: 품질을 결정하는 사용자의 권한을 나타냅니다. 기계 번역 결과의 등급은 5입니다. 익명으로 제공된 번역은 일반적으로 1에서 4까지의 등급을 가합니다. 정식으로 제공된 번역은 일반적으로 6에서 10까지의 등급을 가합니다.
* `Count`: 이 등급을 값는 이 번역이 선택된 횟수입니다. 자동으로 변환된 응답의 값은 0입니다.
* `TranslatedText`: 번역된 텍스트입니다.


### <a name="response-class-status-200"></a>응답 클래스(상태 200)

문자열

응답 내용 유형: 응용 프로그램/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|:--|
|권한 부여  |(empty)    |필드와 헤더가 `appid` `Ocp-Apim-Subscription-Key` 모두 비어 있는 경우 필요합니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|문자열|
|Ocp-Apim-Subscription-Key|(empty)  |필드와 헤더가 `appid` `Authorization` 모두 비어 있는 경우 필요합니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못된 자격 증명입니다.|
|500    |서버 오류 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [번역기로 마이그레이션 텍스트 API v3](../migrate-to-v3.md)


