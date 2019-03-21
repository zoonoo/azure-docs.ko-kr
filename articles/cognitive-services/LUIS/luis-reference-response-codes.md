---
title: API HTTP 응답 코드
titleSuffix: Azure
description: LUIS 작성 및 엔드포인트 API에서 반환되는 HTTP 응답 코드 이해
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 18ec59200d3cf820794ac353e38106ad26aca697
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337247"
---
# <a name="common-api-response-codes-and-their-meaning"></a>일반적인 API 응답 코드 및 해당 의미

[작성](https://aka.ms/luis-authoring-apis) 및 [엔드포인트](https://aka.ms/luis-endpoint-apis) API는 HTTP 응답 코드를 반환합니다. 응답 메시지에는 요청과 관련된 정보가 포함되어 있지만 HTTP 응답 상태 코드는 일반적입니다. 

## <a name="common-status-codes"></a>일반 상태 코드
다음 표에는 [작성](https://aka.ms/luis-authoring-apis) 및 [엔드포인트](https://aka.ms/luis-endpoint-apis) API에 대한 가장 일반적인 몇 가지 HTTP 응답 상태 코드가 나와 있습니다.

|코드|API|설명|
|:--|--|--|
|400|작성, 엔드포인트|요청 매개 변수가 잘못되었습니다. 필수 매개 변수가 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|400|작성, 엔드포인트|요청 본문이 잘못되었습니다. JSON이 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|401|작성|작성 키 대신 엔드포인트 구독 키를 사용했습니다.|
|401|작성, 엔드포인트|잘못되었거나, 형식이 잘못되었거나, 비어 있는 키입니다.|
|401|작성, 엔드포인트| 키가 지역과 일치하지 않습니다.|
|401|작성|소유자 또는 협력자가 아닙니다.|
|401|작성|API 호출 순서가 잘못되었습니다.|
|403|작성, 엔드포인트|총 월별 키 할당량 한도를 초과했습니다.|
|409|엔드포인트|아직 애플리케이션을 로드 중입니다.|
|410|엔드포인트|애플리케이션을 다시 학습한 후 다시 게시해야 합니다.|
|414|엔드포인트|쿼리가 최대 문자 제한을 초과했습니다.|
|429|작성, 엔드포인트|속도 제한(요청 수/초)을 초과했습니다.|

## <a name="next-steps"></a>다음 단계

* REST API [작성](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) 및 [엔드포인트](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) 설명서
