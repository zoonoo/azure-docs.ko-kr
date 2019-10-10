---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: Translator Text API v 2.0에 대 한 참조 설명서입니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242492"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> 이 버전의 Translator Text API는 더 이상 사용되지 않습니다. [Translator Text API 버전 3에 대 한 설명서를 봅니다](v3-0-reference.md).

Translator Text API 버전 2는 다국어 사용자 환경을 제공 하기 위해 앱, 웹 사이트, 도구 또는 기타 솔루션에 원활 하 게 통합할 수 있습니다. 산업 표준에 따라 언어 번역 및 기타 언어 관련 작업 (예: 텍스트 언어 검색 및 텍스트 음성)을 수행 하기 위해 모든 하드웨어 플랫폼 및 운영 체제에서 사용할 수 있습니다. 자세한 내용은 [Translator Text API](../translator-info-overview.md)를 참조 하세요.

## <a name="getting-started"></a>시작
Translator Text API에 액세스 하려면 [Microsoft Azure에 등록](../translator-text-how-to-signup.md)해야 합니다.

## <a name="authentication"></a>인증 
Translator Text API에 대 한 모든 호출에는 인증을 위한 구독 키가 필요 합니다. API는 세 가지 인증 방법을 지원 합니다.

- 액세스 토큰입니다. 인증 서비스에 대 한 POST 요청을 수행 하 여 액세스 토큰을 만들려면 구독 키를 사용 합니다. 자세한 내용은 토큰 서비스 설명서를 참조하세요. @No__t-0 헤더 또는 `access_token` 쿼리 매개 변수를 사용 하 여 액세스 토큰을 번역기 서비스로 전달 합니다. 액세스 토큰은 10분 동안 유효합니다. 10 분 마다 새 액세스 토큰을 획득 하 고 10 분 동안 반복 된 요청에 대해 동일한 액세스 토큰을 계속 사용 합니다.
- 직접 사용 되는 구독 키입니다. Translator Text API에 대 한 요청에 포함 된 `Ocp-Apim-Subscription-Key` 헤더의 값으로 구독 키를 전달 합니다. 구독 키를 직접 사용 하는 경우에는 토큰 인증 서비스를 호출 하 여 액세스 토큰을 만들 필요가 없습니다.
- [Azure Cognitive Services 다중 서비스 구독](https://azure.microsoft.com/pricing/details/cognitive-services/)입니다. 이 메서드를 사용 하면 단일 비밀 키를 사용 하 여 여러 서비스에 대 한 요청을 인증할 수 있습니다.
다중 서비스 비밀 키를 사용 하는 경우 요청에 인증 헤더를 두 개 포함 해야 합니다. 첫 번째 헤더는 비밀 키를 전달 합니다. 두 번째 헤더는 구독과 연결 된 지역을 지정 합니다.
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

지역은 다중 서비스 텍스트 API 구독에 필요 합니다. 선택한 지역은 다중 서비스 구독 키를 사용 하는 경우 텍스트 번역에 사용할 수 있는 유일한 지역입니다. Azure Portal에서 다중 서비스 구독에 등록할 때 선택한 것과 동일한 지역 이어야 합니다.

사용 가능한 영역은 `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, 0, 1, 2, 3, 4, 5, 6입니다.

구독 키 및 액세스 토큰은 보기에서 숨겨야 하는 암호입니다.

## <a name="profanity-handling"></a>욕설 처리
일반적으로 변환기 서비스는 원본에 있는 비속어를 유지 합니다. 비 속한 단어가 문화권에 따라 달라 지는 것이 좋습니다. 따라서 대상 언어의 불경 수준을 늘리거나 줄일 수 있습니다.

원본 텍스트에 있는 경우에도 변환의 사용 방지를 방지 하려는 경우이를 지 원하는 메서드에 대 한 사용 방지 필터링 옵션을 사용할 수 있습니다. 옵션을 사용 하면 사용 되지 않는 사용 금지를 표시할지 아니면 적절 한 태그로 표시할지 선택할 수 있으며, 대상에서 사용 금지를 허용할지 여부를 선택할 수 있습니다. @No__t-0의 허용 되는 값은 `NoAction` (기본값), `Marked` 및 `Deleted`입니다.


|ProfanityAction    |작업 |예제 소스 (일본어)  |변환 예 (영어)  |
|:--|:--|:--|:--|
|NoAction   |기본. 옵션을 설정하지 않는 것과 같습니다. 욕설이 원본에서 대상으로 전달됩니다.        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |비 속한 단어는 XML 태그 \<profanity > 및 \</불경 >로 둘러싸여 있습니다.       |彼はジャッカスです。 |그는 @no__t > jackass @ no__t-1/불경 >입니다.  |
|삭제됨    |욕설 단어가 바뀌지 않고 출력에서 제거됩니다.     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>번역에서 콘텐츠 제외
HTML (`contentType=text/html`)과 같은 태그를 사용 하 여 콘텐츠를 변환 하는 경우 번역에서 특정 콘텐츠를 제외 하는 것이 유용할 수 있습니다. 특성 `class=notranslate`를 사용하여 원래 언어로 유지해야 하는 콘텐츠를 지정할 수 있습니다. 다음 예제에서는 첫 번째 `div` 요소의 내용이 변환 되지 않지만 두 번째 `div` 요소의 콘텐츠는 변환 됩니다.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>구현 참고 사항
한 언어에서 다른 언어로 텍스트 문자열을 번역합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Translate`입니다.

**반환 값:** 번역 된 텍스트를 나타내는 문자열입니다.

이전에 `AddTranslation` 또는 `AddTranslationArray`을 사용 하 여 동일한 원본 문장의 등급이 5 이상인 번역을 입력 한 경우에는-2 @no__t 사용자의 시스템에 사용할 수 있는 최상위 항목만 반환 합니다. "동일 원본 문장"은 문장의 끝에 대문자, 공백, 태그 값 및 문장 부호를 제외 하 고 정확히 동일한 (100% 일치)를 의미 합니다. 등급이 5 이상인 등급이 저장 되지 않은 경우 반환 되는 결과는 Microsoft Translator에서 자동으로 변환 됩니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)

