---
title: C#용 Speech SDK를 사용하여 음성 인식
titleSuffix: Microsoft Cognitive Services
description: >
  C#용 Speech SDK를 사용하여(파일의 음성, 마이크의 음성을 사용자 정의된 모델로, 연속해서 또는 단발로) 음성을 인식하는 방법을 알아봅니다.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: c5ccd813dc1210eabd201c7628b41cc592fb5f6a
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127386"
---
# <a name="recognize-speech-by-using-the-speech-sdk-for-c"></a>C#용 Speech SDK를 사용하여 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-intro.md)]

[!INCLUDE [Introduction for top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-microphone.md)]

[!code-csharp[Speech recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!INCLUDE [Introduction to using customized recognition](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-customized.md)]

[!code-csharp[Speech recognition by using a customized model](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionCustomized)]

[!INCLUDE [Introduction to using a continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-continuous.md)]

[!code-csharp[Continuous speech recognition](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionContinuousWithFile)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 문서에서 사용된 코드는 samples/csharp/sharedcontent/console 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [음성에서 의도를 인식하는 방법](how-to-recognize-intents-from-speech-csharp.md)
- [음성을 번역하는 방법](how-to-translate-speech-csharp.md)

