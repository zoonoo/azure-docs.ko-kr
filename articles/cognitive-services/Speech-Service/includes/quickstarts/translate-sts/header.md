---
title: '빠른 시작: 음성 대 음성 번역 - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981312"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 한 언어에서 다른 언어로 음성을 대화형으로 번역합니다. 몇 가지 필수 구성 요소를 충족한 후 음성을 음성으로 번역하는 작업은 여섯 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 ````SpeechTranslationConfig```` 개체를 만듭니다.
> * ````SpeechTranslationConfig```` 개체를 업데이트하여 원본 및 대상 언어를 지정합니다.
> * ````SpeechTranslationConfig```` 개체를 업데이트하여 음성 출력 음성 이름을 지정합니다.
> * 위의 ````SpeechTranslationConfig```` 개체를 사용하여 ````TranslationRecognizer```` 개체를 만듭니다.
> * ````TranslationRecognizer```` 개체를 사용하여 단일 발화에 대한 인식 프로세스를 시작합니다.
> * 반환된 ````TranslationRecognitionResult````를 검사합니다.
