---
title: '빠른 시작: 오디오 파일에서 음성 인식 - Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037525"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 오디오 파일에서 음성을 인식합니다. 몇 가지 필수 구성 요소를 충족한 후 파일에서 음성을 인식하는 작업은 몇 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 `SpeechConfig` 개체를 만듭니다.
> * .WAV 파일 이름을 지정하는 `AudioConfig` 개체를 만듭니다.
> * 위의 `SpeechConfig` 및 `AudioConfig` 개체를 사용하여 `SpeechRecognizer` 개체를 만듭니다.
> * `SpeechRecognizer` 개체를 사용하여 단일 발화에 대한 인식 프로세스를 시작합니다.
> * 반환된 `SpeechRecognitionResult`를 검사합니다.
