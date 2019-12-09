---
title: '빠른 시작: 음성 합성, Java(Android) - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 Android에서 Java로 음성을 합성하는 방법을 알아봅니다.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: e38b1bdd3258675dfac9a155f7cee338a1f8f806
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818459"
---
## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=android)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=android)

## <a name="create-user-interface"></a>사용자 인터페이스 만들기

애플리케이션의 기본 사용자 인터페이스를 만들어 보겠습니다. 기본 활동을 위한 레이아웃 `activity_main.xml`을 편집합니다. 레이아웃에는 기본적으로 애플리케이션 이름이 있는 제목 표시줄과 "Hello World!" 텍스트가 포함된 TextView가 포함됩니다.

1. TextView 요소를 클릭합니다. 오른쪽 위 모서리에 있는 해당 ID 특성을 `outputMessage`로 변경하고 아래쪽 화면으로 끕니다. 해당 텍스트를 삭제합니다.

1. `activity_main.xml` 창 왼쪽 위에 있는 색상표에서 단추를 텍스트 위의 빈 공간으로 끌어옵니다.

1. 오른쪽의 단추 특성에서 `onClick` 특성 값에 `onSpeechButtonClicked`를 입력합니다. 이 이름으로 버튼 이벤트를 처리하기 위한 메서드를 작성할 것입니다.  오른쪽 위 모서리에 있는 해당 ID 특성을 `button`로 변경합니다.

1. 일반 텍스트를 단추 위의 공간으로 끌어 옵니다. ID 특성을 `speakText`로 변경하고 텍스트 특성을 `Hi there!`로 변경합니다.

1. 레이아웃 제약 조건을 유추하도록 디자이너 맨 위에 있는 마법 지팡이 아이콘을 사용합니다.


    ![마법 지팡이 아이콘의 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

이제 UI의 텍스트 및 그래픽 모양이 다음과 유사하게 표시됩니다.

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. `MainActivity.java` 소스 파일을 엽니다. 이 파일의 모든 코드를 다음으로 바꿉니다.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onSpeechButtonClicked` 메서드는 앞에서 설명한 것처럼 단추 클릭 처리기입니다. 단추를 누르면 음성 합성이 트리거됩니다.

1. 동일한 파일에서 `YourSubscriptionKey` 문자열을 구독 키로 바꿉니다.

1. 또한 `YourServiceRegion` 문자열을 구독과 연결된 [지역](~/articles/cognitive-services/Speech-Service/regions.md)으로 바꿉니다(예: 평가판 구독의 `westus`).

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 개발 PC에 Android 디바이스를 연결합니다. 디바이스에서 [개발 모드 및 USB 디버깅](https://developer.android.com/studio/debug/dev-options)이 사용하도록 설정되어 있는지 확인합니다. 또는 [Android 에뮬레이터](https://developer.android.com/studio/run/emulator)를 만듭니다.

1. 애플리케이션을 빌드하려면 Ctrl + F9를 누르거나 메뉴 모음에서 **빌드** > **프로젝트 만들기**를 선택합니다.

1. 애플리케이션을 시작하려면 Shift+F10을 누르거나 **실행** >  **'앱' 실행**을 선택합니다.

1. 표시되는 배포 대상 창에서 Android 디바이스 또는 에뮬레이터를 선택합니다.

   ![배포 대상 선택 창 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

애플리케이션에서 텍스트를 입력하고 단추를 눌러 음성 합성 섹션을 시작합니다. 기본 스피커에서 합성된 오디오가 나오고 화면에 `speech synthesis succeeded` 정보가 표시됩니다.

![Android 애플리케이션 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>참고 항목

- [사용자 지정 음성 만들기](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [사용자 지정 음성 녹음 샘플](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
