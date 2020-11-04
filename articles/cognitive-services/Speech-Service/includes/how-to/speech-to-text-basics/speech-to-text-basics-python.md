---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: e9560d9af095193451fd4276d830a06d03f2cc6c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135753"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 글로 바꾸는 기능입니다(종종 음성 텍스트 변환이라고도 함). 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성을 텍스트로 변환하는 방법을 알아봅니다.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [Python 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 다음과 같이 가정합니다.

* 여러분이 Azure 계정 및 음성 서비스 구독을 갖고 있습니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-and-import-the-speech-sdk"></a>음성 SDK 설치 및 가져오기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다.

```Python
pip install azure-cognitiveservices-speech
```

macOS를 사용 중인데 설치 문제가 발생하는 경우 다음 명령을 먼저 실행해야 할 수도 있습니다.

```Python
python3 -m pip install --upgrade pip
```

Speech SDK를 설치한 후 Python 프로젝트로 가져옵니다.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)를 만들어야 합니다. 이 클래스에는 키 및 연결된 지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다. 키와 지역을 사용하여 [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)를 만듭니다. 지역 식별자를 찾으려면 [지역 지원](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) 페이지를 참조하세요.

```Python
speech_key, service_region = "<paste-your-subscription-key>", "<paste-your-region>"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

[`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)를 초기화할 수 있는 몇 가지 다른 방법이 있습니다.

* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

## <a name="recognize-from-microphone"></a>마이크에서 인식

디바이스 마이크를 사용하여 음성을 인식하려면 `AudioConfig`를 전달하지 않고 `SpeechRecognizer`를 만들고 `speech_config`를 전달하면 됩니다.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)

print("Speak into your microphone.")
result = speech_recognizer.recognize_once_async().get()
print(result.text)
```

*특정* 오디오 입력 디바이스를 사용하려면 `AudioConfig`에서 디바이스 ID를 지정하고 이를 `SpeechRecognizer` 생성자의 `audio_config` 매개 변수에 전달해야 합니다. 오디오 입력 디바이스의 [디바이스 ID를 가져오는 방법](../../../how-to-select-audio-input-devices.md)을 알아봅니다.

## <a name="recognize-from-file"></a>파일에서 인식

마이크 대신 오디오 파일에서 음성을 인식하려면 [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python&preserve-view=true)을 만들고 `filename` 매개 변수를 사용합니다.

```Python
audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

