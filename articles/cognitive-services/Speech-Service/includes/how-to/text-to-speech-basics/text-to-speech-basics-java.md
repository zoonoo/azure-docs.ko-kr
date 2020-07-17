---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: e0c2554162d54af6c0a483e26f708838c3045a03
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986012"
---
## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java 런타임 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>종속성 가져오기

이 문서의 예제를 실행하려면 스크립트 맨 위에 다음 import 문을 포함합니다.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)를 만들어야 합니다. 이 클래스에는 키 및 연결된 지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

[`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)를 초기화하는 다음과 같은 방법이 있습니다.

* 구독 사용: 키 및 연결된 영역을 전달합니다.
* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

이 예제에서는 구독 키와 지역을 사용하여 [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)를 만듭니다. 지역 식별자를 찾으려면 [지역 지원](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) 페이지를 참조하세요. 또한 이 문서의 나머지 부분에 사용할 몇 가지 기본 상용구 코드를 만들 수 있습니다. 이 문서의 나머지 부분에서는 사용자 지정을 위해 수정합니다.

```java
public class Program 
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>파일로 음성 합성

다음으로, 스피커, 파일 또는 기타 출력 스트림에 대한 텍스트 음성 변환 및 출력을 실행하는 [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) 개체를 만듭니다. [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable)는 이전 단계에서 만든 [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) 개체와 출력 결과를 처리하는 방법을 지정하는 [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) 개체를 매개 변수로 수락합니다.

시작하려면 `AudioConfig`를 만들어 `fromWavFileOutput()` 정적 함수를 사용하여 출력을 `.wav` 파일에 자동으로 작성합니다.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

다음으로, `speechConfig` 개체와 `audioConfig` 개체를 매개 변수로 전달하는 `SpeechSynthesizer`를 인스턴스화합니다. 그러면 음성 합성 및 파일 쓰기를 실행하는 것은 텍스트 문자열을 사용하여 `SpeakText()`를 실행하는 것만큼 간단합니다.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

프로그램을 실행하면 합성된 `.wav` 파일이 지정된 위치에 기록됩니다. 이는 가장 기본적인 사용법의 좋은 예이지만, 다음에는 사용자 지정 시나리오를 사용하기 위해 출력을 사용자 지정하고 출력 응답을 메모리 내 스트림으로 처리하는 방법을 살펴보겠습니다.

## <a name="synthesize-to-speaker-output"></a>스피커 출력으로 합성

경우에 따라 합성된 음성을 스피커로 직접 출력할 수도 있습니다. 이렇게 하려면 `fromDefaultSpeakerOutput()` 정적 함수를 사용하여 `AudioConfig`를 인스턴스화합니다. 현재 활성 출력 디바이스로 출력됩니다.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>결과를 메모리 내 스트림으로 가져오기

음성 애플리케이션 개발의 많은 시나리오의 경우 오디오 테이터는 파일에 직접 쓰기보다는 메모리 내 스트림으로 필요할 가능성이 높습니다. 이를 통해 다음과 같은 사용자 지정 동작을 빌드할 수 있습니다.

* 결과 바이트 배열을 사용자 지정 다운스트림 서비스의 검색 가능 스트림으로 추상화합니다.
* 결과를 다른 API 또는 서비스와 통합합니다.
* 오디오 데이터를 수정하고 사용자 지정 `.wav` 헤더 등을 작성합니다.

이전 예제에서 이 변경을 수행하는 것이 간단합니다. 먼저 제어를 향상하기 위해 이 시점부터 출력 동작을 수동으로 관리하므로 `AudioConfig` 블록을 제거합니다. 그런 다음, `SpeechSynthesizer` 생성자의 `AudioConfig`에 대해 `null`을 전달합니다. 

> [!NOTE]
> 위의 스피커 출력 예제와 같이 `AudioConfig`에 `null`을 전달하지 않으면 현재 활성 출력 디바이스에서 기본적으로 오디오가 재생되지 않습니다.

이번에는 결과를 [`SpeechSynthesisResult`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-java-stable) 변수에 저장합니다. `SpeechSynthesisResult.getAudioData()` 함수는 출력 데이터의 `byte []`를 반환합니다. 이 `byte []`를 수동으로 사용하거나 [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) 클래스를 사용하여 메모리 내 스트림을 관리할 수 있습니다. 이 예제에서는 `AudioDataStream.fromResult()` 정적 함수를 사용하여 결과에서 스트림을 가져옵니다.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    
    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

여기에서 결과 `stream` 개체를 사용하여 사용자 지정 동작을 구현할 수 있습니다.

## <a name="customize-audio-format"></a>오디오 형식 사용자 지정

다음 섹션에서는 다음을 포함하여 오디오 출력 특성을 사용자 지정하는 방법을 보여줍니다.

* 오디오 파일 형식
* 샘플 요금
* 비트 수준

오디오 형식을 변경하려면 `SpeechConfig` 개체에서 `setSpeechSynthesisOutputFormat()` 함수를 사용합니다. 이 함수에는 출력 형식을 선택하는 데 사용하는 [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable) 형식의 `enum`이 필요합니다. 사용 가능한 [오디오 형식 목록](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet)에 대해서는 참조 문서를 참조하세요.

요구 사항에 따라 다른 파일 형식에 대한 다양한 옵션이 있습니다. 정의에 따라 `Raw24Khz16BitMonoPcm`과 같은 원시 형식에는 오디오 헤더가 포함되지 않습니다. 다운스트림 구현에서 원시 비트스트림을 디코딩할 수 있거나 비트 심도, 샘플 속도, 채널 수 등에 따라 헤더를 수동으로 빌드할 계획인 경우에만 원시 형식을 사용합니다.

이 예제에서는 `SpeechConfig` 개체에서 `SpeechSynthesisOutputFormat`을 설정하여 고화질 RIFF 형식 `Riff24Khz16BitMonoPcm`을 지정합니다. 이전 섹션의 예제와 마찬가지로 [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable)을 사용하여 결과의 메모리 내 스트림을 가져온 다음, 파일에 씁니다.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

프로그램을 다시 실행하면 지정된 경로에 `.wav` 파일이 기록됩니다.

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML을 사용하여 음성 특성 사용자 지정

SSML(Speech Synthesis Markup Language)을 사용하면 XML 스키마에서 요청을 제출하여 피치, 발음, 말하는 속도, 볼륨 등 텍스트 음성 변환 출력을 세밀하게 조정할 수 있습니다. 이 섹션에서는 몇 가지 실제 사용 예를 보여 주지만, 보다 자세한 가이드는 [SSML 사용 방법 문서](../../../speech-synthesis-markup.md)를 참조하세요.

사용자 지정을 위한 SSML을 사용하려면 음성을 전환하는 간단한 변경 작업을 수행합니다.
먼저 루트 프로젝트 디렉터리에서 SSML 구성에 대한 새 XML 파일을 만듭니다(이 예제에서는 `ssml.xml`). 루트 요소는 항상 `<speak>`이며 텍스트를 `<voice>` 요소에 래핑하면 `name` 매개 변수를 사용하여 음성을 변경할 수 있습니다. 이 예제에서는 음성을 남성 영어(영국) 음성으로 변경합니다. 이 음성은 **인공신경망** 음성과는 가격 책정과 가용성이 다른 **표준** 음성입니다. 지원되는 **표준** 음성의 [전체 목록](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices)을 참조하세요.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

그런 다음, XML 파일을 참조하도록 음성 합성 요청을 변경해야 합니다. 요청은 거의 같지만 `SpeakText()` 함수를 사용하는 대신 `SpeakSsml()`을 사용합니다. 이 함수에는 XML 문자열이 필요하므로, 먼저 XML 파일을 로드하고 문자열로 반환하는 함수를 만듭니다.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

여기에서 결과 개체는 이전 예제와 정확히 동일합니다.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

출력은 작동하지만 보다 자연스럽게 들리도록 몇 가지 간단한 추가 변경 사항이 있습니다. 전반적인 말하는 속도가 너무 빠르므로 `<prosody>` 태그를 추가하고 속도를 기본 속도의 **90%** 로 줄입니다. 또한 문장의 쉼표 뒤의 일시 중지는 약간 짧고 부자연스러운 소리입니다. 이 문제를 해결하려면 `<break>` 태그를 추가하여 음성을 지연하고 시간 매개 변수를 **200ms**로 설정합니다. 합성을 다시 실행하여 이러한 사용자 지정이 출력에 미치는 영향을 확인합니다.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>인공신경망 음성

인공신경망 음성은 심층 신경망에서 구동하는 음성 합성 알고리즘입니다. 인공신경망 음성을 사용하는 경우 합성된 음성은 인간 음성 녹음과 거의 구분되지 않습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 사용자가 AI 시스템과 상호 작용할 때 인공신경망 음성은 수신 피로도를 현저히 줄일 수 있습니다.

인공신경망 음성으로 전환하려면 `name`을 [인공신경망 음성 옵션](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) 중 하나로 변경합니다. 그런 다음, `mstts`에 대한 XML 네임스페이스를 추가하고 `<mstts:express-as>` 태그에 텍스트를 래핑합니다. `style` 매개 변수를 사용하여 말하기 스타일을 사용자 지정합니다. 이 예제에서는 `cheerful`를 사용하지만 말하기 스타일의 차이를 확인하려면 `customerservice` 또는 `chat`으로 설정합니다.

> [!IMPORTANT]
> 인공신경망 음성은 *미국 동부*, *동남 아시아* 및 *서유럽* 지역에서 만든 음성 리소스에 대해서**만** 지원됩니다.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
