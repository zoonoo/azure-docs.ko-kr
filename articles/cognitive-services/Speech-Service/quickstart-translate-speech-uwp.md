---
title: '빠른 시작: 음성 변환, C#(UWP) - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 사용자 음성을 캡처하고, 다른 언어로 변환하고, 명령줄에 텍스트를 출력하는 간단한 UWP(유니버설 Windows 플랫폼) 애플리케이션을 만듭니다. 이 가이드는 Windows 사용자를 위해 설계되었습니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: bb0296098d0717e95e9aa1d73229d59709d13766
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106089"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>빠른 시작: C#용 Speech SDK를 사용하여 음성 번역(UWP)

이 빠른 시작에서는 컴퓨터의 마이크에서 사용자 음성을 캡처하고, 음성을 변환하고, 변환된 텍스트를 실시간으로 명령줄에 변환하는 간단한 UWP(유니버설 Windows 플랫폼) 애플리케이션을 만듭니다. 이 애플리케이션은 64비트 Windows에서 실행되도록 설계되었으며 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017로 빌드되었습니다.

음성 변환에 사용할 수 있는 언어의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

> [!NOTE]
> UWP를 사용하여 PC, Xbox, Surface Hub 및 기타 디바이스를 비롯하여 Windows 10을 지원하는 모든 디바이스에서 실행되는 앱을 개발할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service에 대한 Azure 구독 키 [무료로 가져올 수 있습니다](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. 애플리케이션의 사용자 인터페이스는 XAML을 사용하여 정의됩니다. 솔루션 탐색기에서 `MainPage.xaml`을 엽니다. 디자이너의 XAML 보기에서 다음 XAML 코드 조각을 `<Grid>`와 `</Grid>` 사이에 삽입합니다.

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 코드 숨김 소스 파일 `MainPage.xaml.cs`를 엽니다(`MainPage.xaml` 아래 그룹화되어 있음). 그 안의 모든 코드를 다음으로 바꿉니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 이 파일의 `SpeechTranslationFromMicrophone_ButtonClicked` 처리기에서 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. `SpeechTranslationFromMicrophone_ButtonClicked` 처리기에서 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 모든 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 애플리케이션을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-csharp-uwp-08-build.png "성공적인 빌드")

1. 애플리케이션을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-csharp-uwp-09-start-debugging.png "앱 디버깅 시작")

1. 창이 팝업됩니다. **마이크 사용**을 클릭하고 팝업되는 권한 요청을 승인합니다.

    ![권한 요청 스크린샷](media/sdk/qs-csharp-uwp-10-access-prompt.png "앱 디버깅 시작")

1. **마이크 입력을 사용하여 음성 인식**을 클릭하고 디바이스의 마이크에 짧은 영어 구나 문장을 말합니다. 음성은 음성 서비스로 전송되어 텍스트로 변환되고 창에 표시됩니다.

    ![음성 인식 사용자 인터페이스 스크린샷](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
