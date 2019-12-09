---
title: '빠른 시작: 음성 합성, C#(UWP) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Cognitive Services Speech SDK를 사용하여 C# UWP(유니버설 Windows 플랫폼) 애플리케이션을 만드는 방법을 설명합니다. 텍스트를 읽는 음성을 실시간으로 디바이스 스피커로 합성합니다. 이 애플리케이션은 Speech SDK NuGet 패키지와 Microsoft Visual Studio 2019로 빌드되었습니다.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 276de42f2a37a0e48cd323e285760c00e6db9f32
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818226"
---
> [!NOTE]
> 유니버설 Windows 플랫폼을 사용하여 PC, Xbox, Surface Hub 및 기타 디바이스를 비롯하여 Windows 10을 지원하는 모든 디바이스에서 실행되는 앱을 개발할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>샘플 코드 추가

이제 애플리케이션의 사용자 인터페이스를 정의하는 XAML 코드를 추가하고, C# 코드 숨김 구현을 추가합니다.

1. **솔루션 탐색기**에서 `MainPage.xaml` 파일을 엽니다.

1. 디자이너의 XAML 뷰에서 다음 XAML 코드 조각을 **Grid** 태그(`<Grid>`와 `</Grid>` 사이)에 삽입합니다.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. **솔루션 탐색기**에서 코드 숨김 원본 파일 `MainPage.xaml.cs`를 엽니다. `MainPage.xaml`로 그룹화되어 있습니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. 원본 파일의 `Speak_ButtonClicked` 처리기에서 `YourSubscriptionKey` 문자열을 찾아 구독 키로 바꿉니다.

1. `Speak_ButtonClicked` 처리기에서 `YourServiceRegion` 문자열을 찾아 구독과 연결된 [지역](~/articles/cognitive-services/Speech-Service/regions.md)으로 바꿉니다. 예를 들어 평가판 구독의 경우 `westus`를 사용합니다.

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택하여 변경 내용을 저장합니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

이제 애플리케이션을 빌드하고 테스트할 준비가 되었습니다.

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**을 선택하거나, **F5** 키를 눌러 애플리케이션을 시작합니다. **helloworld** 창이 나타납니다.

   ![C#으로 작성된 샘플 UWP 음성 합성 애플리케이션 - 빠른 시작](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. 텍스트 상자에 일부 텍스트를 입력하고 **음성 읽기**를 클릭합니다. 해당 텍스트는 Speech Service로 전송되고 음성으로 합성되어 스피커로 재생됩니다.

    ![음성 합성 사용자 인터페이스](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>참고 항목

- [사용자 지정 음성 만들기](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [사용자 지정 음성 녹음 샘플](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
