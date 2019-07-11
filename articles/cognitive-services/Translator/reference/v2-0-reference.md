---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: Translator Text API v2.0 용 참조 설명서입니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: c18c062d5537603284acb37081ac0a4eb8d2fd20
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797811"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> 이 버전의 Translator Text API는 더 이상 사용되지 않습니다. [Translator Text API의 버전 3에 대 한 설명서를 보려면](v3-0-reference.md)합니다.

Translator Text API의 버전 2는 앱, 웹 사이트, 도구 또는 다국어 사용자 환경을 제공 하려면 다른 솔루션에 원활 하 게 통합할 수 있습니다. 사용할 수 있습니다 모든 운영 체제와 하드웨어 플랫폼에서 언어 번역 및 텍스트 언어 감지 및 텍스트 음성 변환, 등의 다른 언어와 관련 된 작업을 수행 하려면 업계 표준에 따라 합니다. 자세한 내용은 [Translator Text API](../translator-info-overview.md)합니다.

## <a name="getting-started"></a>시작
Translator Text API에 액세스 하려면 [Microsoft Azure 신청](../translator-text-how-to-signup.md)합니다.

## <a name="authentication"></a>인증 
Translator Text API에 대 한 모든 호출 인증에 대 한 구독 키가 필요 합니다. API에는 세 가지 인증 방법을 지원합니다.

