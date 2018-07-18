---
title: Azure의 Bing Web Search API에 대해 지원되는 국가 및 언어 | Microsoft Docs
description: Bing Web Search API에서 지원하는 국가 및 언어를 확인합니다.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 7b62c4a4feb7144662a8fe4d692f11f1efe5db1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373303"
---
# <a name="bing-web-search-countries-and-languages"></a>Bing Web Search 국가 및 언어

Bing Web Search API는 수십 개의 국가를 지원하며, 이 중에는 둘 이상의 언어를 사용하는 국가도 많습니다. 쿼리를 통한 국가 지정은 해당 국가의 관심 분야에 따라 검색 결과를 구체화하는 데 주로 사용됩니다. 또한 결과는 Bing에 대한 링크를 포함할 수 있으며 이러한 링크는 지정된 국가 또는 언어에 따라 Bing 사용자 경험을 지역화할 수 있습니다.

`cc` 쿼리 매개 변수를 사용하여 국가를 지정할 수 있습니다. 국가를 지정하는 경우 `Accept-Language` HTTP 헤더를 사용하여 언어 코드도 하나 이상 지정해야 합니다. 지원되는 언어는 국가마다 다르며, 지역/국가 표에서 각 국가에 대해 제공됩니다.

또는 **지역/국가** 표에서 `mkt` 쿼리 매개 변수 및 코드를 사용하여 지역/국가를 지정할 수 있습니다. 지역/국가 지정은 동시에 국가 및 기본 언어를 지정합니다. `setLang` 쿼리 매개 변수는 이 경우 언어 코드로 설정할 수 있습니다. 사용자가 Bing을 다른 언어로 보길 선호하지 않는 한 주로 `mkt`에 의해 지정된 동일한 언어입니다.

## <a name="countries"></a>국가

|국가|코드|
|-------|----|
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
|홍콩|HK|
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
|남아프리카|ZA|
|스페인|ES|
|스웨덴|SE|
|스위스|CH|
|대만|TW|
|터키|TR|
|영국|GB|
|미국|US|


## <a name="markets"></a>지역/국가

|국가|언어|지역/국가 코드|
|-------|--------|-----------|
|아르헨티나|스페인어|es-AR|
|오스트레일리아|영어|en-AU|
|오스트리아|독일어|de-AT|
|벨기에|네덜란드어|nl-BE|
|벨기에|프랑스어|fr-BE|
|브라질|포르투갈어|pt-BR|
|캐나다|영어|en-CA|
|캐나다|프랑스어|fr-CA|
|칠레|스페인어|es-CL|
|덴마크|덴마크어|da-DK|
|핀란드|핀란드어|fi-FI|
|프랑스|프랑스어|fr-FR|
|독일|독일어|de-DE|
|홍콩|중국어 번체|zh-HK|
|인도|영어|en-IN|
|인도네시아|영어|en-ID|
|이탈리아|이탈리아어|it-IT|
|일본|일본어|ja-JP|
|한국|한국어|ko-KR|
|말레이시아|영어|en-MY|
|멕시코|스페인어|es-MX|
|네덜란드|네덜란드어|nl-NL|
|뉴질랜드|영어|en-NZ|
|노르웨이|노르웨이어|no-NO|
|중국|중국어|zh-CN|
|폴란드|폴란드어|pl-PL|
|포르투갈|포르투갈어|pt-PT|
|필리핀|영어|en-PH|
|러시아|러시아어|ru-RU|
|사우디아라비아|아랍어|ar-SA|
|남아프리카|영어|en-ZA|
|스페인|스페인어|es-ES|
|스웨덴|스웨덴어|sv-SE|
|스위스|프랑스어|fr-CH|
|스위스|독일어|de-CH|
|대만|중국어 번체|zh-TW|
|터키|터키어|tr-TR|
|영국|영어|en-GB|
|미국|영어|ko-KR|
|미국|스페인어|es-US|
