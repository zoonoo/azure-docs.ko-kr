---
title: '빠른 시작: 마이크에서 음성 인식 - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469705"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 마이크 입력에서 음성을 대화형으로 인식하고 캡처된 오디오에서 텍스트 전사를 가져옵니다. 대화 기록과 같은 일반적인 인식 작업을 위해 이 기능을 앱 또는 디바이스에 쉽게 통합할 수 있습니다. 또한 Speech SDK와 함께 Bot Framework를 사용하여 음성 도우미를 빌드하는 등 더 복잡한 통합에도 사용할 수 있습니다.

몇 가지 필수 구성 요소를 충족한 후 마이크에서 음성을 인식하는 작업은 네 가지 단계를 수행하면 됩니다.

> [!div class="checklist"]
> * 구독 키 및 지역에서 `SpeechConfig` 개체를 만듭니다.
> * 위의 `SpeechConfig` 개체를 사용하여 `SpeechRecognizer` 개체를 만듭니다.
> * `SpeechRecognizer` 개체를 사용하여 단일 발화에 대한 인식 프로세스를 시작합니다.
> * 반환된 `SpeechRecognitionResult`를 검사합니다.