- 액세스 토큰입니다. 9 단계에서 참조 하는 구독 키를 사용 하 여 인증 서비스에는 POST 요청을 수행 하 여 액세스 토큰을 만들어야 합니다. 자세한 내용은 토큰 서비스 설명서를 참조하세요. Translator 서비스 액세스 토큰을 사용 하 여 전달 된 `Authorization` 헤더 또는 `access_token` 쿼리 매개 변수입니다. 액세스 토큰은 10분 동안 유효합니다. 새 액세스 토큰을 10 분 간격으로 가져오고 유지 동일한 액세스를 사용 하 여 반복 되는 요청에 대 한 토큰은 10 분 동안.
- 직접 사용 하는 구독 키입니다. 구독 키의 값으로 전달 된 `Ocp-Apim-Subscription-Key` Translator Text API에 요청을 사용 하 여 포함 된 헤더입니다. 구독 키를 직접 사용 하는 경우에 액세스 토큰을 만드는 데 토큰 인증 서비스를 호출할 필요가 없습니다.
- [Azure Cognitive Services 다중 서비스 구독](https://azure.microsoft.com/pricing/details/cognitive-services/)합니다. 이 메서드를 사용 하면 여러 서비스에 대 한 요청을 인증 하는 단일 비밀 키를 사용할 수 있습니다.
다중 서비스 비밀 키를 사용 하면 요청을 사용 하 여 두 가지 인증 헤더를 포함 해야 합니다. 첫 번째 머리글 비밀 키를 전달합니다. 두 번째 헤더에는 구독과 연결 된 지역을 지정 합니다.
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

지역이 다중 서비스 Text API 구독은 필요 합니다. 선택한 지역이 유일한 지역 다중 서비스 등록 키를 사용 하면 텍스트 번역에 사용할 수 있습니다. Azure portal에서 다중 서비스 구독에 등록할 때 선택한 동일한 지역에 되도록 해야 합니다.

사용 가능한 지역은 어디 인가요 `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`를 `eastus2`, `japaneast`를 `northeurope`를 `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`를 `westeurope`합니다 `westus`, 및 `westus2`합니다.

구독 키 및 액세스 토큰은 보기에서 숨겨야 하는 암호입니다.

## <a name="profanity-handling"></a>욕설 처리
일반적으로 Translator 서비스는 원본에 존재 하는 불경 한 언어를 유지 합니다. 불경 한 언어 및 컨텍스트는 단어 불쾌 수준의 문화권에 따라 다릅니다. 따라서 대상 언어에서 불경 한 언어가 수준의 증가 하거나 감소 수 없습니다.

소스 텍스트에 있을 때에 번역에 불경 한 언어를 방지 하려는 경우 지원 되는 메서드에 대 한 옵션 필터링 욕설을 사용할 수 있습니다. 옵션을 사용 하면 삭제 또는 적절 한 태그를 사용 하 여 표시 된 불경 한 언어 참조 것인지 또는 대상에는 불경 한 언어가 허용 것인지 선택할 수 있습니다. 허용 되는 값 `ProfanityAction` 됩니다 `NoAction` (기본값) 이면 `Marked`, 및 `Deleted`합니다.


|ProfanityAction    |작업 |예제 원본 (일본어)  |예제 변환 (영어)  |
|:--|:--|:--|:--|
|NoAction   |기본. 옵션을 설정하지 않는 것과 같습니다. 욕설이 원본에서 대상으로 전달됩니다.        |彼はジャッカスです。     |He is a jackass.   |
|Marked     |불쾌 한 단어가 XML 태그로 둘러싸입니다 \<불경 한 언어 > 및 \</profanity >.       |彼はジャッカスです。 |담당 하는 \<불경 한 언어 > jackass\</profanity >.  |
|삭제됨    |욕설 단어가 바뀌지 않고 출력에서 제거됩니다.     |彼はジャッカスです。 |He is a.   |

    
## <a name="excluding-content-from-translation"></a>번역에서 콘텐츠 제외
HTML과 같은 태그를 사용 하 여 콘텐츠를 변환할 때 (`contentType=text/html`), 번역에서 특정 콘텐츠를 제외할 경우에 따라 유용 합니다. 특성 `class=notranslate`를 사용하여 원래 언어로 유지해야 하는 콘텐츠를 지정할 수 있습니다. 다음 예에서 첫 번째에서 콘텐츠 `div` 없습니다 번역할 요소, 두 번째의 콘텐츠를 제외한 `div` 요소 변환 됩니다.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>구현 참고 사항
한 언어에서 다른 언어로 텍스트 문자열을 번역합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Translate`입니다.

**반환 값:** 번역 된 텍스트를 나타내는 문자열입니다.

이전에 사용한 `AddTranslation` 또는 `AddTranslationArray` 5 이상의 같은 원본 문장에 대 한 등급을 사용 하 여 번역을 입력 하려면 `Translate` 시스템에 사용할 수 있는 상위 선택만를 반환 합니다. "같은 원본 문장" 대/소문자, 공백, 태그 값 및 문장의 끝 문장 부호를 제외 하 고 정확 하 게 동일한 (100% 일치)을 의미합니다. 등급이 지정 되지 않은 등급을 5 이상에 저장 된 경우 반환 된 결과에서 Microsoft Translator 자동 번역 됩니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)

string

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|Description    |매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid  |(empty)    |필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|text|(empty)   |필수 요소. 변환할 텍스트를 나타내는 문자열입니다. 텍스트는 10,000 개 보다 많은 문자를 포함할 수 없습니다.|쿼리|string|
|from|(empty)   |선택 사항입니다. 번역할 텍스트의 언어 코드를 나타내는 문자열입니다. 예를 들어 영어의 경우는 en입니다.|쿼리|string|
|다음으로 변경:|(empty) |필수 요소. 에 텍스트를 번역 언어의 코드를 나타내는 문자열입니다.|쿼리|string|
|contentType|(empty)    |선택 사항입니다. 변환되는 텍스트의 형식입니다. 지원 되는 형식은 `text/plain` (기본값) 및 `text/html`합니다. 모든 HTML 요소는 올바른 형식의 전체 요소 해야 합니다.|쿼리|string|
|category|(empty)   |선택 사항입니다. 번역 범주 (도메인)를 포함 하는 문자열입니다. 기본값은 `general`입니다.|쿼리|string|
|Authorization|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|


### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>구현 참고 사항
여러 원본 텍스트에 대 한 번역을 검색합니다.

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

이러한 요소에는 `TranslateArrayRequest`:


* `AppId`: 필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `AppId` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.
* `From`: 선택 사항입니다. 번역할 텍스트의 언어 코드를 나타내는 문자열입니다. 이 필드를 빈 채로 자동 언어 감지의 결과 응답에 포함 됩니다.
* `Options`: 선택 사항입니다. `Options` 다음 값이 들어 있는 개체입니다. 모두 선택 사항 하 고 가장 일반적인 설정을 기본값으로 설정 합니다. 지정된 요소는 사전순으로 나열되어야 합니다.
    - `Category`: 번역 범주 (도메인)를 포함 하는 문자열입니다. 기본값은 `general`입니다.
    - `ContentType`: 변환되는 텍스트의 형식입니다. 지원 되는 형식은 `text/plain` (기본값) 이면 `text/xml`, 및 `text/html`합니다. 모든 HTML 요소는 올바른 형식의 전체 요소 해야 합니다.
    - `ProfanityAction`: 앞에서 설명한 대로 profanities를 처리 하는 방법을 지정 합니다. 허용 되는 값은 `NoAction` (기본값) 이면 `Marked`, 및 `Deleted`합니다.
    - `State`: 요청 및 응답을 상호 연결 하는 데 사용자 상태입니다. 동일한 콘텐츠를 응답에 반환 됩니다.
    - `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 기본값: `all`.
    - `User`: 결과를 이 사용자를 기준으로 필터링합니다. 기본값: `all`.
* `Texts`: 필수 요소. 번역에 대 한 텍스트가 포함 된 배열입니다. 동일한 언어의 모든 문자열 이어야 합니다. 변환할 모든 텍스트 총 10,000 자를 초과할 수 없습니다. 배열 요소의 최대 개수는 2,000 개입니다.
* `To`: 필수 요소. 에 텍스트를 번역 언어의 코드를 나타내는 문자열입니다.

선택적 요소를 생략할 수 있습니다. 요소를 직접 자식의 `TranslateArrayRequest` 알파벳 순서로 나열 되어야 합니다.

합니다 `TranslateArray` 메서드에서 `application/xml` 하거나 `text/xml` 에 대 한 `Content-Type`합니다.

**반환 값:** `TranslateArrayResponse` 배열입니다. 각 `TranslateArrayResponse` 에 이러한 요소가 있습니다.

* `Error`: 오류가 발생 한 경우 오류를 나타냅니다. 그렇지 않은 경우, Null로 설정됩니다.
* `OriginalSentenceLengths`: 원본 텍스트의 각 문장의 길이 나타내는 정수의 배열입니다. 배열의 길이는 문장 수를 나타냅니다.
* `TranslatedText`: 번역된 텍스트입니다.
* `TranslatedSentenceLengths`: 각 문장에 번역 된 텍스트의 길이 나타내는 정수의 배열입니다. 배열의 길이는 문장 수를 나타냅니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 사용자 상태입니다. 요청와 동일한 콘텐츠를 반환합니다.

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

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)
성공적인 응답의 배열을 포함 `TranslateArrayResponse` 앞부분에 설명 된 형식의 배열입니다.

