---
title: C#용 Speech SDK를 사용하여 음성 번역
titleSuffix: Azure Cognitive Services
description: 이 문서에는 C# 환경의 Speech SDK를 사용하여 음성을 번역하는 샘플 코드가 포함되어 있습니다.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 8d45b152885274b63aff660f29fb28e1539f5cb3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215930"
---
# <a name="translate-speech-with-the-cognitive-services-speech-sdk-for-c"></a>Cognitive Services C#용 Speech SDK로 음성 번역

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-csharp[Translation from a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!INCLUDE [Introduction to using a file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-csharp[Translation from file input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 문서에서 사용된 코드는 samples/csharp/sharedcontent/console 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [음성을 인식하는 방법](how-to-recognize-speech-csharp.md)
- [음성에서 의도를 인식하는 방법](how-to-recognize-intents-from-speech-csharp.md)
