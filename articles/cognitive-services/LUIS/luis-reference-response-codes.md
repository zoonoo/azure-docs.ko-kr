---
title: LUIS(Language Understanding) API HTTP 응답 코드 - Azure | Microsoft Docs
titleSuffix: Azure
description: LUIS 작성 및 끝점 API에서 반환되는 HTTP 응답 코드 이해
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 04/16/2018
ms.author: v-geberr
ms.openlocfilehash: 9c7381d9dc2ecf302c85c6b4f1f24b24a28d9ebe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374502"
---
# <a name="luis-api-http-response-codes"></a>LUIS API HTTP 응답 코드
[작성](https://aka.ms/luis-authoring-apis) 및 [끝점](https://aka.ms/luis-endpoint-apis) API는 HTTP 응답 코드를 반환합니다. 응답 메시지에는 요청과 관련된 정보가 포함되어 있지만 HTTP 응답 상태 코드는 일반적입니다. 

## <a name="common-status-codes"></a>일반 상태 코드
다음 표에는 [작성](https://aka.ms/luis-authoring-apis) 및 [끝점](https://aka.ms/luis-endpoint-apis) API에 대한 가장 일반적인 몇 가지 HTTP 응답 상태 코드가 나와 있습니다.

|코드|API|설명|
|:--|--|--|
|400|작성, 끝점|요청 매개 변수가 잘못되었습니다. 필수 매개 변수가 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|400|작성, 끝점|요청 본문이 잘못되었습니다. JSON이 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|401|작성|작성 키 대신 끝점 구독 키를 사용했습니다.|
|401|작성, 끝점|잘못되었거나, 형식이 잘못되었거나, 비어 있는 키입니다.|
|401|작성, 끝점| 키가 지역과 일치하지 않습니다.|
|401|작성|소유자 또는 협력자가 아닙니다.|
|401|작성|API 호출 순서가 잘못되었습니다.|
|403|작성, 끝점|총 월별 키 할당량 한도를 초과했습니다.|
|409|끝점|아직 응용 프로그램을 로드 중입니다.|
|410|끝점|응용 프로그램을 다시 학습한 후 다시 게시해야 합니다.|
|414|끝점|쿼리가 최대 문자 제한을 초과했습니다.|
|429|작성, 끝점|속도 제한(요청 수/초)을 초과했습니다.|