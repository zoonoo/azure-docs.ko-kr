---
title: 음성 합성 마크업 언어(SSML) - 음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech Synthesis Markup Language를 사용하여 텍스트 음성 변환의 발음 및 운율을 제어합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 7d5dd79399b15ade90173a55aeb71dacbc61fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80365815"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>음성 합성 마크 업 언어 (SSML)와 합성을 개선

SSML(음성 합성 태그 언어)은 XML 기반 태그 언어로, 개발자는 텍스트 음성 변환 서비스를 사용하여 입력 텍스트를 합성 된 음성으로 변환하는 방법을 지정할 수 있습니다. 일반 텍스트에 비해 SSML을 사용하면 개발자가 피치, 발음, 말하기 속도, 볼륨 등을 미세 조정할 수 있습니다. 마침표 후에 일시 중지하거나 문장이 물음표로 끝날 때 올바른 억양 사용과 같은 일반 문장 부호는 자동으로 처리됩니다.

SSML의 음성 서비스 구현은 월드 와이드 웹 컨소시엄의 [음성 합성 마크업 언어 버전 1.0을](https://www.w3.org/TR/speech-synthesis)기반으로 합니다.

> [!IMPORTANT]
> 중국어, 일본어 및 한국어 문자는 청구에 대해 두 문자로 계산됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

## <a name="standard-neural-and-custom-voices"></a>표준, 신경 및 사용자 지정 음성

표준 및 신경 음성 중에서 선택하거나 제품 또는 브랜드에 고유한 사용자 지정 음성을 만들 수 있습니다. 75개 이상의 표준 음성은 45개 이상의 언어와 로캘에서 사용할 수 있으며, 5개의 신경 음성은 4개 언어와 로캘에서 사용할 수 있습니다. 지원되는 언어, 로캘 및 음성(인공신경망 및 표준)의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

표준 음성, 신경 및 사용자 지정 음성에 대한 자세한 내용은 [텍스트 음성 변환 개요를](text-to-speech.md)참조하십시오.

## <a name="special-characters"></a>특수 문자

SSML을 사용하는 동안 인용 부호, 아포스트로피 및 대괄호와 같은 특수 문자는 이스케이프되어야 합니다. 자세한 내용은 [확장 가능한 마크업 언어(XML) 1.0: 부록 D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>지원되는 SSML 요소

각 SSML 문서는 SSML 요소(또는 태그)로 만들어집니다. 이러한 요소는 피치, 프로소디, 볼륨 등을 조정하는 데 사용됩니다. 다음 섹션에서는 각 요소가 사용되는 방법과 요소가 필요하거나 선택 사항인 시기를 자세히 설명합니다.  

> [!IMPORTANT]
> 특성 값 주위에 큰따옴표를 사용하는 것을 잊지 마십시오. 잘 형성된 유효한 XML에 대한 표준에는 특성 값을 이중 따옴표로 묶어야 합니다. 예를 들어 `<prosody volume="90">` 잘 형성된 유효한 요소이지만 `<prosody volume=90>` 그렇지 않습니다. SSML은 따옴표에 없는 특성 값을 인식하지 못할 수 있습니다.

## <a name="create-an-ssml-document"></a>SSML 문서 만들기

`speak`은 루트 요소이며 모든 SSML 문서에 **필요합니다.** 이 `speak` 요소에는 버전, 언어 및 태그 어휘 정의와 같은 중요한 정보가 포함되어 있습니다.

**구문**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `version` | 문서 태그를 해석하는 데 사용되는 SSML 사양의 버전을 나타냅니다. 현재 버전은 1.0입니다. | 필수 |
| `xml:lang` | 루트 문서의 언어를 지정합니다. 이 값에는 소문자, 두 글자 언어 코드(예: `en`또는 언어 코드 및 대문자 `en-US`국가/지역)가 포함될 수 있습니다. | 필수 |
| `xmlns` | Uri를 SSML 문서의 태그 어휘(요소 유형 및 특성 이름)를 정의하는 문서에 지정합니다. 현재 URI는 http://www.w3.org/2001/10/synthesis. | 필수 |

## <a name="choose-a-voice-for-text-to-speech"></a>텍스트 음성 변환을 위한 음성 선택

요소가 `voice` 필요합니다. 텍스트 음성 변환에 사용되는 음성을 지정하는 데 사용됩니다.

**구문**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `name` | 텍스트 음성 변환에 사용되는 음성을 식별합니다. 지원되는 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하십시오. | 필수 |

**예제**

> [!NOTE]
> 이 예제에서는 `en-US-AriaRUS` 음성을 사용합니다. 지원되는 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하십시오.

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>여러 음성 사용

`speak` 요소 내에서 텍스트 음성 변환을 위해 여러 음성을 지정할 수 있습니다. 이러한 음성은 다른 언어로 되어 있을 수 있습니다. 각 음성에 대해 텍스트는 요소로 `voice` 줄 바꿈되어야 합니다. 

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `name` | 텍스트 음성 변환에 사용되는 음성을 식별합니다. 지원되는 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하십시오. | 필수 |

> [!IMPORTANT]
> 여러 음성이 단어 경계 기능과 호환되지 않습니다. 여러 음성을 사용하려면 경계 기능을 사용하지 않도록 설정해야 합니다.

### <a name="disable-word-boundary"></a>단어 경계 사용 안 함

Speech SDK 언어에 따라 `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` `false` `SpeechConfig` 속성이 개체의 인스턴스로 설정됩니다.

# <a name="c"></a>[C #](#tab/csharp)

자세한 내용은 을 <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C + +](#tab/cpp)

자세한 내용은 을 <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

자세한 내용은 을 <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

자세한 내용은 을 <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

자세한 내용은 을 <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

자세한 내용은 을 <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

자세한 내용은 을 <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>참조하십시오.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>말하기 스타일 조정

> [!IMPORTANT]
> 말하기 스타일의 조정은 신경 음성에서만 작동합니다.

기본적으로 텍스트 음성 변환 서비스는 표준 음성 및 신경 음성 모두에 대해 중립적인 말하기 스타일을 사용하여 텍스트를 합성합니다. 신경 음성을 사용하여 말하기 스타일을 조정하여 `<mstts:express-as>` 요소로 명랑함, 공감 또는 감정을 표현할 수 있습니다. 이 요소는 음성 서비스에 고유한 선택적 요소입니다.

현재 말하기 스타일 조정은 다음과 같은 신경 음성에 대해 지원됩니다.
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`
* `pt-BR-FranciscaNeural`

변경 사항은 문장 수준에서 적용되며 스타일은 음성에 따라 다릅니다. 스타일이 지원되지 않으면 서비스는 기본 중립 말하기 스타일로 음성을 반환합니다.

**구문**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `style` | 말하기 스타일을 지정합니다. 현재 말하기 스타일은 음성에 따라 다릅니다. | 신경 음성에 대한 말하기 스타일을 조정하는 경우 필요합니다. 을 `mstts:express-as`사용하는 경우 스타일을 제공해야 합니다. 잘못된 값이 제공되면 이 요소는 무시됩니다. |

이 표를 사용하여 각 신경 음성에 대해 지원되는 말하기 스타일을 결정합니다.

| 음성 | Style | 설명 |
|-------|------|-------------|
| `en-US-AriaNeural` | `style="newscast"` | 뉴스 내레이션을 위한 공식적이고 전문적인 어조표현 |
| | `style="customerservice"` | 고객 지원을 위한 친절하고 유용한 어조 표현 |
| | `style="chat"` | 캐주얼하고 편안한 톤을 표현 |
| | `style="cheerful"` | 긍정적이고 행복한 톤을 표현합니다 |
| | `style="empathetic"` | 배려심과 이해감을 표현합니다 |
| `zh-CN-XiaoxiaoNeural` | `style="newscast"` | 뉴스 내레이션을 위한 공식적이고 전문적인 어조표현 |
| | `style="customerservice"` | 고객 지원을 위한 친절하고 유용한 어조 표현 |
| | `style="assistant"` | 디지털 어시스턴트를 위한 따뜻하고 편안한 톤 표현  |
| | `style="lyrical"` | 멜로디와 감성적인 방식으로 감정을 표현합니다. |
| `pt-BR-FranciscaNeural` | `style="cheerful"` | 긍정적이고 행복한 톤을 표현합니다 |

**예제**

이 SSML 코드 조각은 `<mstts:express-as>` 요소가 말하기 스타일을 로 변경하는 `cheerful`데 사용되는 방법을 보여 줍니다.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>휴식/일시 중지 추가 또는 제거

`break` 요소를 사용하여 단어 간에 일시 중지(또는 나누기)를 삽입하거나 텍스트 음성 변환 서비스에서 자동으로 추가되는 일시 중지를 방지합니다.

> [!NOTE]
> 이 요소를 사용하여 해당 단어 나 구에 대한 합성 된 음성이 부자연스럽게 들리면 단어 또는 구에 대한 TTS (텍스트 음성 변환)의 기본 동작을 재정의하십시오. 텍스트 `strength` `none` 음성 변환 서비스에 의해 자동으로 삽입되는 임시 중단을 방지하도록 설정합니다.

**구문**

```xml
<break strength="string" />
<break time="string" />
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `strength` | 다음 값 중 하나를 사용하여 일시 중지의 상대 기간을 지정합니다.<ul><li>none</li><li>x-약한</li><li>약한</li><li>중간(기본값)</li><li>강력</li><li>x-강한</li></ul> | Optional |
| `time` | 일시 중지의 절대 지속 시간을 초 또는 밀리초로 지정합니다. 유효한 값의 예는 `2s``500` | Optional |

| 강도 | 설명 |
|----------|-------------|
| 없음 또는 값이 제공되지 않은 경우 | 0 ms |
| x-약한 | 250 ms |
| 약한 | 500ms |
| 중간 | 750ms |
| 강력 | 1000 ms |
| x-강한 | 1250 ms |


**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>단락 및 문장 지정

`p`요소는 `s` 각각 단락과 문장을 나타내는 데 사용됩니다. 이러한 요소가 없는 경우 텍스트 음성 변환 서비스는 자동으로 SSML 문서의 구조를 결정합니다.

`p` 요소에는 텍스트와 다음 요소가 `audio`포함될 `break` `phoneme`수 `prosody` `say-as` `sub`있습니다. `mstts:express-as` `s`

`s` 요소에는 텍스트와 다음 요소가 `audio`포함될 `break` `phoneme`수 `prosody` `say-as`있습니다. `mstts:express-as` `sub`

**구문**

```XML
<p></p>
<s></s>
```

**예제**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>발음을 개선하기 위해 음소 사용

이 `ph` 요소는 SSML 문서에서 발음 발음에 사용됩니다. 요소에는 `ph` 텍스트만 포함될 수 있으며 다른 요소는 포함할 수 없습니다. 항상 대체로 사람이 읽을 수 있는 음성을 제공합니다.

음성 알파벳은 문자, 숫자 또는 문자로 구성된 휴대폰으로 구성되며 때로는 조합되어 있습니다. 각 전화 음성의 고유한 소리를 설명합니다. 이는 모든 문자가 여러 음성 사운드를 나타낼 수 있는 라틴 알파벳과는 대조적입니다. 단어 "사탕" 및 "중단", "c" 문자의 다양 한 발음 또는 고려 문자 조합과 다른 발음 "th" 단어 "thing" 및 "는" 합니다.

**구문**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `alphabet` | 특성에서 문자열의 발음을 합성할 때 사용할 발음 알파벳을 `ph` 지정합니다. 알파벳을 지정하는 문자열은 소문자로 지정해야 합니다. 다음은 지정할 수 있는 가능한 알파벳입니다.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">국제 음성 <span class="docon docon-navigate-external x-hidden-focus"></span> 알파벳</a></li><li>`sapi`&ndash; [음성 서비스 음성 알파벳](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; 범용 전화 세트</li></ul><br>알파벳은 요소의 `phoneme` 에만 적용됩니다. | Optional |
| `ph` | 요소에서 단어의 발음을 지정하는 전화가 포함된 문자열입니다. `phoneme` 지정된 문자열에 인식되지 않는 휴대폰이 포함된 경우 TTS(텍스트 음성 변환) 서비스는 전체 SSML 문서를 거부하고 문서에 지정된 음성 출력을 생성하지 않습니다. | 음등을 사용하는 경우 필요합니다. |

**예**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>사용자 지정 어휘를 사용하여 발음 을 향상시킵니다.

경우에 따라 TTS는 회사 또는 외국 이름과 같은 단어를 정확하게 발음할 수 없습니다. 개발자는 태그를 사용하여 `phoneme` SSML에서 이러한 엔터티의 읽기를 정의하거나 `sub` 태그를 사용하여 `lexicon` 사용자 지정 어휘 파일을 참조하여 여러 엔터티의 읽기를 정의할 수 있습니다.

**구문**

```XML
<lexicon uri="string"/>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `uri` | 외부 PLS 문서의 주소입니다. | 필수 사항입니다. |

**사용**

1단계: 사용자 지정 어휘 정의 

.xml 또는 .pls 파일로 저장된 사용자 지정 어휘 항목 목록별로 엔터티 읽기를 정의할 수 있습니다.

**예제**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

각 `lexeme` 요소는 어휘 항목입니다. `grapheme`의 지교를 설명하는 텍스트가 `lexeme`포함되어 있습니다. 판독 양식은 로 `alias`제공될 수 있습니다. 전화 문자열은 요소에 `phoneme` 제공 될 수 있습니다.

`lexicon` 요소에는 하나 `lexeme` 이상의 요소가 포함되어 있습니다. 각 `lexeme` 요소에는 하나 `grapheme` 이상의 요소와 `grapheme` `alais`하나 `phoneme` 이상의 " 및 요소가 포함됩니다. 요소에는 `grapheme` <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">맞춤법 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>을 설명하는 텍스트가 포함되어 있습니다. 요소는 `alias` 약어 또는 약어의 발음을 나타내는 데 사용됩니다. 요소는 `phoneme` 발음 하는 방법을 `lexeme` 설명 하는 텍스트를 제공 합니다.

사용자 지정 어휘 파일에 대한 자세한 내용은 W3C 웹 사이트에서 [PLS(발음 어휘 사양) 버전 1.0을](https://www.w3.org/TR/pronunciation-lexicon/) 참조하십시오.

2 단계 : 1 단계에서 온라인으로 만든 사용자 정의 어휘 파일을 업로드하면 어디서나 저장할 수 있으며 Microsoft Azure에 저장하는 것이 좋습니다(예: [Azure Blob Storage.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

3 단계: SSML에서 사용자 지정 어휘 파일 참조

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW"는 "그런데"로 읽혀질 것입니다. "베니니"는 제공된 IPA "bîîninji"와 함께 읽습니다.  

**제한 사항**
- 파일 크기: 사용자 정의 어휘 파일 크기 최대 제한은 100KB, 이 크기를 초과 하면 합성 요청 실패 합니다.
- 어휘 캐시 새로 고침: 사용자 지정 어휘는 처음 로드될 때 TTS 서비스에서 URI키로 캐시됩니다. 동일한 URI를 가진 어휘는 15분 이내에 다시 로드되지 않으므로 사용자 지정 어휘 변경이 적용되려면 최대 15분까지 기다려야 합니다.

**음성 서비스 음성 세트**

위의 샘플에서, 우리는 국제 음성 알파벳을 사용하고 있습니다, 또한 IPA 전화 세트로 알려진. 우리는 개발자가 IPA를 사용하는 것이 좋습니다, 그것은 국제 표준이기 때문에. IPA가 기억하기 쉽지 않다는 점을 고려하여 음성 서비스는 7개 언어(,`en-US` `fr-FR`" `de-DE` `es-ES` `ja-JP` `zh-CN`, 및)에 `zh-TW`대한 음성 집합을 정의합니다.

아래설명과 `sapi` 같이 사용자 지정 `alphabet` 어휘를 사용하여 속성의 골짜기로 사용할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

자세한 음성 서비스 음성 알파벳에 대한 자세한 내용은 [음성 서비스 음성 집합을](speech-ssml-phonetic-sets.md)참조하십시오.

## <a name="adjust-prosody"></a>프로소디 조정

요소는 `prosody` 텍스트 음성 변환출력의 피치, 등고선, 범위, 속도, 지속 시간 및 볼륨에 대한 변경 사항을 지정하는 데 사용됩니다. `prosody` 요소에는 텍스트와 다음 요소가 `audio`포함될 `break` `p`수 `phoneme` `prosody` `say-as`있습니다. `sub` `s`

prosodic 특성 값은 넓은 범위에 따라 다를 수 있으므로 음성 인식기는 할당된 값을 선택한 음성의 실제 prosodic 값이 무엇인지에 대한 제안으로 해석합니다. 텍스트 음성 변환 서비스는 지원되지 않는 값을 제한하거나 대체합니다. 지원되지 않는 값의 예로는 1MHz의 피치 또는 120의 볼륨이 있습니다.

**구문**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `pitch` | 텍스트의 기준선 피치를 나타냅니다. 피치를 다음과 같이 표현할 수 있습니다.<ul><li>"Hz"(Hertz) 다음에 숫자로 표현되는 절대 값입니다. 예를 들어, 600 Hz.</li><li>"+" 또는 "-" 앞에 오는 숫자로 표현되고 피치를 변경할 양을 지정하는 "Hz" 또는 "st"가 뒤에 오는 상대값입니다. 예: +80 Hz 또는 -2st. "st"는 표준 규음선에서 톤(반단계)의 절반인 반음인 변경 단위를 나타냅니다.</li><li>상수 값:<ul><li>x-낮음</li><li>low</li><li>중간</li><li>high</li><li>x-높음</li><li>default</li></ul></li></ul>. | Optional |
| `contour` | 윤곽선은 신경 음성에 대해 지원되지 않습니다. 등고선은 피치의 변화를 나타냅니다. 이러한 변경 내용은 음성 출력의 지정된 시간 위치에서 대상의 배열로 표시됩니다. 각 대상은 매개 변수 쌍 집합으로 정의됩니다. 예를 들어: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>각 매개변수 집합의 첫 번째 값은 피치 변경 위치를 텍스트 지속 시간의 백분율로 지정합니다. 두 번째 값은 피치에 대한 상대값 또는 열거값을 사용하여 피치를 올리거나 낮출 `pitch`양을 지정합니다(참조). | Optional |
| `range` | 텍스트의 피치 범위를 나타내는 값입니다. 을 설명하는 `range` `pitch`데 사용되는 것과 동일한 절대 값, 상대 값 또는 열거형 값을 사용하여 표현할 수 있습니다. | Optional |
| `rate` | 텍스트의 말하기 속도를 나타냅니다. 다음과 같이 `rate` 표현할 수 있습니다.<ul><li>기본값의 승수 역할을 하는 숫자로 표현되는 상대값입니다. 예를 들어 값이 *1이면* 속도는 변경되지 않습니다. 값이 *0.5이면* 비율이 절반으로 됩니다. *값이 3이면* 비율이 세 배로 됩니다.</li><li>상수 값:<ul><li>x-느림</li><li>slow</li><li>중간</li><li>빠른</li><li>x-빠른</li><li>default</li></ul></li></ul> | Optional |
| `duration` | TTS(음성 합성) 서비스가 텍스트를 초 또는 밀리초 단위로 읽는 동안 경과해야 하는 시간입니다. 예를 들어, *2s* 또는 *1800ms.* | Optional |
| `volume` | 말하기 음성의 볼륨 레벨을 나타냅니다. 볼륨을 다음과 같이 표현할 수 있습니다.<ul><li>0.0에서 100.0 범위의 숫자로 표현되는 절대 *quietest* 값입니다. *loudest* 예를 들어, 75. 기본값은 100.0입니다.</li><li>볼륨을 변경할 양을 지정하는 "+" 또는 "-" 앞에 오는 숫자로 표현되는 상대값입니다. 예를 들어 +10 또는 -5.5입니다.</li><li>상수 값:<ul><li>무음</li><li>x-소프트</li><li>부드러운</li><li>중간</li><li>시끄러운</li><li>x-큰 소리로</li><li>default</li></ul></li></ul> | Optional |

### <a name="change-speaking-rate"></a>말하기 속도 변경

말하기 속도는 단어 또는 문장 수준의 표준 음성에 적용 할 수 있습니다. 말하기 속도는 문장 수준에서 신경 음성에만 적용 할 수 있습니다 반면.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>볼륨 변경

볼륨 변경은 단어 또는 문장 수준의 표준 음성에 적용할 수 있습니다. 볼륨 변화는 문장 수준에서 신경 음성에만 적용 할 수 있지만.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>피치 변경

피치 변경은 단어 또는 문장 수준의 표준 음성에 적용 할 수 있습니다. 피치 변화는 문장 수준에서 신경 음성에만 적용 할 수 있지만.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>피치 곡선 변경

> [!IMPORTANT]
> 피치 윤곽 변경은 신경 음성으로 지원되지 않습니다.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>요소로 말하기

`say-as`는 요소 텍스트의 콘텐츠 유형(예: 숫자 또는 날짜)을 나타내는 선택적 요소입니다. 이렇게 하면 텍스트를 발음하는 방법에 대한 음성 합성 엔진에 대한 지침을 제공합니다.

**구문**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `interpret-as` | 요소 텍스트의 콘텐츠 유형을 나타냅니다. 형식 목록은 아래 표를 참조하십시오. | 필수 |
| `format` | 모호한 형식이 있을 수 있는 콘텐츠 형식에 대한 요소 텍스트의 정확한 서식에 대한 추가 정보를 제공합니다. SSML은 이를 사용하는 콘텐츠 형식에 대한 형식을 정의합니다(아래 표 참조). | Optional |
| `detail` | 말할 세부 수준을 나타냅니다. 예를 들어 이 특성은 음성 합성 엔진이 문장 부호를 발음해 달라는 요청을 할 수 있습니다. 에 대해 `detail`정의된 표준 값이 없습니다. | Optional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

다음은 `interpret-as` 및 `format` 특성에 대해 지원되는 콘텐츠 형식입니다. 날짜 `format` 및 시간으로 `interpret-as` 설정된 경우에만 특성을 포함합니다.

| 해석-로 | format | 해석 |
|--------------|--------|----------------|
| `address` | | 텍스트는 주소로 사용됩니다. 음성 합성 엔진은 다음을 발음합니다.<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />"나는 150 법원 북동쪽 레드몬드 워싱턴에있어." |
| `cardinal`, `number` | | 텍스트는 기본 번호로 사용됩니다. 음성 합성 엔진은 다음을 발음합니다.<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />"세 가지 대안이 있습니다." |
| `characters`, `spell-out` | | 텍스트는 개별 문자로 사용됩니다(철자). 음성 합성 엔진은 다음을 발음합니다.<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />"T E S T"로. |
| `date` | dmy, mdy, ymd, ydm, ym, 내, MD, dm, d, m, y | 텍스트는 날짜로 사용됩니다. 속성은 `format` 날짜의*형식(d=일, m=월 및 y=year)을*지정합니다. 음성 합성 엔진은 다음을 발음합니다.<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"오늘은 10월 이천열입니다." |
| `digits`, `number_digit` | | 텍스트는 개별 숫자의 시퀀스로 사용됩니다. 음성 합성 엔진은 다음을 발음합니다.<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9"로. |
| `fraction` | | 텍스트는 소수로 사용됩니다. 음성 합성 엔진은 다음을 발음합니다.<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"인치의 삼분의 일"로. |
| `ordinal` | | 텍스트는 서수 번호로 사용됩니다. 음성 합성 엔진은 다음을 발음합니다.<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"세 번째 옵션 선택"으로. |
| `telephone` | | 텍스트는 전화 번호로 사용됩니다. 특성에는 `format` 국가 코드를 나타내는 숫자가 포함될 수 있습니다. 예를 들어 미국의 경우 "1", 이탈리아의 경우 "39"입니다. 음성 합성 엔진은 이 정보를 사용하여 전화 번호의 발음을 안내할 수 있습니다. 전화 번호에는 국가 코드도 포함될 수 있으며, 이 경우 `format`의 국가 코드보다 우선합니다. 음성 합성 엔진은 다음을 발음합니다.<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />"내 번호는 지역 코드 8 8 여덟 다섯 다섯 다섯 하나 하나 두입니다." |
| `time` | hms12, hms24 | 텍스트는 시간으로 사용됩니다. 속성은 `format` 12시간 시계(hms12) 또는 24시간 클럭(hms24)을 사용하여 시간을 지정할지 여부를 지정합니다. 콜론을 사용하여 시간, 분 및 초를 나타내는 숫자를 구분합니다. 다음은 유효한 시간 예입니다: 12:35, 1:14:32, 08:15, 및 02:50:45. 음성 합성 엔진은 다음을 발음합니다.<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"기차는 네 A M에서 출발합니다." |

**사용**

요소에텍스트만 `say-as` 포함될 수 있습니다.

**예제**

음성 합성 엔진은 다음과 같은 예를 말한다 "당신의 첫 번째 요청은 10 월 19 일 200 일 오후 12 시부 터 일찍 도착 한 방에 대한것이었다."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>녹음된 오디오 추가

`audio`는 MP3 오디오를 SSML 문서에 삽입할 수 있는 선택적 요소입니다. 오디오 요소의 본문에는 오디오 파일을 사용할 수 없거나 재생할 수 없는 경우 사용되는 일반 텍스트 또는 SSML 태그가 포함될 수 있습니다. `audio` 또한 요소에텍스트와 다음 요소를 `audio` `break` `p` `s` `phoneme` `prosody` `say-as` `sub`포함할 수 있습니다.

SSML 문서에 포함된 오디오는 다음 요구 사항을 충족해야 합니다.

* MP3는 인터넷에 액세스할 수 있는 HTTPS 끝점에서 호스팅되어야 합니다. HTTPS가 필요하며 MP3 파일을 호스팅하는 도메인은 신뢰할 수 있는 신뢰할 수 있는 유효한 TLS/SSL 인증서를 제공해야 합니다.
* MP3는 유효한 MP3 파일(MPEG v2)이어야 합니다.
* 비트 레이트(bit rate)는 48kbps여야 합니다.
* 샘플 속도는 16,000Hz여야 합니다.
* 단일 응답의 모든 텍스트 및 오디오 파일에 대한 총 총 시간은 90초를 초과할 수 없습니다.
* MP3에는 고객별 또는 기타 중요한 정보가 포함되어서는 안 됩니다.

**구문**

```xml
<audio src="string"/></audio>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `src` | 오디오 파일의 위치/URL을 지정합니다. | SSML 문서에서 오디오 요소를 사용하는 경우 필요합니다. |

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>배경 오디오 추가

이 `mstts:backgroundaudio` 요소를 사용하면 SSML 문서에 백그라운드 오디오를 추가하거나 오디오 파일을 텍스트 음성 변환과 혼합할 수 있습니다. 백그라운드에서 `mstts:backgroundaudio` 오디오 파일을 반복하고, 텍스트 음성 변환을 시작할 때 페이드 인하고, 텍스트 음성 변환 이 끝날 때 페이드 아웃할 수 있습니다.

제공된 백그라운드 오디오가 텍스트 음성 변환 또는 페이드 아웃보다 짧으면 반복됩니다. 텍스트 음성 변환보다 길면 페이드 아웃이 완료되면 중지됩니다.

SSML 문서당 하나의 백그라운드 오디오 파일만 허용됩니다. 그러나 `audio` `voice` 요소 내에 태그를 산재하여 SSML 문서에 오디오를 추가할 수 있습니다.

**구문**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**특성**

| 특성 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| `src` | 백그라운드 오디오 파일의 위치/URL을 지정합니다. | SSML 문서에서 백그라운드 오디오를 사용하는 경우 필요합니다. |
| `volume` | 백그라운드 오디오 파일의 볼륨을 지정합니다. **허용된** `0` 값 `100` : 포함값. 기본값은 `1`입니다. | Optional |
| `fadein` | 백그라운드 오디오 "페이드 인"의 지속 시간을 밀리초로 지정합니다. 기본값은 `0`페이드 인 없음과 동일합니다. **허용된** `0` 값 `10000` : 포함값.  | Optional |
| `fadeout` | 백그라운드 오디오의 지속 시간이 밀리초 단위로 페이드 아웃되는 것을 지정합니다. 기본값은 `0`페이드 아웃되지 않는 것과 동일합니다. **허용된** `0` 값 `10000` : 포함값.  | Optional |

**예제**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>다음 단계

* [언어 지원: 음성, 로캘, 언어](language-support.md)
