---
title: Speech Synthesis Markup Language (SSML)-음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech Synthesis Markup Language를 사용하여 텍스트 음성 변환의 발음 및 운율을 제어합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8285a76f8cd07863874f9c8e8eebe96f1cb968dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604814"
---
# <a name="speech-synthesis-markup-language-ssml"></a>SSML(Speech Synthesis Markup Language)

Speech Synthesis Markup Language (SSML)는 입력된 텍스트를 지정 하는 개발자가 있도록 하는 XML 기반 태그 언어 텍스트 음성 변환 서비스를 사용 하 여 음성으로 변환 됩니다. 일반 텍스트에 비해, SSML 개발자 피치, 발음을 세밀 하 게 말하기 속도, 볼륨 및 텍스트 음성 변환 출력의 자세한 내용은 합니다. 일반 문장 부호, 예: 기간 후 일시 중지 또는 문장 물음표를 사용 하 여 종료 될 때 올바른 억양을 사용 하 여 자동으로 처리 됩니다.

SSML 음성 서비스 구현의 기반으로 World Wide Web 컨소시엄 [Speech Synthesis Markup Language Version 1.0](https://www.w3.org/TR/speech-synthesis)합니다.

> [!IMPORTANT]
> 중국어, 일본어 및 한국어 문자 청구에 대 한 두 개의 문자로 계산 됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

## <a name="standard-neural-and-custom-voices"></a>Standard, 신경망, 및 사용자 지정 음성

표준 및 신경망 음성에서 선택하거나 고유한 제품 또는 브랜드를 만들기 위해 사용자 고유의 사용자 지정 음성을 만듭니다. 75개 이상 표준 음성이 45개 이상의 언어 및 로캘에서 제공되며, 5개의 신경망 음성은 4개 언어 및 로캘에서 사용할 수 있습니다. 지원되는 언어, 로캘 및 음성(인공신경망 및 표준)의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

신경망, 표준 및 사용자 지정 음성에 대 한 자세한 내용은 참조 하세요 [텍스트 음성 변환 개요](text-to-speech.md)합니다.

## <a name="supported-ssml-elements"></a>지원 되는 SSML 요소

SSML 요소 (또는 태그)를 사용 하 여 각 SSML 문서가 생성 됩니다. 이러한 요소는 피치, prosody, 볼륨을 조정 하는 데 사용 됩니다. 각 요소를 사용 하는 방법 및 필수 또는 선택적 요소가 표시 되는 경우 다음 섹션에 자세히 설명 합니다.  

> [!IMPORTANT]
> 특성 값 주위에 큰따옴표를 사용 하도록 두는 것을 잊지 마세요. 잘 구성 된 유효한 XML에 대 한 표준 큰따옴표로 묶어야 특성 값을 지정 해야 합니다. 예를 들어 `<prosody volume="90">` 잘 구성 요소 이지만 `<prosody volume=90>` 아닙니다. SSML은 따옴표에 있지 않은 특성 값을 인식 하지 못할 수 있습니다.

## <a name="create-an-ssml-document"></a>SSML 문서를 만듭니다.

`speak` 루트 요소는 이며 **필요한** 모든 SSML 문서에 대 한 합니다. `speak` 요소는 버전, 언어 및 태그 어휘 정의 같은 중요 한 정보를 포함 합니다.

**구문**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**특성**

| 특성 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| version | 문서 태그를 해석 하는 데 사용 되는 SSML 사양의 버전을 나타냅니다. 현재 버전은 1.0입니다. | 필수 |
| xml:lang | 문서 루트의 언어를 지정 합니다. 값에는 소문자, 2 자 언어 코드를 포함할 수 있습니다 (예를 들어 **en**), 또는 언어 코드 및 대문자 국가/지역 (예를 들어 **EN-US**). | 필수 |
| xmlns | SSML 문서의 태그 어휘 (요소 형식 및 특성 이름)을 정의 하는 문서에 대 한 URI를 지정 합니다. 현재 URI가 https://www.w3.org/2001/10/synthesis 합니다. | 필수 |

## <a name="choose-a-voice-for-text-to-speech"></a>텍스트 음성 변환에 대 한 음성 선택

`voice` 요소는 필수입니다. 텍스트 음성 변환에 사용 되는 음성을 지정 하는 것이 됩니다.

**구문**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**특성**

| 특성 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| name | 텍스트 음성 변환 출력에 사용 되는 음성을 식별 합니다. 지원 되는 음성의 전체 목록은 참조 하세요 [언어 지원](language-support.md#text-to-speech)합니다. | 필수 |

**예제**

> [!NOTE]
> 이 예제에서는 `en-US-Jessa24kRUS` 음성. 지원 되는 음성의 전체 목록은 참조 하세요 [언어 지원](language-support.md#text-to-speech)합니다.

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>여러 음성 사용

내는 `speak` 요소, 텍스트 음성 변환 출력에 대 한 여러 음성을 지정할 수 있습니다. 이러한 음성 언어 지원 될 수 있습니다. 각 음성에 대 한 텍스트에 래핑되어야 합니다는 `voice` 요소입니다.

**특성**

| 특성 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| name | 텍스트 음성 변환 출력에 사용 되는 음성을 식별 합니다. 지원 되는 음성의 전체 목록은 참조 하세요 [언어 지원](language-support.md#text-to-speech)합니다. | 필수 |

**예제**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>말하기 스타일 조정

> [!IMPORTANT]
> 이 기능은 신경망 음성만 작동 합니다.

기본적으로 텍스트 음성 변환 서비스는 중립 말하기 스타일을 사용 하 여 표준 및 신경망 음성에 대 한 텍스트를 합성 합니다. 신경망 음성 cheerfulness, 공감을 사용 하 여 데이터를 표현 하는 말하기 스타일을 조정할 수 있습니다는 `<mstts:express-as>` 요소입니다. 이것이 Azure 음성 서비스에 고유한 선택적 요소입니다.

현재 말하기 스타일 조정 이러한 신경망 음성 지원 됩니다.
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

문장 수준에서 변경 내용이 적용 및 스타일 의견에 따라 다릅니다. 스타일 지원 되지 않는 경우 서비스는 반환 음성 기본에서 스타일의 말하기 중립입니다.

**구문**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**특성**

| 특성 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| type | 말하기 스타일을 지정합니다. 현재 말하기 스타일은 특정 음성입니다. | 신경망의 음성을 말하기 스타일을 조정 하는 경우 필요 합니다. 사용 하는 경우 `mstts:express-as`, 다음 형식을 제공 해야 합니다. 잘못 된 값을 제공 하는 경우이 요소는 무시 됩니다. |

이 표를 사용 하 여 말하기 스타일에는 각 신경망 음성에 대 한 지원 되는지 확인 합니다.

| 음성 | 형식 | Description |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Emotion이 발생 하는 양의 및 만족 표현 |
| | type=`empathy` | 신경쓰지 이해 하는 데 어느 정도 인지를 표현 합니다. |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | 뉴스 브로드캐스트 비슷합니다 정식 어조를 표현합니다. |
| | type=`sentiment` | 감동적인 메시지 또는 스토리를 전달합니다. |

**예제**

SSML 조각과이 하는 방법을 `<mstts:express-as>` 요소에 말하기 스타일을 변경 하는 데 사용 됩니다 `cheerful`합니다.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>추가 또는 제거/일시 중단

사용 된 `break` 일시 중지 (또는 중단) 단어 사이 삽입 또는 텍스트 음성 변환 서비스에 의해 자동으로 추가 하는 일시 중지를 방지 하는 요소입니다.

> [!NOTE]
> 이 요소를 사용 하 여 해당 단어 또는 구 합성 된 음성 보이기도 자연스럽 지 하는 경우 단어 또는 구 텍스트 음성 변환 (TTS)의 기본 동작을 재정의할 수 있습니다. 설정할 `strength` 에 `none` 텍-음성 서비스에 의해 자동으로 삽입 되는 운율 중단을 방지 하기 위해.

**구문**

```xml
<break strength="string" />
<break time="string" />
```

**특성**

| 특성 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| strength | 다음 값 중 하나를 사용 하 여 일시 중지의 상대적 기간을 지정 합니다.<ul><li>없음</li><li>x-weak</li><li>약한</li><li>중간 (기본값)</li><li>강력한</li><li>x-strong</li></ul> | Optional |
| Time | 절대 초 나 밀리초를 일시 중지 지속 시간을 지정 합니다. 유효한 값의 예로 2s 및 500 | Optional |

| 강도 | Description |
|----------|-------------|
| None, 제공 된 값이 없는 경우 또는 | 0ms |
| x-weak | 250ms |
| 약한 | 500ms |
| 중간 | 750ms |
| 강력한 | 1000ms |
| x-strong | 1250 ms |


**예제**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>단락 및 문장을 지정 합니다.

`p` 및 `s` 요소 단락 및 문장을 각각 나타내는 데 사용 됩니다. 이러한 요소가 없는 경우, 텍스트 음성 변환 서비스 SSML 문서의 구조를 자동으로 결정합니다.

`p` 요소 텍스트와 다음 요소를 포함할 수 있습니다: `audio`, `break`, `phoneme`, `prosody`를 `say-as`, `sub`를 `mstts:express-as`, 및 `s`합니다.

`s` 요소 텍스트와 다음 요소를 포함할 수 있습니다: `audio`, `break`, `phoneme`를 `prosody`를 `say-as`, `mstts:express-as`, 및 `sub`합니다.

**구문**

```XML
<p></p>
<s></s>
```

**예제**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>음소 발음을 개선 하기 위해 사용

`ph` SSML 문서의 음성 발음에 대 한 요소를 사용 합니다. `ph` 요소 텍스트를 다른 요소가 포함할 수 있습니다. 대체 방법으로 사용자를 읽을 수 있는 음성을 항상 제공 합니다.

음성 알파벳 문자, 숫자 또는 문자를 조합에 따라 된 휴대폰으로 구성 됩니다. 각 전화 음성의 고유한 소리를 설명합니다. 즉 라틴 알파벳 문자와 여러 음성된 소리를 나타낼 수 있는입니다. 단어 "사탕" 및 "중단", "c" 문자의 다양 한 발음 또는 고려 문자 조합과 다른 발음 "th" 단어 "thing" 및 "는" 합니다.

**구문**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**특성**

| 특성 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| 알파벳 | 윗주 알파벳의 문자열 발음을 통합 하는 경우 사용 하도록 지정 합니다 `ph` 특성입니다. 소문자에서 알파벳을 지정 하는 문자열을 지정 되어야 합니다. 다음은 지정할 수 있는 가능한 알파벳입니다.<ul><li>ipa &ndash; International Phonetic Alphabet</li><li>sapi &ndash; Speech API 전화 설정</li><li>ups &ndash; 유니버설 전화 설정</li></ul>알파벳 음소 요소에만 적용 됩니다. 자세한 내용은 [Phonetic Alphabet 참조](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)합니다. | Optional |
| ph | 에 있는 단어의 발음을 지정 하는 휴대폰을 포함 하는 문자열을 `phoneme` 요소입니다. 지정 된 문자열이 인식할 수 없는 휴대폰을 포함 하는 경우 텍스트 음성 변환 (TTS) 서비스 SSML 문서 전체를 거부 하 고 없음 문서에서 지정 된 음성 출력을 생성 합니다. | 음소를 사용 하는 경우 필요 합니다. |

**예제**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Prosody 조정

`prosody` 요소는 피치, countour, 범위, 속도, 기간 및 텍스트 음성 변환 출력에 대 한 볼륨에 변경 내용을 지정 하는 데 사용 됩니다. `prosody` 요소 텍스트와 다음 요소를 포함할 수 있습니다: `audio`, `break`, `p`, `phoneme`를 `prosody`, `say-as`를 `sub`, 및 `s`합니다.

광범위 한 운율 특성 값 달라질 수 있으므로 음성 인식기 해야 선택한 음성의 실제 운율 값의 제안으로 할당 된 값을 해석 합니다. 텍스트 음성 변환 서비스를 제한 하거나 지원 되지 않는 값을 대체 합니다. 지원 되지 않는 값의 예로 1 MHz의 피치 또는 120 볼륨을 들 수 있습니다.

**구문**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**특성**

| 특성 | Description | 필수/선택 |
|-----------|-------------|---------------------|
| 피치 | 텍스트의 기준선 피치를 나타냅니다. 피치도 표현할 수 있습니다.<ul><li>"이" (헤르츠) 뒤에 숫자로 표시 되는 절대 값입니다. 예를 들어 600 Hz 합니다.</li><li>앞에 숫자로 나타낸 상대 값을 "+" 또는 "-" 뒤에 "이" 또는 "st"를 지정 하는 용량 및 피치를 변경 합니다. 예를 들어: + 80 Hz 또는 2st 합니다. "St" 변경 단위는 표준 diatonic 눈금에 톤 (1/2 단계)의 절반인 반음 임을 나타냅니다.</li><li>상수 값입니다.<ul><li>x-low</li><li>낮음</li><li>중간</li><li>높음</li><li>x-high</li><li>기본</li></ul></li></ul>을 선택합니다. | Optional |
| 윤곽선 | 윤곽선 신경망 음성에 대 한 지원 되지 않습니다. 윤곽선 음성 콘텐츠 피치의 변경 내용을 음성 출력에서 특정 위치에 있는 대상의 배열을 나타냅니다. 각 대상 매개 변수 쌍의 집합으로 정의 됩니다. 예를 들어: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>텍스트의 지속 시간 비율로 피치 변경의 위치를 지정 하는 각 매개 변수 집합의 첫 번째 값입니다. 두 번째 값 피치에 대 한 상대 값 또는 열거형 값을 사용 하는 피치를 올리거나 내릴 양을 지정 (참조 `pitch`). | Optional |
| range  | 텍스트에 대 한 피치 범위를 나타내는 값입니다. 표현할 수 있습니다 `range` 설명 하는 데 동일한 절대 값, 상대 값 또는 열거형 값을 사용 하 여 `pitch`입니다. | Optional |
| 속도  | 텍스트의 읽어주기 속도 나타냅니다. Express 있습니다 `rate` 으로:<ul><li>기본값의 승수로 사용 되는 숫자로 나타낸 상대적 값입니다. 예를 들어, 값 *1* 결과 속도 변경 되지 않습니다. 값이 *.5* 속도 양분 발생 합니다. 값이 *3* 속도 커지고 발생 합니다.</li><li>상수 값입니다.<ul><li>x-slow</li><li>slow</li><li>중간</li><li>빠른</li><li>x-fast</li><li>기본</li></ul></li></ul> | Optional |
| duration  | 음성 하는 동안 경과 되어야 하는 기간 (TTS) 합성 서비스 밀리초 또는 초 단위로 텍스트를 읽습니다. 예를 들어 *2s* 하거나 *1800ms*합니다. | Optional |
| 볼륨  | 음성의 볼륨 수준을 나타냅니다. 볼륨에 볼륨을 표현할 수 있습니다.<ul><li>100.0 0.0의 범위에 숫자로 표시 되는 절대값 *조용한* 하 *가장 큰*입니다. 예를 들어 75입니다. 기본값은 100.0입니다.</li><li>앞에 숫자로 나타낸 상대 값을 "+" 또는 "-" 볼륨을 변경 하려면 크기를 지정 하는 합니다. 예를 들어 + 10 또는-5.5 합니다.</li><li>상수 값입니다.<ul><li>자동</li><li>x-soft</li><li>soft</li><li>중간</li><li>클라우드</li><li>x-loud</li><li>기본</li></ul></li></ul> | Optional |

### <a name="change-speaking-rate"></a>말하기 속도 변경

읽기 속도 표준 음성 단어 또는 문장 수준에 적용할 수 있습니다. 반면 읽기 속도 신경망 음성 문장 수준에 적용할만 있습니다.

**예제**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>볼륨 변경

볼륨 변경 표준 음성 단어 또는 문장 수준에서 적용할 수 있습니다. 볼륨 변경 신경망 음성 문장 수준에 적용할 수 있습니다 반면.

**예제**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>피치 변경

피치 변경 표준 음성 단어 또는 문장 수준에서 적용할 수 있습니다. 반면 피치 변경 신경망 음성 문장 수준에 적용할 수 있습니다.

**예제**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>피치 곡선 변경

> [!IMPORTANT]
> Contour 변경 피치는 음성 신경망을 사용 하 여 지원 되지 않습니다.

**예제**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="next-steps"></a>다음 단계

* [언어 지원: 음성, 로캘, 언어](language-support.md)
