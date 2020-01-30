---
title: 기본 대답 가져오기-QnA Maker
description: 질문과 일치 하는 항목이 없는 경우 기본 대답이 반환 됩니다. 표준 기본 응답에서 기본 대답을 변경 하는 것이 좋습니다.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846343"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>기술 자료에 대 한 기본 대답 설정

질문과 일치 하는 항목이 없는 경우 기본 대답이 반환 됩니다. 표준 기본 응답에서 기본 대답을 변경 하는 것이 좋습니다.

## <a name="change-default-answer"></a>기본 답변 변경

1. [Azure Portal](https://portal.azure.com)로 이동한 다음, 직접 만든 QnA Maker 서비스를 나타내는 리소스 그룹으로 이동합니다.

2. **App Service**를 클릭하여 엽니다.

    ![Azure Portal에서 QnA Maker에 대한 App service에 액세스](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **애플리케이션 설정**을 클릭하고 **DefaultAnswer** 필드를 원하는 기본 응답으로 편집합니다. **저장**을 클릭합니다.

    ![애플리케이션 설정을 선택한 다음, QnA Maker에 대한 DefaultAnswer 편집](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service 다시 시작

    ![DefaultAnswer를 변경한 후 QnA Maker appservice를 다시 시작](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>다음 단계

* [QnA Maker 및 LUIS를 사용 하 여 봇 만들기](../tutorials/integrate-qnamaker-luis.md)