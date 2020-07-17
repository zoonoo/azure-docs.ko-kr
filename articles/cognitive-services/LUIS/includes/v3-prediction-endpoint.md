---
title: V3 예측 엔드포인트를 가져오는 방법
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589151"
---
1. LUIS 포털의 **관리** 섹션(오른쪽 상단 메뉴)에 있는 **Azure 리소스** 페이지(왼쪽 메뉴)의 **예측 리소스** 탭에서 페이지 하단에 있는 **예제 쿼리**를 복사합니다.

    URL을 새 브라우저 탭에 붙여넣습니다.

    URL에는 앱 ID, 키 및 슬롯 이름이 있습니다. V3 예측 엔드포인트 URL은 다음과 같습니다.

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

