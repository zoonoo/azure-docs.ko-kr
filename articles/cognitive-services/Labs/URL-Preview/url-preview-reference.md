---
title: Project URL Preview 참조
titlesuffix: Azure Cognitive Services
description: Project URL Preview 엔드포인트에 대한 참조입니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 2e4c5e6337c7fb222d18efada6ea9167e58aa024
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471410"
---
# <a name="project-url-preview-v7-reference"></a>Project URL Preview v7 참조

URL Preview는 블로그 게시물, 포럼 토론, 미리 보기 페이지 등의 웹 리소스에 대한 간략한 설명을 지원합니다.  URL은 웹 페이지, 뉴스, 이미지, 비디오 등 임의 유형의 인터넷 리소스일 수 있습니다. 쿼리는 http 또는 https 체계를 사용하는 절대 URL이어야 합니다. 상대 URL이나 기타 체계(예: ftp://)는 지원되지 않습니다.

URL Preview를 사용하는 응용 프로그램은 쿼리 매개 변수에 미리 보기할 URL을 사용하여 웹 요청을 엔드포인트로 보냅니다.  요청에는 *Ocp-Apim-Subscription-Key* 헤더가 포함되어야 합니다.   

이름, 리소스에 대한 설명, *isFamilyFriendly*, 전체 리소스와 대표 이미지에 대한 온라인 액세스를 제공하는 링크 등의 미리 보기 정보를 위해 JSON 응답을 구문 분석할 수 있습니다.

소셜 미디어, 챗봇 또는 유사한 제품에서 최종 사용자가 시작한 URL 공유에 원본 사이트로 하이퍼링크된 썸네일 이미지 및 미리 보기 코드 조각을 표시하려면 URL Preview의 데이터만 사용해야 합니다. Project URL Preview에서 받은 데이터는 복사, 저장 또는 캐시하면 안 됩니다. 웹 사이트 또는 콘텐츠 소유자로부터 받을 수 있는 미리 보기 비활성화 요청을 모두 적용해야 합니다.

## <a name="endpoint"></a>엔드포인트
URL Preview 결과를 요청하려면 다음 엔드포인트로 요청을 보냅니다. 헤더 및 URL 매개 변수를 사용하여 추가 사양을 정의합니다.

엔드포인트 GET: 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

요청이 HTTPS 프로토콜을 사용하고 다음 쿼리 매개 변수를 포함해야 합니다.

 q - 미리 보기할 URL을 식별하는 쿼리입니다. 

다음 섹션에서는 검색 결과에 영향을 주는 응답 개체, 쿼리 매개 변수 및 헤더에 대한 기술 정보를 제공합니다. 
  
요청에 포함되어야 하는 헤더에 대한 자세한 내용은 [헤더](#headers)를 참조하세요.  
  
요청에 포함되어야 하는 쿼리 매개 변수에 대한 자세한 내용은 [쿼리 매개 변수](#query-parameters)를 참조하세요.  
  
응답에 포함된 JSON 개체에 대한 자세한 내용은 [응답 개체](#response-objects)를 참조하세요.

최대 쿼리 URL 길이는 2,048자입니다. URL 길이가 한도를 초과하지 않도록 하려면 쿼리 매개 변수의 최대 길이가 1,500자 미만이어야 합니다. URL이 2,048자를 초과하면 서버에서 404 찾을 수 없음이 반환됩니다.  

허용되는 결과 사용 및 표시에 대한 자세한 내용은 [사용 및 표시 요구 사항](use-display-requirements.md)을 참조하세요. 

> [!NOTE]
> 다른 검색 API에 의미 있는 일부 요청 헤더가 URL Preview에는 영향을 주지 않습니다.
> - Pragma - 호출자가 URL Preview의 캐시 사용 여부를 제어할 수 없습니다.
> - User-Agent - 현재, URL Preview API는 PC, 노트북 또는 모바일에서 시작된 호출에 대해 다른 응답을 제공하지 않습니다.

> 또한 일부 매개 변수는 현재 URL Preview API에서 의미가 없지만, 향상된 세계화를 위해 나중에 사용될 수 있습니다. 
 
## <a name="headers"></a>헤더  
다음은 요청 및 응답이 포함할 수 있는 헤더입니다.  
  
|헤더|설명|  
|------------|-----------------|   
|<a name="market" />BingAPIs-Market|응답 헤더입니다.<br /><br /> 요청에서 사용하는 지역/국가입니다. 형식은 \<languageCode\>-\<countryCode\>입니다. 예를 들어 en-US입니다.|  
|<a name="traceid" />BingAPIs-TraceId|응답 헤더입니다.<br /><br /> 요청의 세부 정보를 포함하는 로그 항목의 ID입니다. 오류가 발생하면 이 ID를 캡처합니다. 문제를 확인하고 해결할 수 없는 경우 지원 팀에게 제공할 다른 정보와 함께 이 ID를 기재합니다.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|필수 요청 헤더입니다.<br /><br /> [Cognitive Services](https://www.microsoft.com/cognitive-services/)에서 이 서비스에 등록할 때 받은 구독 키입니다.|  
|<a name="clientid" />X-MSEdge-ClientID|선택적 요청 및 응답 헤더입니다.<br /><br /> Bing은 이 헤더를 사용하여 Bing API 호출에서 일관된 동작을 사용자에게 제공합니다. Bing은 새로운 기능과 개선 사항을 자주 플라이트하며, 다른 플라이트의 트래픽을 할당하기 위해 클라이언트 ID를 키로 사용합니다. 여러 요청에서 사용자에 대해 동일한 클라이언트 ID를 사용하지 않는 경우 Bing은 여러 개의 충돌하는 플라이트에 사용자를 할당할 수 있습니다. 여러 개의 충돌하는 플라이트에 할당되면 일관되지 않은 사용자 경험이 발생할 수 있습니다. 예를 들어 두 번째 요청의 플라이트 할당이 첫 번째 요청과 다른 경우 예기치 않은 경험이 발생할 수 있습니다. 또한 Bing은 클라이언트 ID를 사용하여 웹 결과를 해당 클라이언트 ID의 검색 기록에 맞게 조정하고 사용자에게 보다 풍부한 경험을 제공할 수 있습니다.<br /><br /> 또한 Bing은 이 헤더를 통해 클라이언트 ID에서 생성된 활동을 분석하여 결과 순위를 개선합니다. 관련성 개선은 Bing API에서 제공하는 결과 품질 향상에 도움이 되며, API 소비자의 클릭 광고율도 높아집니다.<br /><br />다음은 이 헤더에 적용되는 기본 사용 규칙입니다.<br /><ul><li>디바이스에서 애플리케이션을 사용하는 각 사용자에게 Bing에서 생성된 고유한 클라이언트 ID가 있어야 합니다.<br /><br/>이 헤더를 요청에 포함하지 않으면 Bing은 ID를 생성하고 X-MSEdge-ClientID 응답 헤더에 반환합니다. 요청에 이 헤더를 포함해서는 안 되는 경우는 사용자가 해당 디바이스에서 앱을 처음 사용할 때뿐입니다.<br /><br/></li><li>앱이 디바이스에서 이 사용자와 관련하여 수행하는 각 Bing API 요청에 대해 클라이언트 ID를 사용합니다.<br /><br/></li><li>**주의:** 이 클라이언트 ID를 인증 가능한 사용자 계정 정보에 연결할 수 없도록 해야 합니다.</li><br/><li>클라이언트 ID를 유지합니다. 브라우저 앱에서 ID를 유지하려면 영구적 HTTP 쿠키를 사용하여 모든 세션에서 해당 ID가 사용되도록 합니다. 세션 쿠키를 사용하면 안 됩니다. 모바일 앱과 같은 다른 앱의 경우 디바이스의 영구적 저장소를 사용하여 ID를 유지합니다.<br /><br/>다음에 사용자가 해당 디바이스에서 앱을 사용하는 경우 유지된 클라이언트 ID를 가져옵니다.</li></ul><br /> **참고:** Bing 응답에 이 헤더가 포함될 수도 있고, 포함되지 않을 수도 있습니다. 응답에 이 헤더가 포함된 경우, 클라이언트 ID를 캡처하여 해당 디바이스에서 사용자에 대해 수행되는 모든 후속 Bing 요청에 사용합니다.<br /><br /> **참고:** X-MSEdge-ClientID를 포함하는 경우 요청에 쿠키를 포함하면 안 됩니다.|  
|<a name="clientip" />X-MSEdge-ClientIP|선택적 요청 헤더입니다.<br /><br /> 클라이언트 디바이스의 IPv4 또는 IPv6 주소입니다. IP 주소는 사용자 위치를 검색하는 데 사용됩니다. Bing은 위치 정보를 사용하여 유해 정보 차단 동작을 결정합니다.<br /><br />  주소를 난독 처리(예: 마지막 8진수를 0으로 변경)하면 안 됩니다. 주소를 난독 처리하면 디바이스의 실제 위치 근처에 있는 위치로 지정되지 않으므로 Bing이 잘못된 결과를 제공할 수 있습니다.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>쿼리 매개 변수  
요청에 다음과 같은 쿼리 매개 변수를 포함할 수 있습니다. 필수 매개 변수는 필수 열을 참조하세요. 쿼리 매개 변수는 URL로 인코드해야 합니다. 쿼리는 http 또는 https 체계를 사용하는 절대 URL이어야 합니다. 상대 URL이나 기타 체계(예: ftp://)는 지원되지 않습니다.
  
  
|이름|값|type|필수|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|결과가 나오는 지역/국가입니다. <br /><br />가능한 지역/국가 값 목록은 [지역/국가 코드](#market-codes)를 참조하세요.<br /><br /> **참고:** URL Preview API는 현재 미국 지역과 영어 언어만 지원합니다.<br /><br />|문자열|yes|  
|<a name="query" />q|미리 보기할 URL입니다.|문자열|yes|  
|<a name="responseformat" />responseFormat|응답에 사용할 미디어 형식입니다. 다음은 대/소문자를 구분하지 않는 가능한 값입니다.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> 기본값은 JSON입니다. 응답에 포함된 JSON 개체에 대한 자세한 내용은 [응답 개체](#response-objects)를 참조하세요.<br /><br />  JsonLd를 지정하는 경우 응답 본문에 검색 결과를 포함하는 JSON-LD 개체가 포함됩니다. JSON-LD에 대한 자세한 내용은 [JSON-LD](http://json-ld.org/)를 참조하세요.|문자열|아니요|
|<a name="safesearch"/>safeSearch|불법 성인 콘텐츠 또는 해적판 콘텐츠는 오류 코드 400으로 차단되고 *isFamilyFriendly* 플래그는 반환되지 않습니다. <p>합법적 성인 콘텐츠에 대한 동작은 아래와 같습니다. 상태 코드 200을 반환하며 *isFamilyFriendly* 플래그가 false로 설정됩니다.<ul><li>safeSearch=strict: 제목, 설명, URL 및 이미지가 반환되지 않습니다.</li><li>safeSearch=moderate: 설명이 포함된 이미지를 제외한 제목, URL 및 설명을 가져옵니다.</li><li>safeSearch=off: 제목, URL, 설명 및 이미지 등의 모든 응답 개체/요소를 가져옵니다.</li></ul> |문자열|필요하지 않습니다. </br> 기본값은 safeSearch=strict입니다.| 

## <a name="response-objects"></a>응답 개체  
응답 스키마는 Web Search API와 같이 [WebPage] 또는 ErrorResponse입니다. 요청이 실패할 경우 최상위 개체는 [ErrorResponse](#errorresponse) 개체입니다.


|Object|설명|  
|------------|-----------------|  
|[WebPage](#webpage)|미리 보기 특성이 포함된 최상위 JSON 개체입니다.|  

  
### <a name="error"></a>오류  
발생한 오류를 정의합니다.  
  
|요소|설명|type|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|오류 범주를 식별하는 오류 코드입니다. 가능한 코드 목록은 [오류 코드](#error-codes)를 참조하세요.|문자열|  
|<a name="error-message" />message|오류에 대한 설명입니다.|문자열|  
|<a name="error-moredetails" />moreDetails|오류에 대한 추가 정보를 제공하는 설명입니다.|문자열|  
|<a name="error-parameter" />parameter|오류를 발생시킨 요청의 쿼리 매개 변수입니다.|문자열|  
|<a name="error-subcode" />subCode|오류를 식별하는 오류 코드입니다. 예를 들어 `code`가 InvalidRequest이면 `subCode`는 ParameterInvalid 또는 ParameterInvalidValue가 될 수 있습니다. |문자열|  
|<a name="error-value" />value|잘못된 쿼리 매개 변수의 값입니다.|문자열|  
  

### <a name="errorresponse"></a>ErrorResponse  
요청이 실패할 경우 응답에 포함되는 최상위 개체입니다.  
  
|이름|값|type|  
|----------|-----------|----------|  
|_type|유형 힌트입니다.|문자열|  
|<a name="errors" />errors|요청이 실패한 이유를 설명하는 오류 목록입니다.|[Error](#error)[]|   
  

### <a name="webpage"></a>WebPage  
미리 보기의 웹 페이지에 대한 정보를 정의합니다.  
  
|이름|값|type|  
|----------|-----------|----------|
|이름|페이지 제목입니다. HTML 제목이 아닐 수도 있습니다.|문자열|
|URL|실제로 크롤링된 URL입니다. 요청이 리디렉션을 따랐을 수도 있습니다.|문자열|  
|description|페이지 및 콘텐츠에 대한 간략한 설명입니다.|문자열|  
|isFamilyFriendly|웹 인덱스의 항목이 가장 정확합니다. 실시간 페치는 페이지 콘텐츠가 아니라 URL만 사용해서 이 검색을 수행합니다.|부울|
|primaryImageOfPage/contentUrl|미리 보기에 포함할 대표 이미지의 URL입니다.|문자열| 


### <a name="identifiable"></a>Identifiable
|이름|값|type|  
|-------------|-----------------|----------|
|id|리소스 식별자|문자열|
 

## <a name="error-codes"></a>오류 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.  
  
|상태 코드|설명|  
|-----------------|-----------------|  
|200|성공.|  
|400|쿼리 매개 변수 중 하나가 누락되었거나 유효하지 않습니다.| 
|400|ServerError, subCode ResourceError: 요청된 URL에 연결할 수 없습니다.|
|400|ServerError, subCode ResourceError: 요청된 URL이 성공 코드를 반환하지 않았습니다(HTTP 404를 반환한 경우 포함).|
|400|InvalidRequest, subCode Blocked: 요청된 URL에 성인 콘텐츠가 포함되어 차단되었을 수 있습니다.| 
|401|구독 키가 없거나 잘못되었습니다.|  
|403|사용자가 인증되었지만(예: 유효한 구독 키를 사용함), 요청된 리소스에 대한 권한이 없습니다.<br /><br /> Bing은 호출자가 월별 쿼리 할당량을 초과한 경우에도 이 상태를 반환할 수 있습니다.|  
|410|요청이 HTTPS 프로토콜 대신 HTTP를 사용했습니다. HTTPS 프로토콜만 지원됩니다.|  
|429|호출자가 초당 쿼리 수 할당량을 초과했습니다.|  
|500|예기치 않은 서버 오류가 발생했습니다.|

요청이 실패할 경우 오류의 원인을 설명하는 [Error](#error) 개체 목록을 포함하는 [ErrorResponse](#errorresponse) 개체가 응답에 포함됩니다. 오류가 매개 변수와 관련된 경우 `parameter` 필드는 문제가 발생한 매개 변수를 식별합니다. 또한 오류가 매개 변수 값과 관련된 경우 `value` 필드는 잘못된 값을 식별합니다.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

가능한 오류 코드 및 하위 오류 코드 값은 다음과 같습니다.

|코드|SubCode|설명
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP 상태 코드는 500입니다.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blocked|Bing은 요청의 일부가 잘못된 경우 항상 InvalidRequest를 반환합니다. 예를 들어 필수 매개 변수가 없거나 매개 변수 값이 잘못된 경우입니다.<br/><br/>오류가 ParameterMissing 또는 ParameterInvalidValue이면 HTTP 상태 코드는 400입니다.<br/><br/>HTTPS 대신 HTTP 프로토콜을 사용하는 경우 Bing은 HttpNotAllowed를 반환하고 HTTP 상태 코드는 410입니다.
|RateLimitExceeded|하위 코드 없음|Bing은 QPS(초당 쿼리 수) 또는 QPM(월별 쿼리 수) 할당량을 초과할 경우 항상 RateLimitExceeded를 반환합니다.<br/><br/>QPS를 초과할 경우 Bing은 HTTP 상태 코드 429를 반환하고, QPM을 초과할 경우 403을 반환합니다.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing이 호출자를 인증할 수 없는 경우 Bing은 InvalidAuthorization을 반환합니다. 예를 들어 `Ocp-Apim-Subscription-Key` 헤더가 없거나 구독 키가 잘못된 경우입니다.<br/><br/>둘 이상의 인증 방법을 지정한 경우 중복이 발생합니다.<br/><br/>오류가 InvalidAuthorization이면 HTTP 상태 코드는 401입니다.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|리소스에 액세스할 수 있는 사용 권한이 호출자에게 없는 경우 Bing은 InsufficientAuthorization을 반환합니다. 이 오류는 구독 키가 비활성화되었거나 만료된 경우에 발생할 수 있습니다. <br/><br/>오류가 InsufficientAuthorization이면 HTTP 상태 코드는 403입니다.

## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](csharp.md)
- [Java 빠른 시작](java-quickstart.md)
- [JavaScript 빠른 시작](javascript.md)
- [Node 빠른 시작](node-quickstart.md)
- [Python 빠른 시작](python-quickstart.md)

