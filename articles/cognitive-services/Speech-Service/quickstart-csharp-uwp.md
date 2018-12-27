---
title: '빠른 시작: 음성 인식, C#(UWP) - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Cognitive Services Speech SDK를 사용하여 C# UWP(유니버설 Windows 플랫폼) 애플리케이션을 만드는 방법을 설명합니다. 디바이스에서 마이크를 사용하여 실시간으로 음성을 텍스트로 기록합니다. 이 애플리케이션은 Speech SDK NuGet 패키지 및 Microsoft Visual Studio 2017로 빌드되었습니다.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 55988ef65e223c76a485c3cbec13626abf68d3b9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104632"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 UWP 앱에서 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services [Speech SDK](speech-sdk.md)를 사용하여 C# UWP(유니버설 Windows 플랫폼) 응용 프로그램을 만드는 방법을 설명합니다. 디바이스에서 마이크를 사용하여 실시간으로 음성을 텍스트로 기록합니다. 이 응용 프로그램은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017(모든 버전)로 빌드되었습니다.

> [!NOTE]
> 유니버설 Windows 플랫폼을 사용하여 PC, Xbox, Surface Hub 및 기타 디바이스를 비롯하여 Windows 10을 지원하는 모든 디바이스에서 실행되는 앱을 개발할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 음성 서비스 구독 키가 필요합니다. 무료로 가져올 수 있습니다. 자세한 내용은 [음성 서비스를 무료로 체험해보기](get-started.md)를 참조하세요.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio 2017을 시작합니다.

1. **유니버설 Windows 플랫폼 개발** 워크로드를 사용할 수 있는지 확인합니다. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 엽니다. 이 워크로드를 이미 사용하도록 설정한 경우 대화 상자를 닫습니다.

    ![워크로드 탭이 강조 표시된 Visual Studio 설치 관리자 스크린샷](media/sdk/vs-enable-uwp-workload.png)

    그렇지 않으면 **.NET 플랫폼 간 개발** 옆의 상자를 선택하고 대화 상자 오른쪽 아래 모서리의 **수정**을 선택합니다. 새로운 기능을 설치하려면 잠시 시간이 걸립니다.

1. 빈 Visual C# 유니버설 Windows 앱을 만듭니다. 먼저, 메뉴에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** > **Visual C#** > **Windows Universal**을 확장합니다. 그런 다음, **비어 있는 앱(유니버설 Windows)** 을 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

    ![새 프로젝트 대화 상자 스크린샷](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Speed SDK를 사용하려면 응용 프로그램이 Windows 10 Fall Creators Update 이상을 대상으로 빌드되어야 합니다. 팝업되는 **새 유니버설 Windows 플랫폼 프로젝트** 창에서 **Windows 10 Fall Creators Update(10.0; Build 16299)** 를 **최소 버전**으로 선택합니다. **대상 버전** 상자에서 이 이상 버전을 선택한 다음, **확인**을 클릭합니다.

    ![새 유니버설 Windows 플랫폼 프로젝트 창의 스크린샷](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 64비트 Windows를 실행하는 경우 Visual Studio 도구 모음에서 드롭다운 메뉴를 사용하여 빌드 플랫폼을 `x64`로 전환할 수 있습니다. 64비트 Windows는 32비트 응용 프로그램을 실행할 수 있으므로 원한다면 `x86`으로 설정한 상태로 둘 수 있습니다.

   ![x64를 강조 표시한 Visual Studio 도구 모음 스크린샷](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Speech SDK는 Intel 호환 프로세서만 지원합니다. ARM은 현재 지원되지 않습니다.

1. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 참조합니다. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션 옵션의 NuGet 패키지 관리를 강조 표시한 솔루션 탐색기 스크린샷](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 오른쪽 위 모서리의 **패키지 원본** 필드에서 **nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **helloworld** 프로젝트에 설치합니다.

    ![솔루션에 대한 패키지 관리 대화 상자 스크린샷](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "NuGet 패키지 설치")

1. NuGet 패키지를 설치하려면 표시된 라이선스에 동의합니다.

    ![라이선스 동의 대화 상자 스크린샷](media/sdk/qs-csharp-uwp-06-nuget-license.png "라이선스에 동의")

1. 패키지 관리자 콘솔에 다음 출력 줄이 표시됩니다.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.1.0' to helloworld
   ```

1. 응용 프로그램은 음성 입력에 마이크를 사용하므로 **마이크** 기능을 프로젝트에 추가합니다. 솔루션 탐색기에서 **Package.appxmanifest**를 두 번 클릭하여 응용 프로그램 매니페스트를 편집합니다. 그런 다음, **기능** 탭으로 전환하고 **마이크** 기능에 대한 확인란을 선택한 후 변경 내용을 저장합니다.

   ![기능 및 마이크가 강조 표시된 Visual Studio 응용 프로그램 매니페스트의 스크린샷](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>샘플 코드 추가

1. 응용 프로그램의 사용자 인터페이스는 XAML을 사용하여 정의됩니다. 솔루션 탐색기에서 `MainPage.xaml`을 엽니다. 디자이너의 XAML 보기에서 다음 XAML 코드 조각을 Grid 태그(`<Grid>`와 `</Grid>` 사이)에 삽입합니다.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 코드 숨김 소스 파일 `MainPage.xaml.cs`를 엽니다(`MainPage.xaml` 아래 그룹화되어 있음). 그 안의 모든 코드를 다음으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 이 파일의 `SpeechRecognitionFromMicrophone_ButtonClicked` 처리기에서 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. `SpeechRecognitionFromMicrophone_ButtonClicked` 처리기에서 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 모든 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 애플리케이션을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![솔루션 빌드 옵션이 강조 표시된 Visual Studio 응용 프로그램의 스크린샷](media/sdk/qs-csharp-uwp-08-build.png "성공적인 빌드")

1. 애플리케이션을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![디버깅 시작 옵션이 강조 표시된 Visual Studio 응용 프로그램의 스크린샷](media/sdk/qs-csharp-uwp-09-start-debugging.png "앱 디버깅 시작")

1. 창이 팝업됩니다. **마이크 사용**을 클릭하고 팝업되는 권한 요청을 승인합니다.

    ![권한 요청 스크린샷](media/sdk/qs-csharp-uwp-10-access-prompt.png "앱 디버깅 시작")

1. **마이크 입력을 사용하여 음성 인식**을 클릭하고 디바이스의 마이크에 짧은 영어 구나 문장을 말합니다. 음성은 음성 서비스로 전송되어 텍스트로 변환되고 창에 표시됩니다.

    ![음성 인식 사용자 인터페이스 스크린샷](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/csharp-uwp` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [C#용 Speech SDK를 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>참고 항목

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
