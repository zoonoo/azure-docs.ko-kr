---
title: '빠른 시작: 음성 합성, Objective-C - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 iOS에서 Objective-C로 음성을 합성하는 방법 알아보기
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 1f5a569f9c3bfa91b78c836e37a22642238674be
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74975944"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 iOS에서 Objective-C로 음성 합성

이 문서에서는 Cognitive Services Speech SDK를 사용하여 Objective-C에서 iOS 앱을 생성하여 텍스트에서 음성을 합성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 필수 구성 요소 목록은 다음과 같습니다.

* Speech Service에 대한 [구독 키](~/articles/cognitive-services/Speech-Service/get-started.md)
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 이상이 설치된 macOS 머신
* iOS 버전 9.3 이상으로 설정된 대상

## <a name="get-the-speech-sdk-for-ios"></a>iOS용 Speech SDK 가져오기

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

이 자습서는 1.7.0 이전 버전의 SDK에서는 작동하지 않습니다.

iOS용 Cognitive Services Speech SDK는 현재 Cocoa Framework로 배포됩니다.
Xcode 프로젝트에서 [CocoaPod](https://cocoapods.org/)로 사용하거나, https://aka.ms/csspeech/iosbinary 에서 다운로드하거나, 수동으로 연결할 수 있습니다. 이 가이드에서는 CocoaPod를 사용합니다.

## <a name="create-an-xcode-project"></a>Xcode 프로젝트 만들기

Xcode를 시작하고, **파일** > **새로 만들기** > **프로젝트**를 클릭하여 새 프로젝트를 시작합니다.
템플릿 선택 대화 상자에서 "iOS 단일 보기 앱" 템플릿을 선택합니다.

뒤따르는 대화 상자에서 다음과 같이 선택합니다.

1. 프로젝트 옵션 대화 상자
    1. 빠른 시작 앱의 이름을 입력합니다(예: `helloworld`).
    1. Apple 개발자 계정이 이미 있는 경우 적절한 조직 이름 및 조직 식별자를 입력합니다. 테스트를 위해 `testorg`와 같은 이름을 선택할 수 있습니다. 앱에 서명하려면 적절한 프로비전 프로필이 필요합니다. 자세한 내용은 [Apple 개발자 사이트](https://developer.apple.com/)를 참조하세요.
    1. 프로젝트에 대한 언어로 Objective-C가 선택되었는지 확인합니다.
    1. 테스트 및 핵심 데이터에 대한 모든 확인란을 사용하지 않도록 설정합니다.
    ![프로젝트 설정](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. 프로젝트 디렉터리 선택
    1. 프로젝트에 삽입할 홈 디렉터리를 선택합니다. 이렇게 하면 홈 디렉터리에 Xcode 프로젝트에 대한 모든 파일을 포함하는 `helloworld` 디렉터리가 만들어집니다.
    1. 이 예제 프로젝트에 대한 Git 리포지토리 만들기를 사용하지 않도록 설정합니다.

## <a name="install-the-sdk-as-a-cocoapod"></a>CocoaPod로 SDK 설치

1. 해당 [설치 지침](https://guides.cocoapods.org/using/getting-started.html)에 설명된 대로 CocoaPod 종속성 관리자를 설치합니다.
1. 샘플 앱이 있는 디렉터리(`helloworld`)로 이동합니다. 해당 디렉터리에 이름이 `Podfile`인 텍스트 파일 및 다음과 같은 콘텐츠를 배치합니다.  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. 터미널에서 `helloworld` 디렉터리로 이동해 `pod install` 명령을 실행합니다. 이렇게 하면 종속성으로 샘플 앱 및 Speech SDK가 모두 포함되는 `helloworld.xcworkspace` Xcode 작업 영역이 생성됩니다. 이 작업 영역은 다음에서 사용됩니다.

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. XCode에서 `helloworld.xcworkspace` 작업 영역을 엽니다.
1. 자동으로 생성된 `AppDelegate.m` 파일의 콘텐츠를 다음으로 바꿉니다.  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. 자동으로 생성된 `ViewController.m` 파일의 콘텐츠를 다음으로 바꿉니다.  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.
1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](~/articles/cognitive-services/Speech-Service/regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

1. 디버그 출력을 표시되도록 설정합니다(**뷰** > **디버그 영역** > **콘솔 활성화**).
1. **제품** > **대상** 메뉴의 목록에서 앱에 대한 대상으로 개발 머신에 연결된 iOS 시뮬레이터 또는 iOS 디바이스를 선택합니다.
1. 메뉴에서 **제품** > **실행**을 선택하거나 **재생** 단추를 클릭하여 iOS 시뮬레이터에서 예제 코드를 빌드하고 실행합니다.

   ![시뮬레이션된 iOS 앱](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. 텍스트를 입력하고 앱의 단추를 클릭하면 합성된 오디오가 재생됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub의 Objective-C 샘플 살펴보기](https://aka.ms/csspeech/samples)

