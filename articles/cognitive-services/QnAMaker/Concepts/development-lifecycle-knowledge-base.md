---
title: 기술 자료 수명 주기 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 모델 변경, 발언 예제, 게시, 엔드포인트 쿼리의 데이터 수집으로 구성된 반복 주기에서 가장 잘 학습합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 97a4673be2a611149806855e792c5bf1f7a0942a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955168"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker의 기술 자료 수명 주기
QnA Maker는 모델 변경, 발언 예제, 게시, 엔드포인트 쿼리의 데이터 수집으로 구성된 반복 주기에서 가장 잘 학습합니다. 

![제작 주기](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기
QnA Maker KB(기술 자료) 엔드포인트는 KB의 콘텐츠를 기반으로 사용자 쿼리와 가장 일치하는 답변을 제공합니다. 기술 자료 만들기는 질문, 답변, 관련 메타데이터의 콘텐츠 리포지토리를 설정하는 일회성 작업입니다. FAQ 페이지, 제품 설명서 또는 구조화된 Q-A 쌍 같은 기존 콘텐츠를 크롤링하여 기술 자료를 만들 수 있습니다. [기술 자료 만들기](../How-To/create-knowledge-base.md)에 대해 알아보세요.

## <a name="testing-and-updating-the-knowledge-base"></a>기술 자료 테스트 및 업데이트

편집 또는 자동 추출을 통해 기술 자료에 콘텐츠가 채워지면 기술 자료를 테스트할 수 있습니다. 대화형 테스트는 일반 사용자 쿼리를 입력 하 고 응답에 올바른 응답과 충분 한 신뢰도 점수가 반환 되었는지 확인 하 여 **테스트** 패널을 통해 QnA Maker 포털에서 수행할 수 있습니다. 

* **낮은 신뢰도 점수를 수정 하려면**: 대체 질문을 추가 합니다. 
* **쿼리가 잘못 된 [기본 응답](confidence-score.md#change-default-answer)을 반환**하는 경우: 올바른 질문에 새 답변을 추가 합니다. 

이  테스트-업데이트 반복 과정은 사용자가 결과에 만족할 때까지 계속됩니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

대량 kb 경우에는 [generateanswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) 와 함께 자동화 된 테스트를 `isTest` 사용 하 고 게시 된 `test` 기술 자료가 아닌 기술 자료를 쿼리 하는 body 속성을 사용 합니다. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>기술 자료 게시
기술 자료 테스트를 마쳤으면 기술 자료를 게시할 수 있습니다. 게시는 테스트를 마친 최신 버전의 기술 자료를 **게시된** 기술 자료를 나타내는 전용 Azure Search 인덱스에 푸시합니다. 또한 애플리케이션 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이러한 방식으로 테스트 버전 기술 자료를 변경하면 이미 게시된 프로덕션 응용프로그램 기술 자료에 영향을 주지 않습니다.

각각의 기술 자료를 테스트 대상으로 지정할 수 있습니다. Api를 사용 하 여 generateanswer 호출에서 body 속성을 사용 `isTest` 하 여 기술 자료의 테스트 버전을 대상으로 지정할 수 있습니다.

[기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

## <a name="monitor-usage"></a>사용량 모니터링
서비스의 채팅 로그를 기록하려면 [QnA Maker 서비스를 만들 때](../How-To/set-up-qnamaker-service-azure.md) Application Insights를 사용하도록 설정해야 합니다.

다양한 서비스 사용 분석 자료를 얻을 수 있습니다. Application Insights를 사용하여 [QnA Maker 서비스에 대한 분석](../How-To/get-analytics-knowledge-base.md) 자료를 얻는 방법을 자세히 알아보세요.

분석 자료의 내용에 따라 [기술 자료를 적절하게 업데이트](../How-To/edit-knowledge-base.md)하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [신뢰도 점수](./confidence-score.md)

## <a name="see-also"></a>참고자료 

[기술 자료](./knowledge-base.md)
[QnA Maker 개요](../Overview/overview.md)
