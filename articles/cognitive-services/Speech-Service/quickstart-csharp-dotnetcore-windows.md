---
title: '빠른 시작: Cognitive Services Speech SDK를 사용하여 Windows .NET Core의 C#에서 음성 인식'
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services Speech SDK를 사용하여 Windows .NET Core의 C#에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: ee83443c76851506fffbfcaaa12e72790d077cb0
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128505"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows .NET Core의 C#에서 음성을 인식하는 방법 알아보기

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services Speech SDK를 사용하여 Windows .NET Core에서 음성을 텍스트로 변환하는 C# 콘솔 응용 프로그램을 만드는 방법을 설명합니다.
응용 프로그램은 [Microsoft Cognitive Services Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017로 빌드되었습니다.

> [!NOTE]
> .NET Core는 [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 사양을 구현하는 오픈 소스, 플랫폼 간 .NET 플랫폼입니다.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 구독 키. [Speech Service를 무료로 체험해보기](get-started.md)를 참조하세요.
* 작동하는 마이크가 있는 Windows PC
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition 이상
* Visual Studio의 **.NET Core 플랫폼 간 개발** 워크로드 **도구** \> **도구 및 기능 가져오기**에서 사용하도록 설정할 수 있습니다.

  ![.NET Core 플랫폼 간 개발 사용](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio 2017에서 다음과 같이 새로운 Visual C# .NET Core 콘솔 앱을 만듭니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** \> **Visual C#** \> **.NET Core**를 확장한 후 **콘솔 앱(.NET Core)** 을 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

    ![Visual C# 콘솔 앱(.NET Core) 만들기](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# 콘솔 앱(.NET Core) 만들기")

1. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 참조합니다. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션에 대한 NuGet 패키지 관리를 마우스 오른쪽 단추로 클릭](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "솔루션에 대한 NuGet 패키지 관리")

1. 오른쪽 위 모서리의 **패키지 소스** 필드에서 **Nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **helloworld** 프로젝트에 설치합니다.

    ![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "Nuget 패키지 설치")

1. 표시된 라이선스에 동의합니다.

    ![라이선스에 동의](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "라이선스에 동의")

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. `Program.cs`를 열고 모든 코드를 다음으로 바꿉니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

1. 응용 프로그램을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![빌드 성공](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "빌드 성공")

1. 응용 프로그램을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![앱 디버깅 시작](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "앱 디버깅 시작")

1. 콘솔 창이 나타나면 아무 말이나 하라는 메시지가 표시됩니다(영어로). 인식된 텍스트가 같은 창에 표시됩니다.

    ![성공적인 인식 후 콘솔 출력](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "성공적인 인식 후 콘솔 출력")

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/csharp-dotnetcore-windows` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
