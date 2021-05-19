---
title: 립싱크를 위한 얼굴 포즈 이벤트를 가져오는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK는 특정 음소를 생성할 때 입술, 턱 및 혀의 위치와 같은 관찰된 음성의 주요 포즈를 나타내는 데 사용되는 음성 합성의 viseme 이벤트를 지원합니다.
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
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643898"
---
# <a name="get-facial-pose-events"></a>얼굴 포즈 이벤트 가져오기

> [!NOTE]
> Viseme은 현재 `en-US-AriaNeural` 음성에서만 작동합니다.

viseme은 음성 언어로 된 음소의 시각적 설명입니다.
단어를 말할 때 얼굴 및 입의 위치를 정의합니다.
각 viseme은 특정 음소 세트에 대한 주요 얼굴 포즈를 묘사합니다.
viseme과 음소 간에는 일대일 대응이 없습니다.
`s` 및 `z`와 같이 여러 가지 음소가 생성될 때 얼굴에서 동일하게 보이기 때문에 종종 몇 가지 음소는 단일 viseme에 해당합니다.
[viseme과 음소 간의 매핑 테이블](#map-phonemes-to-visemes)을 참조하세요.

viseme을 사용하면 보다 자연스럽고 지능적인 뉴스 브로드캐스트 도우미, 보다 대화형 게임 및 만화 캐릭터, 보다 직관적인 언어 교육 비디오를 만들 수 있습니다. 청각 장애가 있는 사용자는 시각적으로 소리를 포착할 수 있고 애니메이션 얼굴의 viseme을 보여주는 "입술 읽기" 음성 콘텐츠를 선택할 수도 있습니다.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Speech SDK를 사용하여 viseme 이벤트 가져오기

viseme 이벤트를 만들기 위해 입력 텍스트를 음소 세트 시퀀스와 해당 viseme 시퀀스로 변환합니다. 음성 오디오에서 각 viseme의 시작 시간을 추정합니다. Viseme 이벤트에는 viseme이 표시되는 오디오에 오프셋이 있는 viseme ID의 시퀸스가 포함됩니다. 이러한 이벤트는 입력 텍스트를 말하는 사람을 시뮬레이션하는 입 애니메이션을 구동할 수 있습니다.

| 매개 변수 | Description |
|-----------|-------------|
| Viseme ID | viseme을 지정하는 정수입니다. 영어(미국)에서는 특정 음소 세트의 입 모양을 묘사하기 위해 22개의 다른 viseme을 제공합니다. [Viseme ID와 음소 간의 매핑 테이블](#map-phonemes-to-visemes)을 참조하세요.  |
| 오디오 오프셋 | 각 viseme의 시작 시간(틱 단위(100나노초)). |

viseme 이벤트를 가져오려면 Speech SDK에서 `VisemeReceived` 이벤트를 구독합니다.
다음 코드 조각은 viseme 이벤트를 구독하는 방법을 보여줍니다.

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

## <a name="map-phonemes-to-visemes"></a>viseme에 음소 매핑

Viseme은 언어에 따라 달라집니다. 각 언어에는 특정 음소에 해당하는 viseme 세트가 있습니다. 다음 표에서는 IPA(International Phonetic Alphabet) 음소와 영어(미국)의 viseme ID 간의 상관 관계를 보여줍니다.

| IPA | 예제 | Viseme ID |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **a** te | 4 |
| ɛ | **e** very | 4 |
|æ  |   **a** ctive        |1|
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
|ə  |   **a** go           |1|
|ɪɹ |   **ear** s          |[6, 13]|
|ɛɹ |   **air** plane      |[4, 13]|
|ʊɹ |   c **ur** e          |[4, 13]|
|aɪ(ə)ɹ |   **Ire** land   |[11, 13]|
|aʊ(ə)ɹ |   **hour** s     |[9, 13]|
|ɔɹ |   **or** ange        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **ear** th         |[5, 13]|
|ɚ  |   all **er** gy       |[1, 13]|
|w  |   **w** ith, s **ue** de   |7|
|j  |   **y** ard, f **e** w     |6|
|p  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alk          |19|
|일  |   **d** ig           |19|
|k  |   **c** ut           |20|
|g  |   **g** o            |20|
|분  |   **m** at, s **m** ash    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th** in          |17|
|ð  |   **th** en          |17|
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
