---
title: NLP 코그너티브 서비스를 사용하여 대화를 풍부하게 합니다.
titleSuffix: Azure Cognitive Services
description: 코그너티브 서비스는 언어 이해와 QnA Maker라는 두 가지 자연어 처리 서비스를 제공하며, 각 서비스는 서로 다른 목적을 가지고 있습니다. 각 서비스를 사용해야 하는 시기와 각 서비스를 어떻게 칭찬하는지 이해한다.
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: 32159b37d3d1a8609181d81dc1a73f27177adb85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73818207"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>자연어 처리(NLP)를 사용한 코그너티브 서비스 사용으로 봇 대화 강화

코그너티브 서비스는 언어 [이해와](what-is-luis.md) [QnA Maker라는](../qnamaker/overview/overview.md)두 가지 자연어 처리 서비스를 제공하며, 각각 다른 목적을 가지고 있습니다. 각 서비스를 사용해야 하는 시기와 각 서비스를 어떻게 칭찬하는지 이해한다. 

NLP(자연어 처리)를 사용하면 챗 봇과 같은 클라이언트 응용 프로그램이 자연어를 사용하여 사용자와 함께 작업할 수 있습니다. 사용자가 문장이나 구를 입력합니다. 사용자의 텍스트는 문법, 맞춤법 및 문장 부호가 잘못될 수 있습니다. 인지 서비스는 어쨌든 사용자 문장을 통해 작동 할 수 있습니다, 채팅 봇이 사용자를 돕기 위해 필요한 정보를 반환. 

## <a name="cognitive-services-with-nlp"></a>NLP를 갖춘 코그너티브 서비스

언어 이해 (LUIS) 및 QnA 메이커는 NLP를 제공합니다. 클라이언트 응용 프로그램은 자연어 텍스트를 제출합니다. 서비스는 텍스트를 가져와 처리하고 결과를 반환합니다. 

## <a name="when-to-use-each-service"></a>각 서비스를 이용하는 경우

언어 이해 (LUIS) 및 QnA 메이커는 다른 문제를 해결합니다. LUIS는 사용자 텍스트의 의도(발언이라고 함)를 결정하고 QnA Maker는 사용자의 텍스트(쿼리라고 함)에 대한 답을 결정합니다. 

올바른 서비스를 선택하려면 클라이언트 응용 프로그램에서 들어오는 사용자 텍스트와 클라이언트 응용 프로그램이 인지 서비스에서 얻는 데 필요한 정보를 이해해야 합니다.

챗봇이 텍스트를 `How do I get to the Human Resources building on the Seattle North campus?`수신하는 경우 아래 차트를 사용하여 각 서비스가 텍스트와 어떻게 작동하는지 이해합니다.

|서비스|클라이언트 응용 프로그램이 결정합니다.|
|--|--|
|LUIS|**텍스트의 사용자의 의도를 결정합니다** - 서비스가 질문에 대한 답변을 반환하지 않습니다. 예를 들어 이 텍스트는 `FindLocation` 의도와 일치하는 텍스트로 분류됩니다.<br>|
|QnA Maker|사용자 지정 기술 자료에서 **질문에 대한 답변을 반환합니다.** 예를 들어 이 텍스트는 `Get on the #9 bus and get off at Franklin street`의 정적 텍스트 대답이 있는 질문으로 결정됩니다.|
|||

## <a name="when-do-you-use-luis"></a>LUIS는 언제 사용하나요? 

채팅 봇에서 프로세스의 일부로 발언의 의도를 알아야 할 때 LUIS를 사용합니다. 예제 텍스트를 `How do I get to the Human Resources building on the Seattle North campus?`계속 사용하면 사용자의 의도를 알고 위치를 찾으면 발화에 대한 세부 정보(엔터티로 뽑아낸)를 교통 서버와 같은 다른 서비스에 전달하여 답을 얻을 수 있습니다. 

의도를 결정하기 위해 LUIS와 QnA Maker를 결합할 필요가 없습니다. 

채팅 봇이 LUIS 사용(LUIS 사용)에 따라 텍스트를 처리하고 QnA Maker를 사용하여 특정 정적 텍스트 답을 찾아야 하는 경우 이 발언에 대해 두 서비스를 결합할 수 있습니다.

