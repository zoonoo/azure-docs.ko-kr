---
title: C++ 및 Windows용 음성 SDK 빠른 시작 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services에서 Windows 및 C++로 음성 SDK를 사용하여 신속하게 시작할 수 있도록 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049233"
---
# <a name="quickstart-for-c-and-windows"></a>C++ 및 Windows용 빠른 시작

Cognitive Services 음성 SDK의 현재 버전은 `0.4.0`입니다.

음성 SDK를 사용하는 Windows 데스크톱용 C++ 기반 콘솔 응용 프로그램을 만드는 방법을 설명합니다.
응용 프로그램은 [Microsoft Cognitive Services SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) 및 Microsoft Visual Studio 2017에 기반합니다.

> [!NOTE]
> C++ 및 Linux용 빠른 시작을 찾는 경우 [여기](quickstart-cpp-linux.md)로 이동합니다.<br>
> C# 및 Windows용 빠른 시작을 찾는 경우 [여기](quickstart-csharp-windows.md)로 이동합니다.

> [!NOTE]
> 이 빠른 시작에는 마이크가 작동하는 PC가 필요합니다.<br>
> 지정된 오디오 입력 파일의 음성을 인식하는 샘플은 [샘플](speech-to-text-sample.md#speech-recognition-from-a-file)을 참조하세요.

> [!NOTE]
> Visual Studio 설치에 **C++를 사용한 데스크톱 개발** 워크로드가 포함되었는지 확인합니다.
> 확신할 수 없다면 다음 단계를 사용하여 확인하고 해결합니다. Visual Studio 2017에서 **도구** \> **도구 및 기능 가져오기**를 선택하고 **예**를 선택하여 사용자 계정 컨트롤을 승인합니다.
> **워크로드** 탭에서 **C++를 사용한 데스크톱 개발** 옆에 확인란이 설정되지 않은 경우 설정하고 **수정**을 클릭하여 변경 내용을 저장합니다.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>비어 있는 콘솔 응용 프로그램 프로젝트 만들기

Visual Studio 2017에서 "CppHelloSpeech"라는 이름의 새로운 Visual C++ Windows 데스크톱 창 콘솔 응용 프로그램을 만듭니다.

![Visual C++ Windows 데스크톱 창 콘솔 응용 프로그램 만들기](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

64비트 Windows 설치를 실행한다면 필요한 경우 빌드 플랫폼을 `x64`로 전환합니다.

![빌드 플랫폼을 x64로 전환](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>음성 SDK NuGet 패키지 설치 및 참조

> [!NOTE]
> NuGet 패키지 관리자를 Visual Studio 2017 설치에서 사용하도록 설정했는지 확인합니다.
> Visual Studio 2017에서 **도구** \> **도구 및 기능 가져오기**를 선택하고 **예**를 선택하여 사용자 계정 컨트롤을 승인합니다. 그런 다음, **개별 구성 요소** 탭을 선택하고 **코드 도구** 아래에서 **NuGet 패키지 관리자**를 찾습니다.
> 왼쪽 확인란이 설정되지 않은 경우 설정하고 **수정**을 클릭하여 변경 내용을 저장합니다.
>
> ![Visual Studio에서 NuGet 패키지 관리자 사용 ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **솔루션에 대한 NuGet 패키지 관리**를 클릭합니다.

![솔루션에 대한 NuGet 패키지 관리 마우스 오른쪽 단추로 클릭](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

오른쪽 위 모서리의 **패키지 원본** 필드에서 "Nuget.org"를 선택합니다.
**찾아보기** 탭에서 "Microsoft.CognitiveServices.Speech" 패키지를 검색하고, 선택한 다음, 오른쪽에서 **프로젝트** 및 **CppHelloSpeech** 상자를 선택하고, **설치**를 선택하여 CppHelloSpeech 프로젝트에 설치합니다.

![Microsoft.CognitiveServices.Speech NuGet 패키지 설치](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

팝업되는 라이선스 화면에서 라이선스에 동의합니다.

![라이선스 동의](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>샘플 코드 추가

기본 시작 코드를 다음 코드로 바꿉니다.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> 구독 키를 가지고 있는 구독 키로 바꿉니다. <br>
> 지역을 [음성 서비스 REST API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis)의 지역으로 바꿉니다(예: '미국 서부'로 바꿈).

![구독 키 추가](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

코드는 이제 오류 없이 컴파일됩니다.

![성공한 빌드](media/sdk/speechsdk-16-vs-cpp-build.png)

시작 단추 또는 F5 바로 가기 키를 사용하여 디버거에서 프로그램을 시작합니다.

![앱을 디버깅하기 시작](media/sdk/speechsdk-17-vs-cpp-f5.png)

콘솔 창이 팝업되면 아무 말이나 하라는 메시지가 표시됩니다(영어로).
인식 결과가 화면에 표시됩니다.

![성공한 인식 이후 콘솔 출력](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>샘플 다운로드

샘플의 최신 집합은 [Cognitive Services 음성 SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 추가 예제는 [예제 페이지](samples.md)로 이동합니다.