string

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명    |매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid  |(empty)    |필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|text|(empty)   |필수. 변환할 텍스트를 나타내는 문자열입니다. 텍스트는 1만 자를 초과할 수 없습니다.|query|string|
|from|(empty)   |(선택 사항) 번역할 텍스트의 언어 코드를 나타내는 문자열입니다. 예를 들어 영어의 경우는 en입니다.|query|string|
|다음으로 변경:|(empty) |필수. 텍스트를 변환할 언어의 코드를 나타내는 문자열입니다.|query|string|
|contentType|(empty)    |(선택 사항) 변환되는 텍스트의 형식입니다. 지원 되는 형식은 `text/plain` (기본값) 및 `text/html`입니다. 모든 HTML 요소는 올바른 형식의 완전 한 요소가 되어야 합니다.|query|string|
|category|(empty)   |(선택 사항) 번역의 범주 (도메인)가 포함 된 문자열입니다. 기본값은 `general`입니다.|query|string|
|Authorization|(empty)  |@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)  |@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|


### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>구현 참고 사항
여러 소스 텍스트에 대 한 번역을 검색 합니다.

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

이러한 요소는 `TranslateArrayRequest`입니다.


* `AppId`: 필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `AppId` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.
* `From`: (선택 사항) 번역할 텍스트의 언어 코드를 나타내는 문자열입니다. 이 필드를 비워 두면 자동 언어 검색 결과가 응답에 포함 됩니다.
* `Options`: (선택 사항) 다음 값을 포함 하는 @no__t 개체입니다. 모든 옵션 이며 기본값은 가장 일반적인 설정입니다. 지정된 요소는 사전순으로 나열되어야 합니다.
    - `Category`: 번역의 범주 (도메인)가 포함 된 문자열입니다. 기본값은 `general`입니다.
    - `ContentType`: 변환되는 텍스트의 형식입니다. 지원 되는 형식은 `text/plain` (기본값), `text/xml`, `text/html`입니다. 모든 HTML 요소는 올바른 형식의 완전 한 요소가 되어야 합니다.
    - `ProfanityAction`: 앞에서 설명한 대로 profanities 처리 되는 방법을 지정 합니다. 허용 되는 값은 `NoAction` (기본값), `Marked`, `Deleted`입니다.
    - `State`: 요청 및 응답을 상호 연결 하는 데 도움이 되는 사용자 상태입니다. 동일한 콘텐츠가 응답에서 반환 됩니다.
    - `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 기본값: `all`.
    - `User`: 결과를 이 사용자를 기준으로 필터링합니다. 기본값: `all`.
* `Texts`: 필수. 변환할 텍스트를 포함 하는 배열입니다. 모든 문자열이 동일한 언어로 되어 있어야 합니다. 번역할 모든 텍스트의 합계는 1만 자를 초과할 수 없습니다. 배열 요소의 최대 수는 2000입니다.
* `To`: 필수. 텍스트를 변환할 언어의 코드를 나타내는 문자열입니다.

선택적 요소는 생략할 수 있습니다. @No__t-0의 직계 자식인 요소는 사전순으로 나열 되어야 합니다.

@No__t-0 메서드는 `Content-Type`에 대 한 `application/xml` 또는 `text/xml`를 허용 합니다.

**반환 값:** `TranslateArrayResponse` 배열입니다. 각 `TranslateArrayResponse`에는 다음과 같은 요소가 있습니다.

* `Error`: 오류가 발생 하는 경우 오류를 나타냅니다. 그렇지 않은 경우, Null로 설정됩니다.
* `OriginalSentenceLengths`: 소스 텍스트에서 각 문장의 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장 수를 나타냅니다.
* `TranslatedText`: 번역된 텍스트입니다.
* `TranslatedSentenceLengths`: 번역 된 텍스트에서 각 문장의 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장 수를 나타냅니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 도움이 되는 사용자 상태입니다. 요청과 동일한 콘텐츠를 반환 합니다.

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

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)
성공적인 응답에는 앞에서 설명한 형식으로 `TranslateArrayResponse` 배열의 배열이 포함 됩니다.

string

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|Authorization|(empty)  |@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)|@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드   |Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다. 일반적인 오류는 다음과 같습니다. <ul><li>배열 요소는 비워 둘 수 없습니다.</li><li>범주가 잘못 되었습니다.</li><li>언어가 잘못 되었습니다.</li><li>에 대 한 언어가 잘못 되었습니다.</li><li>요청에 너무 많은 요소가 포함 되어 있습니다.</li><li>From 언어는 지원 되지 않습니다.</li><li>To 언어는 지원 되지 않습니다.</li><li>변환 요청의 데이터가 너무 많습니다.</li><li>HTML의 형식이 잘못 되었습니다.</li><li>변환 요청에서 너무 많은 문자열이 전달 되었습니다.</li></ul>|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>구현 참고 사항
전달 된 `locale` 언어로 지역화 된 `languageCodes` 매개 변수로 전달 된 언어에 대 한 이름을 검색 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`입니다.