## <a name="when-do-you-use-qna-maker"></a>QnA 메이커는 언제 사용하나요? 

답변의 정적 지식 기반이 있는 경우 QnA Maker를 사용합니다. 이 기술 자료는 사용자의 요구에 맞게 사용자 지정되며, PDF 및 URL과 같은 문서를 사용하여 작성되었습니다. 

예제 발언을 계속하면 `How do I get to the Human Resources building on the Seattle North campus?`텍스트를 쿼리로 게시된 QnA Maker 서비스로 보내고 최상의 답변을 받습니다. 

LUIS와 QnA Maker를 결합하여 질문에 대한 답을 결정할 필요가 없습니다.

채팅 봇이 LUIS 사용(LUIS 사용)에 따라 텍스트를 처리하고 QnA Maker를 사용하여 답을 찾아야 하는 경우 이 발언에 대해 두 가지 서비스를 결합할 수 있습니다.

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>기술 자료가 불완전할 때 두 서비스 모두 사용

QnA Maker 기술 기반을 구축하고 있지만 주제 도메인이 변경되고 있음을 알고 있는 경우(예: 시기 적합한 정보) LUIS 및 QnA Maker 서비스를 결합할 수 있습니다. 이렇게 하면 기술 자료의 정보를 사용할 수 있을 뿐만 아니라 LUIS를 사용하여 사용자의 의도를 확인할 수도 있습니다. 클라이언트 응용 프로그램에 의도가 있으면 다른 소스에서 관련 정보를 요청할 수 있습니다. 

클라이언트 응용 프로그램은 LUIS 및 QnA Maker 응답을 모두 모니터링하여 점수를 확인해야 합니다. QnA Maker의 점수가 임의 임계값 미만이면 LUIS에서 반환된 의도 및 엔터티 정보를 사용하여 정보를 타사 서비스로 전달합니다.

예제 텍스트를 `How do I get to the Human Resources building on the Seattle North campus?`계속 사용하면 QnA Maker에서 낮은 신뢰도 점수를 반환한다고 가정합니다. LUIS에서 반환된 의도 `FindLocation` 및 `Human Resources building` 및 및 에서 `Seattle North campus`추출된 엔터티를 사용하여 이 정보를 매핑 또는 검색 서비스로 전송하여 다른 답변을 찾습니다. 

유효성 검사를 위해 사용자에게 이 타사 답변을 제출할 수 있습니다. 사용자의 승인을 받으면 QnA Maker로 돌아가 지식을 키우기 위해 정보를 추가할 수 있습니다. 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>챗봇에 더 많은 정보가 필요할 때 두 서비스 모두 사용

챗봇이 제공하는 서비스보다 더 많은 정보가 필요한 경우 의사 결정 트리를 계속 진행하려면 서비스를 모두 사용하고 클라이언트 응용 프로그램에서 두 응답을 모두 처리합니다. 

봇 프레임워크 **[디스패치 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** 도구를 사용하여 두 서비스 모두에서 작업하는 프로세스를 빌드할 수 있습니다. 이 도구는 LUIS와 QnA Maker 간에 자식 앱으로 디스패치하는 의도의 최상위 LUIS 앱을 빌드합니다. 

[C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) 또는 [Node.js에서](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch) **디스패치와 함께 봇 빌더 샘플인 NLP를**사용하여 이러한 유형의 챗봇을 구현합니다. 

## <a name="best-practices"></a>모범 사례

각 서비스에 대한 모범 사례를 구현합니다.

* [LUIS](luis-concept-best-practices.md) 모범 사례
* [QnA 메이커](../qnamaker/concepts/best-practices.md) 모범 사례

## <a name="see-also"></a>참조

* [LUIS(Language Understanding)](what-is-luis.md)
* [QnA 메이커](../qnamaker/overview/overview.md)
* [디스패치 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [봇 프레임워크 샘플](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure 봇 서비스](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure 봇 에뮬레이터](https://github.com/Microsoft/BotFramework-Emulator)
* [봇 프레임워크 웹 채팅](https://github.com/microsoft/BotFramework-WebChat)