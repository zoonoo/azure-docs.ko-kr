---
title: 설계 기술 자료 - QnA 메이커
description: QnA Maker 기술 자료는 QnA(질의응답) 세트와 각 QnA 쌍과 연관된 선택적 메타데이터로 구성됩니다.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76846373"
---
# <a name="question-and-answer-set-concepts"></a>질문 및 답변 세트 개념

기술 자료는 QnA(질문 및 답변) 집합으로 구성됩니다.  각 집합에는 하나의 대답이 있으며 집합에는 해당 _대답과_연결된 모든 정보가 포함됩니다. 대답은 데이터베이스 행 또는 데이터 구조 인스턴스와 느슨하게 유사할 수 있습니다.

## <a name="question-and-answer-sets"></a>질문 및 대답 세트

QnA(질문 및 답변) 집합의 **필수** 설정은 다음과 같습니다.

* **질문** - QnA Maker의 기계 학습에 사용되는 사용자 쿼리 텍스트는 사용자 질문의 텍스트와 다른 단어로 정렬하지만 동일한 답변으로 정렬됩니다.
* **답변** - 집합의 대답은 사용자 쿼리가 연결된 질문과 일치할 때 반환되는 응답입니다.

각 집합은 **ID로**표시됩니다.

집합의 **선택적** 설정은 다음과 같습니다.

* **질문의 대체 형태** - 이것은 QnA 메이커가 질문 관용구의 다양한 정답을 반환하는 데 도움이
* **메타데이터**: 메타데이터는 QnA 쌍과 연결된 태그이며 키-값 쌍으로 표시됩니다. 메타데이터 태그는 QnA 쌍을 필터링하고 쿼리 매칭이 수행되는 집합을 제한하는 데 사용됩니다.
* **멀티 턴 프롬프트,** 멀티 턴 대화를 계속하는 데 사용

![QnA Maker 기술 자료](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>편집자로서 기술 자료에 추가

기술 자료만 채울 기존 콘텐츠가 없는 경우 QnA Maker 포털에 편집적으로 QnA 세트를 추가할 수 있습니다. [여기](../How-To/edit-knowledge-base.md)에서 기술 자료를 업데이트하는 방법을 알아봅니다.

## <a name="editing-your-knowledge-base-locally"></a>로컬로 기술 자료 편집

기술 자료를 만든 후에는 로컬 파일을 통해 내보냈다가 다시 가져오는 것보다는 [QnA Maker 포털](https://qnamaker.ai)에서 기술 자료 텍스트를 편집하는 것이 좋습니다. 그러나 로컬로 기술 자료를 편집해야 하는 경우가 있을 수 있습니다.

**설정** 페이지에서 기술 자료를 내보낸 후 Microsoft Excel을 사용하여 기술 자료를 편집합니다. 다른 응용 프로그램을 사용하여 내보낸 파일을 편집하도록 선택한 경우 응용 프로그램이 완전히 TSV를 준수하지 않으므로 구문 오류가 발생할 수 있습니다. Microsoft Excel의 TSV 파일의 경우 일반적으로 서식 지정 오류가 발생하지 않습니다.

편집을 완료했으면 **설정** 페이지를 통해 TSV 파일을 다시 가져옵니다. 그러면 현재 기술 자료가 가져온 기술 자료로 완전히 바뀝니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker의 기술 자료 수명 주기](./development-lifecycle-knowledge-base.md)