요청 본문에는 이름을 검색할 ISO 639-1 언어 코드를 나타내는 문자열 배열이 포함 되어 있습니다. 예를 들면 다음과 같습니다.

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**반환 값:** 요청 된 언어로 지역화 된 Translator 서비스에서 지원 되는 언어 이름을 포함 하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)
요청 된 언어로 지역화 된 변환기 서비스에서 지 원하는 언어 이름을 포함 하는 문자열 배열입니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|로캘(locale)|(empty) |필수. 언어 이름을 지역화 하는 데 사용 되는 다음 중 하나를 나타내는 문자열입니다. <ul><li>언어 및 ISO 3166 2 문자 대문자 하위 문화권 코드와 관련 된 ISO 639 2 문자 소문자 문화권 코드의 조합입니다. <li>ISO 639 소문자 문화권 코드 자체입니다.|query|string|
|Authorization|(empty)  |@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)  |@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>구현 참고 사항
번역 서비스에서 지 원하는 언어를 나타내는 언어 코드의 목록을 가져옵니다.  `Translate` 및 `TranslateArray`는 이러한 임의의 두 언어 간을 번역할 수 있습니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`입니다.

**반환 값:** Translator 서비스에서 지 원하는 언어 코드를 포함 하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)
Translator 서비스에서 지 원하는 언어 코드를 포함 하는 문자열 배열입니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|Authorization|(empty)  |@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)|@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>구현 참고 사항
음성 합성에 사용할 수 있는 언어를 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`입니다.

