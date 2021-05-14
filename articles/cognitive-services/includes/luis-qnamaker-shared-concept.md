---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: b11a6082a5c86526e04a8b78db30650c37d467fc
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108792705"
---
Cognitive Services는 두 가지 자연어 처리 서비스인 [Language Understanding](../luis/what-is-luis.md)과 [QnA Maker](../qnamaker/overview/overview.md)를 제공하며 각 작업은 서로 다른 용도로 사용됩니다. 각 서비스를 언제 사용해야 하며, 각 서비스가 서로 어떻게 보완되는지 이해합니다.

NLP(자연어 처리)를 사용하면 채팅 봇과 같은 클라이언트 애플리케이션에서 자연어를 사용하여 사용자와의 상호작용이 가능합니다. 사용자가 문장 또는 구를 입력합니다. 사용자의 텍스트에는 잘못된 문법, 맞춤법 및 문장 부호가 있을 수 있습니다. Cognitive Services는 이러한 사용자의 문장을 분석하여 채팅 봇이 사용자에게 도움을 줄 수 있는 정보를 반환합니다.

## <a name="cognitive-services-with-nlp"></a>NLP를 사용하는 Cognitive Services

LUIS(Language Understanding) 및 QnA Maker는 NLP를 제공합니다. 클라이언트 애플리케이션은 자연어 텍스트를 제출합니다. 서비스는 텍스트를 처리하고 결과를 반환합니다.

## <a name="when-to-use-each-service"></a>각 서비스를 사용하는 경우

LUIS(Language Understanding) 및 QnA Maker는 서로 다른 이슈를 해결합니다. LUIS는 사용자 텍스트(발화)의 의도를 결정하는 반면 QnA Maker는 사용자 텍스트(쿼리)에 대한 답변을 결정합니다.

올바른 서비스를 선택하려면 클라이언트 애플리케이션에서 들어오는 사용자 텍스트와 클라이언트 애플리케이션이 인식 서비스에서 가져와야 하는 정보를 이해해야 합니다.

채팅 봇에서 텍스트 `How do I get to the Human Resources building on the Seattle North campus?`를 수신하는 경우 아래 차트를 통해 각 서비스가 텍스트와 함께 작동하는 방식을 이해합니다.

|서비스|클라이언트 애플리케이션의 답변 영역|
|--|--|
|LUIS|**사용자의 텍스트 의도를 결정** 합니다. 이 서비스는 질문에 대한 답변을 반환하지 않습니다. 예를 들어 이 텍스트는 `FindLocation` 의도와 일치하는 것으로 분류됩니다.<br>|
|QnA Maker|사용자 지정 기술 자료의 **질문에 대한 답변을 반환** 합니다. 예를 들어 이 텍스트는 `Get on the #9 bus and get off at Franklin street`와 같이 고정된 텍스트 답변이 있는 질문으로 판단됩니다.|
|||

