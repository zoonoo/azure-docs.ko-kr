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
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9871e0106ee6caf11c5a1e24459fbd2044f5f3d7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021450"
---
# <a name="speech-synthesis-markup-language-ssml"></a>SSML(Speech Synthesis Markup Language)

SSML(Speech Synthesis Markup Language)은 텍스트 음성 변환의 발음 및 *운율*을 제어하는 방법을 제공하는 XML 기반 태그 언어입니다. 운율은 음성의 리듬 및 피치를 가리킵니다. 단어를 발음대로 지정하고, 숫자 해석을 위한 힌트를 제공하고, 일시 중지를 삽입하고, 피치, 볼륨 및 속도를 제어할 수 있습니다. 자세한 내용은 [SSML(Speech Synthesis Markup Language) 버전 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)을 참조하세요. 

지원되는 언어, 로캘 및 음성(인공신경망 및 표준)의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.

다음 섹션에서는 일반적인 음성 합성 작업에 대한 샘플을 제공합니다.

## <a name="adjust-speaking-style-for-neural-voices"></a>말하기 스타일 신경망 음성에 대 한 조정

SSML 신경망 음성 중 하나를 사용 하는 경우 말하기 스타일에 맞게 사용할 수 있습니다.

기본적으로 텍스트 음성 변환 서비스 중립 스타일의에서 텍스트를 합성 합니다. 신경망 음성 확장 된 SSML을 `<mstts:express-as>` 다른 관련 합성 된 음성 텍스트 변환 하는 요소 스타일 지정 합니다. 현재 스타일 태그를 이러한 음성 에서만 지원 됩니다.

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`.

말하기 스타일 변경 문장 수준에서 적용할 수 있습니다. 스타일 의견에 따라 다릅니다. 스타일 형식 지원 되지 않는 경우 서비스는 중립 기본 스타일으로 합성 된 음성을 반환 합니다.

| 음성 | Style | 설명 | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | Emotion이 발생 하는 양의 및 만족 표현 |
| | type=`empathy` | 신경쓰지 이해 하는 데 어느 정도 인지를 표현 합니다. |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | 뉴스 브로드캐스트 비슷합니다 정식 어조를 표현합니다. |
| | type=`sentiment ` | 감동적인 메시지 또는 스토리를 전달합니다. |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>중단 추가
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>말하기 속도 변경

읽기 속도 표준 음성 단어 또는 문장 수준에 적용할 수 있습니다. 반면 읽기 속도 신경망 음성 문장 수준에 적용할만 있습니다.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>발음
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>볼륨 변경

볼륨 변경 표준 음성 단어 또는 문장 수준에서 적용할 수 있습니다. 볼륨 변경 신경망 음성 문장 수준에 적용할 수 있습니다 반면.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>피치 변경

피치 변경 표준 음성 단어 또는 문장 수준에서 적용할 수 있습니다. 반면 피치 변경 신경망 음성 문장 수준에 적용할 수 있습니다.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>피치 곡선 변경

> [!IMPORTANT]
> Contour 변경 피치는 음성 신경망을 사용 하 여 지원 되지 않습니다.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>여러 음성 사용
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>다음 단계

* [언어 지원: 음성, 로캘, 언어](language-support.md)
