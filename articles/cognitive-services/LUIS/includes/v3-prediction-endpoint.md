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
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80287750"
---
1. LUIS 포털의 **관리** 섹션(오른쪽 상단 메뉴)에 있는 **Azure 리소스** 페이지(왼쪽 메뉴)의 **예측 리소스** 탭에서 페이지 하단에 있는 **예제 쿼리**를 복사합니다.

    URL을 새 브라우저 탭에 붙여넣습니다.

    URL에는 앱 ID, 키 및 슬롯 이름이 있습니다. V3 예측 엔드포인트 URL은 다음과 같습니다.

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

