---
title: Lip 동기화에 대 한 얼굴 포즈 이벤트를 가져오는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK는 음성 합성에서 viseme 이벤트를 지원 합니다 .이 이벤트는 특정 음소를 생성할 때 lip, jaw 및 메롱와 같이 관찰 된 음성의 주요 포즈를 나타내는 데 사용 됩니다.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643898"
---
# <a name="get-facial-pose-events"></a>얼굴 포즈 이벤트 가져오기

> [!NOTE]
> Viseme는 지금은 음성에 대해서만 작동 `en-US-AriaNeural` 합니다.

Viseme는 음성 언어의 음소에 대 한 시각적 설명입니다.
단어를 말하면 얼굴 및 입의 위치를 정의 합니다.
각 viseme는 특정 음소 집합에 대 한 주요 얼굴 포즈를 나타냅니다.
Visemes와 음소 간에는 일대일 대응이 없습니다.
여러 가지 음소가 생성 될 때 (예: 및)에서 동일 하 게 보이는 것 처럼 종종 몇 가지 음소는 단일 viseme에 해당 합니다 `s` `z` .
[Visemes와 음소 간의 매핑 표](#map-phonemes-to-visemes)를 참조 하세요.

Visemes를 사용 하 여 보다 자연스럽 고 지능적인 뉴스 브로드캐스트 길잡이, 더 많은 대화형 게임 및 만화 문자 및 보다 직관적인 언어 교육 비디오를 만들 수 있습니다. 청각 장애가 있는 사용자는 시각적 효과를 시각적으로 선택 하 고 "visemes" 음성 콘텐츠를 선택 하 여 애니메이션 효과를 주는 얼굴을 볼 수도 있습니다.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Speech SDK를 사용 하 여 viseme 이벤트 가져오기

Viseme 이벤트를 만들기 위해 입력 텍스트를 일련의 음소 시퀀스와 해당 하는 viseme 시퀀스로 변환 합니다. 음성 오디오에서 각 viseme의 시작 시간을 예측 합니다. Viseme 이벤트는 Viseme가 표시 되는 오디오에 대 한 오프셋을 포함 하는 Viseme Id의 시퀀스를 포함 합니다. 이러한 이벤트는 입력 텍스트를 말하는 사람을 시뮬레이트하는 입 애니메이션을 만들 수 있습니다.

| 매개 변수 | 설명 |
|-----------|-------------|
| Viseme ID | Viseme를 지정 하는 정수입니다. 영어 (미국)에서는 음소의 특정 집합에 대 한 입 셰이프를 나타내는 22 가지 다른 visemes 제공 합니다. [Viseme ID와 음소 사이의 매핑 표](#map-phonemes-to-visemes)를 참조 하세요.  |
| 오디오 오프셋 | 각 viseme의 시작 시간 (틱 (100 나노초))입니다. |

Viseme 이벤트를 가져오려면 `VisemeReceived` SPEECH SDK의 이벤트를 구독 합니다.
다음 코드 조각은 viseme 이벤트를 구독 하는 방법을 보여 줍니다.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Visemes에 음소 매핑

Visemes는 언어에 따라 달라 집니다. 각 언어에는 특정 음소에 해당 하는 visemes 집합이 있습니다. 다음 표에서는 영어 (미국)에 대 한 국제 발음 (IPA) 음소과 viseme Id 간의 상관 관계를 보여 줍니다.

| IPA | 예제 | Viseme ID |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **te** | 4 |
| ɛ | **e** 매우 | 4 |
|→  |   **c)**        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** ce           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **go**           |1|
|ɪɹ |   **귀** s          |[6, 13]|
|ɛɹ |   **공기** 평면      |[4, 13]|
|ʊɹ |   c **(** e)          |[4, 13]|
|aɪ(ə)ɹ |   **Ire** 지역   |[11, 13]|
|aʊ(ə)ɹ |   **시간** s     |[9, 13]|
|ɔɹ |   **또는** a)        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **귀** 번째         |[5, 13]|
|ɚ  |   모든 **er** gy       |[1, 13]|
|w  |   **w** i, s **ue**   |7|
|j  |   **y** 인, f **e** w     |6|
|p  |   **p** 전 세계           |21|
|b  |   **big**           |21|
|t  |   **t** alk          |19|
|일  |   **d**           |19|
|k  |   **c** 세계           |20|
|g  |   **g** o            |20|
|분  |   **m**, m, s **m**    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** o)          |18|
|v  |   **v** alue         |18|
|θ  |   **th**          |17|
|led  |   **번째** en          |17|
|초  |   **s** it           |15|
|z  |   **z** ap           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** elp          |12|
|tʃ |   **ch** in          |16|
|dʒ |   **j** oy           |16|
|l  |   **l** id, g **l** ad     |14|
|ɹ  |   **r** ed, b **r** ing    |13|


## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
