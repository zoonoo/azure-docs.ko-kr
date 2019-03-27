---
title: '빠른 시작: 음성 인식, Unity - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 가이드에 따라 Unity 및 Unity용 Speech SDK(베타)를 사용하여 음성을 텍스트로 변환하는 애플리케이션을 만듭니다. 작업을 마치면 컴퓨터의 마이크를 사용하여 실시간으로 음성을 텍스트로 변환할 수 있습니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: cdde9f0ec69bec48ae0fb747db0cc49e81920817
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57872574"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>빠른 시작: Unity용 Speech SDK(베타)를 사용하여 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 가이드에 따라 [Unity](https://unity3d.com/) 및 Unity용 Speech SDK(베타)를 사용하여 음성을 텍스트로 변환하는 애플리케이션을 만듭니다.
작업을 마치면 컴퓨터의 마이크를 사용하여 실시간으로 음성을 텍스트로 변환할 수 있습니다.
Unity에 익숙하지 않은 경우 애플리케이션 개발을 시작하기 전에 [Unity 사용자 설명서](https://docs.unity3d.com/Manual/UnityManual.html)를 연구하는 것이 좋습니다.

> [!NOTE]
> Unity용 Speech SDK는 현재 베타 버전입니다.
> Windows x86 및 x64(독립 실행형 데스크톱 애플리케이션 또는 유니버설 Windows 플랫폼)와 Android(ARM32/64, x86)를 지원합니다.

## <a name="prerequisites"></a>필수 조건

이 프로젝트를 완료하려면 다음이 필요합니다.

* [Unity 2018.3 이상](https://store.unity.com/)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service에 대한 구독 키 [무료로 가져올 수 있습니다](get-started.md).
* 컴퓨터의 마이크에 액세스합니다.

## <a name="create-a-unity-project"></a>Unity 프로젝트 만들기

* Unity를 시작하고, **프로젝트** 탭 아래에서 **새로 만들기**를 선택합니다.
* **프로젝트 이름**을 **csharp-unity**로, **Template**을 **3D**로 지정하고 위치를 선택합니다.
  그런 다음, **프로젝트 만들기**를 선택합니다.
* 잠시 후에 Unity 편집기 창이 나타납니다.

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Unity용 Speech SDK(베타)는 Unity 자산 패키지(.unitypackage)로 패키지됩니다.
  [여기](https://aka.ms/csspeech/unitypackage)에서 다운로드하세요.
* **자산** > **패키지 가져오기** > **사용자 지정 패키지**를 차례로 선택하여 Speech SDK를 가져옵니다.
  자세한 내용은 [Unity 설명서](https://docs.unity3d.com/Manual/AssetPackages.html)를 참조하세요.
* 파일 선택기에서 위에서 다운로드한 .unitypackage Speech SDK 파일을 선택합니다.
* 모든 파일이 선택되었는지 확인하고 **가져오기**를 클릭합니다.

  ![Speech SDK Unity 자산 패키지를 가져올 때의 Unity 편집기 스크린샷](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>UI 추가

음성 인식을 트리거하는 단추와 결과를 표시하는 텍스트 필드로 구성된 최소한의 UI를 장면에 추가합니다.

* [계층 구조 창](https://docs.unity3d.com/Manual/Hierarchy.html)(기본적으로 왼쪽에 있음)에는 Unity에서 새 프로젝트로 만든 장면 샘플이 표시됩니다.
* 계층 구조 창의 위쪽에 있는 **만들기** 단추를 클릭하고, **UI** > **Button**을 차례로 선택합니다.
* 이렇게 하면 계층 구조 창에서 볼 수 있는 3개의 게임 개체, 즉 **Canvas** 개체 내에 중첩된 **Button** 개체와 **EventSystem** 개체가 만들어집니다.
* [장면 보기](https://docs.unity3d.com/Manual/UsingTheSceneView.html)에서 캔버스와 단추를 잘 볼 수 있도록 [장면 보기](https://docs.unity3d.com/Manual/SceneViewNavigation.html)를 탐색합니다.
* 계층 구조 창에서 **Button** 개체를 클릭하여 [검사기 창](https://docs.unity3d.com/Manual/UsingTheInspector.html)(기본적으로 오른쪽에 있음)에 설정을 표시합니다.
* 단추가 캔버스 중앙에 있도록 **Pos X** 및 **Pos Y** 속성을 **0**으로 설정합니다.
* 계층 구조 창의 위쪽에 있는 **만들기** 단추를 다시 클릭하고, **UI** > **Text**를 선택하여 텍스트 필드를 만듭니다.
* 계층 구조 창에서 **Text** 개체를 클릭하여 [검사기 창](https://docs.unity3d.com/Manual/UsingTheInspector.html)(기본적으로 오른쪽에 있음)에 설정을 표시합니다.
* 텍스트 필드와 단추가 겹치지 않도록 **Pos X** 및 **Pos Y** 속성을 각각 **0** 및 **120**으로 설정하고, **Width** 및 **Height** 속성을 각각 **240** 및 **120**으로 설정합니다.

완료되면 UI가 다음 스크린샷과 비슷하게 표시됩니다.

[![Unity 편집기의 빠른 시작 사용자 인터페이스의 스크린샷](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. [프로젝트 창](https://docs.unity3d.com/Manual/ProjectView.html)(기본적으로 왼쪽 아래에 있음)에서 **만들기** 단추를 클릭한 다음, **C# 스크립트**를 선택합니다. 스크립트 이름을 `HelloWorld`로 지정합니다.

1. 스크립트를 두 번 클릭하여 편집합니다.

   > [!NOTE]
   > **편집** > **기본 설정** 아래에서 시작할 코드 편집기를 구성할 수 있습니다([Unity 사용자 설명서](https://docs.unity3d.com/Manual/Preferences.html) 참조).

1. 모든 코드를 다음으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` 문자열을 찾아서 Speech Service 구독 키로 바꿉니다.

1. `YourServiceRegion` 문자열을 찾아서 구독과 연결된 [Azure 지역](regions.md)으로 바꿉니다. 예를 들어 평가판을 사용하는 경우 Azure 지역은 `westus`입니다.

1. 변경 내용을 스크립트에 저장합니다.

1. Unity 편집기로 돌아가서 스크립트를 구성 요소로 게임 개체 중 하나에 추가해야 합니다.

   * 계층 구조 창에서 **Canvas** 개체를 클릭합니다. 이렇게 하면 [검사기 창](https://docs.unity3d.com/Manual/UsingTheInspector.html)(기본적으로 오른쪽에 있음)에서 설정이 열립니다.
   * 검사기 창에서 **구성 요소 추가** 단추를 클릭한 다음, 위에서 만든 Hello World 스크립트를 검색하여 추가합니다.
   * Hello World 구성 요소에는 `HelloWorld` 클래스의 공용 속성과 일치하는 **출력 텍스트** 및 **녹화 시작 단추**라는 초기화되지 않은 두 가지 속성이 있습니다.
     두 속성을 연결하려면 개체 선택기(속성 오른쪽에 있는 작은 원형 아이콘)를 클릭하고, 앞에서 만든 텍스트 및 단추 개체를 선택합니다.

     > [!NOTE]
     > 단추에는 중첩된 텍스트 개체도 있습니다. 실수로 텍스트 출력을 선택하지 않도록 합니다(또는 혼란을 방지하기 위해 검사기 창의 Name 필드를 사용하여 텍스트 개체 중 하나의 이름을 바꿉니다).

## <a name="run-the-application-in-the-unity-editor"></a>Unity 편집기에서 애플리케이션 실행

* Unity 편집기 도구 모음(메뉴 모음 아래에 있음)에서 **재생** 단추를 누릅니다.

* 앱이 시작되면 단추를 클릭하고 컴퓨터의 마이크에 영어 구 또는 문장을 말합니다. 음성은 Speech Services로 전송되어 텍스트로 변환되고 창에 표시됩니다.

  [![Unity 게임 창에서 실행되는 빠른 시작의 스크린샷](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* [콘솔 창](https://docs.unity3d.com/Manual/Console.html)에서 디버그 메시지를 확인합니다.

* 음성 인식이 완료되면 Unity 편집기 도구 모음에서 **재생** 단추를 클릭하여 앱을 중지합니다.

## <a name="additional-options-to-run-this-application"></a>이 애플리케이션을 실행하기 위한 추가 옵션

이 애플리케이션은 Windows 독립 실행형 애플리케이션 또는 UWP 애플리케이션으로 Android에도 배포할 수 있습니다.
이러한 추가 대상에 대한 구성을 설명하는 quickstart/csharp-unity 폴더의 [샘플 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
