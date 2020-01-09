---
title: '빠른 시작: 오디오 파일에서 음성 인식 - Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467370"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 오디오 파일에서 음성을 인식합니다. 몇 가지 필수 구성 요소를 충족한 후 파일에서 음성을 인식하는 작업은 다음 다섯 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 ````SpeechConfig```` 개체를 만듭니다.
> * .WAV 파일 이름을 지정하는 ````AudioConfig```` 개체를 만듭니다.
> * 위의 ````SpeechConfig```` 및 ````AudioConfig```` 개체를 사용하여 ````SpeechRecognizer```` 개체를 만듭니다.
> * ````SpeechRecognizer```` 개체를 사용하여 단일 발화에 대한 인식 프로세스를 시작합니다.
> * 반환된 ````SpeechRecognitionResult````를 검사합니다.