string

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|Authorization|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드   |이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다. 일반적인 오류는 다음과 같습니다. <ul><li>배열 요소는 비워둘 수 없습니다.</li><li>잘못 된 범주입니다.</li><li>언어에서 잘못 되었습니다.</li><li>언어를 올바르지 않습니다.</li><li>요청에 너무 많은 요소가 있습니다.</li><li>From 언어 지원 되지 않습니다.</li><li>에 언어 지원 되지 않습니다.</li><li>변환 요청에 데이터가 너무 많습니다.</li><li>HTML 올바른 형식이 아닙니다.</li><li>너무 많은 문자열 변환 하는 요청에 전달 되었습니다.</li></ul>|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>구현 참고 사항
매개 변수로 전달 된 언어의 이름을 검색 `languageCodes`에 전달 된 지역화 된 `locale` 언어입니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`입니다.

요청 본문에는 친숙 한 이름을 검색 하는 ISO 639-1 언어 코드를 나타내는 문자열 배열을 포함 합니다. 예를 들면 다음과 같습니다.

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**반환 값:** 요청한 언어로 지역화 Translator 서비스에서 지 원하는 언어 이름을 포함 하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)
요청한 언어로 지역화 Translator 서비스에서 지 원하는 언어 이름을 포함 하는 문자열 배열입니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|로캘(locale)|(empty) |필수 요소. 언어 이름을 지역화를 사용 하는 다음 중 하나를 나타내는 문자열입니다. <ul><li>언어와 연관 된 ISO 639 두 문자의 소문자 문화권 코드와 ISO 3166 두 문자의 대문자 하위 문화권 코드를 조합 합니다. <li>자체적으로 ISO 639 소문자 문화권 코드입니다.|쿼리|string|
|Authorization|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>구현 참고 사항
번역 서비스를 지 원하는 언어를 나타내는 언어 코드의 목록을 가져옵니다.  `Translate` 및 `TranslateArray`는 이러한 임의의 두 언어 간을 번역할 수 있습니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`입니다.

