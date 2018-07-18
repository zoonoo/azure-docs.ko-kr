---
title: 번역 샘플 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 음성 번역에 대한 샘플은 다음과 같습니다.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1151628ed695e74e2196c20b08e33fa5eaf33282
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028390"
---
# <a name="sample-for-translation"></a>번역 샘플

> [!NOTE]
> 이 샘플 및 기타 샘플을 다운로드하기 위한 지침은 [Speech SDK용 샘플](samples.md)을 참조하세요.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 아래의 모든 샘플에서 다음 최상위 선언이 적용되어야 합니다.
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>마이크를 사용하여 번역

아래 코드 조각은 음성 입력을 영어에서 독일어로 번역하고 번역된 텍스트의 음성 출력을 제공하는 방법을 보여 줍니다. 이 기능은 마이크를 사용합니다.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>파일 입력을 사용하여 번역

아래 코드 조각은 음성 입력을 영어에서 독일어 및 프랑스어로 번역하는 방법을 보여 줍니다.
파일을 입력으로 사용합니다.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>샘플 소스 코드

최신 버전의 샘플 및 좀 더 수준 높은 샘플은 전용 [GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-speech-sdk)에 제공됩니다.

## <a name="next-steps"></a>다음 단계

- [음성 인식](./speech-to-text-sample.md)

- [의도 인식](./intent.md)
