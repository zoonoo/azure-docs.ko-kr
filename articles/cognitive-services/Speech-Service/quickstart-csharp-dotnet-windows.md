---
title: '빠른 시작: Speech Service SDK를 사용하여 Windows 기반 .NET Framework의 C#에서 음성 인식'
titleSuffix: Azure Cognitive Services
description: Speech Service SDK를 사용하여 Windows 기반 .NET Framework의 C#에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 06d4a41a4a67d077f8d79eee68938dff65adf0e8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468520"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows 기반 .NET Framework의 C#에서 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 [Speech SDK](speech-sdk.md)를 사용하여 Windows 기반 .NET Framework용 C# 콘솔 응용 프로그램을 만듭니다. PC에서 마이크를 사용하여 실시간으로 음성을 텍스트로 변환합니다. 이 응용 프로그램은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017(모든 버전)로 빌드되었습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 음성 서비스 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [음성 서비스를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. `Program.cs`를 열고 모든 코드를 다음으로 바꿉니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 같은 파일에서 `YourSubscriptionKey` 문자열을 음성 서비스 구독 키로 바꿉니다.

1. 또한 `YourServiceRegion` 문자열을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예: 평가판 구독의 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 응용 프로그램을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 응용 프로그램의 스크린샷](media/sdk/qs-csharp-dotnet-windows-08-build.png "성공적인 빌드")

1. 응용 프로그램을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 응용 프로그램의 스크린샷](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "앱 디버깅 시작")

1. 콘솔 창이 나타나면 무엇이든 말해보라는 메시지가 표시됩니다. 영어 구 또는 문장을 말씀하세요. 음성은 음성 서비스로 전송되어 텍스트로 변환되고, 동일한 창에 표시됩니다.

    ![인식이 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "인식이 성공한 후의 콘솔 출력")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/csharp-dotnet-windows` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [C#용 Speech SDK를 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>참고 항목

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
