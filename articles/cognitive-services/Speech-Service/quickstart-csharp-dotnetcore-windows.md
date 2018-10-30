---
title: '빠른 시작: Speech Service SDK를 사용하여 Windows .NET Core의 C#에서 음성 인식'
titleSuffix: Azure Cognitive Services
description: Speech Service SDK를 사용하여 Windows .NET Core의 C#에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 891de4700470931d8eaec3e2a3bb7ca2110e48dd
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469138"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows .NET Core의 C#에서 음성을 인식하는 방법 알아보기

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 Windows .NET Core에서 C# 콘솔 응용 프로그램을 만듭니다. PC에서 마이크를 사용하여 실시간으로 음성을 텍스트로 변환합니다. 이 응용 프로그램은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017(모든 버전)로 빌드되었습니다.

> [!NOTE]
> .NET Core는 [.NET 표준](https://docs.microsoft.com/dotnet/standard/net-standard) 사양을 구현하는 플랫폼 간 오픈 소스 .NET 플랫폼입니다.

이 빠른 시작을 완료하려면 음성 서비스 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [음성 서비스를 무료로 체험해보기](get-started.md)를 참조하세요.


## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio 2017을 시작합니다.

1. **.NET 플랫폼 간 개발** 워크로드를 사용할 수 있는지 확인합니다. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 엽니다. 이 워크로드를 이미 사용하도록 설정한 경우 대화 상자를 닫습니다.

    ![워크로드 탭이 강조 표시된 Visual Studio 설치 관리자 스크린샷](media/sdk/vs-enable-net-core-workload.png)

    그렇지 않으면 **.NET Core 플랫폼 간 개발** 옆의 상자를 선택하고 대화 상자의 오른쪽 아래 모서리에서 **수정**을 선택합니다. 새로운 기능을 설치하려면 잠시 시간이 걸립니다.

1. 새로운 Visual C# .NET Core 콘솔 앱을 만듭니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** > **Visual C#** > **.NET Core**를 확장합니다. 그런 다음, **콘솔 앱(.NET Core)** 을 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

    ![새 프로젝트 대화 상자 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# 콘솔 앱 만들기(.NET Core)")

1. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 참조합니다. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션의 NuGet 패키지 관리를 강조 표시한 솔루션 탐색기 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "솔루션의 NuGet 패키지 관리")

1. 오른쪽 위 모서리의 **패키지 원본** 필드에서 **nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **helloworld** 프로젝트에 설치합니다.

    ![솔루션에 대한 패키지 관리 대화 상자 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet 패키지 설치")

1. NuGet 패키지를 설치하려면 표시된 라이선스에 동의합니다.

    ![라이선스 동의 대화 상자 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "라이선스에 동의")

패키지를 설치한 후에 패키지 관리자 콘솔에서 확인 메시지가 나타납니다.


## <a name="add-sample-code"></a>샘플 코드 추가

1. `Program.cs`를 열고 모든 코드를 다음으로 바꿉니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. 동일한 파일에서 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 또한 `YourServiceRegion` 문자열을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예: 평가판 구독의 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 응용 프로그램을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 응용 프로그램의 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "성공적인 빌드")

1. 응용 프로그램을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 응용 프로그램의 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "앱 디버깅 시작")

1. 콘솔 창이 나타나면 무엇이든 말해보라는 메시지가 표시됩니다. 영어 구 또는 문장을 말씀하세요. 음성은 음성 서비스로 전송되어 텍스트로 변환되고, 동일한 창에 표시됩니다.

    ![인식이 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "인식이 성공한 후의 콘솔 출력")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/csharp-dotnetcore-windows` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [C#용 Speech SDK를 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>참고 항목

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
