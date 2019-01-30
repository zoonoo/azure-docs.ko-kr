---
title: '빠른 시작: 음성 변환, C++(Windows) - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 사용자 음성을 캡처하고, 다른 언어로 변환하고, 명령줄에 텍스트를 출력하는 간단한 C++ 애플리케이션을 만듭니다. 이 가이드는 Windows 사용자를 위해 설계되었습니다.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 1531ad879b3345cfa089792220e7a2044570a6f8
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729682"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>빠른 시작: C++용 Speech SDK를 사용하여 음성 번역

이 빠른 시작에서는 컴퓨터의 마이크에서 사용자 음성을 캡처하고, 음성을 변환하고, 변환된 텍스트를 실시간으로 명령줄에 변환하는 간단한 C++ 애플리케이션을 만듭니다. 이 애플리케이션은 64비트 Windows에서 실행되도록 설계되었으며 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017로 빌드되었습니다.

음성 변환에 사용할 수 있는 언어의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service에 대한 Azure 구독 키 [무료로 가져올 수 있습니다](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. 원본 파일 *helloworld.cpp*를 엽니다. 다음을 사용하여 아래 코드를 초기 include 문(`#include "stdafx.h"` 또는 `#include "pch.h"`)으로 바꿉니다.

    ```cpp
    #include "pch.h"
    #include <iostream>
    #include <vector>
    #include <speechapi_cxx.h>

    using namespace std;
    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Translation;

    // Translation with microphone input.
    void TranslationWithMicrophone()
    {
        // Creates an instance of a speech translation config with specified subscription key and service region.
        // Replace with your own subscription key and service region (e.g., "westus").
        auto config = SpeechTranslationConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

        // Sets source and target languages
        // Replace with the languages of your choice.
        auto fromLanguage = "en-US";
        config->SetSpeechRecognitionLanguage(fromLanguage);
        config->AddTargetLanguage("de");
        config->AddTargetLanguage("fr");

        // Creates a translation recognizer using microphone as audio input.
        auto recognizer = TranslationRecognizer::FromConfig(config);
        cout << "Say something...\n";

        // Starts translation. RecognizeOnceAsync() returns when the first utterance has been recognized,
        // so it is suitable only for single shot recognition like command or query. For long-running
        // recognition, use StartContinuousRecognitionAsync() instead.
        auto result = recognizer->RecognizeOnceAsync().get();

        // Checks result.
        if (result->Reason == ResultReason::TranslatedSpeech)
        {
            cout << "RECOGNIZED: Text=" << result->Text << std::endl
                << "  Language=" << fromLanguage << std::endl;

            for (const auto& it : result->Translations)
            {
                cout << "TRANSLATED into '" << it.first.c_str() << "': " << it.second.c_str() << std::endl;
            }
        }
        else if (result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << result->Text << " (text could not be translated)" << std::endl;
        }
        else if (result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
        else if (result->Reason == ResultReason::Canceled)
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

            if (cancellation->Reason == CancellationReason::Error)
            {
                cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
    }

    int wmain()
    {
        TranslationWithMicrophone();
        cout << "Please press a key to continue.\n";
        cin.get();
        return 0;
    }
    ```

1. 동일한 파일에서 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 애플리케이션을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 오류 없이 컴파일됩니다.

   ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-cpp-windows-06-build.png)

1. 애플리케이션을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

   ![디버깅 시작 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 콘솔 창이 나타나면 무엇이든 말해보라는 메시지가 표시됩니다. 영어 구 또는 문장을 말씀하세요. 음성은 음성 서비스로 전송되어 텍스트로 번역 및 변환되고, 동일한 창에 표시됩니다.

   ![변환에 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽고, 합성된 음성으로 변환된 텍스트를 출력하는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 C++ 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
