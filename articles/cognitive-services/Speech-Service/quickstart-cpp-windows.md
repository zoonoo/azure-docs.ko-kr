---
title: '빠른 시작: Cognitive Services Speech # SDK를 사용하여 Windows 데스크톱의 C++에서 음성 인식 | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: 'Cognitive Services Speech # SDK를 사용하여 Windows 데스크톱의 C++에서 음성을 인식하는 방법 알아보기'
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 4a8c5f7053c1976233bf9de6a0c142885b73c8aa
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071201"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Windows 데스크톱의 C++에서 음성 인식

음성 SDK를 사용하는 Windows 데스크톱용 C++ 기반 콘솔 응용 프로그램을 만드는 방법을 설명합니다.
응용 프로그램은 [Microsoft Cognitive Services Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2017에 기반합니다.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 구독 키. [Speech Service를 무료로 체험해보기](get-started.md)를 참조하세요.
* 작동하는 마이크가 있는 Windows PC
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition 이상
* Visual Studio의 **C++를 사용한 데스크톱 개발** 워크로드 및 Visual Studio의 **NuGet 패키지 관리자** 구성 요소
  각각 **도구** \> **도구 및 기능 가져오기**의 **워크로드** 및 **개별 구성 요소** 탭에서 사용하도록 설정할 수 있습니다.

  ![C++을 사용한 데스크톱 개발 워크로드 사용](media/sdk/vs-enable-cpp-workload.png)

  ![Visual Studio에서 NuGet 패키지 관리자 사용 ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

Visual Studio 2017에서 새로운 Visual C++ Windows 데스크톱 창 콘솔 응용 프로그램을 만듭니다. **새 프로젝트** 대화 상자의 왼쪽 창에서 **설치됨** \> **Visual C++** \> **Windows 데스크톱**을 선택한 후 **Windows 콘솔 응용 프로그램**을 확장합니다. 프로젝트 이름으로 *helloworld*를 입력합니다.

![Visual C++ Windows 데스크톱 창 콘솔 응용 프로그램 만들기](media/sdk/qs-cpp-windows-01-new-console-app.png)

64비트 Windows 설치를 실행한다면 필요한 경우 빌드 플랫폼을 `x64`로 전환합니다.

![빌드 플랫폼을 x64로 전환](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>음성 SDK NuGet 패키지 설치 및 참조

솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션에 대한 NuGet 패키지 관리**를 클릭합니다.

![솔루션에 대한 NuGet 패키지 관리 마우스 오른쪽 단추로 클릭](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

오른쪽 위 모서리의 **패키지 원본** 필드에서 "Nuget.org"를 선택합니다.
**찾아보기** 탭에서 "Microsoft.CognitiveServices.Speech" 패키지를 검색하고, 선택한 다음, 오른쪽에서 **프로젝트** 및 **helloworld** 상자를 선택하고, **설치**를 선택하여 helloworld 프로젝트에 설치합니다.

> [!NOTE]
> Cognitive Services 음성 SDK의 현재 버전은 `0.5.0`입니다.

![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

팝업되는 라이선스 화면에서 라이선스에 동의합니다.

![라이선스 동의](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. 기본 시작 코드를 다음 코드로 바꿉니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

1. 응용 프로그램을 빌드합니다. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택합니다. 코드는 이제 오류 없이 컴파일됩니다.

   ![성공한 빌드](media/sdk/qs-cpp-windows-06-build.png)

1. 응용 프로그램을 시작합니다. 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.

   ![앱을 디버깅하기 시작](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 콘솔 창이 팝업되면 아무 말이나 하라는 메시지가 표시됩니다(영어로).
   인식 결과가 화면에 표시됩니다.

   ![성공한 인식 이후 콘솔 출력](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/cpp-windows` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 추가 예제는 [예제 페이지](samples.md)로 이동합니다.
