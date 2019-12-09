---
title: '빠른 시작: 음성, 의도 및 엔터티 인식 - Speech Service'
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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a87ed9355a5939393fd5e20f395cc96f35e7f150
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815965"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 마이크에서 캡처된 오디오 데이터의 음성을 대화형으로 인식합니다. 몇 가지 필수 구성 요소를 충족한 후 마이크에서 음성을 인식하는 작업은 네 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
>
> * 구독 키 및 지역에서 ````SpeechConfig```` 개체를 만듭니다.
> * 위의 ````SpeechConfig```` 개체를 사용하여 ````IntentRecognizer```` 개체를 만듭니다.
> * ````IntentRecognizer```` 개체를 사용하여 단일 발화에 대한 인식 프로세스를 시작합니다.
> * 반환된 ````IntentRecognitionResult````를 검사합니다.
