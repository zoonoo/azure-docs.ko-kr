---
title: Conversation Learner란? - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 및 작동 방식에 대해 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: f8bc7590f2d7a622b4b1ffb21bfeccef89691fd5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389508"
---
# <a name="what-is-conversation-learner"></a>Conversation Learner란?

Conversation Learner를 통해 예제 상호 작용에서 배운 대화형 인터페이스를 빌드하고 학습할 수 있습니다. 

기존 방법과 달리 Conversation Learner는 대화의 엔드투엔드 컨텍스트를 고려하여 응답을 개선하고 보다 매력적인 사용자 경험을 제공합니다. Conversation Learner는 광범위한 작업 지향 사용 사례에서 Machine Learning을 백그라운드로 적용하여 봇과 지능형 에이전트가 사용자 불만 및 추가적인 고객 서비스 비용을 초래할 가능성을 줄이고 더욱 직관적인 상호 작용을 지원합니다.

개발자가 모방하려는 모범 대화를 입력하는 것으로 시작합니다. 모델은 더 많은 대화가 입력되면서 학습됩니다. 모델이 제대로 작동하면 최종 사용자에게 봇을 배포할 수 있습니다. Conversation Learner가 사용자와의 대화를 기록하고, 개발자는 대화를 검토할 수 있습니다. 실수가 발견될 경우 개발자가 즉석에서 수정할 수 있으며, 모델이 다시 학습되고 즉시 사용할 수 있게 됩니다.

이 접근 방법을 사용하면 대화 제어 논리의 수동 코딩이 감소하며, 비즈니스 소유자나 도메인 전문가가 사전 Machine Learning 지식 없이도 대화형 인터페이스에 참여할 수 있습니다. 봇, 스마트 디바이스 또는 지능형 에이전트의 일부로 배포하든 간에 Conversation Learner는 새로운 기술, 동작 또는 역량을 신속하게 반복하고 품질을 빠르게 개선할 수 있습니다. 

Conversation Learner를 사용하면 개발자가 Microsoft Bot Framework를 통해 여러 대화 채널에서 또는 자체 인프라를 사용하여 독립형으로 성공적인 대화를 진행하고 시장 진출 속도를 높일 수 있습니다.

요약 및 강조 표시:

- Conversation Learner는 작업 중심 봇을 빌드하기 위한 AI 우선 방법입니다.

- 엔드투엔드 반복 신경망(LSTM)을 사용하며 대화의 다중 턴 예제를 통해 직접 학습합니다. 

- 설계자, 개발자, 비즈니스 사용자 및 콜 센터 작업자가 봇을 빌드하고 유지 관리할 수 있게 합니다. 

- 코드에서 비즈니스 규칙과 상식을 표현하는 기능을 제공합니다.

- 학습 세션 중에 신경망 모델은 대화에서 예상되는 다음 작업 집합에 점수를 지정하는 데 사용됩니다. 그런 다음, 봇 개발자가 올바른 작업을 선택하고, 적절한 응답을 제공하도록 네트워크를 학습할 수 있습니다.
 
- 학습이 완료되면 개발자는 사용자 조작의 로그 대화를 사용하여 봇 응답을 수정하고 모델을 다시 학습할 수 있습니다. 

- 도메인 특정 API 및 타사 API를 호출하여 작업을 완료할 수 있습니다.

