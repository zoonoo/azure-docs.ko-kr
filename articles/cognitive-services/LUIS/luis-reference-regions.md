---
title: 게시 지역 및 엔드포인트
titleSuffix: Azure Cognitive Services
description: LUIS 앱을 게시하는 지역은 Azure Portal에서 Azure LUIS 엔드포인트 키를 만들 때 Azure Portal에서 지정한 지역 또는 위치에 해당합니다. 앱을 게시하면 LUIS가 키와 연결된 지역의 엔드포인트 URL을 자동으로 생성합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/10/2019
ms.author: diberry
ms.openlocfilehash: 655a2c0e2a7ee483dccbf129f36611d0b36df61f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246316"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>작성 및 게시 지역과 관련 키

LUIS 앱을 게시하는 지역은 Azure Portal에서 Azure LUIS 엔드포인트 키를 만들 때 Azure Portal에서 지정한 지역 또는 위치에 해당합니다. [앱을 게시](./luis-how-to-publish-app.md)하면 LUIS가 키와 연결된 지역의 엔드포인트 URL을 자동으로 생성합니다. LUIS 앱을 둘 이상의 지역에 게시하려면 지역당 하나 이상의 키가 필요합니다. 

## <a name="luis-website"></a>LUIS 웹 사이트
지역에 따라 세 가지 LUIS 웹 사이트가 있습니다. 동일한 지역에서 작성하고 게시해야 합니다. 

|LUIS|지역|
|--|--|
|[www.luis.ai][www.luis.ai]|미국<br>유럽 이외<br>오스트레일리아 이외|
|[au.luis.ai][au.luis.ai]|오스트레일리아|
|[eu.luis.ai][eu.luis.ai]|유럽|

## <a name="regions-and-azure-resources"></a>지역 및 Azure 리소스
앱은 LUIS 포털에 추가된 LUIS 리소스와 관련된 모든 지역에 게시됩니다. 예를 들어, [www.luis.ai][www.luis.ai]에서 만든 앱의 경우 **westus**에서 LUIS 리소스를 만든 후 앱에 리소스로 추가하면 이 앱은 해당 지역에 게시됩니다. 

## <a name="public-apps"></a>공용 앱
공용 앱은 모든 지역에 게시되므로 지역 기반 LUIS 리소스 키를 가진 사용자가 해당 리소스 키와 연결된 지역에서 앱에 액세스할 수 있습니다.

## <a name="publishing-regions"></a>게시 지역

https://www.luis.ai에서 만들어진 LUIS 앱은 [유럽](#publishing-to-europe) 및 [오스트레일리아](#publishing-to-australia) 지역을 제외한 모든 엔드포인트에 게시될 수 있습니다. 

작성 지역 앱은 해당 게시 지역에만 게시할 수 있습니다. 현재 앱이 잘못된 작성 지역에 있는 경우 앱을 내보내고 게시 지역의 올바른 작성 지역으로 가져옵니다. 

 글로벌 지역 | API 지역 제작 및 웹 사이트 작성| 게시 및 쿼리 지역<br>`API region name`   |  엔드포인트 URL 형식   |
|-----|------|------|------|
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 인도 중부<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 동아시아<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 일본 동부<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 일본 서부<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 한국 중부<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 동남아시아<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[오스트레일리아](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| 오스트레일리아 동부<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[유럽](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 프랑스 중부<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[유럽](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 북유럽<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[유럽](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 서유럽<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[유럽](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 영국 남부<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 캐나다 중부<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 미국 중부<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 미국 동부<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 미국 동부 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 미국 중북부<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 북아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 미국 중남부<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 미국 중서부<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 미국 서부<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 미국 서부 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 남미 | `westus`<br>[www.luis.ai][www.luis.ai] | 브라질 남부<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |




## <a name="publishing-to-europe"></a>유럽에 게시

유럽 지역에 게시하려면 https://eu.luis.ai에만 LUIS 앱을 만듭니다. 유럽 지역의 키를 사용하여 다른 곳에 게시하려고 하면 LUIS가 경고 메시지를 표시합니다. 대신 https://eu.luis.ai 을(를) 사용하세요. [https://eu.luis.ai][eu.luis.ai]에서 만들어진 LUIS 앱은 다른 지역으로 자동으로 마이그레이션되지 않습니다. LUIS 앱을 마이그레이션하려면 내보낸 다음, 가져옵니다.

## <a name="publishing-to-australia"></a>오스트레일리아에 게시

오스트레일리아 지역에 게시하려면 https://au.luis.ai에만 LUIS 앱을 만듭니다. 오스트레일리아 지역의 키를 사용하여 다른 곳에 게시하려고 하면 LUIS가 경고 메시지를 표시합니다. 대신 https://au.luis.ai 을(를) 사용하세요. [https://au.luis.ai][au.luis.ai]에서 만들어진 LUIS 앱은 다른 지역으로 자동으로 마이그레이션되지 않습니다. LUIS 앱을 마이그레이션하려면 내보낸 다음, 가져옵니다.

## <a name="endpoints"></a>엔드포인트

LUIS에는 현재 2개의 엔드포인트가 있음: 하나는 작성용이고 다른 하나는 텍스트 분석용입니다.

|목적|URL|
|--|--|
|작성|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|텍스트 분석(쿼리 예측)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

다음 표에서는 앞의 표에서 중괄호 `{}`로 표시된 매개 변수에 대해 설명합니다.

|매개 변수|목적|
|--|--|
|region|Azure 지역 - 작성 및 게시에 서로 다른 지역 포함|
|appID|URL 경로에 사용되며 앱 대시보드에서 발견된 LUIS 앱 ID|
|q|채팅 봇과 같은 클라이언트 애플리케이션에서 보낸 발언 텍스트|


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai