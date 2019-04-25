---
title: '빠른 시작: 음성 인식, Objective-C - Speech Services'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 macOS에서 Objective-C로 음성을 인식하는 방법을 알아봅니다.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: 55fc671d926880375b0420e0eafb6dc63f170ba6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012476"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 macOS에서 Objective-C로 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 Cognitive Services Speech SDK를 사용하여 Objective-C에서 마이크로 녹음한 음성을 텍스트로 변환하는 macOS 앱을 만드는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 필수 구성 요소 목록은 다음과 같습니다.

* Speech Service에 대한 [구독 키](get-started.md)
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 이상이 설치된 macOS 머신 및 macOS 10.13 이상

## <a name="get-the-speech-sdk-for-macos"></a>macOS용 Speech SDK 가져오기

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services 음성 SDK의 현재 버전은 `1.4.0`입니다.

Mac용 Cognitive Services Speech SDK는 프레임워크 번들로 배포됩니다.
Xcode 프로젝트에서 [CocoaPod](https://cocoapods.org/)로 사용하거나, https://aka.ms/csspeech/macosbinary에서 다운로드하거나, 수동으로 연결할 수 있습니다. 이 가이드에서는 CocoaPod를 사용합니다.

## <a name="create-an-xcode-project"></a>Xcode 프로젝트 만들기

Xcode를 시작하고, **파일** > **새로 만들기** > **프로젝트**를 클릭하여 새 프로젝트를 시작합니다.
템플릿 선택 대화 상자에서 "Cocoa 앱" 템플릿을 선택합니다.

뒤따르는 대화 상자에서 다음과 같이 선택합니다.

1. 프로젝트 옵션 대화 상자
    1. 빠른 시작 앱의 이름을 입력합니다(예: `helloworld`).
    1. Apple 개발자 계정이 이미 있는 경우 적절한 조직 이름 및 조직 식별자를 입력합니다. 테스트를 위해 `testorg`와 같은 이름을 선택할 수 있습니다. 앱에 서명하려면 적절한 프로비전 프로필이 필요합니다. 자세한 내용은 [Apple 개발자 사이트](https://developer.apple.com/)를 참조하세요.
    1. 프로젝트에 대한 언어로 Objective-C가 선택되었는지 확인합니다.
    1. 스토리보드를 사용하고 문서 기반 애플리케이션을 만들려면 확인란을 사용하지 않도록 설정합니다. 샘플 앱에 대한 간단한 UI는 프로그래밍 방식으로 생성됩니다.
    1. 테스트 및 핵심 데이터에 대한 모든 확인란을 사용하지 않도록 설정합니다.
    ![프로젝트 설정](media/sdk/qs-objectivec-macos-project-settings.png)
1. 프로젝트 디렉터리 선택
    1. 프로젝트에 삽입할 디렉터리를 선택합니다. 이렇게 하면 홈 디렉터리에 Xcode 프로젝트에 대한 모든 파일을 포함하는 `helloworld` 디렉터리가 만들어집니다.
    1. 이 예제 프로젝트에 대한 Git 리포지토리 만들기를 사용하지 않도록 설정합니다.
1. 네트워크 및 마이크 액세스에 대한 자격을 설정합니다. 앱 구성으로 이동하려면 왼쪽의 개요에서 첫 번째 줄에 있는 앱 이름을 클릭한 다음, "기능" 탭을 선택합니다.
    1. 앱에 대한 "앱 샌드박스" 설정을 사용합니다.
    1. "나가는 연결" 및 "마이크" 액세스에 대한 확인란을 사용하도록 설정합니다.
    ![샌드박스 설정](media/sdk/qs-objectivec-macos-sandbox.png)
1. 또한 앱은 `Info.plist` 파일에서 마이크 사용을 선언해야 합니다. 개요에서 파일을 클릭하고 "음성 인식에 마이크 필요"와 같은 값을 사용하여 "개인 정보 - 마이크 사용 설명" 키를 추가합니다.
    ![Info.plist의 설정](media/sdk/qs-objectivec-macos-info-plist.png)
1. Xcode 프로젝트를 닫습니다. CocoaPods 설정 후 나중에 다른 인스턴스를 사용합니다.

## <a name="install-the-sdk-as-a-cocoapod"></a>CocoaPod로 SDK 설치

1. 해당 [설치 지침](https://guides.cocoapods.org/using/getting-started.html)에 설명된 대로 CocoaPod 종속성 관리자를 설치합니다.
1. 샘플 앱이 있는 디렉터리(`helloworld`)로 이동합니다. 해당 디렉터리에 이름이 `Podfile`인 텍스트 파일 및 다음과 같은 콘텐츠를 배치합니다.
    ```
    target 'helloworld' do
        platform :osx, '10.13'
        pod 'MicrosoftCognitiveServicesSpeech-macOS', '~> 1.4.0'
    end
    ```
1. 터미널에서 `helloworld` 디렉터리로 이동해 `pod install` 명령을 실행합니다. 이렇게 하면 종속성으로 샘플 앱 및 Speech SDK가 모두 포함되는 `helloworld.xcworkspace` Xcode 작업 영역이 생성됩니다. 이 작업 영역은 다음에서 사용됩니다.

## <a name="add-the-sample-code"></a>샘플 코드 추가

1. XCode에서 `helloworld.xcworkspace` 작업 영역을 엽니다.
1. 자동으로 생성된 `AppDelegate.m` 파일의 콘텐츠를 [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]로 바꿉니다.
1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.
1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

1. 디버그 출력을 표시되도록 설정합니다(**뷰** > **디버그 영역** > **콘솔 활성화**).
1. 메뉴에서 **제품** -> **실행**을 선택하거나 **재생** 단추를 클릭하여 예제 코드를 빌드하고 실행합니다.
1. 단추를 클릭하고 몇 단어를 말하면 화면 아래쪽 부분에 사용자가 말한 텍스트가 보입니다. 처음으로 앱을 실행하는 경우 컴퓨터 마이크에 대한 앱 액세스 권한을 부여하라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub의 Objective-C 샘플 살펴보기](https://aka.ms/csspeech/samples)