**반환 값:** Translator 서비스에서 지 원하는 언어 코드를 포함 하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)
Translator 서비스에서 지 원하는 언어 코드를 포함 하는 문자열 배열입니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|Authorization|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>구현 참고 사항
음성 합성에 사용할 수 있는 언어를 검색합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`입니다.

**반환 값:** 음성 합성에 Translator 서비스에서 지원 되는 언어 코드를 포함 하는 문자열 배열입니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)
음성 합성에 Translator 서비스에서 지원 되는 언어 코드를 포함 하는 문자열 배열입니다.

string

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|Authorization|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|
 
### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401|잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>구현 참고 사항
원하는 언어의 음성 전달 된 텍스트의 WAV 또는 MP3 스트림을 반환 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Speak`입니다.

**반환 값:** 원하는 언어의 음성 전달 된 텍스트의 WAV 또는 MP3 스트림.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)

binary

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|text|(empty)   |필수 요소. 스트림으로 지정 된 언어로 읽을 하나 이상의 문장을 포함 하는 문자열입니다. 텍스트는 2,000 자를 초과 하지 않아야 합니다.|쿼리|string|
|language|(empty)   |필수 요소. 텍스트 읽어주기를 언어의 지원 되는 언어 코드를 나타내는 문자열입니다. 코드는 확장 메서드에서 반환한 코드 중 하나 여야 합니다 `GetLanguagesForSpeak`합니다.|쿼리|string|
|format|(empty)|선택 사항입니다. 콘텐츠 형식 ID를 지정 하는 문자열 현재 `audio/wav` 및 `audio/mp3`를 사용할 수 있습니다. 기본값은 `audio/wav`입니다.|쿼리|string|
|옵션|(empty)    |선택 사항입니다. 합성 된 음성의 속성을 지정 하는 문자열:<ul><li>`MaxQuality` 및 `MinSize` 오디오 신호의 품질을 지정 합니다. `MaxQuality` 최고 품질을 제공합니다. `MinSize` 최소 파일 크기를 제공합니다. 기본값은 `MinSize`합니다.</li><li>`female` 및 `male` 원하는 음성의 성별을 지정 합니다. 기본값은 `female`입니다. 세로 막대를 사용 하 여 (<code>\|</code>) 여러 옵션을 포함 하도록 합니다. 예: `MaxQuality|Male`</li></li></ul>  |쿼리|string|
|Authorization|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>구현 참고 사항
텍스트의 섹션의 언어를 식별합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/Detect`입니다.

**반환 값:** 텍스트에 대 한 두 자의 언어 코드를 포함 하는 문자열입니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)

string

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)  |필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|text|(empty)|필수 요소. 언어를 식별 하는 텍스트가 포함 된 문자열입니다. 텍스트는 10,000 자를 초과할 수 없습니다.|쿼리|  string|
|Authorization|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key  |(empty)    |둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>구현 참고 사항

문자열의 배열에서 언어를 식별합니다. 독립적으로 각 개별 배열 요소의 언어를 검색 하 고 배열의 각 행에 대 한 결과 반환 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`입니다.

요청 본문의 형식은 다음과 같습니다.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

텍스트에는 10,000 자를 초과할 수 없습니다.

**반환 값:** 입력 배열의 각 행에 대 한 두 자의 언어 코드를 포함 하는 문자열 배열입니다.

응답 본문의 형식은 다음과 같습니다.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)
`DetectArray` 성공 했습니다. 입력 배열의 각 행에 대 한 두 자의 언어 코드를 포함 하는 문자열 배열을 반환 합니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|Authorization|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>구현 참고 사항

> [!IMPORTANT]
> **사용 중단 참고:** 이 메서드는 2018 년 1 월 31 일 후 새 문장을 서브 미션을 허용 하지 않습니다. 오류 메시지가 표시 됩니다. Collaborative Translation Framework (CTF)를 변경 하는 방법에 대 한 공지를 참조 하세요.

