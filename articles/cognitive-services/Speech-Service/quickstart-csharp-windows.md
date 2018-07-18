---
title: '빠른 시작: Windows용 Cognitive Services Speech C# SDK를 사용하여 음성 인식 | Microsoft Docs'
description: Speech Service에 C# SDK를 사용하여 음성을 인식하는 방법을 알아봅니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 64281215d139731b61365936bc1b837798ad8fbf
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016837"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>빠른 시작: Cognitive Services Speech C# SDK를 사용하여 음성 인식

이 문서에서는 Cognitive Services Speech SDK를 사용하여 Windows에서 음성을 텍스트로 변환하는 C# 콘솔 응용 프로그램을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 구독 키. [Speech Service를 무료로 체험해보기](get-started.md)를 참조하세요.
* Visual Studio 2017 Community Edition 이상
* Visual Studio의 **.NET 데스크톱 개발** 워크로드. **도구** \> **도구 및 기능 가져오기**에서 사용하도록 설정할 수 있습니다. 

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio 2017에서 다음과 같이 새로운 Visual C# 콘솔 앱을 만듭니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨**을 확장한 후 **콘솔 앱(.NET Framework)** 을 선택합니다. 프로젝트 이름으로 *CsharpHelloSpeech*를 입력합니다.

    ![Visual C# 콘솔 앱(.NET Framework) 만들기](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Visual C# 콘솔 앱 만들기")

2. [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고 참조합니다. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.

    ![솔루션에 대한 NuGet 패키지 관리를 마우스 오른쪽 단추로 클릭](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "솔루션에 대한 NuGet 패키지 관리")

3. 오른쪽 위 모서리의 **패키지 소스** 필드에서 **Nuget.org**를 선택합니다. `Microsoft.CognitiveServices.Speech` 패키지를 검색한 후 **CsharpHelloSpeech** 프로젝트에 설치합니다.

    ![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](media/sdk/speechsdk-08-vs-cs-nuget-install.png "Nuget 패키지 설치")

4. 팝업되는 라이선스 화면에서 라이선스에 동의합니다.

    ![라이선스에 동의](media/sdk/speechsdk-09-vs-cs-nuget-license.png "라이선스에 동의")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>PC 아키텍처와 일치하는 플랫폼 구성 만들기

이 섹션에서 프로세서 아키텍처와 일치하는 구성에 새 플랫폼을 추가합니다.

1. 구성 관리자를 시작합니다. **빌드** > **구성 관리자**를 선택합니다.

    ![구성 관리자 시작](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "구성 관리자 시작")

2. **구성 관리자** 대화 상자에서 새 플랫폼을 추가합니다. **활성 솔루션 플랫폼** 드롭다운 목록에서 **새로 만들기**를 선택합니다.

    ![구성 관리자 창 아래에서 새 플랫폼 추가](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "구성 관리자 창 아래에서 새 플랫폼 추가")

3. 64비트 Windows를 실행하는 경우 `x64`라는 새 플랫폼 구성을 만듭니다. 32비트 Windows를 실행하는 경우 `x86`라는 새 플랫폼 구성을 만듭니다. 이 문서에서는 `x64` 플랫폼 구성을 만듭니다. 

    ![64비트 Windows에서 "x64"라는 새 플랫폼 추가](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "x64 플랫폼 추가")

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. Visual Studio 프로젝트에 대한 `Program.cs`에서 `Program` 클래스의 본문을 다음으로 바꿉니다. 구독 키 및 지역을 서비스에서 가져온 항목으로 바꾸어야 합니다.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. 코드를 붙여 넣은 후에 `Main()` 메서드는 다음 스크린샷에 표시된 것과 유사해야 합니다.

    ![코드를 붙여 넣은 후의 Main 메서드](media/sdk/speechsdk-17-vs-cs-paste-code.png "최종 Main 메서드")

3. Visual Studio의 IntelliSense는 확인할 수 없는 Speech SDK 클래스에 대한 참조를 강조 표시합니다. 이 오류를 해결하려면 다음 `using` 문을 코드의 시작 부분에 추가합니다(수동으로 또는 Visual Studio의 [빠른 작업](https://docs.microsoft.com/visualstudio/ide/quick-actions)을 사용하여).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![빠른 작업을 사용하여 누락된 using 문 추가](media/sdk/speechsdk-18-vs-cs-add-using.png "IntelliSense 해결 문제")

4. IntelliSense 강조 표시 문제가 해결되었는지 확인한 후 프로젝트에 변경 내용을 저장합니다.

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

1. 응용 프로그램을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

    ![빌드 성공](media/sdk/speechsdk-20-vs-cs-build.png "빌드 성공")

2. 응용 프로그램을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다. 

    ![앱 디버깅 시작](media/sdk/speechsdk-21-vs-cs-f5.png "앱 디버깅 시작")

3. 콘솔 창이 팝업되면 아무 말이나 하라는 메시지가 표시됩니다(영어로).
인식 결과가 화면에 표시됩니다.

    ![성공적인 인식 후 콘솔 출력](media/sdk/speechsdk-22-cs-vs-console-output.png "성공적인 인식 후 콘솔 출력")

## <a name="download-code"></a>코드 다운로드

샘플의 최신 집합은 [Cognitive Services Speech SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조합니다.

## <a name="next-steps"></a>다음 단계

- [음성 번역](how-to-translate-speech.md)
- [음성 모델 사용자 지정](how-to-customize-speech-models.md)
