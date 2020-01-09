---
title: '빠른 시작: 오디오 파일에 음성 합성 - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467520"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 텍스트를 오디오 파일의 합성된 음성으로 변환합니다. 몇 가지 필수 구성 요소를 충족한 후 파일에 음성을 합성하는 작업은 다음 다섯 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 ````SpeechConfig```` 개체를 만듭니다.
> * .WAV 파일 이름을 지정하는 오디오 구성 개체를 만듭니다.
> * 위의 구성 개체를 사용하여 ````SpeechSynthesizer```` 개체를 만듭니다.
> * ````SpeechSynthesizer```` 개체를 사용하여 텍스트를 합성된 음성으로 변환한 후 지정된 오디오 파일에 저장합니다.
> * 오류에 대해 반환된 ````SpeechSynthesizer````를 검사합니다.
