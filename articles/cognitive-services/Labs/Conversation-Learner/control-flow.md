---
title: Conversation Learner 제어 흐름 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 제어 흐름에 대해 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 22a2a3472a54188f9298c580a95d53ac681822aa
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385422"
---
## <a name="control-flow"></a>흐름 제어

이 문서에서는 아래 다이어그램에 표시된 CL(Conversation Learner)의 제어 흐름을 설명합니다.

![](media/controlflow.PNG)

1. 사용자가 봇에 용어 또는 구를 입력합니다(예: ‘what's the weather in Seattle?’).
1. CL이 엔터티를 추출하는 Machine Learning 모델에 사용자 입력을 전달합니다.
   - 이 모델은 Conversation Learner에서 빌드되고 www.luis.ai에 호스트됩니다.
1. 추출된 엔터티 및 사용자 입력 텍스트가 봇 코드의 엔터티 검색 콜백 메서드에 전달됩니다.
    - 이 코드는 엔터티 값을 설정/지우기/조작할 수 있습니다.
1. CL 신경망이 엔터티 추출 출력과 사용자 입력을 사용하여 봇에 정의된 모든 작업에 점수를 지정합니다.
   - 이 예제에서 확률이 가장 높은 작업은 날씨 예측을 제공하는 것입니다.

     ![](media/controlflow_forecast.PNG)

1. 이 경우, 선택한 작업에 날씨 예측을 검색하기 위한 API 호출이 필요합니다. 
1. 그런 다음, CL.AddCallback 메서드를 사용하여 등록된 이 API가 호출됩니다.  이 API의 결과가 사용자에게 메시지로 반환됩니다(예: ‘Sunny with a high of 67’).
1. 이전 단계에 따라 다음 작업을 결정하기 위해 신경망이 호출됩니다.
1. 신경망이 가능한 다음 작업 집합을 예측하고, 선택한 작업(여기서는 ‘Anything else?’)이 사용자에게 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대화 학습자를 사용 하 여 설명 하는 방법](./how-to-teach-cl.md)