번역 메모리에 번역을 추가합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`입니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)

string

응답 콘텐츠 형식: 응용 프로그램: xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식   |
|:--|:--|:--|:--|:--|
|appid|(empty)|필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|originalText|(empty)|필수 요소. 변환할 텍스트가 포함 된 문자열입니다. 문자열의 최대 길이 1, 000 자입니다.|쿼리|string|
|translatedText|(empty) |필수 요소. 텍스트가 포함 된 문자열은 대상 언어로 번역 합니다. 문자열의 최대 길이 2,000 자입니다.|쿼리|string|
|from|(empty)   |필수 요소. 텍스트의 원래 언어의 언어 코드를 나타내는 문자열입니다. 예를 들어 en 사용 하 여 영어 및 독일어는 de 합니다.|쿼리|string|
|다음으로 변경:|(empty)|필수 요소. 에 텍스트를 번역 언어의 언어 코드를 나타내는 문자열입니다.|쿼리|string|
|rating|(empty) |선택 사항입니다. 문자열에 대 한 품질 등급이 나타내는 정수입니다. 값-10에서 10 사이입니다. 기본값은 1입니다.|쿼리|integer|
|contentType|(empty)    |선택 사항입니다. 변환되는 텍스트의 형식입니다. 지원 되는 형식은 `text/plain` 고 `text/html`입니다. 모든 HTML 요소는 올바른 형식의 전체 요소 해야 합니다.    |쿼리|string|
|category|(empty)|선택 사항입니다. 번역 범주 (도메인)를 포함 하는 문자열입니다. 기본값은 `general`입니다.|쿼리|string|
|사용자|(empty)|필수 요소. 전송의 송신자를 추적 하는 데 사용 되는 문자열입니다.|쿼리|string|
|uri|(empty)|선택 사항입니다. 번역의 콘텐츠 위치를 포함 하는 문자열입니다.|쿼리|string|
|Authorization|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.  |헤더|string|
|Ocp-Apim-Subscription-Key|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|410|`AddTranslation` 지원 되지 않습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>구현 참고 사항

> [!IMPORTANT]
> **사용 중단 참고:** 이 메서드는 2018 년 1 월 31 일 후 새 문장을 서브 미션을 허용 하지 않습니다. 오류 메시지가 표시 됩니다. Collaborative Translation Framework (CTF)를 변경 하는 방법에 대 한 공지를 참조 하세요.

번역 메모리에 배열을 번역을 추가합니다. 이 메서드는 배열 된 `AddTranslation`합니다.

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

이러한 요소에는 `AddtranslationsRequest`:

* `AppId`: 필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `AppId` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.
* `From`: 필수 요소. 원본 언어의 언어 코드를 포함 하는 문자열입니다. `GetLanguagesForTranslate` 메서드에서 반환되는 언어 중 하나여야 합니다.
* `To`: 필수 요소. 대상 언어의 언어 코드를 포함 하는 문자열입니다. `GetLanguagesForTranslate` 메서드에서 반환되는 언어 중 하나여야 합니다.
* `Translations`: 필수 요소. 번역 메모리에 추가할 번역의 배열입니다. 각 번역 있어야 `OriginalText`, `TranslatedText`, 및 `Rating`합니다. 각각의 최대 크기 `OriginalText` 고 `TranslatedText` 1,000 자입니다. 모든 합계 `OriginalText` 고 `TranslatedText` 요소에는 10,000 자를 초과할 수 없습니다. 배열 요소의 최대 개수는 100입니다.
* `Options`: 필수 요소. 옵션을 포함 하 여 집합이 `Category`, `ContentType`를 `Uri`, 및 `User`합니다. `User` 가 필요 합니다. `Category`를 `ContentType`, 및 `Uri` 는 선택 사항입니다. 지정된 요소는 사전순으로 나열되어야 합니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)
`AddTranslationArray` 메서드가 성공 했습니다. 

2018 년 1 월 31 일 후 문장 서브 미션을 받아들이지 않습니다. 이 서비스는 오류 코드 410으로 응답합니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|Authorization|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|410    |`AddTranslation` 지원 되지 않습니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>구현 참고 사항
문장에는 텍스트 섹션을 중단 하 고 각 문장의 길이 포함 하는 배열을 반환 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`입니다.

**반환 값:** 문장의 길이 나타내는 정수의 배열입니다. 배열의 길이 문장 수를 나타냅니다. 값은 각 문장의 길이 나타냅니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)
문장의 길이 나타내는 정수의 배열입니다. 배열의 길이 문장 수를 나타냅니다. 값은 각 문장의 길이 나타냅니다.

integer

응답 콘텐츠 형식: application/xml

### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)  |필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리| string|
|text|(empty)   |필수 요소. 문장으로 분할 하는 텍스트를 나타내는 문자열입니다. 텍스트의 최대 크기는 10, 000 자입니다.|쿼리|string|
|language   |(empty)    |필수 요소. 입력된 텍스트의 언어 코드를 나타내는 문자열입니다.|쿼리|string|
|Authorization|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.   |헤더|string|
|Ocp-Apim-Subscription-Key|(empty)|둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401|잘못 된 자격 증명입니다.|
|500|서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>구현 참고 사항
저장소 및 MT 엔진에서 지정된 언어 쌍에 대한 번역 배열을 검색합니다. `GetTranslations` 다른 `Translate` 는 모든 사용 가능한 번역을 반환 합니다.

