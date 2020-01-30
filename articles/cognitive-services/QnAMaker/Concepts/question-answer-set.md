---
title: 기술 자료 설계-QnA Maker
description: QnA Maker 기술 자료는 QnA (질문과 대답) 집합 및 각 QnA 쌍과 연결 된 선택적 메타 데이터로 구성 됩니다.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846373"
---
# <a name="question-and-answer-set-concepts"></a>질문 및 답변 집합 개념

기술 자료는 QnA (질문과 대답) 집합으로 구성 됩니다.  각 집합에는 하나의 대답이 있으며 집합에는 해당 _대답과_연결 된 모든 정보가 포함 됩니다. 대답은 데이터베이스 행 또는 데이터 구조 인스턴스와 느슨하게 유사할 수 있습니다.

## <a name="question-and-answer-sets"></a>질문 및 답변 집합

질문 및 답변 (QnA) 집합에서 **필요한** 설정은 다음과 같습니다.

* 기계 학습을 QnA Maker 하는 데 사용 되는 **질문** 텍스트를 사용 하 여 다른 단어를 사용 하는 사용자의 질문 텍스트에 맞추고 동일한 답변을 사용 합니다.
* **대답** -이 집합의 대답은 사용자 쿼리가 관련 질문과 일치 하는 경우 반환 되는 응답입니다.

각 집합은 **ID**로 표시 됩니다.

집합에 대 한 **선택적** 설정에는 다음이 포함 됩니다.

* **질문의 대체 형태** -다양 한 질문에 대 한 올바른 답을 반환 QnA Maker 수 있습니다. 위해 구와
* **메타 데이터**: 메타 데이터는 QnA 쌍과 연결 된 태그 이며 키-값 쌍으로 표현 됩니다. 메타데이터 태그는 QnA 쌍을 필터링하고 쿼리 매칭이 수행되는 집합을 제한하는 데 사용됩니다.
* 다중 전환 **프롬프트**(멀티 턴 대화를 계속 하는 데 사용 됨)

![QnA Maker 기술 자료](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>편집자로서 기술 자료에 추가

기술 자료를 채울 기존 콘텐츠가 없는 경우 QnA Maker 포털에서 QnA sets editorially를 추가할 수 있습니다. [여기](../How-To/edit-knowledge-base.md)에서 기술 자료를 업데이트하는 방법을 알아봅니다.

## <a name="editing-your-knowledge-base-locally"></a>로컬로 기술 자료 편집

기술 자료를 만든 후에는 로컬 파일을 통해 내보냈다가 다시 가져오는 것보다는 [QnA Maker 포털](https://qnamaker.ai)에서 기술 자료 텍스트를 편집하는 것이 좋습니다. 그러나 로컬로 기술 자료를 편집해야 하는 경우가 있을 수 있습니다.

**설정** 페이지에서 기술 자료를 내보낸 후 Microsoft Excel을 사용하여 기술 자료를 편집합니다. 다른 응용 프로그램을 사용 하 여 내보낸 파일을 편집 하도록 선택 하는 경우 응용 프로그램은 완전히 TSV 호환 되지 않기 때문에 구문 오류가 발생할 수 있습니다. Microsoft Excel의 TSV 파일의 경우 일반적으로 서식 지정 오류가 발생하지 않습니다.

편집을 완료했으면 **설정** 페이지를 통해 TSV 파일을 다시 가져옵니다. 그러면 현재 기술 자료가 가져온 기술 자료로 완전히 바뀝니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker의 기술 자료 수명 주기](./development-lifecycle-knowledge-base.md)