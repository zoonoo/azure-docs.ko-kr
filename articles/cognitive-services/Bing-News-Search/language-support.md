---
title: 언어 지원 - Bing News Search API
titleSuffix: Azure Cognitive Services
description: Bing News Search API에서 지원되는 자연 언어, 국가 및 지역 목록입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384979"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Bing News Search API에 대한 언어 및 지역 지원

Bing News Search API는 다양한 국가/지역을 지원하며, 이 중에는 둘 이상의 언어를 사용하는 국가/지역도 많습니다. 쿼리를 통한 국가/지역 지정은 해당 국가/지역의 관심 분야에 따라 검색 결과를 구체화하는 데 주로 사용됩니다. 또한 결과는 Bing에 대한 링크를 포함할 수 있으며 이러한 링크는 지정된 국가/지역 또는 언어에 따라 Bing 사용자 경험을 지역화할 수 있습니다.

`cc` 쿼리 매개 변수를 사용하여 국가/지역을 지정할 수 있습니다. 국가/지역을 지정하는 경우 `Accept-Language` HTTP 헤더를 사용하여 언어 코드도 하나 이상 지정해야 합니다. 지원되는 언어는 국가/지역마다 다르며, 지역/국가 표에서 각 국가/지역에 대해 제공됩니다.

또는 **지역/국가** 표에서 `mkt` 쿼리 매개 변수 및 코드를 사용하여 지역/국가를 지정할 수 있습니다. 지역/국가 지정은 동시에 국가/지역 및 기본 언어를 지정합니다. `setLang` 쿼리 매개 변수는 이 경우 언어 코드로 설정할 수 있습니다. 사용자가 Bing을 다른 언어로 보길 선호하지 않는 한 주로 `mkt`에 의해 지정된 동일한 언어입니다.

## <a name="supported-markets-for-news-search-endpoint"></a>뉴스 검색 엔드포인트에 대해 지원되는 시장

`/news/search` 엔드포인트의 경우 다음 테이블은 `mkt` 쿼리 매개 변수를 지정하는 데 사용할 수 있는 시장 코드 값을 나열합니다. Bing은 이러한 시장에 대한 콘텐츠를 반환합니다. 목록은 변경될 수 있습니다.  

