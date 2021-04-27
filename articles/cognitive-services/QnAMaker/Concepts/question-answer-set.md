---
title: 기술 자료 설계 - QnA Maker 개념
description: 기술 자료 - QnA Maker를 설계하는 방법에 대해 알아봅니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c21c82b5fd024598da30f4ac7b1ed01e64561e3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000116"
---
# <a name="question-and-answer-pair-concepts"></a>질문 및 대답 쌍 개념

기술 자료는 질문 및 대답(QnA) 쌍으로 구성됩니다.  각 쌍에는 하나의 대답이 있으며, 한 쌍에는 해당 _대답_ 과 관련된 모든 정보가 포함됩니다. 대답은 데이터베이스 행 또는 데이터 구조 인스턴스와 비슷할 수 있습니다.

## <a name="question-and-answer-pairs"></a>질문 및 대답 쌍

질문 및 대답(QnA) 쌍에 **필요한** 설정은 다음과 같습니다.

* **질문** - QnA Maker의 기계 학습에 사용되는 사용자 쿼리 텍스트로, 표현은 다르지만 대답이 같은 사용자 질문의 텍스트에 연결됩니다.
* **대답** - 쌍의 대답은 사용자 쿼리가 관련 질문과 일치할 경우 반환되는 대답입니다.

각 쌍은 **ID** 로 나타납니다.

쌍에 대한 **선택적** 설정에는 다음이 포함됩니다.

* **질문의 다른 형태** - 이를 통해 QnA Maker가 보다 다양한 질문 구문에 대한 올바른 대답을 반환할 수 있습니다.
* **메타데이터**: 메타데이터는 QnA 쌍과 연결된 태그이며 키-값 쌍으로 표현됩니다. 메타데이터 태그는 QnA 쌍을 필터링하고 쿼리 매칭이 수행되는 집합을 제한하는 데 사용됩니다.
* 멀티 턴 대화를 이어가는 데 사용되는 **멀티 턴 프롬프트**

![QnA Maker 기술 자료](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>편집자로서 기술 자료에 추가

기술 자료를 채울 기존 콘텐츠가 없는 경우 편집자로서 QnA Maker 포털에 QnA 쌍을 추가할 수도 있습니다. [여기](../How-To/edit-knowledge-base.md)에서 기술 자료를 업데이트하는 방법을 알아봅니다.

## <a name="editing-your-knowledge-base-locally"></a>로컬로 기술 자료 편집

기술 자료를 만든 후에는 로컬 파일을 통해 내보냈다가 다시 가져오는 것보다는 [QnA Maker 포털](https://qnamaker.ai)에서 기술 자료 텍스트를 편집하는 것이 좋습니다. 그러나 로컬로 기술 자료를 편집해야 하는 경우가 있을 수 있습니다.

**설정** 페이지에서 기술 자료를 내보낸 후 Microsoft Excel을 사용하여 기술 자료를 편집합니다. 다른 애플리케이션을 사용하여 내보낸 파일을 편집하도록 선택하면 TSV와 완전히 호환되지 않기 때문에 애플리케이션에서 구문 오류가 발생할 수 있습니다. Microsoft Excel의 TSV 파일의 경우 일반적으로 서식 지정 오류가 발생하지 않습니다.

편집을 완료했으면 **설정** 페이지를 통해 TSV 파일을 다시 가져옵니다. 그러면 현재 기술 자료가 가져온 기술 자료로 완전히 바뀝니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker의 기술 자료 수명 주기](./development-lifecycle-knowledge-base.md)