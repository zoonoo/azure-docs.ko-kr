---
title: '빠른 시작: 음성 변환, C#(.NET Framework Windows) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 사용자 음성을 캡처하고, 다른 언어로 변환하고, 명령줄에 텍스트를 출력하는 .NET Framework 애플리케이션을 만듭니다. 이 가이드는 Windows 사용자를 위해 설계되었습니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327353"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>빠른 시작: .NET Framework용 Speech SDK를 사용하여 음성 번역(Windows)

빠른 시작은 [음성 인식](quickstart-csharp-dotnet-windows.md) 및 [음성 합성](quickstart-text-to-speech-dotnet-windows.md)에도 사용할 수 있습니다.

이 빠른 시작에서는 컴퓨터의 마이크에서 사용자 음성을 캡처하고, 음성을 변환하고, 변환된 텍스트를 실시간으로 명령줄에 변환하는 .NET Framework 애플리케이션을 만듭니다. 이 애플리케이션은 32비트 또는 64비트 Windows에서 실행할 수 있으며 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2019로 빌드되었습니다.

음성 변환에 사용할 수 있는 언어의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Speech Service에 대한 Azure 구독 키. [무료로 가져올 수 있습니다](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. **Program.cs**를 열고 그 안에 있는 모든 코드를 다음과 같이 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` 문자열을 찾아서 구독 키로 바꿉니다.

1. `YourServiceRegion` 문자열을 찾아서 구독과 연결된 [지역](regions.md)으로 바꿉니다. 예를 들어 평가판 구독을 사용하는 경우 지역은 `westus`입니다.

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택합니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**(또는 **F5** 선택)을 선택하여 **helloworld** 애플리케이션을 시작합니다.

1. 디바이스의 마이크에 영어 구나 문장을 말합니다. 애플리케이션이 음성 서비스로 음성을 전송하고, 여기서 음성을 다른 언어(이 예제에서는 독일어)의 텍스트로 번역합니다. 음성 서비스는 번역된 텍스트를 애플리케이션으로 다시 보내며, 애플리케이션 창에 번역이 표시됩니다.

   ![음성 번역 사용자 인터페이스](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽고, 합성된 음성으로 변환된 텍스트를 출력하는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [Custom Speech 모델 학습](how-to-custom-speech-train-model.md)
