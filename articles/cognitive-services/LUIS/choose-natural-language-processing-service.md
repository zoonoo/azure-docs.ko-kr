---
title: NLP Cognitive Services를 사용 하 여 대화를 보강 합니다.
titleSuffix: Azure Cognitive Services
description: Cognitive Services는 두 가지 자연어 처리 서비스, Language Understanding 및 QnA Maker를 제공 하며 각각 다른 용도로 사용 됩니다. 각 서비스를 사용 해야 하는 시기와 각 서비스를 어떻게 서로 어떻게 보완 하는지 이해 합니다.
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: 32159b37d3d1a8609181d81dc1a73f27177adb85
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818207"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>NLP (자연어 처리)와 Cognitive Services를 사용 하 여 봇 대화를 보강 합니다.

Cognitive Services는 두 가지 자연어 처리 서비스, [Language Understanding](what-is-luis.md) 및 [QnA Maker](../qnamaker/overview/overview.md)를 제공 하며 각각 다른 용도로 사용 됩니다. 각 서비스를 사용 해야 하는 시기와 각 서비스를 어떻게 서로 어떻게 보완 하는지 이해 합니다. 

NLP (자연어 처리)를 사용 하면 채팅 봇과 같은 클라이언트 응용 프로그램에서 자연어를 사용 하 여 사용자 작업을 수행할 수 있습니다. 사용자가 문장 또는 구를 입력 합니다. 사용자의 텍스트에는 잘못 된 문법, 맞춤법 및 문장 부호가 있을 수 있습니다. 인식 서비스는 사용자 문장을 통해 작업할 수 있으며 채팅 봇이 사용자에 게 도움을 주는 데 필요한 정보를 반환 합니다. 

## <a name="cognitive-services-with-nlp"></a>NLP를 사용 하 여 Cognitive Services

Language Understanding (LUIS) 및 QnA Maker NLP를 제공 합니다. 클라이언트 응용 프로그램은 자연어 텍스트를 제출 합니다. 서비스는 텍스트를 사용 하 여 처리 하 고 결과를 반환 합니다. 

## <a name="when-to-use-each-service"></a>각 서비스를 사용 하는 경우

Language Understanding (LUIS) 및 QnA Maker는 다른 문제를 해결 합니다. LUIS은 사용자 텍스트 (utterance)의 의도를 결정 하는 반면 QnA Maker은 사용자의 텍스트 (쿼리)에 대 한 대답을 결정 합니다. 

올바른 서비스를 선택 하려면 클라이언트 응용 프로그램에서 들어오는 사용자 텍스트와 클라이언트 응용 프로그램이 인식 서비스에서 가져와야 하는 정보를 이해 해야 합니다.

채팅 봇에서 텍스트 `How do I get to the Human Resources building on the Seattle North campus?`받는 경우 아래 차트를 사용 하 여 각 서비스가 텍스트와 함께 작동 하는 방식을 이해 합니다.

|부여|클라이언트 응용 프로그램 확인|
|--|--|
|LUIS|**사용자의 텍스트 의도를 결정** 합니다. 서비스에서 질문에 대 한 답을 반환 하지 않습니다. 예를 들어이 텍스트는 `FindLocation` 의도와 일치 하는 것으로 분류 됩니다.<br>|
|QnA Maker|사용자 지정 기술 자료에서 **질문에 대 한 답변을 반환** 합니다. 예를 들어이 텍스트는 `Get on the #9 bus and get off at Franklin street`의 정적 텍스트 대답을 사용 하 여 질문으로 결정 됩니다.|
|||

## <a name="when-do-you-use-luis"></a>LUIS를 사용 하는 경우 

채팅 봇에서 프로세스의 일부로 utterance의 의도를 알고 있어야 하는 경우 LUIS를 사용 합니다. 예제 텍스트 `How do I get to the Human Resources building on the Seattle North campus?`를 계속 사용 하 여 사용자가 위치를 찾을 수 있다는 것을 알고 있는 경우 utterance에 대 한 세부 정보 (엔터티 포함)를 교통 서버와 같은 다른 서비스에 전달 하 여 답변을 받을 수 있습니다. 

LUIS와 QnA Maker를 결합 하 여 의도를 확인할 필요가 없습니다. 

