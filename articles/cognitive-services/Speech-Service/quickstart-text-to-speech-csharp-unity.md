---
title: '빠른 시작: 음성 합성, Unity - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 통해 Unity 및 Unity용 Speech SDK를 사용하여 텍스트를 음성으로 변환하는 애플리케이션을 만듭니다. 이 과정을 마치면 텍스트를 음성으로 합성하여 디바이스 스피커에서 실시간으로 재생할 수 있습니다.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: b4d329d9d3c2a259fb90b0278c54ba8590590995
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675495"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>빠른 시작: Unity용 Speech SDK를 사용하여 음성 합성

빠른 시작은 [음성 인식](quickstart-csharp-unity.md)에도 사용할 수 있습니다.

이 가이드를 통해 [Unity](https://unity3d.com/) 및 Unity용 Azure Cognitive Services Speech SDK를 사용하여 텍스트 음성 변환 애플리케이션을 만듭니다.
완료되면 텍스트에서 음성을 실시간으로 디바이스의 스피커로 합성할 수 있습니다.
Unity에 익숙하지 않은 경우 애플리케이션 개발을 시작하기 전에 [Unity 사용자 설명서](https://docs.unity3d.com/Manual/UnityManual.html)를 살펴보세요.

> [!NOTE]
> Unity는 Windows 데스크톱(x86 및 x64) 또는 유니버설 Windows 플랫폼(x86, x64, ARM/ARM64), Android(x86, ARM32/64) 및 iOS(x64 시뮬레이터, ARM32 및 ARM64)를 지원합니다.

## <a name="prerequisites"></a>필수 조건

이 프로젝트를 완료하려면 다음이 필요합니다.

* [Unity 2018.3 이상](https://store.unity.com/)([UWP ARM64 지원이 추가된 Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal) 포함)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Visual Studio 2017 버전 15.9 이상도 허용됩니다.
* Windows ARM64를 지원하려면 [ARM64용 선택적 빌드 도구 및 ARM64용 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)를 설치합니다.
* Speech Service에 대한 구독 키 [무료로 가져올 수 있습니다](get-started.md).

## <a name="create-a-unity-project"></a>Unity 프로젝트 만들기

* Unity를 시작하고, **프로젝트** 탭 아래에서 **새로 만들기**를 선택합니다.
* **프로젝트 이름**을 **csharp-unity**로 지정하고, **템플릿**을 **3D**로 지정하고, 위치를 선택합니다.
  그런 다음, **프로젝트 만들기**를 선택합니다.
* 잠시 후에 Unity 편집기 창이 나타납니다.

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Unity용 Speech SDK(베타)는 Unity 자산 패키지(.unitypackage)로 패키지됩니다. [이 웹 사이트](https://aka.ms/csspeech/unitypackage)에서 다운로드합니다.
* **자산** > **패키지 가져오기** > **사용자 지정 패키지**를 차례로 선택하여 Speech SDK를 가져옵니다. 자세한 내용은 [Unity 설명서](https://docs.unity3d.com/Manual/AssetPackages.html)를 참조하세요.
* 파일 선택기에서 다운로드한 Speech SDK .unitypackage 파일을 선택합니다.
* 모든 파일이 선택되었는지 확인하고, **가져오기**를 선택합니다.

  ![Speech SDK Unity 자산 패키지를 가져올 때의 Unity 편집기 스크린샷](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-a-ui"></a>UI 추가

합성을 위한 텍스트를 입력하는 입력 필드, 음성 합성을 트리거하는 단추, 결과를 표시하는 텍스트 필드로 구성된 최소한의 UI만 장면에 추가합니다.

* [계층 구조 창](https://docs.unity3d.com/Manual/Hierarchy.html)(기본적으로 왼쪽)에는 Unity에서 새 프로젝트로 만든 장면 샘플이 표시됩니다.
* **계층 구조** 창의 위쪽에서 **만들기** 단추를 선택하고, **UI** > **입력 필드**를 차례로 선택합니다.
* 이 옵션은 **계층 구조** 창에서 볼 수 있는 3개의 게임 개체, 즉 **Canvas** 개체 내에 중첩된 **입력 필드** 개체와 **EventSystem** 개체를 만듭니다.
* [장면 보기](https://docs.unity3d.com/Manual/UsingTheSceneView.html)에서 캔버스와 입력 필드를 잘 볼 수 있도록 [장면 보기를 탐색합니다](https://docs.unity3d.com/Manual/SceneViewNavigation.html).
* **계층 구조** 창에서 **입력 필드** 개체를 선택하여 [검사기 창](https://docs.unity3d.com/Manual/UsingTheInspector.html)(기본적으로 오른쪽)에 해당 설정을 표시합니다.
* 입력 필드가 캔버스의 중앙에 오도록 **Pos X** 및 **Pos Y** 속성을 **0**으로 설정합니다.
* **계층 구조** 창의 위쪽에서 **만들기** 단추를 다시 선택합니다. **UI** > **Button**을 차례로 선택하여 단추를 만듭니다.
* **계층 구조** 창에서 **Button** 개체를 선택하여 [검사기 창](https://docs.unity3d.com/Manual/UsingTheInspector.html)(기본적으로 오른쪽)에 해당 설정을 표시합니다.
* **Pos X** 및 **Pos Y** 속성을 **0** 및 **-48**로 설정합니다. 단추와 입력 필드가 겹치지 않도록 **Width** 및 **Height** 속성을 **160** 및 **30**으로 설정합니다.
* **계층 구조** 창의 위쪽에서 **만들기** 단추를 다시 선택합니다. **UI** > **Text**를 차례로 선택하여 텍스트 필드를 만듭니다.
* **계층 구조** 창에서 **Text** 개체를 선택하여 [검사기 창](https://docs.unity3d.com/Manual/UsingTheInspector.html)(기본적으로 오른쪽)에 해당 설정을 표시합니다.
* **Pos X** 및 **Pos Y** 속성을 **0** 및 **80**으로 설정합니다. 텍스트 필드와 입력 필드가 겹치지 않도록 **Width** 및 **Height** 속성을 각각 **320** 및 **80**으로 설정합니다.
* **계층 구조** 창의 위쪽에서 **만들기** 단추를 다시 선택합니다. **오디오** > **오디오 원본**을 차례로 선택하여 오디오 원본을 만듭니다.

완료되면 UI가 다음 스크린샷과 비슷하게 표시됩니다.

[![Unity 편집기의 빠른 시작 사용자 인터페이스의 스크린샷](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. [프로젝트 창](https://docs.unity3d.com/Manual/ProjectView.html)(기본적으로 왼쪽 아래)에서 **만들기** 단추, **C# 스크립트**를 차례로 선택합니다. 스크립트 이름을 `HelloWorld`로 지정합니다.

1. 스크립트를 두 번 클릭하여 편집합니다.

   > [!NOTE]
   > **편집** > **기본 설정**을 차례로 선택하여 시작되는 코드 편집기를 구성할 수 있습니다. 자세한 내용은 [Unity 사용자 설명서](https://docs.unity3d.com/Manual/Preferences.html)를 참조하세요.

1. 모든 코드를 다음으로 바꿉니다.

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` 문자열을 찾아서 Speech Service 구독 키로 바꿉니다.

1. `YourServiceRegion` 문자열을 찾아서 구독과 연결된 [지역](regions.md)으로 바꿉니다. 예를 들어 평가판을 사용하는 경우 지역은 `westus`입니다.

1. 변경 내용을 스크립트에 저장합니다.

1. Unity 편집기로 돌아가서 스크립트를 구성 요소로 게임 개체 중 하나에 추가합니다.

   * **계층 구조** 창에서 **Canvas** 개체를 선택하여 [검사기 창](https://docs.unity3d.com/Manual/UsingTheInspector.html)(기본적으로 오른쪽)에서 해당 설정을 엽니다.
   * **검사기** 창에서 **구성 요소 추가** 단추를 선택합니다. 그런 다음, 이전에 만든 `HelloWorld` 스크립트를 검색하여 추가합니다.
   * HelloWorld 구성 요소에는 `HelloWorld` 클래스의 공용 속성과 일치하는 초기화되지 않은 네 가지 속성, 즉 **출력 텍스트**, **입력 필드**, **말하기 단추** 및 **오디오 원본**이 있습니다.
     이러한 속성을 연결하려면 개체 선택기(속성의 오른쪽에 있는 작은 원 아이콘)를 선택합니다. 이전에 만든 텍스트와 단추 개체를 선택합니다.

     > [!NOTE]
     > 입력 필드와 단추에는 중첩 텍스트 개체도 있습니다. 실수로 텍스트 출력을 위해 선택하지 않도록 합니다. 또는 이러한 혼동을 방지하기 위해 **검사기** 창에서 **이름** 필드를 사용하는 텍스트 개체의 이름을 바꿀 수 있습니다.

## <a name="run-the-application-in-the-unity-editor"></a>Unity 편집기에서 애플리케이션 실행

* 메뉴 모음의 아래쪽에 있는 Unity 편집기 도구 모음에서 **재생** 단추를 선택합니다.
* 앱이 시작되면 입력 필드에 텍스트를 입력하고, 단추를 선택합니다. 텍스트가 Speech Services로 전송되고, 음성으로 합성되어 스피커에서 재생됩니다.

  [![Unity 게임 창에서 실행 중인 빠른 시작의 스크린샷](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* [콘솔 창](https://docs.unity3d.com/Manual/Console.html)에서 디버그 메시지를 확인합니다.
* 음성 합성이 완료되면 Unity 편집기 도구 모음에서 **재생** 단추를 선택하여 앱을 중지합니다.

## <a name="additional-options-to-run-this-application"></a>이 애플리케이션을 실행하기 위한 추가 옵션

이 애플리케이션은 Windows 독립 실행형 애플리케이션 또는 UWP 애플리케이션으로 Android에 배포할 수도 있습니다.
이러한 추가 대상에 대한 구성을 설명하는 quickstart/csharp-unity 폴더의 [샘플 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음성 글꼴 사용자 지정](how-to-customize-voice-font.md)
- [음성 샘플 레코드](record-custom-voice-samples.md)
