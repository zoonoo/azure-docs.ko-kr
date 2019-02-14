---
title: Speech Synthesis Markup Language - Speech Services
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
ms.openlocfilehash: f721c91c828b5c7c8497c07bb0cfe79646daf0f8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868840"
---
# <a name="speech-synthesis-markup-language"></a>Speech Synthesis Markup Language

SSML(Speech Synthesis Markup Language)은 텍스트 음성 변환의 발음 및 *운율*을 제어하는 방법을 제공하는 XML 기반 태그 언어입니다. 운율은 음성의 리듬 및 피치를 가리킵니다. 단어를 발음대로 지정하고, 숫자 해석을 위한 힌트를 제공하고, 일시 중지를 삽입하고, 피치, 볼륨 및 속도를 제어할 수 있습니다. 자세한 내용은 [SSML(Speech Synthesis Markup Language) 버전 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)을 참조하세요.

지원되는 언어, 로캘 및 음성(인공신경망 및 표준)의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.

다음 섹션에서는 일반적인 음성 합성 작업에 대한 샘플을 제공합니다.

>[!IMPORTANT]
> 현재는 표준 음성에 대해 운율 태그 지정만 가능합니다.

## <a name="add-a-break"></a>중단 추가
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>말하기 속도 변경
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>발음
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>볼륨 변경
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>피치 변경
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>피치 곡선 변경
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>여러 음성 사용
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>다음 단계

* [언어 지원: 음성, 로캘, 언어](language-support.md)
