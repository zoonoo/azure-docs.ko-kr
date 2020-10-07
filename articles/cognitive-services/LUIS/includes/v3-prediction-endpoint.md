---
title: V3 예측 엔드포인트를 가져오는 방법
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91545345"
---
1. LUIS 포털의 **관리** 섹션(오른쪽 상단 메뉴)에 있는 **Azure 리소스** 페이지(왼쪽 메뉴)의 **예측 리소스** 탭에서 페이지 하단에 있는 **예제 쿼리**를 복사합니다.

    URL을 새 브라우저 탭에 붙여넣습니다.

    URL에는 앱 ID, 키 및 슬롯 이름이 있습니다. V3 예측 엔드포인트 URL은 다음과 같습니다.

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

