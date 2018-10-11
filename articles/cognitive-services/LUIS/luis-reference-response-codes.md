---
title: LUIS(Language Understanding) API HTTP 응답 코드 - Azure | Microsoft Docs
titleSuffix: Azure
description: LUIS 작성 및 엔드포인트 API에서 반환되는 HTTP 응답 코드 이해
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/16/2018
ms.author: diberry
ms.openlocfilehash: f005c7f13cd05ce3ca6ce494c4a2670106cb3637
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900984"
---
# <a name="luis-api-http-response-codes"></a>LUIS API HTTP 응답 코드
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
|409|엔드포인트|아직 응용 프로그램을 로드 중입니다.|
|410|엔드포인트|응용 프로그램을 다시 학습한 후 다시 게시해야 합니다.|
|414|엔드포인트|쿼리가 최대 문자 제한을 초과했습니다.|
|429|작성, 엔드포인트|속도 제한(요청 수/초)을 초과했습니다.|