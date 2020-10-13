---
title: 앱 계획-QnA Maker
description: QnA Maker 앱을 계획 하는 방법을 알아봅니다. QnA Maker 작동 하 고 다른 Azure 서비스와 상호 작용 하는 방법 및 몇 가지 기술 자료 개념을 이해 합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: 84e4d6907c9036503f43cd607b54577fd3d97444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776938"
---
# <a name="plan-your-qna-maker-app"></a>QnA Maker 앱 계획

QnA Maker 앱을 계획 하려면 QnA Maker 작동 하 고 다른 Azure 서비스와 상호 작용 하는 방법을 이해 해야 합니다. 또한 기술 자료 개념을 확실 하 게 이해 해야 합니다.

## <a name="azure-resources"></a>Azure 리소스

QnA Maker를 사용 하 여 만든 각 [Azure 리소스](azure-resources.md#resource-purposes) 는 특정 목적이 있습니다. 각 리소스에는 고유한 용도, 제한 및 [가격 책정 계층이](azure-resources.md#pricing-tier-considerations)있습니다. 이러한 리소스의 기능을 이해 하 여 계획 프로세스에 해당 정보를 사용할 수 있도록 하는 것이 중요 합니다.

| 리소스 | 목적 |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) 리소스 | 작성 및 쿼리 예측 |
| [Cognitive Search](azure-resources.md#cognitive-search-resource) 리소스 | 데이터 저장 및 검색 |
| [App Service 리소스 및 앱 계획 서비스](azure-resources.md#app-service-and-app-service-plan) 리소스 | 예측 엔드포인트 쿼리 |
| [Application Insights](azure-resources.md#application-insights) 리소스 | 쿼리 예측 원격 분석 |

### <a name="resource-planning"></a>리소스 계획

`F0`각 리소스의 무료 계층은 작동 하며 작성 및 쿼리 예측 환경을 모두 제공할 수 있습니다. 이 계층을 사용 하 여 작성 및 쿼리 예측을 학습할 수 있습니다. 프로덕션 또는 라이브 시나리오로 전환 하는 경우 리소스 선택을 다시 계산 합니다.

#### <a name="qna-maker-resource"></a>QnA Maker 리소스

단일 QnA Maker 리소스가 둘 이상의 기술 자료를 호스트할 수 있습니다. 기술 자료의 수는 Cognitive Search 가격 책정 계층의 지원 되는 인덱스 수량에 따라 결정 됩니다. [인덱스와 기술 자료의 관계](azure-resources.md#index-usage)에 대해 자세히 알아보세요.

#### <a name="knowledge-base-size-and-throughput"></a>기술 자료 크기 및 처리량

실제 앱을 빌드할 때 기술 자료의 크기와 예상 되는 쿼리 예측 요청에 대 한 충분 한 리소스를 계획 합니다.

기술 자료 크기는 다음에 의해 제어 됩니다.
* [Cognitive Search 리소스](../../../search/search-limits-quotas-capacity.md) 가격 책정 계층 제한
* [QnA Maker 제한](../limits.md)

기술 자료 쿼리 예측 요청은 웹 앱 계획 및 웹 앱에 의해 제어 됩니다. 가격 책정 계층을 계획 하려면 [권장 설정](azure-resources.md#recommended-settings) 을 참조 하세요.

### <a name="resource-sharing"></a>리소스 공유

이러한 리소스 중 일부를 이미 사용 중인 경우 리소스 공유를 고려할 수 있습니다. 리소스 공유를 고급 시나리오로 이해 하는 것과 [공유할 수](azure-resources.md#share-services-with-qna-maker) 있는 리소스를 확인 합니다.

동일한 QnA Maker 리소스에서 만든 모든 기술 자료가 동일한 **테스트** 쿼리 예측 끝점을 공유 합니다.

### <a name="understand-the-impact-of-resource-selection"></a>리소스 선택의 영향 이해

적절 한 리소스 선택은 기술 자료가 쿼리 예측에 성공적으로 응답 하는 것을 의미 합니다.

기술 자료가 제대로 작동 하지 않는 경우 일반적으로 부적절 한 리소스 관리의 문제입니다.

리소스를 부적절 하 게 선택 하려면 [변경 해야](azure-resources.md#when-to-change-a-pricing-tier)하는 리소스를 결정 하기 위해 조사가 필요 합니다.

## <a name="knowledge-bases"></a>기술 자료

기술 자료가 QnA Maker 리소스에 직접 연결 됩니다. 쿼리 예측 요청에 응답 하는 데 사용 되는 QnA (질문 및 답변) 쌍을 포함 합니다.

### <a name="language-considerations"></a>언어 관련 고려 사항

QnA Maker 리소스에 대해 만들어진 첫 번째 기술 자료는 리소스의 언어를 설정 합니다. QnA Maker 리소스에 대해 언어가 하나만 있을 수 있습니다.

쿼리 예측 끝점에 쿼리를 보내기 전에 언어를 사용 하 여 QnA Maker 리소스를 구조화 하거나 [번역기](../../translator/translator-info-overview.md) 를 사용 하 여 다른 언어에서 기술 자료의 언어로 쿼리를 변경할 수 있습니다.

### <a name="ingest-data-sources"></a>데이터 원본 수집

다음 수집 [데이터 원본](knowledge-base.md) 중 하나를 사용 하 여 기술 자료를 만들 수 있습니다.

* 공용 URL
* 비공개 SharePoint URL
* 파일

수집 프로세스는 [지원 되는 콘텐츠 형식을](content-types.md) markdown로 변환 합니다. *답변* 의 모든 편집은 markdown를 사용 하 여 수행 됩니다. 기술 자료를 만든 후에는 [서식 있는 텍스트 작성](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)기능을 사용 하 여 QnA Maker 포털에서 [QnA 쌍](question-answer-set.md) 을 편집할 수 있습니다.

### <a name="data-format-considerations"></a>데이터 형식 고려 사항

QnA 쌍의 최종 형식은 markdown 이므로 [markdown 지원을](../reference-markdown-format.md)이해 하는 것이 중요 합니다.

연결 된 이미지는 QnA Maker 포털 또는 클라이언트 응용 프로그램의 테스트 창에 표시 되는 공용 URL에서 사용할 수 있어야 합니다. QnA Maker는 이미지를 포함 하 여 콘텐츠에 대 한 인증을 제공 하지 않습니다.

### <a name="bot-personality"></a>Bot

[Chit-채팅](../how-to/chit-chat-knowledge-base.md)을 통해 기술 자료에 봇 개성을 추가 합니다. 이 특성은 *전문가* 와 *친숙*한 특정 대화형 톤에 제공 된 답변을 통해 제공 됩니다. 이 chit-채팅은 사용자가 추가, 편집 및 제거 하는 데 사용할 수 있는 대화 집합으로 제공 됩니다.

봇이 기술 자료에 연결 하는 경우 bot를 권장 합니다. 다른 서비스에도 연결 하는 경우에도 기술 자료에서 chit-채팅을 사용 하도록 선택할 수 있지만, 사용자가 사용 하기에 적합 한 아키텍처 디자인 인지 확인 하기 위해 봇 서비스의 상호 작용 방식을 검토 해야 합니다.

### <a name="conversation-flow-with-a-knowledge-base"></a>기술 자료가 있는 대화 흐름

일반적으로 대화 흐름은 또는와 같은 사용자의 인사말으로 시작 `Hi` `Hello` 합니다. 기술 자료는와 같은 일반적인 답변을 사용 하 여 답변할 수 있으며 `Hi, how can I help you` , 대화를 계속 하는 추가 작업 프롬프트를 제공할 수도 있습니다.

사용자가 봇을 사용 하는 방법을 인식 하 고 대화의 봇에서 중단 되지 않도록 하기 위해 루프를 염두에 두면 대화형 흐름을 디자인 해야 합니다. [추가 작업 프롬프트](../how-to/multiturn-conversation.md) 에는 대화형 흐름을 허용 하는 QnA 쌍 간 연결이 제공 됩니다.

### <a name="authoring-with-collaborators"></a>공동 작업자를 사용 하 여 제작

협력자는 기술 자료 응용 프로그램의 전체 개발 스택을 공유 하거나 기술 자료를 제작 하는 것으로 제한 될 수 있는 다른 개발자 일 수 있습니다.

기술 자료 제작에서는 Azure Portal에 적용 하는 여러 [역할 기반 액세스 권한을](../how-to/collaborate-knowledge-base.md) 통해 협력자의 기능 범위를 제한 합니다.

## <a name="integration-with-client-applications"></a>클라이언트 응용 프로그램과 통합

[클라이언트 응용 프로그램과](integration-with-other-applications.md) 의 통합은 예측 런타임 끝점에 쿼리를 전송 하 여 수행 됩니다. 쿼리는 QnA Maker의 웹 앱 끝점에 대 한 SDK 또는 REST 기반 요청을 사용 하 여 특정 기술 자료에 전송 됩니다.

클라이언트 요청을 올바르게 인증 하려면 클라이언트 응용 프로그램에서 올바른 자격 증명과 기술 자료 ID를 전송 해야 합니다. Azure Bot Service 사용 하는 경우 Azure Portal에서 봇 구성의 일부로 이러한 설정을 구성 합니다.

### <a name="conversation-flow-in-a-client-application"></a>클라이언트 응용 프로그램의 대화 흐름

Azure 봇과 같은 [클라이언트 응용 프로그램](integration-with-other-applications.md)의 대화 흐름에는 기술 자료와 상호 작용 하기 전후에 기능이 필요할 수 있습니다.

클라이언트 응용 프로그램은 추가 기능을 제공 하 여 추가 요청을 처리 하거나 chit-chit를 포함 하 여 대화 흐름을 지원 하나요? 그렇다면 이러한 작업을 조기에 설계 하 고 클라이언트 응용 프로그램 쿼리가 다른 서비스 또는 기술 자료로 전송 될 때 올바르게 처리 되는지 확인 합니다.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>QnA Maker와 Language Understanding 간 디스패치 (LUIS)

클라이언트 응용 프로그램은 여러 기능을 제공할 수 있으며,이 중 하나는 기술 자료에서 답변 합니다. 다른 기능은 여전히 대화형 텍스트를 이해 하 고 그에 대 한 의미를 추출 해야 합니다.

일반적인 클라이언트 응용 프로그램 아키텍처는 QnA Maker와 [Language Understanding (LUIS)](../../LUIS/what-is-luis.md) 를 함께 사용 하는 것입니다. LUIS는 다른 서비스를 비롯 하 여 모든 쿼리에 대 한 텍스트 분류 및 추출을 제공 합니다. QnA Maker는 기술 자료에서 답변을 제공 합니다.

이러한 [공유 아키텍처](../choose-natural-language-processing-service.md) 시나리오에서 두 서비스 간의 디스패치는 Bot Framework의 [디스패치](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) 도구를 통해 수행 됩니다.

### <a name="active-learning-from-a-client-application"></a>클라이언트 응용 프로그램에서의 활성 학습

QnA Maker는 답변에 대 한 대체 질문을 제안 하 여 기술 자료를 개선 하는 데 _활성 학습_ 을 사용 합니다. 클라이언트 응용 프로그램은이 [활성 학습](active-learning-suggestions.md)의 일부를 담당 합니다. 클라이언트 응용 프로그램은 대화형 프롬프트를 통해 기술 자료가 사용자에 게 유용 하지 않은 대답을 반환 하는 것을 확인할 수 있으며 더 나은 답을 확인할 수 있습니다. 클라이언트 응용 프로그램은 예측 품질을 향상 시키기 위해 [해당 정보를 기술 자료로 다시 보내야](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) 합니다.

### <a name="providing-a-default-answer"></a>기본 대답 제공

기술 자료가 대답을 찾지 못하면 _기본 답_을 반환 합니다. 이 응답은 QnA Maker 포털 또는 [api](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)의 **설정** 페이지에서 구성할 수 있습니다.

이 기본 대답은 Azure bot 기본 대답과 다릅니다. 구성 설정의 일부로 Azure Portal에서 Azure bot의 기본 대답을 구성 합니다. 점수 임계값이 충족 되지 않는 경우 반환 됩니다.

## <a name="prediction"></a>예측

예측은 기술 자료의 응답 이며 대답 보다 많은 정보를 포함 합니다. 쿼리 예측 응답을 가져오려면 [Generateanswer API](query-knowledge-base.md)를 사용 합니다.

### <a name="prediction-score-fluctuations"></a>예측 점수 변동

점수는 다음과 같은 여러 가지 요인에 따라 변경 될 수 있습니다.

* 속성을 사용 하 여 [Generateanswer](query-knowledge-base.md) 에 응답 하 여 요청 된 대답 수 `top`
* 사용 가능한 다양 한 대체 질문
* 메타 데이터 필터링
* `test`또는 `production` 기술 자료로 전송 되는 쿼리

[2 단계 대답 순위](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)는 다음과 같습니다.
- Cognitive Search 우선 순위입니다. Cognitive Search에서 가장 적합 한 답변을 반환 하 고 QnA Maker ranker에 전달할 수 있을 만큼 충분 한 _응답_ 수를 설정 합니다.
- QnA Maker-두 번째 순위입니다. 기능화 및 기계 학습을 적용 하 여 최상의 답을 확인 하세요.

### <a name="service-updates"></a>서비스 업데이트

[최신 런타임 업데이트](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) 를 적용 하 여 서비스 업데이트를 자동으로 관리 합니다.

### <a name="scaling-throughput-and-resiliency"></a>크기 조정, 처리량 및 복원 력

크기 조정, 처리량 및 복원 력은 [Azure 리소스](../how-to/set-up-qnamaker-service-azure.md), 해당 가격 책정 계층 및 모든 주변 아키텍처 (예: [Traffic manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager))에 의해 결정 됩니다.

### <a name="analytics-with-application-insights"></a>Application Insights 분석

기술 자료에 대 한 모든 쿼리는 Application Insights에 저장 됩니다. [가장 많이](../how-to/get-analytics-knowledge-base.md) 사용 되는 쿼리를 사용 하 여 메트릭을 파악 합니다.

## <a name="development-lifecycle"></a>개발 수명 주기

기술 자료의 [개발 수명 주기](development-lifecycle-knowledge-base.md) 는 진행 중인 기술 자료 편집, 테스트 및 게시입니다.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker 쌍의 기술 자료 개발

[QnA 쌍](question-answer-set.md) 은 클라이언트 응용 프로그램 사용에 따라 설계 및 개발 해야 합니다.

각 쌍에는 다음이 포함 될 수 있습니다.
* 메타 데이터-데이터의 원본, 내용, 형식 및 용도에 대 한 추가 정보로 QnA 쌍에 태그를 지정할 수 있도록 쿼리할 때 필터링 할 수 있습니다.
* 후속 작업 프롬프트-사용자가 올바른 답변을 받을 수 있도록 기술 자료를 통해 경로를 확인 하는 데 도움이 됩니다.
* 대체 질문-검색을 다양 한 형식의 질문에 대 한 답변에 일치 시킬 수 있도록 하는 것이 중요 합니다. [활성 학습 제안은](active-learning-suggestions.md) 대체 질문으로 전환 됩니다.

### <a name="devops-development"></a>DevOps 개발

DevOps 파이프라인에 삽입할 기술 자료를 개발 하려면 [일괄 처리 테스트](../quickstarts/batch-testing.md)중에 기술 자료가 격리 되어야 합니다.

기술 자료는 Cognitive Search 인덱스를 QnA Maker 리소스의 다른 모든 기술 자료와 공유 합니다. 기술 자료가 파티션에 의해 격리 되는 동안 인덱스를 공유 하면 게시 된 기술 자료와 비교할 때 점수가 달라질 수 있습니다.

및 기술 자료에 대해 _동일한 점수_ 를 갖도록 `test` `production` QnA Maker 리소스를 단일 기술 자료로 격리 합니다. 이 아키텍처에서 리소스는 격리 된 일괄 처리 테스트 만큼만 살고 있으면 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스](../how-to/set-up-qnamaker-service-azure.md)
* [질문 및 대답 쌍](question-answer-set.md)