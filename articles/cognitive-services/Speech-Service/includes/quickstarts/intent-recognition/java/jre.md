---
title: '빠른 시작: 음성, 의도 및 엔터티 인식, Java - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c9aa4200dc217e9c79290ca91b03810f0769bdc2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280406"
---
## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
>
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [LUIS(Language Understanding) 애플리케이션 만들기 및 엔드포인트 키 가져오기](../../../../quickstarts/create-luis.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>프로젝트 열기

프로젝트를 로드하고 `Main.java`를 엽니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`IntentRecognizer` 개체를 초기화하기 전에 LUIS 엔드포인트 키와 지역을 사용하는 구성을 만들어야 합니다. Main의 try/catch 블록에 이 코드 삽입

이 샘플에서는 `FromSubscription()` 메서드를 사용하여 `SpeechConfig`를 빌드합니다. 사용 가능한 메서드의 전체 목록은 [SpeechConfig 클래스](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)를 참조하세요.

> [!NOTE]
> 음성 의도 변환 인식에는 엔드포인트 키만 유효하므로 시작 키 또는 저작 키가 아닌 LUIS 엔드포인트 키를 사용하는 것이 중요합니다. 올바른 키를 가져오는 방법에 대한 지침에 대해서는 [LUIS 애플리케이션 만들기 및 엔드포인트 키 가져오기](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)를 참조하세요.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer 초기화

이제 `IntentRecognizer`를 만들어 보겠습니다. 음성 구성 바로 아래에 이 코드를 삽입합니다.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel 및 의도 추가

이제 `LanguageUnderstandingModel`을 의도 인식기와 연결하고 인식되길 원하는 의도를 추가해야 합니다.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>의도 인식

`IntentRecognizer` 개체에서 `recognizeOnceAsync()` 메서드를 호출합니다. 이 메서드를 사용하면 Speech Service에서 사용자가 인식을 위해 단일 구를 보내고, 구가 식별되고 나면 음성 인식이 중지됨을 알 수 있습니다.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>인식 결과(또는 오류) 표시

Speech Service에서 인식 결과가 반환되면 이에 따라 작업을 수행합니다. 작업을 간단하게 유지하고 결과를 콘솔에 출력합니다.

`recognizeOnceAsync()`에 대한 호출 아래에 다음 코드를 추가합니다. [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>리소스 해제

사용이 완료되었으면 음성 리소스를 해제하는 것이 중요합니다. try/catch 블록의 끝에 다음 코드를 삽입합니다. [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>코드 확인

이 시점에서 코드는 다음과 같습니다.  
(이 버전에 몇 가지 주석을 추가함) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

F11 키를 누르거나 **실행** > **디버그**를 선택합니다.
마이크에서 나오는 다음 15초 음성 입력이 인식되어 콘솔 창에 기록됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]