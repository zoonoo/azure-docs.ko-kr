---
title: '빠른 시작: 음성 인식, Objective-C - Speech Services'
titleSuffix: Azure Cognitive Services
description: Speech Service SDK를 사용하여 iOS의 Objective-C에서 음성을 인식하는 방법 알아보기
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: chlandsi
ms.openlocfilehash: 044d703af50a951e1c406014f3e00eb49c1632a2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209624"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-service-sdk"></a>빠른 시작: Speech Service SDK를 사용하여 iOS의 Objective-C에서 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services Speech SDK를 사용하여 Objective-C에서 녹음된 음성의 오디오 파일을 텍스트로 변환하는 iOS 앱을 만드는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 필수 구성 요소 목록은 다음과 같습니다.

* Speech Service에 대한 [구독 키](get-started.md)
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 이상이 설치된 macOS 머신
* iOS 버전 11.4 이상으로 설정된 대상

## <a name="get-the-speech-sdk-for-ios"></a>iOS용 Speech SDK 가져오기

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services 음성 SDK의 현재 버전은 `1.2.0`입니다.

Mac 및 iOS용 Cognitive Services Speech SDK는 현재 Cocoa Framework로 배포됩니다.
https://aka.ms/csspeech/iosbinary에서 다운로드할 수 있습니다. 홈 디렉터리에 파일을 다운로드합니다.

## <a name="create-an-xcode-project"></a>Xcode 프로젝트 만들기

Xcode를 시작하고, **파일** > **새로 만들기** > **프로젝트**를 클릭하여 새 프로젝트를 시작합니다.
템플릿 선택 대화 상자에서 "iOS 단일 보기 앱" 템플릿을 선택합니다.

뒤따르는 대화 상자에서 다음과 같이 선택합니다.

1. 프로젝트 옵션 대화 상자
    1. 빠른 시작 앱의 이름을 입력합니다(예: `helloworld`).
    1. Apple 개발자 계정이 이미 있는 경우 적절한 조직 이름 및 조직 식별자를 입력합니다. 테스트를 위해 `testorg`와 같은 이름을 선택할 수 있습니다. 앱에 서명하려면 적절한 프로비전 프로필이 필요합니다. 자세한 내용은 [Apple 개발자 사이트](https://developer.apple.com/)를 참조하세요.
    1. 프로젝트에 대한 언어로 Objective-C가 선택되었는지 확인합니다.
    1. 테스트 및 핵심 데이터에 대한 모든 확인란을 사용하지 않도록 설정합니다.
    ![프로젝트 설정](media/sdk/qs-objectivec-project-settings.png)
1. 프로젝트 디렉터리 선택
    1. 프로젝트에 삽입할 홈 디렉터리를 선택합니다. 이렇게 하면 홈 디렉터리에 Xcode 프로젝트에 대한 모든 파일을 포함하는 `helloworld` 디렉터리가 만들어집니다.
    1. 이 예제 프로젝트에 대한 Git 리포지토리 만들기를 사용하지 않도록 설정합니다.
    1. *프로젝트 설정*에서 SDK에 대한 경로를 조정합니다.
        1. **포함된 이진 파일** 헤더 아래의 **일반** 탭에서 프레임워크로 SDK 라이브러리를 추가합니다. **포함된 이진 파일 추가** > **기타 추가...** > 홈 디렉터리로 이동 및 파일 선택`MicrosoftCognitiveServicesSpeech.framework`. 이렇게 하면 헤더 **연결된 프레임워크 및 라이브러리**에 SDK 라이브러리를 자동으로 추가합니다.
        ![추가된 프레임워크](media/sdk/qs-objectivec-framework.png)
        1. **빌드 설정** 탭으로 이동하고 **모든** 설정을 활성화합니다.
        1. `$(SRCROOT)/..` 디렉터리를 **검색 경로** 제목 아래의 *프레임워크 검색 경로*에 추가합니다.
        ![프레임워크 검색 경로 설정](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>UI 설정

예제 앱에서는 UI가 아주 단순합니다. 파일 또는 마이크 입력에서 음성 인식을 시작하는 단추 두 개와 결과를 표시하는 텍스트 레이블로 구성되어 있습니다.
UI는 프로젝트의 일부로 `Main.storyboard`에 설정되어 있습니다.
마우스 오른쪽 단추로 프로젝트 트리의 `Main.storyboard` 항목을 클릭하고 **다음 형식으로 열기...** > **소스 코드**를 선택하여 스토리보드의 XML 뷰를 엽니다.
자동으로 생성된 XML을 이 코드로 바꿉니다.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. 마우스 오른쪽 단추로 링크를 클릭하고 **다른 이름으로 대상 저장...** 을 선택하여 [샘플 wav 파일](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)을 다운로드합니다. Finder 창에서 프로젝트 보기의 루트 수준으로 끌어 놓아 wav 파일을 리소스로 프로젝트에 추가합니다.
설정을 변경하지 않고 다음 대화 상자에서 **완료**를 클릭합니다.
1. 자동으로 생성된 `ViewController.m` 파일의 콘텐츠를 다음으로 바꿉니다.

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.
1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).
1. 마이크 액세스에 대한 요청을 추가합니다. 프로젝트 트리의 `Info.plist` 항목을 마우스 오른쪽 단추로 클릭하고 **다음 형식으로 열기...** > **소스 코드**를 선택합니다. `<dict>` 섹션으로 다음 줄을 추가한 다음, 파일을 저장합니다.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>샘플 빌드 및 실행

1. 디버그 출력을 표시되도록 설정합니다(**뷰** > **디버그 영역** > **콘솔 활성화**).
1. **제품** -> **대상** 메뉴의 목록에서 앱에 대한 대상으로 개발 머신에 연결된 iOS 시뮬레이터 또는 iOS 디바이스를 선택합니다.
1. 메뉴에서 **제품** -> **실행**을 선택하거나 **재생** 단추를 클릭하여 iOS 시뮬레이터에서 예제 코드를 빌드하고 실행합니다.
현재 Speech SDK는 64비트 iOS 플랫폼만 지원합니다.
1. 앱에서 "Recognize (File)" 단추를 클릭하면 오디오 파일의 콘텐츠인 "What's the weather like?"가 화면 아래쪽 부분에 표시됩니다.

 ![시뮬레이션된 iOS 앱](media/sdk/qs-objectivec-simulated-app.png)

1. 앱에서 "Recognize (Microphone)" 단추를 클릭하고 몇 단어를 말하면 화면 아래쪽 부분에 사용자가 말한 텍스트가 보영 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub의 Objective-C 샘플 살펴보기](https://aka.ms/csspeech/samples)
