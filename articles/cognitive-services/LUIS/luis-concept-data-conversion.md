---
title: LUIS의 데이터 변환 개념 이해 - Azure | Microsoft Docs
description: LUIS(Language Understanding)에서 예측 전에 발언이 변경될 수 있는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/27/2018
ms.author: v-geberr;
ms.openlocfilehash: 16b0df4b81220885e2c3747470272cee9536e10c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063564"
---
# <a name="data-conversion-concepts-in-luis"></a>LUIS의 데이터 변환 개념
LUIS는 예측 전에 음성 발언을 텍스트로 변환하는 방법을 제공합니다. 

## <a name="speech-to-intent-conversion-concepts"></a>음성 의도 변환 개념
LUIS의 음성 텍스트 변환 기능을 사용하여 음성 발언을 끝점으로 보내고, LUIS 예측 응답을 수신할 수 있습니다. 이 프로세스는 [Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) Service를 LUIS와 통합하는 것입니다. 

### <a name="key-requirements"></a>핵심 요구 사항
이 통합을 위해 **Bing Speech API** 키를 만들 필요는 없습니다. Azure Portal에서 만든 **Language Understanding** 키가 이 통합에 사용됩니다. LUIS 시작 키는 이러한 통합에 작동하지 않으므로 사용하지 마세요.

### <a name="new-endpoint"></a>새 끝점 
이러한 통합은 새 끝점과 [가격 책정](luis-boundaries.md#key-limits) 모델을 만듭니다. [Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk)를 통한 끝점은 음성 발언과 텍스트를 둘 다 수신할 수 있으므로 단일 끝점으로 사용할 수 있습니다. 

### <a name="quota-usage"></a>할당량 사용량
[제한 키](luis-boundaries.md#key-limits)를 참조하세요. 

### <a name="data-retention"></a>데이터 보존
음성이든 텍스트든 관계없이, Speech SDK를 통해 끝점으로 전송된 데이터는 음성 모델을 향상시키는 데만 사용됩니다. 사용자 모델 외에, 일반적인 용량에서 음성 또는 LUIS를 향상시키는 데는 사용되지 않습니다. LUIS 앱이 삭제되면 보존된 데이터도 삭제됩니다.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 텍스트 변환 사용](luis-tutorial-speech-to-intent.md)

