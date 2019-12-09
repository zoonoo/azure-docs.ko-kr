---
title: '빠른 시작: 음성을 여러 언어로 번역 - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: bc35c1efcad2ca9ebb5eaf23dd84f7189858a159
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817096"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 한 언어에서 다른 언어로 음성을 대화형으로 변환합니다. 몇 가지 필수 구성 요소를 충족한 후 음성을 여러 언어의 텍스트로 변환하는 작업은 다음 여섯 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 ````SpeechTranslationConfig```` 개체를 만듭니다.
> * ````SpeechTranslationConfig```` 개체를 업데이트하여 음성 인식 소스 언어를 지정합니다.
> * ````SpeechTranslationConfig```` 개체를 업데이트하여 여러 번역 대상 언어를 지정합니다.
> * 위의 ````SpeechTranslationConfig```` 개체를 사용하여 ````TranslationRecognizer```` 개체를 만듭니다.
> * ````TranslationRecognizer```` 개체를 사용하여 단일 발화에 대한 인식 프로세스를 시작합니다.
> * 반환된 ````TranslationRecognitionResult````를 검사합니다.
