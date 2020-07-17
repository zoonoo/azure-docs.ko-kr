---
title: 기본 대답 가져오기-QnA Maker
description: 질문과 일치 하는 항목이 없는 경우 기본 대답이 반환 됩니다. 표준 기본 응답에서 기본 대답을 변경 하는 것이 좋습니다.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979976"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker 리소스에 대 한 기본 응답 변경

답변을 찾을 수 없을 때 기술 자료에 대 한 기본 답은 반환 됩니다. [Azure Bot service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)와 같은 클라이언트 응용 프로그램을 사용 하는 경우 점수 임계값을 충족 하는 답변이 없음을 나타내는 별도의 기본 응답이 있을 수도 있습니다.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>기술 자료를 만들 때 기본 답변 설정

새 기술 자료를 만들 때 기본 대답 텍스트는 설정 중 하나입니다. 만들기 프로세스 중에 설정 하지 않도록 선택 하는 경우 나중에 다음 절차를 수행 하 여 변경할 수 있습니다.

## <a name="change-default-answer-in-qna-maker-portal"></a>QnA Maker 포털에서 기본 대답 변경

QnA Maker 서비스에서 응답이 반환 되지 않으면 기술 자료 기본 응답이 반환 됩니다.

1. [QnA Maker 포털](https://www.qnamaker.ai/) 에 로그인 하 고 목록에서 기술 자료를 선택 합니다.
1. 탐색 모음에서 **설정** 을 선택 합니다.
1. **기술 자료 관리** 섹션에서 **기본 대답 텍스트** 의 값을 변경 합니다.

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="기본 답변 텍스트 상자가 강조 표시 된 QnA Maker 포털, 설정 페이지의 스크린샷":::

1. **저장 및 학습** 을 선택 하 여 변경 내용을 저장 합니다.

## <a name="next-steps"></a>다음 단계

* [기술 자료 만들기](../How-to/manage-knowledge-bases.md)