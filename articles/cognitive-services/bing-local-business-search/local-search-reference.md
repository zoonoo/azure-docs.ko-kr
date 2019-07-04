---
title: Bing Local Business Search API v7 참조 | Microsoft Docs
description: Bing Local Business Search API의 프로그래밍 요소를 설명합니다.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 82b2f5ca70927856aeac889675b5ec4a54ae034f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796757"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Bing Local Business Search API v7 참조

Local Business Search API는 Bing에 검색 쿼리를 전송하여 식당, 호텔 또는 기타 로컬 비즈니스를 포함하는 결과를 가져옵니다. 장소의 경우 쿼리에서 로컬 비즈니스의 이름 또는 범주를 지정할 수 있습니다(예: 근처의 식당). 엔터티 결과에는 사람, 장소 또는 사물이 포함됩니다. 이 컨텍스트에서 비즈니스 엔터티, 상태, 국가/지역 등.  

이 섹션에서는 검색 결과에 영향을 주는 응답 개체 및 쿼리 매개 변수 및 헤더에 대한 기술 정보를 제공합니다. 요청을 만드는 방법을 보여주는 예제는 [Local Business Search C# 빠른 시작](quickstarts/local-quickstart.md) 또는 [Local Business Search Java 빠른 시작](quickstarts/local-search-java-quickstart.md)을 참조하세요. 
  
요청에 포함되어야 하는 헤더에 대한 자세한 내용은 [헤더](#headers)를 참조하세요.  
  
요청에 포함되어야 하는 쿼리 매개 변수에 대한 자세한 내용은 [쿼리 매개 변수](#query-parameters)를 참조하세요.  
  
응답에 포함된 JSON 개체에 대한 자세한 내용은 [응답 개체](#response-objects)를 참조하세요.

허용되는 결과 사용 및 표시에 대한 자세한 내용은 [사용 및 표시 요구 사항](use-display-requirements.md)을 참조하세요.


  
## <a name="endpoint"></a>엔드포인트  
로컬 비즈니스 결과를 요청하려면 GET 요청을 다음에 보냅니다. 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
요청은 HTTPS 프로토콜을 사용해야 합니다.  
  
> [!NOTE]
> 최대 URL 길이는 2,048자입니다. URL 길이가 한도를 초과하지 않도록 하려면 쿼리 매개 변수의 최대 길이가 1,500자 미만이어야 합니다. URL이 2,048자를 초과하면 서버에서 404 찾을 수 없음이 반환됩니다.  
  
  
## <a name="headers"></a>헤더  
다음은 요청 및 응답이 포함할 수 있는 헤더입니다.  
  
|헤더|설명|  
|------------|-----------------|  
|허용|선택적 요청 헤더입니다.<br /><br /> 기본 미디어 형식은 application/json입니다. 응답이 [JSON-LD](https://json-ld.org/)를 사용하도록 지정하려면 Accept 헤더를 application/ld+json으로 설정합니다.|  
|<a name="acceptlanguage" />Accept-Language|선택적 요청 헤더입니다.<br /><br /> 사용자 인터페이스 문자열에 사용할 언어의 쉼표로 구분된 목록입니다. 목록은 기본 설정의 내림차순으로 표시됩니다. 필요한 형식을 포함하여 자세한 내용은 [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 참조하세요.<br /><br /> 이 헤더와 [setLang](#setlang) 쿼리 매개 변수는 함께 사용할 수 없으므로 둘 다 지정하면 안 됩니다.<br /><br /> 이 헤더를 설정하는 경우 cc 쿼리 매개 변수도 지정해야 합니다. 결과를 반환할 지역/국가를 결정하기 위해 Bing은 목록에서 찾은 첫 번째 지원되는 언어를 사용하고, 이 언어를 `cc` 매개 변수 값과 결합합니다. 목록에 지원되는 언어가 없으면 Bing은 요청을 지원하는 가장 가까운 언어와 지역/국가를 찾거나 집계 또는 기본 지역/국가를 결과에 사용합니다. Bing이 사용한 지역/국가를 확인하려면 BingAPIs-Market 헤더를 참조하세요.<br /><br /> 여러 언어를 지정하는 경우에만 이 헤더와 `cc` 쿼리 매개 변수를 사용합니다. 여러 언어를 지정하지 않는 경우 [mkt](#mkt) 및 [setLang](#setlang) 쿼리 매개 변수를 사용합니다.<br /><br /> 사용자 인터페이스 문자열은 사용자 인터페이스에서 레이블로 사용되는 문자열입니다. JSON 응답 개체에는 몇 개의 사용자 인터페이스 문자열이 있습니다. 응답 개체에서 Bing.com 속성에 대한 링크는 지정된 언어를 적용합니다.|  
|<a name="market" />BingAPIs-Market|응답 헤더입니다.<br /><br /> 요청에서 사용하는 지역/국가입니다. 형식은 \<languageCode\>-\<countryCode\>입니다. 예를 들어 en-US입니다.|  
|<a name="traceid" />BingAPIs-TraceId|응답 헤더입니다.<br /><br /> 요청의 세부 정보를 포함하는 로그 항목의 ID입니다. 오류가 발생하면 이 ID를 캡처합니다. 문제를 확인하고 해결할 수 없는 경우 지원 팀에게 제공할 다른 정보와 함께 이 ID를 기재합니다.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|필수 요청 헤더입니다.<br /><br /> [Cognitive Services](https://www.microsoft.com/cognitive-services/)에서 이 서비스에 등록할 때 받은 구독 키입니다.|  
|<a name="pragma" />Pragma|선택적 요청 헤더입니다.<br /><br /> 기본적으로 Bing은 사용 가능한 경우 캐시된 콘텐츠를 반환합니다. Bing이 캐시된 콘텐츠를 반환하지 않도록 하려면 Pragma 헤더를 no-cache로 설정합니다(예: Pragma: no-cache).
|<a name="useragent" />User-Agent|선택적 요청 헤더입니다.<br /><br /> 요청을 시작하는 사용자 에이전트입니다. Bing은 사용자 에이전트를 사용하여 모바일 사용자에게 최적화된 환경을 제공합니다. 선택 사항이지만, 이 헤더를 항상 지정하는 것이 좋습니다.<br /><br /> user-agent는 일반적으로 사용되는 브라우저에서 보내는 것과 동일한 문자열이어야 합니다. 사용자 에이전트에 대한 자세한 내용은 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 참조하세요.<br /><br /> 다음은 user-agent 문자열의 예입니다.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|선택적 요청 및 응답 헤더입니다.<br /><br /> Bing은 이 헤더를 사용하여 Bing API 호출에서 일관된 동작을 사용자에게 제공합니다. Bing은 새로운 기능과 개선 사항을 자주 플라이트하며, 다른 플라이트의 트래픽을 할당하기 위해 클라이언트 ID를 키로 사용합니다. 여러 요청에서 사용자에 대해 동일한 클라이언트 ID를 사용하지 않는 경우 Bing은 여러 개의 충돌하는 플라이트에 사용자를 할당할 수 있습니다. 여러 개의 충돌하는 플라이트에 할당되면 일관되지 않은 사용자 경험이 발생할 수 있습니다. 예를 들어 두 번째 요청의 플라이트 할당이 첫 번째 요청과 다른 경우 예기치 않은 경험이 발생할 수 있습니다. 또한 Bing은 클라이언트 ID를 사용하여 웹 결과를 해당 클라이언트 ID의 검색 기록에 맞게 조정하고 사용자에게 보다 풍부한 경험을 제공할 수 있습니다.<br /><br /> 또한 Bing은 이 헤더를 통해 클라이언트 ID에서 생성된 활동을 분석하여 결과 순위를 개선합니다. 관련성 개선은 Bing API에서 제공하는 결과 품질 향상에 도움이 되며, API 소비자의 클릭 광고율도 높아집니다.<br /><br /> **중요:** 이 헤더는 선택 사항이지만 필수 사항으로 간주해야 합니다. 동일한 일반 사용자 및 디바이스 조합에 대해 여러 요청에서 클라이언트 ID를 유지하면 1) API 소비자에게 일관된 사용자 환경이 수신되고, 2) Bing API의 결과 품질 향상을 통해 클릭 광고율이 높아집니다.<br /><br /> 다음은 이 헤더에 적용되는 기본 사용 규칙입니다.<br /><ul><li>장치에서 애플리케이션을 사용하는 각 사용자에게 Bing에서 생성된 고유한 클라이언트 ID가 있어야 합니다.<br /><br/>이 헤더를 요청에 포함하지 않으면 Bing은 ID를 생성하고 X-MSEdge-ClientID 응답 헤더에 반환합니다. 요청에 이 헤더를 포함해서는 안 되는 경우는 사용자가 해당 디바이스에서 앱을 처음 사용할 때뿐입니다.<br /><br/></li><li>앱이 디바이스에서 이 사용자와 관련하여 수행하는 각 Bing API 요청에 대해 클라이언트 ID를 사용합니다.<br /><br/></li><li>**주의:** 이 클라이언트 ID를 인증 가능한 사용자 계정 정보에 연결할 수 없도록 해야 합니다.</li><br/><li>클라이언트 ID를 유지합니다. 브라우저 앱에서 ID를 유지하려면 영구적 HTTP 쿠키를 사용하여 모든 세션에서 해당 ID가 사용되도록 합니다. 세션 쿠키를 사용하면 안 됩니다. 모바일 앱과 같은 다른 앱의 경우 디바이스의 영구적 저장소를 사용하여 ID를 유지합니다.<br /><br/>다음에 사용자가 해당 디바이스에서 앱을 사용하는 경우 유지된 클라이언트 ID를 가져옵니다.</li></ul><br /> **참고:** 이 헤더는 Bing 응답에 포함되거나 포함되지 않을 수도 있습니다. 응답에 이 헤더가 포함된 경우, 클라이언트 ID를 캡처하여 해당 디바이스에서 사용자에 대해 수행되는 모든 후속 Bing 요청에 사용합니다.<br /><br /> **참고:** X-MSEdge-ClientID가 포함되는 경우 요청에 쿠키를 포함하면 안 됩니다.|  
|<a name="clientip" />X-MSEdge-ClientIP|선택적 요청 헤더입니다.<br /><br /> 클라이언트 디바이스의 IPv4 또는 IPv6 주소입니다. IP 주소는 사용자 위치를 검색하는 데 사용됩니다. Bing은 위치 정보를 사용하여 유해 정보 차단 동작을 결정합니다.<br /><br /> **참고:** 이 헤더와 X-Search-Location 헤더는 선택 사항이지만 항상 지정하는 것이 좋습니다.<br /><br /> 주소를 난독 처리(예: 마지막 8진수를 0으로 변경)하면 안 됩니다. 주소를 난독 처리하면 디바이스의 실제 위치 근처에 있는 위치로 지정되지 않으므로 Bing이 잘못된 결과를 제공할 수 있습니다.|  
|<a name="location" />X-Search-Location|선택적 요청 헤더입니다.<br /><br /> 클라이언트의 지리적 위치를 설명하는 키/값 쌍의 세미콜론으로 구분된 목록입니다. Bing은 위치 정보를 사용하여 유해 정보 차단 동작을 결정하고 관련된 로컬 콘텐츠를 반환합니다. 키/값 쌍을 \<키\>:\<값\>으로 지정합니다. 다음은 사용자 위치를 지정하는 데 사용하는 키입니다.<br /><br /><ul><li>lat&mdash;클라이언트 위치의 위도(도)입니다. 위도는 -90.0보다 크거나 같고 +90.0보다 작거나 같아야 합니다. 음수 값은 남위를 나타내고, 양수 값은 북위를 나타냅니다.<br /><br /></li><li>long&mdash;클라이언트 위치의 경도(도)입니다. 경도는 -180.0보다 크거나 같고 +180.0보다 작거나 같아야 합니다. 음수 값은 서경을 나타내고, 양수 값은 동경을 나타냅니다.<br /><br /></li><li>re&mdash;좌표의 가로 정확도를 지정하는 반경(미터)입니다. 디바이스의 위치 서비스에서 반환된 값을 전달합니다. 일반적인 값은 GPS/Wi-Fi의 경우 22m, 기지국 삼각측량의 경우 380m, 역방향 IP 조회의 경우 18,000m입니다.<br /><br /></li><li>ts&mdash;클라이언트가 위치에 있었던 시간의 UTC UNIX 타임스탬프입니다. UNIX 타임스탬프는 1970년 1월 1일 이후의 시간(초)입니다.<br /><br /></li><li>head&mdash;선택 사항입니다. 클라이언트 상대적인 이동 방향입니다. 정북을 기준으로 시계 반대 방향으로 계산해서, 이동 방향을 0에서 360 사이의 도로 지정합니다. `sp` 키가 0이 아닌 경우에만 이 키를 지정합니다.<br /><br /></li><li>sp&mdash; 클라이언트 디바이스가 이동하는 가로 벡터(속도)(초당 미터)입니다.<br /><br /></li><li>alt&mdash; 클라이언트 디바이스의 고도(미터)입니다.<br /><br /></li><li>are&mdash;선택 사항입니다. 좌표의 세로 정확도를 지정하는 반경(미터)입니다. 반경의 기본값은 50킬로미터입니다. `alt` 키를 지정하는 경우에만 이 키를 지정합니다.<br /><br /></li></ul> **참고:** 이러한 키는 선택 사항이지만, 제공하는 정보가 많을수록 위치 결과가 정확해집니다.<br /><br /> **참고:** 사용자의 지리적 위치를 항상 지정하는 것이 좋습니다. 클라이언트의 IP 주소가 사용자의 실제 위치를 정확하게 반영하지 않는 경우(예: 클라이언트가 VPN을 사용하는 경우) 위치를 제공하는 것이 특히 중요합니다. 최적의 결과를 얻으려면 이 헤더와 X-MSEdge-ClientIP 헤더를 포함해야 하지만, 최소한 이 헤더를 포함해야 합니다.|

> [!NOTE] 
> 사용 약관에 따라, 이러한 헤더 사용과 관련해서 해당 법률을 모두 준수해야 합니다. 예를 들어 유럽 등의 특정 관할지에서는 사용자 디바이스에 특정 추적 디바이스를 배치하기 전에 사용자 동의를 받아야 하는 요구 사항이 있습니다.
  

## <a name="query-parameters"></a>쿼리 매개 변수  
요청에 다음과 같은 쿼리 매개 변수를 포함할 수 있습니다. 필수 매개 변수는 필수 열을 참조하세요. 쿼리 매개 변수는 URL로 인코드해야 합니다.  
  
  
|Name|값|Type|필수|  
|----------|-----------|----------|--------------|
|<a name="count" />count|지정 된 인덱스부터 반환할 결과 수는 `offset` 매개 변수입니다.|String|아닙니다.|   
|<a name="localCategories" />localCategories|비즈니스 범주별로 검색을 정의하는 옵션 목록입니다.  [로컬 비즈니스 범주 검색](local-categories.md) 참조|String|아닙니다.|  
|<a name="mkt" />mkt|결과가 나오는 지역/국가입니다. <br /><br />가능한 지역/국가 값 목록은 지역/국가 코드를 참조하세요.<br /><br /> **참고:** Local Business Search API는 현재 en-us 지역/국가와 언어만 지원합니다.<br /><br />|String|예|
|<a name="offset"/>offset|`count` 매개 변수에서 지정된 결과를 시작하는 인덱스입니다.|Integer|아닙니다.|  
|<a name="query" />q|사용자의 검색 용어입니다.|문자열|아닙니다.|  
|<a name="responseformat" />responseFormat|응답에 사용할 미디어 형식입니다. 다음은 대/소문자를 구분하지 않는 가능한 값입니다.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> 기본값은 JSON입니다. 응답에 포함된 JSON 개체에 대한 자세한 내용은 [응답 개체](#response-objects)를 참조하세요.<br /><br />  JsonLd를 지정하는 경우 응답 본문에 검색 결과를 포함하는 JSON-LD 개체가 포함됩니다. JSON-LD에 대한 자세한 내용은 [JSON-LD](https://json-ld.org/)를 참조하세요.|문자열|아닙니다.|  
|<a name="safesearch" />safeSearch|성인 콘텐츠를 필터링하는 데 사용되는 필터입니다. 다음은 대/소문자를 구분하지 않는 가능한 필터 값입니다.<br /><ul><li>Off&mdash;성인 텍스트, 이미지 또는 비디오를 포함하는 웹 페이지를 반환합니다.<br /><br/></li><li>Moderate&mdash;성인 텍스트만 포함하고 성인 이미지 또는 비디오는 포함하지 않는 웹 페이지를 반환합니다.<br /><br/></li><li>Strict&mdash;성인 텍스트, 이미지 또는 비디오를 포함하는 웹 페이지를 반환하지 않습니다.</li></ul><br /> 기본값은 Moderate입니다.<br /><br /> **참고:** Bing의 성인 정책에 따라 `safeSearch`가 Strict로 설정되어야 하는 지역/국가에서 요청이 나온 경우 Bing은 `safeSearch` 값을 무시하고 Strict를 사용합니다.<br/><br/>**참고:** `site:` 쿼리 연산자를 사용하는 경우 `safeSearch` 쿼리 매개 변수가 설정된 값에 관계없이 성인 콘텐츠가 응답에 포함될 수 있습니다. 사이트의 콘텐츠를 알고 있고 시나리오가 성인 컨텐츠를 지원하는 경우에만 `site:`를 사용합니다. |String|아닙니다.|  
|<a name="setlang" />setLang|사용자 인터페이스 문자열에 사용할 언어입니다. ISO 639-1 2자 언어 코드를 사용하여 언어를 지정합니다. 예를 들어 영어의 언어 코드는 EN입니다. 기본값은 EN(영어)입니다.<br /><br /> 선택 사항이지만, 언어를 항상 지정해야 합니다. 일반적으로, 사용자가 사용자 인터페이스 문자열을 다른 언어로 표시하려는 경우가 아니면 `setLang`을 `mkt`에 지정된 것과 동일한 언어로 설정합니다.<br /><br /> 이 매개 변수와 [Accept-Language](#acceptlanguage) 헤더는 함께 사용할 수 없으므로 둘 다 지정하면 안 됩니다.<br /><br /> 사용자 인터페이스 문자열은 사용자 인터페이스에서 레이블로 사용되는 문자열입니다. JSON 응답 개체에는 몇 개의 사용자 인터페이스 문자열이 있습니다. 또한, 응답 개체에서 Bing.com 속성에 대한 링크는 지정된 언어를 적용합니다.|문자열|아닙니다.| 


## <a name="response-objects"></a>응답 개체  
다음은 응답에 포함할 수 있는 JSON 응답 개체입니다. 요청이 성공하는 경우 응답의 최상위 개체는 [SearchResponse](#searchresponse) 개체입니다. 요청이 실패할 경우 최상위 개체는 [ErrorResponse](#errorresponse) 개체입니다.


|Object|설명|  
|------------|-----------------|  
|[Place](#place)|식당 또는 호텔과 같은 로컬 비즈니스에 대한 정보를 정의합니다.|  

  
### <a name="error"></a>오류  
발생한 오류를 정의합니다.  
  
|요소|설명|Type|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|오류 범주를 식별하는 오류 코드입니다. 가능한 코드 목록은 [오류 코드](#error-codes)를 참조하세요.|String|  
|<a name="error-message" />message|오류에 대한 설명입니다.|String|  
|<a name="error-moredetails" />moreDetails|오류에 대한 추가 정보를 제공하는 설명입니다.|String|  
|<a name="error-parameter" />parameter|오류를 발생시킨 요청의 쿼리 매개 변수입니다.|String|  
|<a name="error-subcode" />subCode|오류를 식별하는 오류 코드입니다. 예를 들어 `code`가 InvalidRequest이면 `subCode`는 ParameterInvalid 또는 ParameterInvalidValue가 될 수 있습니다. |String|  
|<a name="error-value" />value|잘못된 쿼리 매개 변수의 값입니다.|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
요청이 실패할 경우 응답에 포함되는 최상위 개체입니다.  
  
|이름|값|Type|  
|----------|-----------|----------|  
|_type|유형 힌트입니다.|String|  
|<a name="errors" />errors|요청이 실패한 이유를 설명하는 오류 목록입니다.|[Error](#error)[]|  

  
  
### <a name="license"></a>License  
텍스트 또는 사진을 사용할 수 있는 라이선스를 정의합니다.  
  
|이름|값|Type|  
|----------|-----------|----------|  
|name|라이선스의 이름입니다.|String|  
|url|사용자가 라이선스에 대한 자세한 정보를 얻을 수 있는 웹 사이트의 URL입니다.<br /><br /> 하이퍼링크를 만들려면 이름 및 URL을 사용합니다.|문자열|  


### <a name="link"></a>연결  
하이퍼링크의 구성 요소를 정의합니다.  
  
|이름|값|Type|  
|----------|-----------|----------|  
|_type|유형 힌트입니다.|String|  
|text|표시 텍스트입니다.|String|  
|url|URL입니다. 하이퍼링크를 만들려면 URL과 표시 텍스트를 사용합니다.|문자열|  
  


  
### <a name="organization"></a>조직  
게시자를 정의합니다.  
  
게시자는 자신의 이름, 웹 사이트 또는 둘 다를 제공할 수 있습니다.  
  
|이름|값|Type|  
|----------|-----------|----------|  
|name|게시자의 이름입니다.|String|  
|url|게시자 웹 사이트의 URL입니다.<br /><br /> 게시자가 웹 사이트를 제공하지 않을 수도 있습니다.|String|  
  
  

### <a name="place"></a>Place  
식당 또는 호텔과 같은 로컬 비즈니스에 대한 정보를 정의합니다.  
  
|이름|값|Type|  
|----------|-----------|----------|  
|_type|다음 중 하나로 설정할 수 있는 힌트를 입력합니다.<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>식당</ul><li>|문자열|  
|address|엔터티가 위치한 우편 주소입니다.|PostalAddress|  
|entityPresentationInfo|엔터티의 형식을 확인하는 데 사용할 수 있는 힌트와 같은 엔터티에 대한 추가 정보입니다. 예를 들어 식당 또는 호텔인지 여부입니다. `entityScenario` 필드는 ListItem으로 설정됩니다.|EntityPresentationInfo|  
|name|엔터티의 이름입니다.|String|  
|telephone|엔터티의 전화 번호입니다.|String|  
|url|엔터티의 웹 사이트에 대한 URL입니다.<br /><br /> 엔터티의 이름과 함께 이 URL을 사용하여 클릭하면 사용자를 엔터티의 웹 사이트로 이동시키는 하이퍼링크를 만듭니다.|String|  
|webSearchUrl|이 위치의 Bing 검색 결과에 대한 URL입니다.|문자열| 
  
  
### <a name="querycontext"></a>QueryContext  
Bing이 요청에 사용한 쿼리 컨텍스트를 정의합니다.  
  
|요소|설명|Type|  
|-------------|-----------------|----------|  
|adultIntent|지정한 쿼리에 성인 의도가 있는지 여부를 나타내는 부울 값입니다. 쿼리에 성인 의도가 있으면 값이 **true**이고, 없으면 **false**입니다.|Boolean|  
|alterationOverrideQuery|Bing에서 원래 문자열을 사용하도록 강제하는 데 사용할 쿼리 문자열입니다. 예를 들어 쿼리 문자열이 *saling downwind*인 경우 대체 쿼리 문자열은 *+saling downwind*가 됩니다. 쿼리 문자열을 인코드하여 *%2Bsaling+downwind*로 만들어야 합니다.<br /><br /> 이 필드는 원래 쿼리 문자열에 오타가 있는 경우에만 포함됩니다.|String|  
|alteredQuery|Bing에서 쿼리를 수행하는 데 사용하는 쿼리 문자열입니다. Bing은 원래 쿼리 문자열에 오타가 포함된 경우 변경된 쿼리 문자열을 사용합니다. 예를 들어 쿼리 문자열이 `saling downwind`이면 변경된 쿼리 문자열은 `sailing downwind`가 됩니다.<br /><br /> 이 필드는 원래 쿼리 문자열에 오타가 있는 경우에만 포함됩니다.|String|  
|askUserForLocation|Bing에서 정확한 결과를 제공하기 위해 사용자 위치가 필요한지 여부를 나타내는 부울 값입니다. [X-MSEdge-ClientIP](#clientip) 및 [X-Search-Location](#location) 헤더를 사용하여 사용자 위치를 지정한 경우에는 이 필드를 무시해도 됩니다.<br /><br /> 정확한 결과를 제공하기 위해 사용자 위치가 필요한 위치 인식 쿼리의 경우(예: “today's weather” 또는 “restaurants near me”) 이 필드가 **true**로 설정됩니다.<br /><br /> 위치를 포함하는 위치 인식 쿼리의 경우(예: “Seattle weather”) 이 필드가 **false**로 설정됩니다. “best sellers”와 같이 위치 인식이 아닌 쿼리의 경우에도 이 필드가 **false**로 설정됩니다.|Boolean|  
|originalQuery|요청에 지정된 쿼리 문자열입니다.|String|  

### <a name="identifiable"></a>Identifiable

|Name|값|Type|  
|-------------|-----------------|----------|
|id|리소스 식별자|String|
 
### <a name="rankinggroup"></a>RankingGroup
mainline과 같은 검색 결과 그룹을 정의합니다.

|이름|값|Type|  
|-------------|-----------------|----------|
|items|그룹에 표시할 검색 결과 목록입니다.|RankingItem|

### <a name="rankingitem"></a>RankingItem
표시할 검색 결과 항목을 정의합니다.

|이름|값|Type|  
|-------------|-----------------|----------|
|resultIndex|답변에서 표시할 항목의 0부터 시작 인덱스입니다. 항목에 이 필드가 없는 경우 답변에 있는 모든 항목을 표시합니다. 예를 들어 뉴스 답변에 있는 모든 뉴스 기사를 표시합니다.|Integer|
|answerType|표시할 항목이 포함된 답변입니다. 예를 들어 뉴스입니다.<br /><br />유형을 사용하여 SearchResponse 개체에서 답변을 찾습니다. 유형은 SearchResponse 필드의 이름입니다.<br /><br /> 그러나 이 개체에 value 필드가 포함된 경우에만 답변 유형을 사용합니다. value 필드가 없으면 무시합니다.|String|
|textualIndex|textualAnswers에서 표시할 답변의 인덱스입니다.| 부호 없는 정수|
|값|표시할 답변 또는 표시할 답변 항목을 식별하는 ID입니다. ID가 답변을 식별하는 경우 답변의 모든 항목을 표시합니다.|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
검색 결과 페이지에서 콘텐츠를 배치할 위치와 순서를 정의합니다.  
  
|이름|값|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|mainline에 표시할 검색 결과입니다.|  
|<a name="ranking-pole" />pole|가장 잘 보이도록 처리(예: 기본 줄과 사이드바 위에 표시)해야 하는 검색 결과입니다.|  
|<a name="ranking-sidebar" />sidebar|사이드바에 표시할 검색 결과입니다.| 

### <a name="searchresponse"></a>SearchResponse  
요청이 성공할 경우 응답에 포함되는 최상위 개체를 정의합니다.  
  
서비스에서 서비스 거부 공격을 의심할 경우 요청이 성공하지만(HTTP 상태 코드는 200 OK) 응답의 본문이 비어 있습니다.  
  
|이름|값|Type|  
|----------|-----------|----------|  
|_type|SearchResponse로 설정되는 유형 힌트입니다.|String|  
|장소|검색 쿼리와 관련 있는 엔터티 목록입니다.|JSON 개체|  
|queryContext|Bing이 요청에 사용한 쿼리 문자열을 포함하는 개체입니다.<br /><br /> 이 개체는 사용자가 입력한 대로 쿼리 문자열을 포함합니다. 쿼리 문자열에 철자 오류가 포함되었던 경우 Bing이 쿼리에 사용한 변경된 쿼리 문자열을 포함할 수도 있습니다.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>오류 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다.  
  
|상태 코드|설명|  
|-----------------|-----------------|  
|200|성공.|  
|400|쿼리 매개 변수 중 하나가 누락되었거나 유효하지 않습니다.|  
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
- [Local Business Search 빠른 시작](quickstarts/local-quickstart.md)
- [Local Business Search Java 빠른 시작](quickstarts/local-search-java-quickstart.md)
- [Local Business Search Node 빠른 시작](quickstarts/local-search-node-quickstart.md)
- [Local Business Search Python 빠른 시작](quickstarts/local-search-python-quickstart.md)
