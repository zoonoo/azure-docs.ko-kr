---
title: '빠른 시작: Blob storage에 저장된 음성 인식 - Speech Service'
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
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500773"
---
이 빠른 시작에서는 [Batch Transcription REST API](../../../batch-transcription.md)를 사용하여 [SAS Blob](https://aka.ms/ignite2019/speech/placeholder)에 저장된 음성을 인식합니다. 몇 가지 필수 구성 요소를 충족한 후 REST API를 사용하여 음성을 인식하는 작업은 몇 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * Speech Service에 JSON 요청을 보내면 음성 전사를 시작합니다.
> * 전사 상태를 확인합니다.
> * 완료되면 전사 결과를 다운로드합니다.