요청 URI는 `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`입니다.

요청 본문이 포함 선택적 `TranslationOptions` 이 형식의 개체:

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

`TranslateOptions` 다음 목록의 값을 포함 하는 개체입니다. 모두 선택 사항 하 고 가장 일반적인 설정을 기본값으로 설정 합니다. 지정된 요소는 사전순으로 나열되어야 합니다.

* `Category`: 번역 범주 (도메인)를 포함 하는 문자열입니다. 기본값은 `general`입니다.
* `ContentType`: 지원 되는 유일한 옵션인 이며 기본값인 `text/plain`합니다.
* `IncludeMultipleMTAlternatives`: 둘 이상의 대체 MT 엔진에서 반환될지 여부를 지정 하는 부울 플래그. 유효한 값은 `true` 고 `false` (대/소문자 구분). 기본값은 `false`를 하나만 대신 반환 합니다. 플래그 설정을 `true` Collaborative Translation Framework (CTF) 사용 하 여 완벽 하 게 통합 하는 인위적인 대안 만들 수 있습니다. 기능에서 인공 대안을 추가 하 여 CTF 번역이 없는 있는 문장 반환 대안을 사용 하는 *n*-디코더의 모범 목록입니다.
    - 등급입니다. 등급은 다음과 같이 적용 됩니다. 
         - 최고 품질의 자동 번역은 5등급입니다.
       - CTF 대안의 검토자의 권한을 반영합니다. 이러한 범위를-10 + 10에서.
       - 자동으로 생성 된 (*n*-최상의) 번역 대안 등급이 0과 100 어느 정도 일치 합니다.
    - 다양 한 대안입니다. 반환 된 대체 항목 수가 지정 된 값으로 수 `maxTranslations`, 낮은 수 있지만.
    - 언어 쌍입니다. 이 기능은 어느 방향으로든에서 중국어 간체 및 중국어 번체 간 변환에 사용할 수 없습니다. Microsoft Translator에서 지원 되는 모든 언어 쌍에 대해 제공 됩니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 사용자 상태입니다. 동일한 콘텐츠를 응답에 반환 됩니다.
