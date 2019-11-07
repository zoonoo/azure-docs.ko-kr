---
title: '빠른 시작: 음성 합성 - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 음성을 합성하는 방법 알아보기
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500517"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 텍스트를 합성된 음성으로 변환합니다. 몇 가지 필수 구성 요소를 충족한 후 합성된 음성을 기본 스피커로 렌더링하는 작업은 다음 네 개의 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 ````SpeechConfig```` 개체를 만듭니다.
> * 위의 ````SpeechConfig```` 개체를 사용하여 ````SpeechSynthesizer```` 개체를 만듭니다.
> * ````SpeechSynthesizer```` 개체를 사용하여 텍스트를 말합니다.
> * 오류에 대해 반환된 ````SpeechSynthesisResult````를 확인합니다.
