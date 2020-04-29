---
title: 다른 응용 프로그램과 통합-QnA Maker
description: QnA Maker은 채팅 봇과 같은 클라이언트 응용 프로그램과 Language Understanding (LUIS) 같은 기타 자연어 처리 서비스와 통합 됩니다.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804191"
---
# <a name="design-knowledge-base-for-client-applications"></a>클라이언트 응용 프로그램에 대 한 기술 자료 디자인

QnA Maker은 채팅 봇과 같은 클라이언트 응용 프로그램과 Language Understanding (LUIS) 같은 기타 자연어 처리 서비스와 통합 됩니다.

## <a name="integration-with-a-conversational-client"></a>대화형 클라이언트와의 통합

QnA Maker는 [Microsoft Bot Framework](https://dev.botframework.com/)와 같은 대화형 클라이언트 응용 프로그램과 통합 됩니다. QnA Maker 전송 된 텍스트는 정리 하거나 변환할 필요가 없습니다. QnA Maker 자연어를 수락 하 고 최상의 답을 반환 합니다.

## <a name="create-a-bot-without-writing-any-code"></a>코드를 작성 하지 않고 봇 만들기

기술 자료를 게시 한 후에는 [ **봇 만들기** ] 단추를 선택 하 여 **게시** 페이지에서 봇을 만듭니다. [봇 자습서](../Quickstarts/create-publish-knowledge-base.md) 를 사용 하 여 단추를 선택한 후 발생 하는 상황을 알아봅니다.

## <a name="providing-multi-turn-conversations"></a>다중 전환 대화 제공

Bot 클라이언트는 기술 자료에서 가장 잘 선택 된 답변을 제공 하 고, 답변이 멀티 턴 QnA 쌍의 일부인 경우 추가 작업 프롬프트를 제공할 수 있습니다. 기술 자료에 다중 턴 대화 질문과 대답 집합을 추가 하 [는 방법](../how-to/multiturn-conversation.md) 에 대해 알아봅니다.

## <a name="natural-language-processing"></a>자연어 처리

QnA Maker 자연어 처리를 사용 하는 질문을 처리 하는 동안 여러 기술 자료의 질문에 답변 하는 더 큰 시스템의 일부를 사용할 수도 있습니다. 특정 기술 자료를 얻기 전에 다른 인지 Language Understanding 서비스 (LUIS)와 QnA Maker 결합 하 여 자연어 처리를 제공할 수 있습니다. [LUIS 및 QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) 를 함께 사용 하는 시기 및 방법에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

QnA Maker에 대 한 개발 주기 [개념](development-lifecycle-knowledge-base.md) 을 알아봅니다.