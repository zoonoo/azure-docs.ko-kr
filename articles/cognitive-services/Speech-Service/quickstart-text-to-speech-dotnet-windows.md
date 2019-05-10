---
title: '빠른 시작: 음성 합성, .NET Framework(Windows) - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 가이드에 따라 Windows용 .NET Framework 및 Speech SDK를 사용하여 텍스트를 음성으로 변환하는 콘솔 애플리케이션을 만들 수 있습니다. 마치면, 텍스트를 음성으로 합성하여 스피커를 통해 실시간으로 들을 수 있습니다.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: 3b7addd369d5e26554dd4300e65bee66b8a62768
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465570"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>빠른 시작: .NET Framework용 Speech SDK를 사용하여 음성 합성(Windows)

빠른 시작은 [음성 인식](quickstart-csharp-dotnet-windows.md) 및 [음성 번역](quickstart-translate-speech-dotnetframework-windows.md)에도 사용할 수 있습니다.

이 가이드에 따라 Windows용 .NET Framework 및 Speech SDK를 사용하여 텍스트를 음성으로 변환하는 콘솔 애플리케이션을 만들 수 있습니다. 마치면, 텍스트를 음성으로 합성하여 스피커를 통해 실시간으로 들을 수 있습니다.

빠른 데모를 보려면(아래와 같이 Visual Studio 프로젝트를 직접 빌드하지 않음)

GitHub에서 최신 [Cognitive Services Speech SDK 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk)을 가져옵니다.

## <a name="prerequisites"></a>필수 조건

이 프로젝트를 완료하려면 다음이 필요합니다.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service에 대한 구독 키 [무료로 가져올 수 있습니다](get-started.md).
* 스피커(또는 헤드셋)를 사용할 수 있습니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. `Program.cs`를 열고 자동으로 생성된 코드를 이 샘플로 바꿉니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` 문자열을 찾아서 Speech Service 구독 키로 바꿉니다.

1. `YourServiceRegion` 문자열을 찾아서 구독과 연결된 [Azure 지역](regions.md)으로 바꿉니다. 예를 들어 평가판을 사용하는 경우 Azure 지역은 `westus`입니다.

1. 프로젝트 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-csharp-dotnet-windows-08-build.png "성공적인 빌드")

1. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 눌러 애플리케이션을 시작합니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "앱 디버깅 시작")

1. 콘솔 창이 나타나고 텍스트를 입력하라는 메시지가 표시됩니다. 몇 가지 단어나 문장을 입력합니다. 입력한 텍스트는 Speech Services로 전송되고 음성으로 합성되어 스피커로 재생됩니다.

    ![인식이 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "인식이 성공한 후의 콘솔 출력")

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음성 글꼴 사용자 지정](how-to-customize-voice-font.md)
- [음성 샘플 레코드](record-custom-voice-samples.md)
