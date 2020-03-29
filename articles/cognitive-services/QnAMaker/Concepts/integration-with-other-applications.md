---
title: 다른 응용 프로그램과 통합 - QnA 메이커
description: QnA Maker는 채팅 봇과 같은 클라이언트 응용 프로그램뿐만 아니라 언어 이해(LUIS)와 같은 다른 자연어 처리 서비스에 통합됩니다.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 38b275aa2b8cf5768a2bc95634a7ff4892893eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300522"
---
# <a name="design-knowledge-base-for-client-applications"></a>클라이언트 애플리케이션을 위한 설계 기술 자료

QnA Maker는 채팅 봇과 같은 클라이언트 응용 프로그램뿐만 아니라 언어 이해(LUIS)와 같은 다른 자연어 처리 서비스에 통합됩니다.

## <a name="integration-with-a-conversational-client"></a>대화형 클라이언트와의 통합

QnA Maker는 [Microsoft 봇 프레임워크와](https://dev.botframework.com/)같은 대화형 클라이언트 응용 프로그램과 통합됩니다. QnA Maker로 전송된 텍스트를 청소하거나 변환할 필요가 없습니다. QnA 메이커는 자연언어를 받아들이고 최고의 답을 반환합니다.

## <a name="create-a-bot-without-writing-any-code"></a>코드를 작성하지 않고 봇 만들기

기술 자료는 게시한 후 봇 만들기 단추를 선택하여 **게시** 페이지에서 **봇을** 만듭니다. 봇 [자습서를](../Quickstarts/create-publish-knowledge-base.md) 사용하여 단추를 선택한 후 어떤 일이 발생하는지 알아봅니다.

## <a name="providing-multi-turn-conversations"></a>멀티 턴 대화 제공

봇 클라이언트는 기술 기반에서 가장 잘 선택된 답변을 제공하며, 답이 다중 턴 QnA 집합의 일부인 경우 후속 프롬프트를 제공할 수 있습니다. 기술 자료에 다중 전환 대화 질문 및 답변 세트를 추가하는 [방법을](../how-to/multiturn-conversation.md) 알아봅니다.

## <a name="natural-language-processing"></a>자연어 처리

QnA Maker는 자연어 처리를 사용하는 질문을 처리하는 동안 여러 기술 자료의 질문에 답하는 더 큰 시스템의 일부로 사용할 수도 있습니다. QnA Maker를 다른 인지 서비스, 언어 이해(LUIS)와 결합하여 특정 기술 자료에 들어가기 전에 자연어 처리를 제공할 수 있습니다. [LUIS와 QnA Maker를](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) 함께 사용하는 시기와 방법에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

QnA Maker의 개발 주기 [개념에](development-lifecycle-knowledge-base.md) 대해 알아보십시오.