* `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 설정 된 경우 기본값은 `all`합니다.
* `User`: 결과를 이 사용자를 기준으로 필터링합니다. 설정 된 경우 기본값은 `all`합니다.

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

이 응답을 포함 한 `GetTranslationsResponse` 다음 값을 포함 하는 요소:

* `Translations`: 에 저장 된 일치 항목 배열의 찾을 `TranslationMatch` 개체 (다음 섹션에서 설명). 번역 (유사 항목 일치) 원본 텍스트의 변형이 포함 될 수 있습니다. 번역은 100% 일치 먼저 유사 항목 일치 다음 합니다.
* `From`: 지정 하지 않으면 메서드는 `From` 언어에서이 값은 자동 언어 감지에서 제공 됩니다. 그렇지 않은 경우 지정 된 수는 `From` 언어입니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 사용자 상태입니다. 제공 된 값이 포함 된 `TranslateOptions` 매개 변수입니다.

`TranslationMatch` 개체 이러한 값으로 구성 됩니다.

* `Error`: 특정 입력된 문자열에 대 한 오류가 발생 하면 오류 코드입니다. 그렇지 않은 경우이 필드가 비어 있습니다.
* `MatchDegree`: 저장소에 있는 원래 텍스트 입력된 텍스트와 일치 하는 정도 나타냅니다. 시스템은 입력 문장과 일치하는 문장을 저장소에서 찾습니다. 정확하지 않은 일치도 검색됩니다. 값을 0에서 100으로 여기서 0은 없음을 하 고 100은 정확한, 대/소문자 구분 일치를 사용 하는 범위를 반환 합니다.
* `MatchedOriginalText`: 이 결과에서 일치된 원본 텍스트입니다. 이 값은 일치 하는 원본 텍스트를 입력된 텍스트에서 다른 경우에 반환 합니다. 유사 항목 일치의 원본 텍스트를 반환 하는 것이 됩니다. Microsoft Translator 결과 대 한이 값이 반환 되지 않습니다.
* `Rating`: 품질 결정 담당자의 권한을 나타냅니다. 기계 번역 결과 5 등급이 됩니다. 익명으로 제공 된 번역에는 일반적으로 1 ~ 4에서에서 등급이 있어야 합니다. 정식으로 제공 된 번역은 6 ~ 10에서에서 등급을 해야 일반적으로 합니다.
* `Count`: 이 등급의 현재 번역이 선택된 횟수입니다. 값은 자동으로 변환 된 응답에는 0입니다.
* `TranslatedText`: 번역된 텍스트입니다.

### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)
`GetTranslationsResponse` 이전에 설명 된 형식의 개체입니다.

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|appid|(empty)|필수 요소. 경우는 `Authorization` 또는 `Ocp-Apim-Subscription-Key` 헤더를 사용, 유지는 `appid` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.|쿼리|string|
|text|(empty)|필수 요소. 변환할 텍스트를 나타내는 문자열입니다. 텍스트의 최대 크기는 10, 000 자입니다.|쿼리|string|
|from|(empty)|필수 요소. 번역할 텍스트의 언어 코드를 나타내는 문자열입니다.|쿼리|string|
|다음으로 변경: |(empty)    |필수 요소. 에 텍스트를 번역 언어의 언어 코드를 나타내는 문자열입니다.|쿼리|string|
|maxTranslations|(empty)|필수 요소. 반환할 번역의 최대 수를 나타내는 정수입니다.|쿼리|integer|
|Authorization| (empty)|둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다. 권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|string|  헤더|
|Ocp-Apim-Subscription-Key|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 주시기 대략적인 날짜와 시간 요청 및 응답 헤더에 포함 된 요청 ID를 사용 하 여 `X-MS-Trans-Info`입니다.|
|503|서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>구현 참고 사항
여러 원본 텍스트에 대 한 여러 변환 후보를 검색합니다.

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

`GetTranslationsArrayRequest` 이러한 요소가 포함 됩니다.

* `AppId`: 필수 요소. 경우는 `Authorization` 헤더를 사용, 유지는 `AppId` 필드는 비워둡니다. 그렇지 않으면 포함 된 문자열을 포함 `"Bearer" + " " + "access_token"`합니다.
* `From`: 필수 요소. 번역할 텍스트의 언어 코드를 나타내는 문자열입니다.
* `MaxTranslations`: 필수 요소. 반환할 번역의 최대 수를 나타내는 정수입니다.
* `Options`: 선택 사항입니다. `Options` 다음 값이 들어 있는 개체입니다. 모두 선택 사항 하 고 가장 일반적인 설정을 기본값으로 설정 합니다. 지정된 요소는 사전순으로 나열되어야 합니다.
    - `Category`: 번역 범주 (도메인)를 포함 하는 문자열입니다. 기본값은 `general`입니다.
    - `ContentType`: 지원 되는 유일한 옵션인 이며 기본값인 `text/plain`합니다.
    - `IncludeMultipleMTAlternatives`: 둘 이상의 대체 MT 엔진에서 반환될지 여부를 지정 하는 부울 플래그. 유효한 값은 `true` 고 `false` (대/소문자 구분). 기본값은 `false`를 하나만 대신 반환 합니다. 플래그 설정을 `true` 번역을 완벽 하 게 공동 작업 번역 프레임 워크 (CTF) 사용 하 여 통합 인위적인 대안을 생성 하도록 설정 합니다. 기능에서 인공 대안을 추가 하 여 CTF 없는 대안에 있는 문장 반환 대안을 사용 하는 *n*-디코더의 모범 목록입니다.
        - 등급 등급은 다음과 같이 적용 됩니다.
          - 최고 품질의 자동 번역은 5등급입니다.
          - CTF 대안의 검토자의 권한을 반영합니다. 이러한 범위를-10 + 10에서.
          - 자동으로 생성 된 (*n*-최상의) 번역 대안 등급이 0과 100 어느 정도 일치 합니다.
        - 다양 한 대안입니다. 반환 된 대체 항목 수가 지정 된 값으로 수 `maxTranslations`, 낮은 수 있지만.
        - 언어 쌍입니다. 이 기능은 어느 방향으로든에서 중국어 간체 및 중국어 번체 간 변환에 사용할 수 없습니다. Microsoft Translator에서 지원 되는 모든 언어 쌍에 대해 제공 됩니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 사용자 상태입니다. 동일한 콘텐츠를 응답에 반환 됩니다.
* `Uri`: 결과를 이 URI를 기준으로 필터링합니다. 설정 된 경우 기본값은 `all`합니다.
* `User`: 결과를 이 사용자를 기준으로 필터링합니다. 설정 된 경우 기본값은 `all`합니다.
* `Texts`: 필수 요소. 번역에 대 한 텍스트가 포함 된 배열입니다. 동일한 언어의 모든 문자열 이어야 합니다. 변환할 모든 텍스트 총 10,000 자를 초과할 수 없습니다. 배열 요소의 최대 개수는 10입니다.
* `To`: 필수 요소. 에 텍스트를 번역 언어의 언어 코드를 나타내는 문자열입니다.

선택적 요소를 생략할 수 있습니다. 요소를 직접 자식의 `GetTranslationsArrayRequest` 알파벳 순서로 나열 되어야 합니다.

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

각 `GetTranslationsResponse` 요소는 이러한 값을 포함 합니다.

* `Translations`: 에 저장 된 일치 항목 배열의 찾을 `TranslationMatch` 개체 (다음 섹션에서 설명). 번역 (유사 항목 일치) 원본 텍스트의 변형이 포함 될 수 있습니다. 번역은 100% 일치 먼저 유사 항목 일치 다음 합니다.
* `From`: 지정 하지 않으면 메서드는 `From` 언어에서이 값은 자동 언어 감지에서 제공 됩니다. 그렇지 않은 경우 지정 된 수는 `From` 언어입니다.
* `State`: 요청 및 응답을 상호 연결 하는 데 사용자 상태입니다. 제공 된 값이 포함 된 `TranslateOptions` 매개 변수입니다.

`TranslationMatch` 개체는 다음 값을 포함 합니다.
* `Error`: 특정 입력된 문자열에 대 한 오류가 발생 하면 오류 코드입니다. 그렇지 않은 경우이 필드가 비어 있습니다.
* `MatchDegree`: 저장소에 있는 원래 텍스트 입력된 텍스트와 일치 하는 정도 나타냅니다. 시스템은 입력 문장과 일치하는 문장을 저장소에서 찾습니다. 정확하지 않은 일치도 검색됩니다. 값을 0에서 100으로 여기서 0은 없음을 하 고 100은 정확한, 대/소문자 구분 일치를 사용 하는 범위를 반환 합니다.
* `MatchedOriginalText`: 이 결과에서 일치된 원본 텍스트입니다. 이 값은 일치 하는 원본 텍스트를 입력된 텍스트에서 다른 경우에 반환 합니다. 유사 항목 일치의 원본 텍스트를 반환 하는 것이 됩니다. Microsoft Translator 결과 대 한이 값이 반환 되지 않습니다.
* `Rating`: 품질 결정 담당자의 권한을 나타냅니다. 기계 번역 결과 5 등급이 됩니다. 익명으로 제공 된 번역에는 일반적으로 1 ~ 4에서에서 등급이 있어야 합니다. 정식으로 제공 된 번역에는 일반적으로 6-10에서에서 등급이 있어야 합니다.
* `Count`: 이 등급의 현재 번역이 선택된 횟수입니다. 값은 자동으로 변환 된 응답에는 0입니다.
* `TranslatedText`: 번역된 텍스트입니다.


### <a name="response-class-status-200"></a>응답 클래스 (상태: 200)

string

응답 콘텐츠 형식: application/xml
 
### <a name="parameters"></a>매개 변수

|매개 변수|값|Description|매개 변수 유형|데이터 형식|
|:--|:--|:--|:--|:--|
|Authorization  |(empty)    |둘 다 필요 합니다 `appid` 필드 및 `Ocp-Apim-Subscription-Key` 헤더를 비워 두면 됩니다.  권한 부여 토큰:  `"Bearer" + " " + "access_token"`.|헤더|string|
|Ocp-Apim-Subscription-Key|(empty)  |둘 다 필요 합니다 `appid` 필드 및 `Authorization` 헤더를 비워 두면 됩니다.|헤더|string|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400    |잘못된 요청입니다. 입력 매개 변수 및 자세한 오류 응답을 확인합니다.|
|401    |잘못 된 자격 증명입니다.|
|500    |서버 오류입니다. 오류가 지속되면 알려주세요. 요청의 대략적인 날짜 및 시간과 응답 헤더 `X-MS-Trans-Info`에서 포함된 요청 ID를 제공하세요.|
|503    |서비스를 일시적으로 사용할 수 없습니다. 다시 시도한 후 오류가 지속되면 알려주세요.|

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Translator Text API로 마이그레이션 v3](../migrate-to-v3.md)


