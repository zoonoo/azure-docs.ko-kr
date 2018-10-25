---
title: 기술 자료 수명 주기 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 모델 변경, 발언 예제, 게시 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 좋은 사례를 학습합니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: ec5e9f92114e9bae1aaa840a1d02f5a42b2fd7bf
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857174"
---
# <a name="knowledge-base-lifecycle"></a>기술 자료 수명 주기
QnA Maker는 모델 변경, 발언 예제, 게시 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 좋은 사례를 학습합니다. 

![제작 주기](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기
QnA Maker KB(기술 자료) 엔드포인트는 KB의 콘텐츠를 기반으로 사용자 쿼리와 가장 일치하는 답변을 제공합니다. 기술 자료 만들기는 질문, 답변, 관련 메타데이터의 콘텐츠 리포지토리를 설정하는 일회성 작업입니다. FAQ 페이지, 제품 설명서 또는 구조화된 Q-A 쌍 같은 기존 콘텐츠를 크롤링하여 기술 자료를 만들 수 있습니다. [기술 자료 만들기](../How-To/create-knowledge-base.md)에 대해 알아보세요.

## <a name="testing-and-updating-the-knowledge-base"></a>기술 자료 테스트 및 업데이트
편집을 통해 또는 자동 추출을 통해 기술 자료에 콘텐츠가 채워지면 기술 자료를 즉시 테스트에 사용할 수 있습니다. **테스트** 패널을 통해 일반적인 사용자 쿼리를 입력하고 반환된 응답이 예상과 일치하고 신뢰성 점수가 충분한지 확인하여 테스트를 수행할 수 있습니다. 낮은 신뢰성 점수를 회복하기 위해 대체 질문을 추가할 수 있습니다. 또한 쿼리에서 "KB에 일치하는 항목이 없습니다" 기본 응답을 반환하면 새 답변을 추가할 수 있습니다. 사용자가 결과에 만족할 때까지 이 조밀한 테스트-업데이트 반복이 계속됩니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

큰 KB의 경우 generateAnswer API를 사용하여 테스트를 자동화할 수 있습니다. 

## <a name="publish-the-knowledge-base"></a>기술 자료 게시
기술 자료 테스트를 마쳤으면 기술 자료를 게시할 수 있습니다. 게시는 테스트를 마친 최신 버전의 기술 자료를 **게시된** 기술 자료를 나타내는 전용 Azure Search 인덱스에 푸시합니다. 또한 응용 프로그램 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이러한 방식으로, 테스트 버전의 기술 자료 변경 내용은 프로덕션 응용 프로그램에 있을지도 모르는 게시된 버전에 영향을 주지 않습니다.

각각의 기술 자료를 테스트 대상으로 지정할 수 있습니다. API를 사용하면 generateAnswer 호출에서 `isTest=true` 플래그가 있는 테스트 버전의 기술 자료를 대상으로 지정할 수 있습니다.

[기술 자료 게시](../How-To/publish-knowledge-base.md) 방법을 알아보세요.

## <a name="monitor-usage"></a>사용량 모니터링
서비스의 채팅 로그를 기록하려면 [QnA Maker 서비스를 만들 때](../How-To/set-up-qnamaker-service-azure.md) Application Insights를 사용하도록 설정해야 합니다.

다양한 서비스 사용 분석 자료를 얻을 수 있습니다. Application Insights를 사용하여 [QnA Maker 서비스에 대한 분석](../How-To/get-analytics-knowledge-base.md) 자료를 얻는 방법을 자세히 알아보세요.

분석 자료의 내용에 따라 [기술 자료를 적절하게 업데이트](../How-To/edit-knowledge-base.md)하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [신뢰도 점수](./confidence-score.md)

## <a name="see-also"></a>참고 항목 

[기술 자료](./knowledge-base.md)
[QnA Maker 개요](../Overview/overview.md)
