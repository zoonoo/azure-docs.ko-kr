---
title: 기본 대답 가져오기-QnA Maker
description: 질문과 일치 하는 항목이 없는 경우 기본 대답이 반환 됩니다. 표준 기본 응답에서 기본 대답을 변경 하는 것이 좋습니다.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097101"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker 리소스에 대 한 기본 응답 변경

질문과 일치 하는 항목이 없는 경우 기본 대답이 반환 됩니다. 표준 기본 응답에서 기본 대답을 변경 하는 것이 좋습니다.

## <a name="change-default-answer-in-the-azure-portal"></a>Azure Portal의 기본 대답 변경

1. [Azure Portal](https://portal.azure.com)로 이동한 다음, 직접 만든 QnA Maker 서비스를 나타내는 리소스 그룹으로 이동합니다.

2. **App Service**를 클릭하여 엽니다.

    ![Azure Portal에서 QnA Maker에 대한 App service에 액세스](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **애플리케이션 설정**을 클릭하고 **DefaultAnswer** 필드를 원하는 기본 응답으로 편집합니다. **Save**을 클릭합니다.

    ![애플리케이션 설정을 선택한 다음, QnA Maker에 대한 DefaultAnswer 편집](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service 다시 시작

    ![DefaultAnswer를 변경한 후 QnA Maker appservice를 다시 시작](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>다음 단계

* [기술 자료 만들기](../How-to/manage-knowledge-bases.md)