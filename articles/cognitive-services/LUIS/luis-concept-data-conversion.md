---
title: LUIS의 데이터 변환 개념 - Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)에서 예측 전에 발언이 변경될 수 있는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9324f7b4f7bed844f16d17b8960878892be4b165
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638395"
---
# <a name="data-conversion-concepts-in-luis"></a>LUIS의 데이터 변환 개념
LUIS는 Cognitive Services Speech 서비스를 사용하여 예측 전에 음성 발언을 텍스트로 변환하는 방법을 제공합니다. 

## <a name="speech-to-intent-conversion-concepts"></a>음성 의도 변환 개념
LUIS의 음성 텍스트 변환 기능을 사용하여 음성 발언을 엔드포인트로 보내고, LUIS 예측 응답을 수신할 수 있습니다. 이 프로세스는 [Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) Service를 LUIS와 통합하는 것입니다. 

### <a name="key-requirements"></a>핵심 요구 사항
이 통합을 위해 **Bing Speech API** 키를 만들 필요는 없습니다. Azure Portal에서 만든 **Language Understanding** 키가 이 통합에 사용됩니다. LUIS 시작 키는 이러한 통합에 작동하지 않으므로 사용하지 마세요.

### <a name="new-endpoint"></a>새 엔드포인트 
이러한 통합은 새 엔드포인트와 [가격 책정](luis-boundaries.md#key-limits) 모델을 만듭니다. [Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk)를 통한 엔드포인트는 음성 발언과 텍스트를 둘 다 수신할 수 있으므로 단일 엔드포인트로 사용할 수 있습니다. 

### <a name="quota-usage"></a>할당량 사용량
[제한 키](luis-boundaries.md#key-limits)를 참조하세요. 

### <a name="data-retention"></a>데이터 보존
음성이든 텍스트든 관계없이, Speech SDK를 통해 엔드포인트로 전송된 데이터는 음성 모델을 향상시키는 데만 사용됩니다. 사용자 모델 외에, 일반적인 용량에서 음성 또는 LUIS를 향상시키는 데는 사용되지 않습니다. LUIS 앱이 삭제되면 보존된 데이터도 삭제됩니다.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 텍스트 변환 사용](luis-tutorial-speech-to-intent.md)