**반환 값:** Translator 서비스의 음성 합성에 대해 지원 되는 언어 코드를 포함 하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)
Translator 서비스의 음성 합성에 대해 지원 되는 언어 코드를 포함 하는 문자열 배열입니다.

string

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|Authorization|(empty)|@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)|@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|
 
### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401|자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>구현 참고 사항
원하는 언어로 음성으로 전달 된 텍스트의 WAV 또는 MP3 스트림을 반환 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Speak`입니다.

**반환 값:** 원하는 언어로 말한 전달 된 텍스트의 WAV 또는 MP3 스트림입니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)

binary

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|text|(empty)   |필수. 지정 된 언어로 스트림에 대해 말할 문장이 하나 이상 포함 된 문자열입니다. 텍스트는 2000 자를 초과 하면 안 됩니다.|query|string|
|language|(empty)   |필수. 텍스트를 말할 언어의 지원 되는 언어 코드를 나타내는 문자열입니다. 코드는-0 @no__t 메서드에서 반환 하는 코드 중 하나 여야 합니다.|query|string|
|format|(empty)|(선택 사항) Content-type ID를 지정 하는 문자열입니다. 현재 `audio/wav` 및 `audio/mp3`를 사용할 수 있습니다. 기본값은 `audio/wav`입니다.|query|string|
|options|(empty)    |(선택 사항) 합성 된 음성의 속성을 지정 하는 문자열입니다.<ul><li>`MaxQuality` 및 `MinSize`은 오디오 신호의 품질을 지정 합니다. `MaxQuality`은 최고 품질을 제공 합니다. `MinSize`은 가장 작은 파일 크기를 제공 합니다. 기본값은 `MinSize`입니다.</li><li>`female` 및 `male`은 원하는 음성의 성별을 지정 합니다. 기본값은 `female`입니다. 세로 막대 (<code>\|</code>)를 사용 하 여 여러 옵션을 포함 합니다. 예: `MaxQuality|Male`</li></li></ul>  |query|string|
|Authorization|(empty)|@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)  |@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>구현 참고 사항
텍스트 섹션의 언어를 식별 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Detect`입니다.

**반환 값:** 텍스트에 대 한 2 자 언어 코드를 포함 하는 문자열입니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)

string

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)  |필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|text|(empty)|필수. 해당 언어를 식별할 텍스트가 포함 된 문자열입니다. 텍스트는 1만 자를 초과 하면 안 됩니다.|query|  string|
|Authorization|(empty)|@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key  |(empty)    |@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>구현 참고 사항

문자열 배열에서 언어를 식별 합니다. 각 개별 배열 요소의 언어를 독립적으로 검색 하 고 배열의 각 행에 대 한 결과를 반환 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`입니다.

요청 본문의 형식은 다음과 같습니다.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

텍스트는 1만 자를 초과할 수 없습니다.

**반환 값:** 입력 배열의 각 행에 대 한 2 자 언어 코드를 포함 하는 문자열 배열입니다.

응답 본문의 형식은 다음과 같습니다.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)
`DetectArray`이 성공 했습니다. 입력 배열의 각 행에 대 한 2 자 언어 코드를 포함 하는 문자열 배열을 반환 합니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|Authorization|(empty)|@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)|@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>구현 참고 사항

> [!IMPORTANT]
> **사용 중단 정보:** 2018 년 1 월 31 일 이후에는이 메서드가 새 문장 제출을 수락 하지 않습니다. 오류 메시지를 받게 됩니다. CTF (공동 작업 변환 프레임 워크) 변경에 대 한 공지를 참조 하세요.

번역 메모리에 번역을 추가합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`입니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)

string

