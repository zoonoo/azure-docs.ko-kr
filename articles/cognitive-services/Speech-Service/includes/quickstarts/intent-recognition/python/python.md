---
title: '빠른 시작: 음성, 의도 및 엔터티 인식, Python - Speech Service'
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
ms.openlocfilehash: 9a4c7f24a2e28743679e312e3dce0bc605db6749
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816013"
---
## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
>
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [LUIS(Language Understanding) 애플리케이션 만들기 및 엔드포인트 키 가져오기](../../../../quickstarts/create-luis.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>프로젝트 열기

Python 편집기에서 Quickstart.py를 엽니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`IntentRecognizer` 개체를 초기화하기 전에 LUIS 엔드포인트 키와 지역을 사용하는 구성을 만들어야 합니다. 다음으로 이 코드를 삽입합니다.

이 샘플은 LUIS 키 및 지역을 사용하여 `SpeechConfig` 개체를 생성합니다. 사용 가능한 메서드의 전체 목록은 [SpeechConfig 클래스](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)를 참조하세요.
Speech SDK는 기본적으로 언어에 en-us를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../../how-to-specify-source-language.md)을 참조하세요.

> [!NOTE]
> 음성 의도 변환 인식에는 엔드포인트 키만 유효하므로 시작 키 또는 저작 키가 아닌 LUIS 엔드포인트 키를 사용하는 것이 중요합니다. 올바른 키를 가져오는 방법에 대한 지침에 대해서는 [LUIS 애플리케이션 만들기 및 엔드포인트 키 가져오기](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)를 참조하세요.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer 초기화

이제 `IntentRecognizer`를 만들어 보겠습니다. 음성 구성 바로 아래에 이 코드를 삽입합니다.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel 및 의도 추가

이제 `LanguageUnderstandingModel`을 의도 인식기와 연결하고 인식되길 원하는 의도를 추가해야 합니다.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>의도 인식

`IntentRecognizer` 개체에서 `recognize_once()` 메서드를 호출합니다. 이 메서드를 사용하면 Speech Service에서 사용자가 인식을 위해 단일 구를 보내고, 구가 식별되고 나면 음성 인식이 중지됨을 알 수 있습니다.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>인식 결과(또는 오류) 표시

Speech Service에서 인식 결과가 반환되면 이에 따라 작업을 수행합니다. 작업을 간단하게 유지하고 결과를 콘솔에 출력합니다.

using 문 내의 `recognize_once()` 호출 아래에 다음 코드를 추가합니다. [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>코드 확인

이 시점에서 코드는 다음과 같습니다.  
(이 버전에 몇 가지 주석을 추가함) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

콘솔 또는 IDE에서 샘플을 실행합니다.

    ````
    python quickstart.py
    ````

마이크에서 나오는 다음 15초 음성 입력이 인식되어 콘솔 창에 기록됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
