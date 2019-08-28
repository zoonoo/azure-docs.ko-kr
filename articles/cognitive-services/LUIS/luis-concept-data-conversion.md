---
title: 데이터 변환-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)에서 예측 전에 발언이 변경될 수 있는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619796"
---
# <a name="convert-data-format-of-utterances"></a>발언의 데이터 형식 변환
LUIS는 예측 전 사용자 utterance를 다음과 같이 변환 합니다. "

* 음성 텍스트 [Cognitive Services 음성](../Speech-Service/overview.md) 서비스를 사용 합니다. 

## <a name="speech-to-text"></a>음성을 텍스트로 변환

음성 텍스트는 LUIS와의 통합으로 제공 됩니다. 

### <a name="intent-conversion-concepts"></a>의도 변환 개념
LUIS의 음성 텍스트 변환 기능을 사용하여 음성 발언을 엔드포인트로 보내고, LUIS 예측 응답을 수신할 수 있습니다. 이 프로세스는 [Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) Service를 LUIS와 통합하는 것입니다. [자습서](../speech-service/how-to-recognize-intents-from-speech-csharp.md)에서 음성 의도 변환에 대해 자세히 알아보세요.

### <a name="key-requirements"></a>핵심 요구 사항
이 통합을 위해 **Bing Speech API** 키를 만들 필요는 없습니다. Azure Portal에서 만든 **Language Understanding** 키가 이 통합에 사용됩니다. LUIS 시작 키는 사용하지 마세요.

### <a name="pricing-tier"></a>가격 책정 계층
이 통합에서는 Language Understanding 가격 책정 계층 이외의 다른 [가격 책정](luis-boundaries.md#key-limits) 모델을 사용합니다. 

### <a name="quota-usage"></a>할당량 사용량
[제한 키](luis-boundaries.md#key-limits)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데이터 추출](luis-concept-data-extraction.md)

