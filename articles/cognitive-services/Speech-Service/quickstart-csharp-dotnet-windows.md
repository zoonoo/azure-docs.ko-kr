---
title: '빠른 시작: 음성 인식, .NET Framework(Windows) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드에 따라 Windows용 .NET Framework 및 Speech SDK를 사용하여 음성을 텍스트로 변환하는 콘솔 애플리케이션을 만들 수 있습니다. 작업을 마치면 컴퓨터의 마이크를 사용하여 실시간으로 음성을 텍스트로 변환할 수 있습니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327034"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>빠른 시작: .NET Framework(Windows)에 대한 Speech SDK를 사용하여 음성 인식

빠른 시작은 [음성 합성](quickstart-text-to-speech-dotnet-windows.md) 및 [음성 번역](quickstart-translate-speech-dotnetframework-windows.md)에도 사용할 수 있습니다.

원하는 경우 다른 프로그래밍 언어 및/또는 환경을 선택합니다.<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 가이드에 따라 Windows용 .NET Framework 및 Speech SDK를 사용하여 음성을 텍스트로 변환하는 콘솔 애플리케이션을 만들 수 있습니다. 작업을 마치면 컴퓨터의 마이크를 사용하여 실시간으로 음성을 텍스트로 변환할 수 있습니다.

이 문서에 설명된 대로 직접 Visual Studio 프로젝트를 빌드하지 않고 신속하게 시연하려면 GitHub에서 최신 [Cognitive Services Speech SDK 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk)을 가져옵니다.

## <a name="prerequisites"></a>필수 조건

이 프로젝트를 완료하려면 다음이 필요합니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Speech Service에 대한 구독 키 [무료로 가져올 수 있습니다](get-started.md).
* 컴퓨터의 마이크에 액세스합니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. **Program.cs**를 열고 자동으로 생성된 코드를 이 샘플로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` 문자열을 찾아서 Speech Services 구독 키로 바꿉니다.

1. `YourServiceRegion` 문자열을 찾아서 구독과 연결된 [지역](regions.md)으로 바꿉니다. 예를 들어 평가판 구독을 사용하는 경우 지역은 `westus`입니다.

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**(또는 **F5** 선택)을 선택하여 **helloworld** 애플리케이션을 시작합니다.

1. 디바이스의 마이크에 영어 구나 문장을 말합니다. 이 애플리케이션은 음성을 Speech Services로 전송하고, 이는 기록된 텍스트를 표시하기 위해 애플리케이션으로 다시 보냅니다.

   ![음성 인식 사용자 인터페이스](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [Custom Speech 모델 학습](how-to-custom-speech-train-model.md)