응답 콘텐츠 형식: application: xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식   |
|:--|:--|:--|:--|:--|
|appid|(empty)|필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|originalText|(empty)|필수. 변환할 텍스트를 포함 하는 문자열입니다. 문자열의 최대 길이는 1000 자입니다.|query|string|
|translatedText|(empty) |필수. 대상 언어로 번역 된 텍스트를 포함 하는 문자열입니다. 문자열의 최대 길이는 2000 자입니다.|query|string|
|from|(empty)   |필수. 텍스트의 원래 언어에 대 한 언어 코드를 나타내는 문자열입니다. 예를 들어 영어의 경우 en, 독일의 경우 de입니다.|query|string|
|다음으로 변경:|(empty)|필수. 텍스트를 변환할 언어의 언어 코드를 나타내는 문자열입니다.|query|string|
|rating|(empty) |(선택 사항) 문자열의 품질 등급을 나타내는 정수입니다. 값은-10에서 10 사이입니다. 기본값은 1입니다.|query|integer|
|contentType|(empty)    |(선택 사항) 변환되는 텍스트의 형식입니다. 지원 되는 형식은 `text/plain` 및 `text/html`입니다. 모든 HTML 요소는 올바른 형식의 완전 한 요소가 되어야 합니다.    |query|string|
|category|(empty)|(선택 사항) 번역의 범주 (도메인)가 포함 된 문자열입니다. 기본값은 `general`입니다.|query|string|
|사용자|(empty)|필수. 제출 작성기를 추적 하는 데 사용 되는 문자열입니다.|query|string|
|uri|(empty)|(선택 사항) 변환의 콘텐츠 위치를 포함 하는 문자열입니다.|query|string|
|Authorization|(empty)|@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.  |머리글|string|
|Ocp-Apim-Subscription-Key|(empty)|@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|410|`AddTranslation`은 더 이상 지원 되지 않습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>구현 참고 사항

> [!IMPORTANT]
> **사용 중단 정보:** 2018 년 1 월 31 일 이후에는이 메서드가 새 문장 제출을 수락 하지 않습니다. 오류 메시지를 받게 됩니다. CTF (공동 작업 변환 프레임 워크) 변경에 대 한 공지를 참조 하세요.

번역 메모리에 번역 배열을 추가 합니다. 이 메서드는 `AddTranslation`의 배열 버전입니다.

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

이러한 요소는 `AddtranslationsRequest`입니다.

* `AppId`: 필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `AppId` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.
* `From`: 필수. 소스 언어의 언어 코드를 포함 하는 문자열입니다. `GetLanguagesForTranslate` 메서드에서 반환되는 언어 중 하나여야 합니다.
* `To`: 필수. 대상 언어의 언어 코드를 포함 하는 문자열입니다. `GetLanguagesForTranslate` 메서드에서 반환되는 언어 중 하나여야 합니다.
* `Translations`: 필수. 번역 메모리에 추가할 번역의 배열입니다. 각 번역에는 `OriginalText`, `TranslatedText`, `Rating`가 포함 되어야 합니다. 각 @no__t의 최대 크기는-0이 고 `TranslatedText`은 1000 자입니다. 모든 `OriginalText` 및 `TranslatedText` 요소의 합계는 1만 자를 초과할 수 없습니다. 배열 요소의 최대 개수는 100입니다.
* `Options`: 필수. @No__t-0, `ContentType`, `Uri` 및 `User`을 포함 한 옵션 집합입니다. `User`이 필요 합니다. `Category`, `ContentType` 및 `Uri`는 선택 사항입니다. 지정된 요소는 사전순으로 나열되어야 합니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)
`AddTranslationArray` 메서드가 성공 했습니다. 

2018 년 1 월 31 일 이후에는 문장 제출이 허용 되지 않습니다. 이 서비스는 오류 코드 410으로 응답합니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|Authorization|(empty)|@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)|@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|410    |`AddTranslation`은 더 이상 지원 되지 않습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>구현 참고 사항
텍스트의 섹션을 문장으로 나누고 각 문장의 길이를 포함 하는 배열을 반환 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`입니다.

**반환 값:** 문장의 길이를 나타내는 정수의 배열입니다. 배열의 길이는 문장의 수를 나타냅니다. 값은 각 문장의 길이를 나타냅니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)
문장의 길이를 나타내는 정수의 배열입니다. 배열의 길이는 문장의 수를 나타냅니다. 값은 각 문장의 길이를 나타냅니다.

integer

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)  |필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query| string|
|text|(empty)   |필수. 문장으로 분할할 텍스트를 나타내는 문자열입니다. 텍스트의 최대 크기는 1만 자입니다.|query|string|
|language   |(empty)    |필수. 입력 텍스트의 언어 코드를 나타내는 문자열입니다.|query|string|
|Authorization|(empty)|@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.   |머리글|string|
|Ocp-Apim-Subscription-Key|(empty)|@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401|자격 증명이 잘못 되었습니다.|
|500|서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>구현 참고 사항
저장소 및 MT 엔진에서 지정된 언어 쌍에 대한 번역 배열을 검색합니다. `GetTranslations`은 사용 가능한 모든 번역을 반환 한다는 점에서 `Translate`과 다릅니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`입니다.

