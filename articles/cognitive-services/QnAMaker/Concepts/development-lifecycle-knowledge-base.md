---
title: 기술 자료 수명 주기 - QnA Maker
description: QnA Maker는 모델 변경, 발언 예제, 게시, 엔드포인트 쿼리의 데이터 수집으로 구성된 반복 주기에서 가장 잘 학습합니다.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914955"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker의 기술 자료 수명 주기
QnA Maker는 모델 변경, 발언 예제, 게시, 엔드포인트 쿼리의 데이터 수집으로 구성된 반복 주기에서 가장 잘 학습합니다.

![제작 주기](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기
QnA Maker KB(기술 자료) 엔드포인트는 KB의 콘텐츠를 기반으로 사용자 쿼리와 가장 일치하는 답변을 제공합니다. 기술 자료 만들기는 질문, 답변 및 관련 메타데이터의 콘텐츠 리포지토리를 설정하는 일회성 작업입니다. FAQ 페이지, 제품 설명서 또는 구조화된 Q-A 쌍 같은 기존 콘텐츠를 크롤링하여 기술 자료를 만들 수 있습니다. [기술 자료 만들기](../quickstarts/create-publish-knowledge-base.md)에 대해 알아보세요.

## <a name="testing-and-updating-the-knowledge-base"></a>기술 자료 테스트 및 업데이트

편집 또는 자동 추출을 통해 기술 자료에 콘텐츠가 채워지면 기술 자료를 테스트할 수 있습니다. 대화형 테스트는 일반적인 사용자 쿼리를 입력하고 응답이 올바른 응답과 충분한 신뢰도 점수로 반환되었는지 확인하여 **테스트** 패널을 통해 QnA Maker 포털에서 수행할 수 있습니다.

* **낮은 신뢰도 점수를 수정하려면**다른 질문을 추가합니다.
* **쿼리가 [기본 응답을](../How-to/change-default-answer.md)잘못 반환하면**올바른 질문에 새 답변을 추가합니다.

이  테스트-업데이트 반복 과정은 사용자가 결과에 만족할 때까지 계속됩니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

대규모 KB의 경우 게시된 기술 자료 대신 `isTest` `test` 기술 자료가 쿼리되는 [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) 및 본문 속성을 사용하여 자동화된 테스트를 사용합니다.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>기술 자료 게시
기술 자료 테스트를 마쳤으면 기술 자료를 게시할 수 있습니다. Publish는 테스트된 기술 자료의 최신 버전을 **게시된** 기술 자료로 나타내는 전용 Azure 인지 검색 인덱스로 푸시합니다. 또한 애플리케이션 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이러한 방식으로 테스트 버전 기술 자료를 변경하면 이미 게시된 프로덕션 응용프로그램 기술 자료에 영향을 주지 않습니다.

각각의 기술 자료를 테스트 대상으로 지정할 수 있습니다. API를 사용하여 생성Answer 호출에서 본문 속성을 `isTest` 사용하여 기술 자료의 테스트 버전을 대상으로 지정할 수 있습니다.

[기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

## <a name="monitor-usage"></a>사용량 모니터링
서비스의 채팅 로그를 기록하려면 [QnA Maker 서비스를 만들 때](../How-To/set-up-qnamaker-service-azure.md) Application Insights를 사용하도록 설정해야 합니다.

다양한 서비스 사용 분석 자료를 얻을 수 있습니다. Application Insights를 사용하여 [QnA Maker 서비스에 대한 분석](../How-To/get-analytics-knowledge-base.md) 자료를 얻는 방법을 자세히 알아보세요.

분석 자료의 내용에 따라 [기술 자료를 적절하게 업데이트](../How-To/edit-knowledge-base.md)하세요.

## <a name="version-control-for-data-in-your-knowledge-base"></a>기술 자료의 데이터에 대한 버전 제어

데이터에 대한 버전 제어는 QnA Maker 포털의 **설정** 페이지에서 가져오기/내보내기 기능을 통해 제공됩니다.

기술 자료 는 `.tsv` 기술 `.xls` 자료 또는 형식으로 내보내 서 기술 기반을 백업할 수 있습니다. 내보낸 후에는 이 파일을 일반 소스 제어 검사의 일부로 포함합니다.

특정 버전으로 돌아가야 하는 경우 로컬 시스템에서 해당 파일을 가져와야 합니다. 내보낸 기술 자료는 **설정** 페이지에서 가져오기를 통해서만 사용해야 **합니다.** 파일 또는 URL 문서 데이터 원본으로 사용할 수 없습니다. 이렇게 하면 현재 기술 자료의 질문과 답변이 가져온 파일의 내용으로 바꿉습니다.

## <a name="test-and-production-knowledge-base"></a>테스트 및 생산 기술 자료
기술 자료는 QnA Maker를 통해 생성, 유지 관리 및 사용되는 질문 및 답변 집합의 리포지토리입니다. 각 QnA Maker 리소스에는 여러 기술 자료가 있습니다.

기술 자료에는 *테스트* 및 *게시의*두 가지 상태가 있습니다.

### <a name="test-knowledge-base"></a>테스트 기술 자료

*테스트 기술 자료는* 응답의 정확성과 완전성을 위해 현재 편집, 저장 및 테스트된 버전입니다. 테스트 기술 자료에 대한 변경 사항은 응용 프로그램 또는 채팅 봇의 최종 사용자에게 영향을 주지 않습니다. 테스트 기술 자료는 `test` HTTP 요청에서로 알려져 있습니다. 이 `test` 지식은 QnA Maker의 포털 대화형 **테스트** 창에서 사용할 수 있습니다.

### <a name="production-knowledge-base"></a>생산 기술 자료

*게시된 기술 자료는* 채팅 봇 또는 응용 프로그램에서 사용되는 버전입니다. 기술 자료 게시 작업은 기술 자료의 게시된 버전에 테스트 기술 자료의 내용을 넣습니다. 게시된 기술 자료는 응용 프로그램이 끝점을 통해 사용하는 버전이므로 콘텐츠가 정확하고 잘 테스트되었는지 확인합니다. 게시된 기술 자료는 `prod` HTTP 요청에서 로 알려져 있습니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [적극적인 학습 제안](./active-learning-suggestions.md)