> [!div class="mx-imgBorder"]
> ![LUIS 및 QnA Maker를 언제 사용해야 하는지 보여주는 인포그래픽](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>LUIS를 사용하는 경우

채팅 봇에서 프로세스의 일부로 발화의 의도를 알아야 할 때 LUIS를 사용합니다. 예제 텍스트 `How do I get to the Human Resources building on the Seattle North campus?`의 경우 사용자가 위치를 찾고 있다는 것을 알게 되면, 발화에 대한 세부 정보(엔터티 포함)를 교통 서버와 같은 다른 서비스에 전달하여 답변을 얻을 수 있습니다.

의도를 확인하기 위해 LUIS와 QnA Maker를 결합할 필요는 없습니다.

만약 채팅 봇이 의도와 엔터티에 따라 텍스트를 처리(LUIS 사용)해야 할 뿐 아니라 특별한 고정 답변도 찾아야 한다면(QnA Maker 사용) 두 서비스를 결합할 수 있습니다.

## <a name="when-do-you-use-qna-maker"></a>QnA Maker를 사용하는 경우

답변에 대한 정적 기술 자료가 있는 경우 QnA Maker를 사용합니다. 이 기술 자료는 사용자의 요구에 맞게 사용자 지정되며, PDF 및 URL과 같은 문서를 사용하여 작성되었습니다.

게시된 QnA Maker 서비스에 예제 발화 `How do I get to the Human Resources building on the Seattle North campus?` 텍스트를 쿼리로 보내 최상의 답변을 받습니다.

답변을 받기 위해 LUIS와 QnA Maker를 결합할 필요는 없습니다.

만약 채팅 봇이 의도와 엔터티에 따라 텍스트를 처리(LUIS 사용)해야 할 뿐 아니라 답변도 찾아야 한다면(QnA Maker 사용) 두 서비스를 결합할 수 있습니다.

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>기술 자료가 완전하지 않은 경우 두 서비스 모두 사용

QnA Maker 기술 자료를 구축하고 있지만 주제 영역이 변경되는 것을 알고 있는 경우(예: 시간 관련 정보) LUIS와 QnA Maker 서비스를 결합할 수 있습니다. 이렇게 하면 기술 자료의 정보를 사용할 수 있지만 LUIS를 사용하여 사용자의 의도를 확인할 수도 있습니다. 클라이언트 애플리케이션에 의도가 있으면 다른 소스에서 관련 정보를 요청할 수 있습니다.

클라이언트 애플리케이션은 점수에 대한 LUIS 및 QnA Maker 응답을 모두 모니터링할 필요가 있을 수 있습니다. QnA Maker의 점수가 임의의 임계값보다 낮은 경우 LUIS에서 반환된 의도 및 엔터티 정보를 사용하여 타사 서비스에 정보를 전달합니다.

예제 텍스트 `How do I get to the Human Resources building on the Seattle North campus?`의 경우 QnA Maker가 낮은 신뢰 점수를 반환한다고 가정합니다. LUIS에서 반환된 의도 `FindLocation` 및 `Human Resources building`, `Seattle North campus`와 같이 예제에서 추출한 엔터티를 사용하여 이 정보를 매핑 또는 검색 서비스에 보내 다른 답변을 받을 수 있습니다.

유효성 검사를 위해 사용자에게 타사의 답변을 제공할 수 있습니다. 사용자의 승인을 받은 후에는 QnA Maker로 돌아가서 정보를 추가하여 정보를 늘릴 수 있습니다.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>채팅 봇에 추가 정보가 필요한 경우 두 서비스 모두 사용

서비스에서 제공하는 것보다 많은 정보가 채팅 봇에 필요한 경우 의사 결정 트리를 계속 사용하려면 두 서비스를 모두 사용하고 두 응답을 클라이언트 애플리케이션에서 처리합니다.

Bot framework **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** 도구를 사용하여 두 서비스를 모두 사용하는 프로세스를 빌드할 수 있습니다. 이 도구는 자식 앱으로 LUIS와 QnA Maker 간에 디스패치할 상위 LUIS 앱을 빌드합니다. LUIS, QnA Maker 및 Bot framework와의 통합에 대해 [자세히 알아보세요](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs).

[C#](https://github.com/microsoft/BotBuilder-Samples/tree/main/samples/csharp_dotnetcore/14.nlp-with-orchestrator) 또는 [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/main/samples/javascript_nodejs/14.nlp-with-orchestrator)를 지원하는 봇 작성기 샘플 **NLP with dispatch** 를 사용하여 이와 같은 유형의 채팅 봇을 구현하세요.

## <a name="best-practices"></a>모범 사례

각 서비스에 대한 모범 사례 구현:

* [LUIS](../luis/luis-concept-best-practices.md) 모범 사례
* [QnA Maker](../qnamaker/concepts/best-practices.md) 모범 사례

## <a name="see-also"></a>참조

* [언어 이해(LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [디스패치 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot framework 샘플](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure 봇 서비스](/azure/bot-service/bot-service-overview-introduction)
* [Azure 봇 에뮬레이터](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot framework 웹 채팅](https://github.com/microsoft/BotFramework-WebChat)