요청 본문에는 다음과 같은 형식의 선택적 `TranslationOptions` 개체가 포함 되어 있습니다.

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

@No__t-0 개체는 다음 목록의 값을 포함 합니다. 모든 옵션 이며 기본값은 가장 일반적인 설정입니다. 지정된 요소는 사전순으로 나열되어야 합니다.

* `Category`: 번역의 범주 (도메인)가 포함 된 문자열입니다. 기본값은 `general`입니다.
* `ContentType`: 유일 하 게 지원 되는 옵션과 기본값은 `text/plain`입니다.
* `IncludeMultipleMTAlternatives`: 두 개 이상의 대체를 MT 엔진에서 반환할지 여부를 지정 하는 부울 플래그입니다. 유효한 값은 `true` 및 `false` (대/소문자 구분)입니다. 기본값은 1만을 반환 하는 `false`입니다. 플래그를 `true`으로 설정 하면 CTF (공동 작업 변환 프레임 워크)와 완전히 통합 된 인공 대안을 만들 수 있습니다. 이 기능을 사용 하면 디코더의 *n*-최적 목록에서 인공 대안을 추가 하 여 ctf에서 번역이 없는 문장에 대 한 대체 (no)를 반환할 수 있습니다.
    - 평점. 등급은 다음과 같이 적용 됩니다. 
         - 최고 품질의 자동 번역은 5등급입니다.
       - CTF의 대안은 검토자의 권한을 반영 합니다. 범위는-10에서 + 10 사이입니다.
       - 자동으로 생성 된 (*n*최적) 번역 대안은 등급은 0이 고 일치 정도는 100입니다.
    - 대안의 수입니다. 반환 되는 대체 수는 `maxTranslations`에 지정 된 값 만큼 높을 수 있지만 더 낮을 수 있습니다.
    - 언어 쌍. 이 기능은 두 방향 모두에서 중국어 간체와 중국어 번체 간 번역에는 사용할 수 없습니다. Microsoft Translator에서 지 원하는 다른 모든 언어 쌍에 사용할 수 있습니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 도움이 되는 사용자 상태입니다. 동일한 콘텐츠가 응답에서 반환 됩니다.
