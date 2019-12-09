---
title: '빠른 시작: 오디오 파일에 음성 합성 - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818009"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 텍스트를 오디오 파일의 합성된 음성으로 변환합니다. 몇 가지 필수 구성 요소를 충족한 후 파일에 음성을 합성하는 작업은 다음 다섯 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 ````SpeechConfig```` 개체를 만듭니다.
> * .WAV 파일 이름을 지정하는 오디오 구성 개체를 만듭니다.
> * 위의 구성 개체를 사용하여 ````SpeechSynthesizer```` 개체를 만듭니다.
> * ````SpeechSynthesizer```` 개체를 사용하여 텍스트를 합성된 음성으로 변환한 후 지정된 오디오 파일에 저장합니다.
> * 오류에 대해 반환된 ````SpeechSynthesizer````를 검사합니다.
