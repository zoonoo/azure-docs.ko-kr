---
title: LUIS 미리 빌드된 엔터티 참조 - Azure | Microsoft Docs
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)에 포함된 미리 빌드된 엔터티 목록이 포함되어 있습니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 7ce50e4c0be605e1700a2c18533cb087384f524c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316887"
---
# <a name="entities-per-culture"></a>문화권별 엔터티

LUIS(Language Understanding)에서는 미리 빌드된 엔터티를 제공합니다. 미리 빌드된 엔터티가 응용 프로그램에 포함되어 있으면 LUIS의 끝점 응답에는 해당 엔터티 예측이 포함됩니다. 또한 모든 예제 발언은 해당 엔터티로 레이블이 지정됩니다. 미리 빌드된 엔터티의 동작은 수정할 수 **없습니다**. 다른 설명이 없는 한, 미리 빌드된 엔터티는 모든 LUIS 응용 프로그램 로캘(문화권)에서 사용할 수 있습니다. 다음 표에는 각 문화권에 대해 지원되는 미리 빌드된 엔터티가 나와 있습니다.

미리 빌드된 엔터티   |   영어(미국)<br>```En-us```   |   프랑스어(프랑스)<br>```fr-FR```   |   이탈리아어(이탈리아)<br>```it-IT```   |   스페인어(스페인)<br>```es-ES```   |   중국어<br>```zh-CN```   |   독일어<br>```de-DE```   |   포르투갈어(브라질)<br>```pt-BR```   |   일본어(일본)<br>```ja-JP```   |   한국어(한국)<br>```ko-kr```   | 프랑스어(캐나다)<br>```fr-CA```   |   스페인어(멕시코)<br>```es-MX```   |   네덜란드어(네덜란드)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>일   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Currency](luis-reference-prebuilt-currency.md):<br>dollar<br>소수부 단위(예: penny)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>실시간<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Email](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Number](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

[사용되지 않는 미리 빌드된 엔터티](luis-reference-prebuilt-deprecated.md)에 대한 참고를 참조하세요.

## <a name="examples-of-prebuilt-entities-in-en-us-culture"></a>en-us 문화권의 미리 빌드된 엔터티 예
다음 표에는 미리 빌드된 엔터티와 예제 데이터 및 반환 값이 제공됩니다.

미리 빌드된 엔터티   |   예제 발언   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) 참조 | [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) 참조 |
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.email```     |  ```patti.owens@microsoft.com```   |```{ "type": "builtin.email", "entity": "patti.owens@microsoft.com" }```|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.phonenumber```   |   ```my mobile is 00 44 161 1234567```   |```{ "type": "builtin.phonenumber", "entity": "00 44 161 1234567" }```|
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.url```     |   ```http://www.luis.ai is a great cognitive service```   |```{ "type": "builtin.url", "entity": "http://www.luis.ai" }```|


## <a name="contribute-to-prebuilt-entity-cultures"></a>미리 빌드된 엔터티 문화권에 참여
미리 빌드된 엔터티는 Recognizers-Text open-source 프로젝트에서 개발됩니다. 이 프로젝트에 [참여](https://github.com/Microsoft/Recognizers-Text)하세요. 이 프로젝트에는 문화권별 currency 예제가 포함되어 있습니다. 

## <a name="next-steps"></a>다음 단계

[number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) 및 [currency](luis-reference-prebuilt-currency.md) 엔터티에 대해 알아봅니다. 