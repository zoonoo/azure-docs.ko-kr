---
title: '빠른 시작: 음성 인식, C#(.NET Core Windows) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 Windows의 .NET Core에서 C#으로 음성을 인식하는 방법을 알아봅니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: 1f6cbb511546784b316da1e978abf84c41ac753c
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446055"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>빠른 시작: .NET Core용 Speech SDK를 사용하여 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 Windows .NET Core에서 C# 콘솔 애플리케이션을 만듭니다. PC에서 마이크를 사용하여 실시간으로 음성을 텍스트로 변환합니다. 이 애플리케이션은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017(모든 버전)로 빌드되었습니다.

> [!NOTE]
> .NET Core는 [.NET 표준](https://docs.microsoft.com/dotnet/standard/net-standard) 사양을 구현하는 플랫폼 간 오픈 소스 .NET 플랫폼입니다.

이 빠른 시작을 완료하려면 음성 서비스 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [음성 서비스를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service에 대한 Azure 구독 키 [무료로 가져올 수 있습니다](get-started.md).

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. `Program.cs`를 열고 모든 코드를 다음으로 바꿉니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. 동일한 파일에서 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 또한 `YourServiceRegion` 문자열을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예: 평가판 구독의 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 애플리케이션을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "성공적인 빌드")

1. 애플리케이션을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 애플리케이션의 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "앱 디버깅 시작")

1. 콘솔 창이 나타나면 무엇이든 말해보라는 메시지가 표시됩니다. 영어 구 또는 문장을 말씀하세요. 음성은 음성 서비스로 전송되어 텍스트로 변환되고, 동일한 창에 표시됩니다.

    ![인식이 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "인식이 성공한 후의 콘솔 출력")

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
