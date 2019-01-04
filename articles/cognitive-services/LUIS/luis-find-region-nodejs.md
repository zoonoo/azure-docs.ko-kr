---
title: 엔드포인트 지역, Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: Node.js를 사용하면 LUIS에 대한 엔드포인트 키 및 애플리케이션 ID를 사용하여 프로그래밍 방식으로 게시 지역을 찾습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 6bcea43fb21cae6f000f3522b498dfc6c0f6d9a7
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166220"
---
# <a name="find-endpoint-region-with-nodejs"></a>Node.js를 사용하여 엔드포인트 지역 찾기
LUIS 앱 ID와 LUIS 구독 ID가 있는 경우 엔드포인트 쿼리에 사용할 지역을 찾을 수 있습니다.

> [!NOTE] 
> 전체 Node.js 솔루션은 [**LUIS-Samples** GitHub 리포지토리](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/)에서 사용할 수 있습니다.

## <a name="luis-endpoint-query-strategy"></a>LUIS 엔드포인트 쿼리 전략
각 LUIS 엔드포인트 쿼리에는 다음이 필요합니다.

* 끝점 키
* 앱 ID
* 지역

LUIS 끝점 쿼리에서 올바른 끝점 키와 앱 ID를 사용하지만 잘못된 지역을 사용하는 경우 응답 코드는 401입니다. 401 요청은 구독 할당량으로 계산되지 않습니다. 이 요청을 모든 지역을 폴링하는 전략으로 전환하여 올바른 지역을 찾습니다. 올바른 지역은 2xx 상태 코드를 반환하는 유일한 요청입니다. 

|응답 코드|매개 변수|
|--|--|
|2xx|올바른 끝점 키<br>올바른 앱 ID<br>올바른 호스트 지역|
|401|올바른 끝점 키<br>올바른 앱 ID<br>‘잘못된’ 호스트 지역|

## <a name="nodejs-code-to-find-region"></a>지역을 찾는 Node.js 코드
콘솔 응용 프로그램은 LUIS 앱 ID와 끝점 키를 사용하며 이와 연결된 모든 지역을 반환합니다. 현재 끝점 키는 지역별로 만들어지므로 하나의 지역만 반환되어야 합니다.

NPM 종속성을 포함합니다.

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

상수를 추가합니다. `subscriptionKey` 및 `appId`의 변수 값을 고유한 값으로 바꿉니다.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

`searchRegions` 함수를 추가하여 지역을 찾습니다. 모든 잘못된 지역은 401을 반환하며 이는 catch되고 무시됩니다.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

`searchRegions` 함수를 호출하고 단일 지역을 반환합니다.

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

응용 프로그램이 실행되면 터미널에는 앱 ID에 해당하는 지역이 표시됩니다.

![LUIS 지역을 보여주는 콘솔 앱 스크린샷](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>다음 단계

LUIS [지역](luis-reference-regions.md)에 대해 자세히 알아봅니다.