result = speech_recognizer.recognize_once_async().get()
print(result.text)
```

## <a name="recognize-speech"></a>음성 인식

Python용 음성 SDK의 [인식기 클래스](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python&preserve-view=true)는 음성 인식에 사용할 수 있는 몇 가지 메서드를 공개합니다.

### <a name="single-shot-recognition"></a>단일 샷 인식

단일 샷 인식은 단일 발화를 비동기적으로 인식합니다. 단일 발화의 끝은 끝에서 무음을 수신하거나 최대 15초의 오디오가 처리될 때까지 대기하여 결정됩니다. 다음은 [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture)를 사용하는 비동기 단일 샷 인식의 예입니다.

```Python
result = speech_recognizer.recognize_once_async().get()
```

결과를 반복하는 코드를 작성해야 합니다. 이 샘플은 [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python)을 평가합니다.

* 인식 결과 `speechsdk.ResultReason.RecognizedSpeech`를 출력합니다.
* 일치하는 인식이 없는 경우 사용자에게 `speechsdk.ResultReason.NoMatch `를 알립니다.
* 오류가 발생하는 경우 오류 메시지 `speechsdk.ResultReason.Canceled`를 출력합니다.

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>연속 인식

연속 인식은 단일 샷 인식보다 약간 더 복잡합니다. 사용자가 `EventSignal`에 연결하여 인식 결과를 가져와야 하며, 인식을 중지하려면 [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) 또는 [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)을 호출해야 합니다. 다음은 오디오 입력 파일에서 연속 인식이 수행되는 방식을 보여주는 예입니다.

먼저 입력을 정의하고 [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python&preserve-view=true)를 초기화하겠습니다.

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

다음으로, 음성 인식 상태를 관리하는 변수를 만들어 보겠습니다. 시작하기 위해, 이 변수를 `False`로 설정합니다. 이렇게 하면 인식을 시작할 때 인식이 완료되지 않았다고 안전하게 가정할 수 있습니다.

```Python
done = False
```

이제 `evt`가 수신되면 연속 인식을 중지하는 콜백을 만들어 보겠습니다. 몇 가지 주의할 사항이 있습니다.

* `evt`가 수신되면 `evt` 메시지가 출력됩니다.
* `evt`가 수신된 후에는 인식을 중지하기 위해 [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--)가 호출됩니다.
* 인식 상태가 `True`로 변경됩니다.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

다음 코드 샘플은 [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--)에서 보낸 이벤트에 콜백을 연결하는 방법을 보여줍니다.

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): 중간 인식 결과가 포함된 이벤트에 대한 신호입니다.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): 최종 인식 결과가 포함된 이벤트에 대한 신호입니다(성공적인 인식 시도를 나타냄).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): 인식 세션(작업)의 시작을 나타내는 이벤트에 대한 신호입니다.
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): 인식 세션(작업)의 끝을 나타내는 이벤트에 대한 신호입니다.
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): 취소된 인식 결과가 포함된 이벤트에 대한 신호입니다(직접 취소 요청이나 전송 또는 프로토콜 오류로 인해 취소된 인식 시도를 나타냄).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

모든 설정이 끝났으면 [start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped)를 호출할 수 있습니다.

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>받아쓰기 모드

연속 인식을 사용하면 해당하는 "받아쓰기 사용" 함수를 사용하여 받아쓰기 처리를 사용하도록 설정할 수 있습니다. 이 모드에서는 음성 구성 인스턴스가 문장 부호와 같은 문장 구조의 단어 설명을 해석합니다. 예를 들어 "도시에 살고 계신가요 물음표"라는 발화는 "도시에 살고 계신가요?"라는 텍스트로 해석됩니다.

받아쓰기 모드를 사용하도록 설정하려면 [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python&preserve-view=true)에서 [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) 메서드를 사용합니다.

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>소스 언어 변경

음성 인식에 대한 일반적인 작업 중 하나는 입력(또는 소스) 언어를 지정하는 것입니다. 입력 언어를 독일어로 변경하는 방법을 살펴보겠습니다. 코드에서 SpeechConfig를 찾아서 바로 아래에 다음 줄을 추가합니다.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language)는 문자열을 인수로 사용하는 매개 변수입니다. 지원되는 [로캘/언어](../../../language-support.md) 목록에 원하는 값을 입력할 수 있습니다.

## <a name="improve-recognition-accuracy"></a>인식 정확도 향상

음성 SDK를 사용하여 인식 정확도를 향상하는 몇 가지 방법이 있습니다. 구 목록을 살펴보겠습니다. 구 목록은 오디오 데이터에서 사람의 이름이나 특정 위치처럼 알려진 문구를 식별하는 데 사용됩니다. 단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 중에 전체 구와 정확히 일치하는 항목이 오디오에 포함된 경우 구 목록의 항목이 사용됩니다. 구와 정확히 일치하는 항목을 찾을 수 없는 경우 인식이 지원되지 않습니다.

> [!IMPORTANT]
> 구 목록 기능은 영어로만 제공됩니다.

구 목록을 사용하려면 [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python&preserve-view=true) 개체를 만든 다음, [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-)를 사용하여 특정 단어와 구를 추가합니다.

[`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python&preserve-view=true)를 변경하면 그 다음 인식부터 또는 음성 서비스에 다시 연결한 후에 변경 내용이 적용됩니다.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

구 목록을 지워야 하는 경우: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>인식 정확도를 높이는 다른 옵션

구 목록은 인식 정확도를 높이는 여러 옵션 중 하나일 뿐입니다. 다음도 가능합니다. 

* [Custom Speech를 사용하여 정확도 향상](../../../how-to-custom-speech.md)
* [테넌트 모델을 사용하여 정확도 향상](../../../tutorial-tenant-model.md)
