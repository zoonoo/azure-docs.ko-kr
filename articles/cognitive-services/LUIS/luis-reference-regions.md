---
title: LUIS(Language Understanding) 지역 및 엔드포인트 - Azure - Cognitive Services LUIS | Microsoft Docs
titleSuffix: Azure
description: LUIS 앱을 게시하는 지역은 Azure Portal에서 Azure LUIS 엔드포인트 키를 만들 때 Azure Portal에서 지정한 지역 또는 위치에 해당합니다. 앱을 게시하면 LUIS가 키와 연결된 지역의 엔드포인트 URL을 자동으로 생성합니다. LUIS 앱을 둘 이상의 지역에 게시하려면 지역당 하나 이상의 키가 필요합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 7812ce37cb22c8774c785f5f645b8fef90b02a3e
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42815506"
---
# <a name="regions-and-keys"></a>지역 및 키

LUIS 앱을 게시하는 지역은 Azure Portal에서 Azure LUIS 엔드포인트 키를 만들 때 Azure Portal에서 지정한 지역 또는 위치에 해당합니다. [앱을 게시](./luis-how-to-publish-app.md)하면 LUIS가 키와 연결된 지역의 엔드포인트 URL을 자동으로 생성합니다. LUIS 앱을 둘 이상의 지역에 게시하려면 지역당 하나 이상의 키가 필요합니다. 

## <a name="luis-website"></a>LUIS 웹 사이트
지역에 따라 세 가지 LUIS 웹 사이트가 있습니다. 동일한 지역에서 작성하고 게시해야 합니다. 

|LUIS|지역|
|--|--|
|[www.luis.ai][www.luis.ai]|미국<br>유럽 이외<br>오스트레일리아 이외|
|[au.luis.ai][au.luis.ai]|오스트레일리아|
|[eu.luis.ai][eu.luis.ai]|유럽|


## <a name="publishing-regions"></a>게시 지역

https://www.luis.ai에서 만들어진 LUIS 앱은 [유럽](#publishing-to-europe) 및 [오스트레일리아](#publishing-to-australia) 지역을 제외한 모든 엔드포인트에 게시될 수 있습니다. 

작성 지역 앱은 해당 게시 지역에만 게시할 수 있습니다. 현재 앱이 잘못된 작성 지역에 있는 경우 앱을 내보내고 게시 지역의 올바른 작성 지역으로 가져옵니다. 

 글로벌 지역 | 작성 지역 | 게시 및 쿼리 지역   |   LUIS 웹 사이트 | 엔드포인트 URL 형식   |
|-----|------|------|------|------|
| 아시아 | 미국 서부| 동아시아     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | 미국 서부| 동남아시아     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[오스트레일리아](#publishing-to-australia) | 오스트레일리아 동부| 오스트레일리아 동부     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[유럽](#publishing-to-europe)| 서유럽| 북유럽     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[유럽](#publishing-to-europe) | 서유럽| 서유럽     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 북아메리카 | 미국 서부 | 미국 동부      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | 미국 서부 | 미국 동부 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | 미국 서부 | 미국 중남부     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 북아메리카 | 미국 서부 | 미국 중서부     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | 미국 서부 | 미국 서부 |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 북아메리카 | 미국 서부 | 미국 서부 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 남미 | 미국 서부 | 브라질 남부     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>유럽에 게시

유럽 지역에 게시하려면 https://eu.luis.ai에만 LUIS 앱을 만듭니다. 유럽 지역의 키를 사용하여 다른 곳에 게시하려고 하면 LUIS가 경고 메시지를 표시합니다. 대신 https://eu.luis.ai을(를) 사용하세요. [https://eu.luis.ai][eu.luis.ai]에서 만들어진 LUIS 앱은 다른 지역으로 자동으로 마이그레이션되지 않습니다. LUIS 앱을 마이그레이션하려면 내보낸 다음, 가져옵니다.

## <a name="publishing-to-australia"></a>오스트레일리아에 게시

오스트레일리아 지역에 게시하려면 https://au.luis.ai에만 LUIS 앱을 만듭니다. 오스트레일리아 지역의 키를 사용하여 다른 곳에 게시하려고 하면 LUIS가 경고 메시지를 표시합니다. 대신 https://au.luis.ai을(를) 사용하세요. [https://au.luis.ai][au.luis.ai]에서 만들어진 LUIS 앱은 다른 지역으로 자동으로 마이그레이션되지 않습니다. LUIS 앱을 마이그레이션하려면 내보낸 다음, 가져옵니다.

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
|q|채팅 봇과 같은 클라이언트 응용 프로그램에서 보낸 발언 텍스트|


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai