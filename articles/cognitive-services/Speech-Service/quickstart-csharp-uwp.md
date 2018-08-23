---
title: '빠른 시작: Cognitive Services Speech SDK를 사용하여 UWP 앱에서 C#으로 음성 인식'
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services Speech SDK를 사용하여 UWP 앱에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 551439d28bcd7c64faf7ac6ac7388145456f7a53
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42093838"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 UWP 앱에서 음성 인식

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services Speech SDK를 사용하여 UWP(유니버설 Windows 플랫폼) 응용 프로그램을 만드는 방법을 설명합니다.
응용 프로그램은 [Microsoft Cognitive Services Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017로 빌드되었습니다.

> [!NOTE]
> 유니버설 Windows 플랫폼을 사용하여 PC, Xbox, Surface Hub 및 기타 장치를 비롯하여 Windows 10을 지원하는 모든 장치에서 실행되는 앱을 개발할 수 있습니다. Speech SDK를 사용하는 앱은 아직 WACK(Windows 앱 인증 키트)를 제공하지 않습니다. 앱을 사이드로드할 수 있지만 현재, Windows 스토어로 제출하지 못할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 구독 키. [Speech Service를 무료로 체험해보기](get-started.md)를 참조하세요.
* 작동하는 마이크가 장착된 Windows PC(Windows 10 Fall Creators Update 이상)
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition 이상
* Visual Studio의 **유니버설 Windows 플랫폼 개발** 워크로드. **도구** \> **도구 및 기능 가져오기**에서 사용하도록 설정할 수 있습니다.

  ![유니버설 Windows 플랫폼 개발 사용](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio 2017에서 새 Visual C# Windows 유니버설 비어 있는 앱을 만듭니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** \> **Visual C#** \> **Windows 유니버설**을 확장한 후 **비어 있는 앱(유니버설 Windows)** 을 선택합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. 팝업되는 **새 유니버설 Windows 플랫폼 프로젝트** 창에서 **Windows 10 Fall Creators Update(10.0; Build 16299)** 를 **최소 버전**으로 선택하고 이 버전 또는 이후 버전을 **대상 버전**으로 선택한 후 **확인**을 클릭합니다.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 64비트 Windows 설치를 실행한다면 빌드 플랫폼을 `x64`로 전환할 수 있습니다.

   ![빌드 플랫폼을 x64로 전환](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > 이때 Speech SDK는 Intel 호환 프로세서를 지원하지만 ARM은 지원하지 않습니다.

1. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 참조합니다. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션에 대한 NuGet 패키지 관리 마우스 오른쪽 단추로 클릭](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 오른쪽 위 모서리의 **패키지 소스** 필드에서 **Nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **helloworld** 프로젝트에 설치합니다.

    ![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "Nuget 패키지 설치")

1. 표시된 라이선스에 동의합니다.

    ![라이선스에 동의](media/sdk/qs-csharp-uwp-06-nuget-license.png "라이선스에 동의")

1. 패키지 관리자 콘솔에 다음 출력 줄이 표시됩니다.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.6.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. 솔루션 탐색기에서 **Package.appxmanifest**를 두 번 클릭하여 응용 프로그램 매니페스트를 편집합니다.
   **기능** 탭을 선택하고 **마이크** 기능에 대한 확인란을 선택한 후 변경 내용을 저장합니다.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. 솔루션 탐색기에서 `MainPage.xaml`을 두 번 클릭하여 응용 프로그램의 사용자 인터페이스를 편집합니다. 

    디자이너의 XAML 보기에서 다음 XAML 코드 조각을 Grid 태그(`<Grid>`와 `</Grid>` 사이)에 삽입합니다.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 솔루션 탐색기에서 `MainPage.xaml.cs`를 두 번 클릭하여 XAML 코드 숨김 파일을 편집합니다(`MainPage.xaml` 항목 아래에 그룹화되어 있음).
   이 파일의 모든 코드를 다음으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. `SpeechRecognitionFromMicrophone_ButtonClicked` 처리기에서 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. `SpeechRecognitionFromMicrophone_ButtonClicked` 처리기에서 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 모든 변경 내용을 저장합니다.

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

1. 응용 프로그램을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![빌드 성공](media/sdk/qs-csharp-uwp-08-build.png "빌드 성공")

1. 응용 프로그램을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

    ![앱 디버깅 시작](media/sdk/qs-csharp-uwp-09-start-debugging.png "앱 디버깅 시작")

1. GUI 창이 팝업됩니다. 먼저 **마이크 사용** 단추를 클릭하고 팝업되는 권한 요청을 승인합니다.

    ![앱 디버깅 시작](media/sdk/qs-csharp-uwp-10-access-prompt.png "앱 디버깅 시작")

1. **Speech recognition with microphone input**(마이크 입력을 사용하여 음성 인식)을 클릭하고 장치의 마이크에 짧은 구를 말합니다. 인식된 텍스트가 창에 표시됩니다.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/csharp-uwp` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [음성 번역](how-to-translate-speech-csharp.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