* `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 값을 설정 하지 않으면 기본값은 `all`입니다.
* `User`: 결과를 이 사용자를 기준으로 필터링합니다. 값을 설정 하지 않으면 기본값은 `all`입니다.

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

이 응답에는 다음 값을 포함 하는 `GetTranslationsResponse` 요소가 포함 됩니다.

* `Translations`: @No__t-0 개체에 저장 된 일치 하는 항목의 배열입니다 (다음 섹션에 설명 되어 있음). 번역에는 원래 텍스트의 약간 변형 (유사 항목 일치)이 포함 될 수 있습니다. 번역은 100%는 먼저 일치 하 고, 다음은 유사 항목 일치와 일치 합니다.
* `From`: 메서드가 `From` 언어를 지정 하지 않으면이 값은 자동 언어 검색에서 제공 됩니다. 그렇지 않으면 지정 된 @no__t 0 언어가 됩니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 도움이 되는 사용자 상태입니다. @No__t-0 매개 변수에 제공 된 값을 포함 합니다.

@No__t-0 개체는 다음 값으로 구성 됩니다.

* `Error`: 특정 입력 문자열에 대 한 오류가 발생 하는 경우 오류 코드입니다. 그렇지 않으면이 필드는 비어 있습니다.
* `MatchDegree`: 입력 텍스트가 저장소에 있는 원본 텍스트와 얼마나 일치 하는지 나타냅니다. 시스템은 입력 문장과 일치하는 문장을 저장소에서 찾습니다. 정확하지 않은 일치도 검색됩니다. 반환 되는 값의 범위는 0에서 100입니다. 여기서 0은 유사성은 없고 100은 대/소문자를 구분 하는 정확히 일치 합니다.
* `MatchedOriginalText`: 이 결과에서 일치된 원본 텍스트입니다. 이 값은 일치 하는 원본 텍스트가 입력 텍스트와 다른 경우에만 반환 됩니다. 유사 항목 일치의 원본 텍스트를 반환 하는 데 사용 됩니다. Microsoft Translator 결과에 대해서는이 값이 반환 되지 않습니다.
* `Rating`: 품질 결정 담당자의 권한을 나타냅니다. 기계 번역 결과의 등급은 5입니다. 익명으로 제공 된 번역은 일반적으로 1에서 4 사이의 등급을 갖습니다. 정식으로 제공 되는 번역의 등급은 일반적으로 6부터 10 까지입니다.
* `Count`: 이 등급의 현재 번역이 선택된 횟수입니다. 자동으로 번역 된 응답의 값은 0입니다.
* `TranslatedText`: 번역된 텍스트입니다.

### <a name="response-class-status-200"></a>Response 클래스 (상태 200)
앞에서 설명한 형식의 `GetTranslationsResponse` 개체입니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수. @No__t-0 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용 하는 경우 `appid` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.|query|string|
|text|(empty)|필수. 변환할 텍스트를 나타내는 문자열입니다. 텍스트의 최대 크기는 1만 자입니다.|query|string|
|from|(empty)|필수. 번역할 텍스트의 언어 코드를 나타내는 문자열입니다.|query|string|
|다음으로 변경: |(empty)    |필수. 텍스트를 변환할 언어의 언어 코드를 나타내는 문자열입니다.|query|string|
|maxTranslations|(empty)|필수. 반환할 최대 번역 수를 나타내는 정수입니다.|query|integer|
|Authorization| (empty)|@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|string|  머리글|
|Ocp-Apim-Subscription-Key|(empty)  |@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청에 대 한 대략적인 날짜 & 시간을 제공 하 고, 응답 헤더에 포함 된 요청 ID `X-MS-Trans-Info`을 제공 하세요.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>구현 참고 사항
여러 소스 텍스트에 대 한 여러 번역 후보를 검색 합니다.

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

`GetTranslationsArrayRequest`에는 다음 요소가 포함 됩니다.

* `AppId`: 필수. @No__t-0 헤더를 사용 하는 경우 `AppId` 필드를 비워 둡니다. 그렇지 않으면 `"Bearer" + " " + "access_token"`을 포함 하는 문자열을 포함 합니다.
* `From`: 필수. 번역할 텍스트의 언어 코드를 나타내는 문자열입니다.
* `MaxTranslations`: 필수. 반환할 최대 번역 수를 나타내는 정수입니다.
* `Options`: (선택 사항) 다음 값을 포함 하는 @no__t 개체입니다. 모든 옵션 이며 기본값은 가장 일반적인 설정입니다. 지정된 요소는 사전순으로 나열되어야 합니다.
    - `Category`: 번역의 범주 (도메인)가 포함 된 문자열입니다. 기본값은 `general`입니다.
    - `ContentType`: 유일 하 게 지원 되는 옵션과 기본값은 `text/plain`입니다.
    - `IncludeMultipleMTAlternatives`: 두 개 이상의 대체를 MT 엔진에서 반환할지 여부를 지정 하는 부울 플래그입니다. 유효한 값은 `true` 및 `false` (대/소문자 구분)입니다. 기본값은 1만을 반환 하는 `false`입니다. 플래그를 `true`으로 설정 하면 CTF (공동 작업 번역 프레임 워크)와 완전히 통합 된 번역에서 인공 대안을 생성할 수 있습니다. 이 기능을 사용 하면 디코더의 *n*-최적 목록에서 인공 대안을 추가 하 여 ctf에서 대안이 없는 문장에 대 한 대체 (no)를 반환할 수 있습니다.
        - 등급 등급은 다음과 같이 적용 됩니다.
          - 최고 품질의 자동 번역은 5등급입니다.
          - CTF의 대안은 검토자의 권한을 반영 합니다. 범위는-10에서 + 10 사이입니다.
          - 자동으로 생성 된 (*n*최적) 번역 대안은 등급은 0이 고 일치 정도는 100입니다.
        - 대안의 수입니다. 반환 되는 대체 수는 `maxTranslations`에 지정 된 값 만큼 높을 수 있지만 더 낮을 수 있습니다.
        - 언어 쌍. 이 기능은 두 방향 모두에서 중국어 간체와 중국어 번체 간 번역에는 사용할 수 없습니다. Microsoft Translator에서 지 원하는 다른 모든 언어 쌍에 사용할 수 있습니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 도움이 되는 사용자 상태입니다. 동일한 콘텐츠가 응답에서 반환 됩니다.
* `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 값을 설정 하지 않으면 기본값은 `all`입니다.
* `User`: 결과를 이 사용자를 기준으로 필터링합니다. 값을 설정 하지 않으면 기본값은 `all`입니다.
* `Texts`: 필수. 변환할 텍스트를 포함 하는 배열입니다. 모든 문자열이 동일한 언어로 되어 있어야 합니다. 번역할 모든 텍스트의 합계는 1만 자를 초과할 수 없습니다. 배열 요소의 최대 개수는 10입니다.
* `To`: 필수. 텍스트를 변환할 언어의 언어 코드를 나타내는 문자열입니다.

