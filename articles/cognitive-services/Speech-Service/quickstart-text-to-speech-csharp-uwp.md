---
title: '빠른 시작: 음성 합성, C#(UWP) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Cognitive Services Speech SDK를 사용하여 C# UWP(유니버설 Windows 플랫폼) 애플리케이션을 만드는 방법을 설명합니다. 텍스트를 읽는 음성을 실시간으로 디바이스 스피커로 합성합니다. 이 애플리케이션은 Speech SDK NuGet 패키지와 Microsoft Visual Studio 2019로 빌드되었습니다.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382201"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 UWP 앱에서 음성 합성

[음성 인식](quickstart-csharp-uwp.md), [음성 번역](quickstart-translate-speech-uwp.md) 및 [음성 우선 가상 도우미](quickstart-virtual-assistant-csharp-uwp.md)에 대한 빠른 시작도 제공됩니다.

이 문서에서는 Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 C# UWP(유니버설 Windows 플랫폼) 애플리케이션을 개발합니다. 프로그램이 텍스트를 읽는 음성을 실시간으로 디바이스 스피커로 합성합니다. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2019(모든 버전)를 사용하여 애플리케이션을 빌드합니다.

> [!NOTE]
> 유니버설 Windows 플랫폼을 사용하여 PC, Xbox, Surface Hub 및 기타 디바이스를 비롯하여 Windows 10을 지원하는 모든 디바이스에서 실행되는 앱을 개발할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Speech Service에 대한 Azure 구독 키. [무료로 가져올 수 있습니다](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

이제 애플리케이션의 사용자 인터페이스를 정의하는 XAML 코드를 추가하고, C# 코드 숨김 구현을 추가합니다.

1. **솔루션 탐색기**에서 `MainPage.xaml` 파일을 엽니다.

1. 디자이너의 XAML 뷰에서 다음 XAML 코드 조각을 **Grid** 태그(`<Grid>`와 `</Grid>` 사이)에 삽입합니다.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. **솔루션 탐색기**에서 코드 숨김 원본 파일 `MainPage.xaml.cs`를 엽니다. `MainPage.xaml`로 그룹화되어 있습니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 원본 파일의 `Speak_ButtonClicked` 처리기에서 `YourSubscriptionKey` 문자열을 찾아 구독 키로 바꿉니다.

1. `Speak_ButtonClicked` 처리기에서 `YourServiceRegion` 문자열을 찾아 구독과 연결된 [지역](regions.md)으로 바꿉니다. 예를 들어 평가판 구독의 경우 `westus`를 사용합니다.

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택하여 변경 내용을 저장합니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

이제 애플리케이션을 빌드하고 테스트할 준비가 되었습니다.

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**을 선택하거나, **F5** 키를 눌러 애플리케이션을 시작합니다. **helloworld** 창이 나타납니다.

   ![C#으로 작성된 샘플 UWP 음성 합성 애플리케이션 - 빠른 시작](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. 텍스트 상자에 일부 텍스트를 입력하고 **음성 읽기**를 클릭합니다. 해당 텍스트는 Speech Services로 전송되고 음성으로 합성되어 스피커로 재생됩니다.

    ![음성 합성 사용자 인터페이스](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [Custom Voice 모델 만들기 및 사용](how-to-custom-voice-create-voice.md)
- [음성 샘플 레코드](record-custom-voice-samples.md)
