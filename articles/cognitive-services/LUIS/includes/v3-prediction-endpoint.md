---
title: V3 예측 엔드포인트를 가져오는 방법
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128221"
---
1. LUIS 포털의 **관리** 섹션(오른쪽 상단 메뉴)에 있는 **Azure 리소스** 페이지(왼쪽 메뉴)의 **예측 리소스** 탭에서 페이지 하단에 있는 **예제 쿼리** 를 복사합니다. URL에는 앱 ID, 키 및 슬롯 이름이 있습니다. V3 예측 엔드포인트 URL의 형식은 다음(`https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`)과 같습니다.

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="예측 리소스 섹션의 예제 쿼리" lightbox="../media/prediction-resources-example-query.png":::
    
    URL을 새 브라우저 탭에 붙여넣습니다. URL이 표시되지 않는 경우 예측 리소스가 없는 것이므로 예측 리소스를 만들어야 합니다. 

    

    

