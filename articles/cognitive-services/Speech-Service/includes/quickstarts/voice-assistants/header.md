---
title: '빠른 시작: 사용자 지정 음성 도우미 만들기 - Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "80241864"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 이미 작성하고 구성한 봇에 연결하는 사용자 지정 음성 도우미 애플리케이션을 만듭니다. 봇을 만들어야 하는 경우 더 포괄적인 가이드는 [관련 자습서](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md)를 참조하세요.

몇 가지 필수 구성 요소를 충족한 후에는 사용자 도우미를 연결하는 데 몇 가지 단계만 거치면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 `BotFrameworkConfig` 개체를 만듭니다.
> * 위의 `BotFrameworkConfig` 개체를 사용하여 `DialogServiceConnector` 개체를 만듭니다.
> * `DialogServiceConnector` 개체를 사용하여 단일 발화에 대한 수신 대기 프로세스를 시작합니다.
> * 반환된 `ActivityReceivedEventArgs`를 검사합니다.
