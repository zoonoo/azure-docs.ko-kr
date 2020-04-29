---
title: Cognitive Services Text Analytics 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Cognitive Services Text Analytics 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876440"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **리소스 만들기**를 선택 하 고 **AI + Machine Learning** > **Text Analytics**로 이동 합니다.
   또는 [Create Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)로 이동 합니다.
1. 필요한 설정을 모두 입력 합니다.

    |설정|값|
    |--|--|
    |속성|이름 (2-64 자)을 입력 합니다.|
    |Subscription|적절한 구독을 선택합니다.|
    |위치|주변 위치를 선택 합니다.|
    |가격 책정 계층| 표준 가격 책정 계층 인 **S**를 입력 합니다.|
    |Resource group|사용 가능한 리소스 그룹을 선택 합니다.|

1. **만들기**를 선택 하 고 리소스가 생성 될 때까지 기다립니다. 브라우저가 새로 만든 리소스 페이지로 자동으로 리디렉션됩니다.
1. 구성 `endpoint` 된 및 API 키를 수집 합니다.

    |포털의 리소스 탭|설정|Value|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사 합니다. 와 유사 하 게 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`표시 됩니다.|
    |**키**|API 키|두 키 중 하나를 복사 합니다. <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>는 공백이 나 대시가 없는 32 자리의 영숫자 문자열입니다.|