선택적 요소는 생략할 수 있습니다. @No__t-0의 직계 자식인 요소는 사전순으로 나열 되어야 합니다.

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

각 `GetTranslationsResponse` 요소는 다음 값을 포함 합니다.

* `Translations`: @No__t-0 개체에 저장 된 일치 하는 항목의 배열입니다 (다음 섹션에 설명 되어 있음). 번역에는 원래 텍스트의 약간 변형 (유사 항목 일치)이 포함 될 수 있습니다. 번역은 100%는 먼저 일치 하 고, 다음은 유사 항목 일치와 일치 합니다.
* `From`: 메서드가 `From` 언어를 지정 하지 않으면이 값은 자동 언어 검색에서 제공 됩니다. 그렇지 않으면 지정 된 @no__t 0 언어가 됩니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 도움이 되는 사용자 상태입니다. @No__t-0 매개 변수에 제공 된 값을 포함 합니다.

@No__t-0 개체는 다음 값을 포함 합니다.
* `Error`: 특정 입력 문자열에 대 한 오류가 발생 하는 경우 오류 코드입니다. 그렇지 않으면이 필드는 비어 있습니다.
* `MatchDegree`: 입력 텍스트가 저장소에 있는 원본 텍스트와 얼마나 일치 하는지 나타냅니다. 시스템은 입력 문장과 일치하는 문장을 저장소에서 찾습니다. 정확하지 않은 일치도 검색됩니다. 반환 되는 값의 범위는 0에서 100입니다. 여기서 0은 유사성은 없고 100은 대/소문자를 구분 하는 정확히 일치 합니다.
* `MatchedOriginalText`: 이 결과에서 일치된 원본 텍스트입니다. 이 값은 일치 하는 원본 텍스트가 입력 텍스트와 다른 경우에만 반환 됩니다. 유사 항목 일치의 원본 텍스트를 반환 하는 데 사용 됩니다. Microsoft Translator 결과에 대해서는이 값이 반환 되지 않습니다.
* `Rating`: 품질 결정 담당자의 권한을 나타냅니다. 기계 번역 결과의 등급은 5입니다. 익명으로 제공 된 번역은 일반적으로 1에서 4 사이의 등급을 갖습니다. 정식으로 제공 되는 번역의 등급은 일반적으로 6부터 10 까지입니다.
* `Count`: 이 등급의 현재 번역이 선택된 횟수입니다. 자동으로 번역 된 응답의 값은 0입니다.
* `TranslatedText`: 번역된 텍스트입니다.


### <a name="response-class-status-200"></a>Response 클래스 (상태 200)

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|Authorization  |(empty)    |@No__t-0 필드와 `Ocp-Apim-Subscription-Key` 머리글을 모두 비워 두면 필수입니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|머리글|string|
|Ocp-Apim-Subscription-Key|(empty)  |@No__t-0 필드와 `Authorization` 머리글을 모두 비워 두면 필수입니다.|머리글|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|Reason|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |자격 증명이 잘못 되었습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Translator Text API v3로 마이그레이션](../migrate-to-v3.md)


