---
title: 용어 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 서비스에는 서비스 관련 용어와 함께 Machine Learning의 많은 새로운 용어 및 자연어 처리 기능이 있습니다. 이 목록은 그러한 용어를 이해하는 데 도움이 됩니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 832bf85087b706451a6ee58c538d7a163ef18203
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079945"
---
# <a name="glossary-for-qna-maker-knowledge-base-and-service"></a>QnA Maker 기술 자료 및 서비스에 대한 용어집

## <a name="qna-maker-service"></a>QnA Maker Service
QnA Maker 서비스는 QnA Maker 사용을 시작하기 위한 필수 조건입니다. QnA Maker 계층을 구매하면 Azure 구독에 기술 자료를 만들고 관리하기 위한 리소스가 설정됩니다. 각 QnA Maker 사용자 계정은 Azure 구독에 여러 개의 QnA Maker 서비스를 만들 수 있습니다.

## <a name="knowledge-base"></a>기술 자료
기술 자료는 QnA Maker를 통해 생성, 유지 관리 및 사용되는 질문 및 답변의 리포지토리입니다. 각 QnA Maker 계층을 여러 기술 자료에 사용할 수 있습니다.

## <a name="endpoint"></a>엔드포인트
응용 프로그램, 일반적으로 챗봇에 통합할 수 있는 기술 자료 콘텐츠를 제공하는 REST 기반 HTTP 엔드포인트입니다. 

## <a name="test-knowledge-base"></a>테스트 기술 자료
기술 자료에는 두 가지 상태(테스트 및 게시됨)가 있습니다. 테스트 기술 자료는 응답의 정확성 및 완전성을 위해 편집, 저장 및 테스트 중인 버전입니다. 테스트 기술 자료의 변경 내용은 응용 프로그램/챗봇의 최종 사용자에게 영향을 주지 않습니다.

## <a name="published-knowledge-base"></a>게시된 기술 자료
기술 자료에는 두 가지 상태(테스트 및 게시됨)가 있습니다.  게시된 기술 자료는 챗봇/응용 프로그램에서 사용되는 버전입니다. 기술 자료를 게시하는 작업은 테스트 기술 자료 콘텐츠를 게시된 기술 자료 버전에 넣습니다. 게시된 기술 자료는 응용 프로그램이 엔드포인트를 통해 사용하는 버전이므로, 콘텐츠가 올바르고 잘 테스트되었는지 신중하게 확인해야 합니다.

## <a name="query"></a>쿼리
사용자 쿼리는 기술 자료에 대한 최종 사용자 또는 테스터의 질문입니다. 쿼리가 자연어 형식이거나 질문을 나타내는 몇 개의 키워드인 경우도 있습니다.

## <a name="response"></a>response
응답은 지정된 사용자 쿼리에 가장 잘 일치하는 항목을 기준으로 기술 자료에서 검색된 답변입니다.

## <a name="confidence-score"></a>신뢰도 점수
응답의 신뢰도 점수는 0에서 100 사이의 숫자 값이며, 100은 사용자 쿼리와 기술 자료의 질문이 정확히 일치하는 쿼리이고, 제공된 응답이 지정된 사용자 쿼리에 대한 적절한 응답임을 나타냅니다. 답변은 일반적으로 신뢰도 점수에 의해 순위가 지정되며 신뢰도 점수가 더 높은 답변이 기본 응답으로 제공됩니다.
