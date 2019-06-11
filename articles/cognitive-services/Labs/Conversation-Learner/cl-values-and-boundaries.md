---
title: Conversation Learner 기본 구성 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 기본 Conversation Learner 구성에 대해 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: ebdc1e1c100329e95bd19359408cb138d233b1c2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385433"
---
# <a name="default-values-and-boundaries"></a>기본값 및 경계

이 문서에서는 Conversation Learner의 기본 구성과 주요 서비스 경계에 대해 설명합니다.

## <a name="default-configuration"></a>기본 구성

매개 변수 | 기본값
--- | --- 
기본 세션 시간 제한 | 30분

## <a name="boundaries"></a>경계

매개 변수 | 제한
--- | --- 
작성 API, 월별 최대 HTTP 호출 수 | 5M
작성 API, 초당 최대 HTTP 호출 수 | 25
세션 API, 월별 최대 HTTP 호출 수 | 500K
세션 API, 초당 최대 HTTP 호출 수 | 10
모델당 최대 사용자 지정(비프로그래밍) 엔터티 수 | [LUIS 경계 문서](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)를 참조하세요. 실제 개수는 약간 더 작을 수 있습니다.
모델당 최대 미리 빌드된 엔터티 수 | [LUIS 경계 문서](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)를 참조하세요.
모델당 최대 엔터티 수(합계) | 100
모델당 최대 작업 수 | 32
모델당 최대 학습 대화 수 | 1000
학습 대화당 최대 사용자 반환 수 | 100
모델당 최대 로그 대화 수 | 미리 설정된 제한은 없지만 로그 대화는 삭제되기 전에 일정 기간 동안만 유지됩니다.  또한 Conversation Learner UI는 한 번에 100개의 로그 대화를 표시합니다. 
사용자별 모델의 최대 수 | 미리 설정된 제한 없음
최대 순차적 비대기 작업 수 | 5(*)

(*) 5개의 순차적 비대기 작업이 수행된 후 모든 비대기 작업이 마스크되고, Conversation Learner가 사용 가능한 대기 작업 중에서 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Conversation Learner 시작](./quickstart.md)
