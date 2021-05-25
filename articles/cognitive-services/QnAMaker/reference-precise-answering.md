---
title: 답변 범위 검색을 사용한 정확한 답변 - QnA Maker
description: QnA Maker 관리에서 사용할 수 있는 정확한 응답 기능을 이해합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 9c7b265118e395dde2b4b5f3959ba397d75ddac5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232191"
---
# <a name="precise-answering"></a>정확한 대답

QnA Maker 관리(미리 보기)에 도입된 정확한 응답 기능을 사용하면 모든 사용자 쿼리에 대한 기술 자료에 있는 가장 적합한 후보 응답 통로에서 정확한 짧은 답변을 얻을 수 있습니다. 이 기능은 런타임 시 사용자 쿼리의 의도를 이해하고 응답 경로에 팩트로 표시되는 짧은 답변이 있는 경우 응답 통로에서 정확한 짧은 답변을 검색하는 딥 러닝 모델을 사용합니다. 

이 기능은 테스트 창에 기본적으로 설정되어 있으므로 시나리오별로 기능을 테스트할 수 있습니다. 이 기능은 콘텐츠 개발자와 최종 사용자 모두에게 매우 유용합니다. 이제 콘텐츠 개발자는 기술 자료에 있는 모든 팩트에 대해 특정 QnA 쌍을 수동으로 큐레이션할 필요가 없으며, 최종 사용자는 서비스에서 반환되는 전체 대답 통로를 확인하여 사용자의 쿼리에 대답하는 실제 팩트를 찾을 필요가 없습니다. [답변 생성 API를 통해 정확한 답변](How-To/metadata-generateanswer-usage.md#get-precise-answers-with-generateanswer-api)을 페치할 수 있습니다.

## <a name="precise-answering-on-qna-maker-portal"></a>QnA Maker 포털에서 정확한 응답

QnA Maker 포털에서 테스트 창을 열면 위쪽에 **짧은 답변을 표시** 하는 옵션이 표시됩니다. 이 옵션은 기본적으로 선택됩니다. 테스트 창에 쿼리를 입력할 때 대답 통로에 짧은 답변이 있는 경우 대답 통로와 함께 짧은 답변이 표시됩니다.
 
![활성화된 관리형 테스트 창](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

테스트 창에서 대답 통로만 표시하려는 경우 **짧은 대답 표시** 옵션을 선택 취소할 수 있습니다. 

또한 이 서비스는 테스트 창에서 쿼리 바로 아래에 있는 **검사** 옵션을 선택하여 확인할 수 있는 **대답 범위 점수** 로 정확한 대답의 신뢰성 점수를 다시 반환합니다.

![관리형 대답 범위 점수](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>QnA Maker 봇 게시

봇을 게시하면 애플리케이션에서 기본적으로 정확한 답변 사용 환경을 가져올 수 있으며, 여기에서 대답 통로와 함께 짧은 답변이 표시됩니다. 응답에서 정확한 응답(AnswerSpan이라고 함)을 사용하는 방법을 보려면 응답 [생성](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan)에 대한 API 참조를 참조하세요. 사용자는 Bot 앱 서비스를 통해 템플릿을 업데이트하여 다른 환경을 유연하게 선택할 수 있습니다. 

## <a name="language-support"></a>언어 지원

현재 정확한 답변 기능은 영어로먄 지원됩니다.