채팅 봇이 utterance 엔터티 (LUIS 사용)를 기반으로 텍스트를 처리 하 고 특정 정적 텍스트 대답 (QnA Maker 사용)을 찾는 데 필요한 경우 두 서비스를 결합할 수 있습니다.

## <a name="when-do-you-use-qna-maker"></a>QnA Maker를 사용 하는 경우 

답변에 대 한 정적 기술 자료가 있는 경우 QnA Maker를 사용 합니다. 이 기술 자료는 Pdf 및 Url과 같은 문서를 사용 하 여 빌드한 사용자의 요구에 맞게 사용자 지정 됩니다. 

예 utterance `How do I get to the Human Resources building on the Seattle North campus?`사용 하 여 게시 된 QnA Maker 서비스로 텍스트를 전송 하 고 최상의 답을 받습니다. 

질문에 대 한 답변을 결정 하기 위해 LUIS 및 QnA Maker를 결합할 필요가 없습니다.

채팅 봇이 utterance 엔터티 (LUIS 사용)를 기반으로 하는 텍스트를 처리 하 고 QnA Maker를 사용 하 여 답변을 찾는 데 필요한 경우 두 서비스를 결합할 수 있습니다.

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>기술 자료가 완전 하지 않은 경우 두 서비스 모두 사용

QnA Maker 기술 자료를 구축 하 고 있지만 주체 도메인이 변경 되는 것을 알고 있는 경우 (예: 시기 적절 한 정보) LUIS 및 QnA Maker 서비스를 결합할 수 있습니다. 이렇게 하면 기술 자료의 정보를 사용할 수 있지만 LUIS를 사용 하 여 사용자의 의도를 확인할 수도 있습니다. 클라이언트 응용 프로그램에 대 한 의도가 있으면 다른 소스에서 관련 정보를 요청할 수 있습니다. 

클라이언트 응용 프로그램은 점수에 대 한 LUIS 및 QnA Maker 응답을 모두 모니터링 해야 합니다. QnA Maker의 점수가 임의의 임계값 보다 낮은 경우 LUIS에서 반환 된 의도 및 엔터티 정보를 사용 하 여 타사 서비스에 정보를 전달 합니다.

`How do I get to the Human Resources building on the Seattle North campus?`예제 텍스트를 계속 사용 하면 QnA Maker가 낮은 신뢰도 점수를 반환 한다고 가정 합니다. LUIS `FindLocation`에서 반환 된 의도를 사용 하 고 `Human Resources building` 및 `Seattle North campus`와 같은 추출 된 엔터티를 사용 하 여이 정보를 다른 답변에 대 한 매핑 또는 검색 서비스로 보냅니다. 

유효성 검사를 위해 사용자에 게이 타사 답변을 제공할 수 있습니다. 사용자의 승인을 받은 후에는 QnA Maker로 돌아가서 정보를 추가 하 여 정보를 늘릴 수 있습니다. 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>채팅 봇에 추가 정보가 필요한 경우 두 서비스 모두 사용

서비스에서 제공 하는 것 보다 많은 정보가 채팅 봇에 필요한 경우 의사 결정 트리를 계속 사용 하려면 두 서비스를 모두 사용 하 고 클라이언트 응용 프로그램에서 두 응답을 모두 처리 합니다. 

Bot framework **[디스패치 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** 도구를 사용 하 여 두 서비스를 모두 사용 하는 프로세스를 빌드할 수 있습니다. 이 도구는 LUIS와 QnA Maker를 자식 앱으로 디스패치할 상위 LUIS 앱을 작성 합니다. 

이 유형의 채팅 봇을 구현하려면 또는 [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) [node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)에서 봇 작성기 샘플 인 NLP을 사용 합니다. 

## <a name="best-practices"></a>모범 사례

각 서비스에 대 한 모범 사례 구현:

* [LUIS](luis-concept-best-practices.md) 모범 사례
* [QnA Maker](../qnamaker/concepts/best-practices.md) 모범 사례

## <a name="see-also"></a>참고 항목

* [LUIS(Language Understanding)](what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [CLI 디스패치](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot framework 샘플](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure 봇 에뮬레이터](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot framework 웹 채팅](https://github.com/microsoft/BotFramework-WebChat)