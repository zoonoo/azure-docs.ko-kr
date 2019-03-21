---
title: 질문과 대답 - Personality Chat
titlesuffix: Azure Cognitive Services
description: Personality Chat에 대한 FAQ
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 88dfbb2a547b44de1bb98ca536c7841570a12168
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766838"
---
# <a name="frequently-asked-questions"></a>질문과 대답

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>이 기능이 다른 챗봇처럼 내가 하는 모든 질문에 답변하지는 못하는 이유는 무엇인가요?

Project Personality Chat은 개인 정보를 공개하고 보다 완전한 사용자 경험을 창출하는 일반적인 짧은 대화로 봇을 향상시킵니다. 봇의 주요 기능과 관련이 없는 주제에 대해 긴 대화를 나누도록 디자인되어 있지 않습니다. 모든 대화에 응답할 수 있지만 베타 버전에서는 일반적인 짧은 대화 영역으로 제한됩니다.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>내 브랜드에 맞게 성향을 사용자 지정하려면 어떻게 해야 하나요?

사용 가능한 기본 가상 사용자 중에서 가장 가까운 가상 사용자를 선택합니다. 현재, 설명 라이브러리를 가져와 사용자 브랜드에 보다 잘 맞도록 응답을 편집할 수 있습니다. 향후에는 선택한 개인 정보에서 샘플 발언 집합을 업로드한 후 가장 가까운 가상 사용자 ID 버전을 찾을 수 있습니다. 모델을 다시 학습하고 사용자 지정하는 방식도 있습니다.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>이 서비스는 Zo와 같은 기존의 인텔리전트 에이전트를 지원하나요?

Zo, Cortana 및 프로젝트 개인 정보 채팅을 지원하는 서비스는 비슷한 기술을 일부 공유하지만 스택은 다릅니다. 이 서비스는 Zo 및 Cortana의 경험에서 얻은 학습을 통합하고 있습니다.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>이 서비스가 고객에게 좋지 않은 경험을 줄 수 있나요?

보다 풍부한 경험을 제공하기 위해 Personality Chat은 설명 데이터 시트에 있는 응답 이외의 응답을 생성할 수 있으며 모든 사용자 입력을 해석하려고 합니다. 따라서 응답이 컨텍스트에서 적절하지 않은 것처럼 보일 수 있습니다. 적절하지 않은 응답을 방지하고 Zo 같은 지능형 에이전트에서 기술 자료를 구축하는 데 도움을 주기 위해 다양한 컨트롤이 추가되었습니다. 기본적으로 Project Personality Chat은 인식된 사용자 의도에만 대응하도록 설정됩니다. Project Personality Chat이 사용자 환경에 적절한지 여부를 테스트할 수도 있습니다. 추가 학습이 필요한 부분이 확인되면 언제든지 의견을 보내주세요. 앞으로 고객에게 이 서비스를 사용할 경우, 실시간 사용자 상호 작용에서 발생하는 문제를 식별하기 위해 익명 로깅을 사용하는 것이 좋습니다.