`cc` 쿼리 매개 변수에서 지정할 수 있는 국가/지역 코드의 목록은 [국가 코드](#countrycodes)를 참조하세요.  

|국가/지역|언어|시장 코드|  
|---------------------|--------------|-----------------|
|덴마크|덴마크어|da-DK|
|오스트리아|독일어|de-AT|
|스위스|독일어|de-CH|
|독일|독일어|de-DE|
|오스트레일리아|영어|en-AU|
|캐나다|영어|en-CA|
|영국|영어|en-GB|
|인도네시아|영어|en-ID|
|아일랜드|영어|en-IE|
|인도|영어|en-IN|
|말레이시아|영어|en-MY|
|뉴질랜드|영어|en-NZ|
|필리핀|영어|en-PH|
|싱가포르|영어|en-SG|
|미국|영어|en-US|
|영어|일반|en-WW|
|영어|일반|en-XA|
|남아프리카 공화국|영어|en-ZA|
|아르헨티나|스페인어|es-AR|
|칠레|스페인어|es-CL|
|스페인|스페인어|es-ES|
|멕시코|스페인어|es-MX|
|미국|스페인어|es (미국)|
|스페인어|일반|es-XL|
|핀란드|핀란드어|fi-FI|  
|프랑스|프랑스어|fr-BE|
|캐나다|프랑스어|fr-CA|
|벨기에|네덜란드어|nl-BE|
|스위스|프랑스어|fr-CH|
|프랑스|프랑스어|fr-FR|  
|이탈리아|이탈리아어|it-IT|
|홍콩 특별 행정구|중국어(번체)|zh-HK|  
|대만|중국어(번체)|zh-TW|
|일본|일본어|ja-JP|  
|한국|한국어|en-US|  
|네덜란드|네덜란드어|nl-NL|  
|중국|중국어|zh-CN|  
|브라질|포르투갈어|pt-BR|
|러시아|러시아어|ru-RU|  
|스웨덴|스웨덴어|sv-SE|  
|터키|터키어|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>뉴스 엔드포인트에 대해 지원되는 시장
`/news` 엔드포인트의 경우 다음 테이블은 `mkt` 쿼리 매개 변수를 지정하는 데 사용할 수 있는 시장 코드 값을 나열합니다. Bing은 이러한 시장에 대한 콘텐츠를 반환합니다. 목록은 변경될 수 있습니다.  

`cc` 쿼리 매개 변수에서 지정할 수 있는 국가/지역 코드의 목록은 [국가 코드](#countrycodes)를 참조하세요.  

|국가/지역|언어|시장 코드|  
|---------------------|--------------|-----------------|
|덴마크|덴마크어|da-DK|
|독일|독일어|de-DE|
|오스트레일리아|영어|en-AU|
|영국|영어|en-GB|
|미국|영어|en-US|
|영어|일반|en-WW|
|칠레|스페인어|es-CL|
|멕시코|스페인어|es-MX|
|미국|스페인어|es (미국)|
|핀란드|핀란드어|fi-FI|  
|캐나다|프랑스어|fr-CA|
|프랑스|프랑스어|fr-FR|  
|이탈리아|이탈리아어|it-IT|
|브라질|포르투갈어|pt-BR|
|중국|중국어|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>뉴스 추세 엔드포인트에 대해 지원되는 시장
`/news/trendingtopics` 엔드포인트의 경우 다음 테이블은 `mkt` 쿼리 매개 변수를 지정하는 데 사용할 수 있는 시장 코드 값을 나열합니다. Bing은 이러한 시장에 대한 콘텐츠를 반환합니다. 목록은 변경될 수 있습니다.  

`cc` 쿼리 매개 변수에서 지정할 수 있는 국가/지역 코드의 목록은 [국가 코드](#countrycodes)를 참조하세요.  

|국가/지역|언어|시장 코드|  
|---------------------|--------------|-----------------|
|독일|독일어|de-DE|
|오스트레일리아|영어|en-AU|
|영국|영어|en-GB|
|미국|영어|en-US|
|캐나다|영어|en-CA|
|인도|영어|en-IN|
|프랑스|프랑스어|fr-FR|
|캐나다|프랑스어|fr-CA|
|브라질|포르투갈어|pt-BR|
|중국|중국어|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>국가 코드  

다음은 `cc` 쿼리 매개 변수에서 지정할 수 있는 국가/지역 코드입니다. 목록은 변경될 수 있습니다.  

|국가/지역|국가 코드|  
|---------------------|------------------|  
|아르헨티나|AR|  
|오스트레일리아|AU|  
|오스트리아|AT|  
|벨기에|BE|  
|브라질|BR|  
|캐나다|CA|  
|칠레|CL|  
|덴마크|DK|  
|핀란드|FI|  
|프랑스|FR|  
|독일|DE|  
|홍콩 특별 행정구|HK|  
|인도|IN|  
|인도네시아|ID|  
|이탈리아|IT|  
|일본|JP|  
|한국|KR|  
|말레이시아|MY|  
|멕시코|MX|  
|네덜란드|NL|  
|뉴질랜드|NZ|  
|노르웨이|아니요|  
|중국|CN|  
|폴란드|PL|  
|포르투갈|PT|  
|필리핀|PH|  
|러시아|RU|  
|사우디아라비아|SA|  
|남아프리카 공화국|ZA|  
|스페인|ES|  
|스웨덴|SE|  
|스위스|CH|  
|대만|TW|  
|터키|TR|  
|영국|GB|  
|미국|US|

## <a name="next-steps"></a>다음 단계
Bing News Search 엔드포인트에 대한 자세한 내용은 [News Search API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)를 참조하세요.
