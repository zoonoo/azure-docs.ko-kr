---
title: '빠른 시작: 음성 합성, C++(Windows) - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 Windows Desktop에서 C++로 음성을 합성하는 방법 알아보기
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383092"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows에서 C++로 음성 합성

빠른 시작은 [음성 인식](quickstart-cpp-windows.md) 및 [음성 번역](quickstart-translate-speech-cpp-windows.md)에도 사용할 수 있습니다.

이 문서에서는 Windows용 C++ 콘솔 애플리케이션을 만듭니다. Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 실시간으로 텍스트를 음성으로 합성하여 PC 스피커에서 재생합니다. 이 애플리케이션은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2019(모든 버전)로 빌드되었습니다.

음성 합성에 사용할 수 있는 전체 언어/음성 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Speech Services 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [Speech Services를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. 원본 파일 **helloworld.cpp**를 엽니다.

1. 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. 동일한 파일에서 `YourSubscriptionKey` 문자열을 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택합니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**을 선택하거나, **F5** 키를 눌러 **helloworld** 애플리케이션을 시작합니다.

1. 영어 구 또는 문장을 입력합니다. 애플리케이션은 텍스트를 음성 서비스로 전송하고, 여기서 합성된 음성을 스피커에서 재생하기 위해 애플리케이션으로 다시 보냅니다.

   ![음성 합성에 성공한 후의 콘솔 출력](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에 음성을 저장하는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 C++ 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [사용자 지정 음성 만들기](how-to-custom-voice-create-voice.md)
- [사용자 지정 음성 녹음 샘플](record-custom-voice-samples.md)
