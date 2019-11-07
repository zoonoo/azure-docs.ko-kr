---
title: V3 예측 엔드포인트를 가져오는 방법
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495242"
---
1. LUIS 포털의 관리 섹션(오른쪽 위 메뉴)에 있는 키 및 엔드포인트 페이지(왼쪽 메뉴)에서, 페이지 맨 아래의 엔드포인트 URL을 선택합니다.

    그러면 주소 표시줄에 엔드포인트 URL이 표시된 브라우저 탭이 열립니다.

    URL에는 앱 ID, 키 및 슬롯 이름이 있습니다. V3 예측 엔드포인트 URL은 다음과 같습니다.

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

