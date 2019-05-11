---
title: 기술 자료를 테스트하는 방법 - QnA Maker
titlesuffix: Azure Cognitive Services
description: QnA Maker 기술 자료를 테스트하는 작업은 반환되는 응답의 정확도를 향상시키기 위한 반복 프로세스의 중요한 부분입니다. 또한 편집할 수도 있는 향상된 채팅 인터페이스를 통해 기술 자료를 테스트할 수 있습니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2c596b49d5587b07fe75cefde72e897478dc3dc8
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472077"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>QnA Maker에서 대화형으로 기술 자료 테스트

QnA Maker 기술 자료를 테스트하는 작업은 반환되는 응답의 정확도를 향상시키기 위한 반복 프로세스의 중요한 부분입니다. 또한 편집할 수도 있는 향상된 채팅 인터페이스를 통해 기술 자료를 테스트할 수 있습니다.

## <a name="test-answer-matching"></a>응답 일치 테스트

1. **내 기술 자료** 페이지에서 해당 이름을 선택하여 기술 자료에 액세스합니다.
1. 테스트 슬라이드 아웃 패널에 액세스하려면 애플리케이션의 위쪽 패널에서 **테스트**를 선택합니다.
1. 텍스트 상자에 쿼리를 입력하고 Enter 키를 선택합니다.
1. 기술 자료에서 가장 일치하는 응답은 응답으로 반환됩니다.

## <a name="clear-test-panel"></a>테스트 패널 지우기

테스트 콘솔에서 입력된 테스트 쿼리 및 해당 결과를 모두 지우려면 테스트 패널의 왼쪽 위 모서리에 있는 **다시 시작**을 선택합니다.

## <a name="close-test-panel"></a>테스트 패널 닫기

테스트 패널을 닫으려면 **테스트** 단추를 다시 선택합니다. 테스트 패널이 열려 있는 동안 기술 자료 콘텐츠를 편집할 수 없습니다.

## <a name="inspect-score"></a>점수 검사

검사 패널에서 테스트 결과의 세부 정보를 검사합니다.

1.  테스트 슬라이드 아웃 패널이 열려 있을 때 해당 응답에 대한 자세한 내용에 대한 **검사**를 선택합니다.

    ![응답 검사](../media/qnamaker-how-to-test-kb/inspect.png)

2.  검사 패널이 나타납니다. 패널에는 식별된 엔터티뿐만 아니라 상위 채점 의도도 포함됩니다. 패널에는 선택한 음성의 결과가 표시됩니다.

## <a name="correct-the-top-scoring-answer"></a>상위 점수 대답 수정

상위 점수 대답이 올바르지 않으면 목록에서 올바른 대답을 선택하고 **저장 후 학습**을 선택합니다.

![상위 점수 대답 수정](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>대체 질문 추가

질문의 대체 형식을 지정된 대답에 추가할 수 있습니다. 텍스트 상자에서 대체 답변을 입력하고 Enter 키를 클릭하여 추가합니다. **저장 후 학습**을 선택하여 업데이트를 저장합니다.

![대체 질문 추가](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>새 대답 추가

일치된 기존 대답이 올바르지 않거나 기술 자료에 존재하지 않는(KB에서 일치 항목을 찾을 수 없음) 경우 새 응답을 추가할 수 있습니다. 

답변 목록의 맨 아래에 텍스트 상자를 사용 하 여 새 대답을 입력 하 고 추가 하려면 enter 키를 누릅니다. 

**저장 후 학습**을 선택하여 이 대답을 유지합니다. 새로운 질문-대답 쌍이 기술 자료에 추가되었습니다. 

> [!NOTE]
> **저장 후 학습**을 누를 경우에만 기술 자료에 대한 모든 편집 내용이 저장됩니다.

## <a name="test-the-published-knowledge-base"></a>게시 된 기술 자료를 테스트 합니다.

테스트 창에 게시 된 버전의 기술 자료를 테스트할 수 있습니다. KB를 게시 한 후 선택 합니다 **게시 된 KB** 상자 하 고 게시 된 기술 자료에서 결과 가져오는 쿼리를 보냅니다.

![게시 된 KB 테스트](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 게시](./publish-knowledge-base.md)
