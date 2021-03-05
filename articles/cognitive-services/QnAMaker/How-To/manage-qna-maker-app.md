---
title: QnA Maker 앱 관리-QnA Maker
description: QnA Maker를 사용하면 여러 사용자가 기술 자료를 공동으로 작업할 수 있습니다. QnA Maker은 활성 학습을 사용 하 여 기술 자료의 품질을 향상 시키는 기능을 제공 합니다. 하나는 기존 질문을 제거 하거나 변경 하지 않고도 검토, 수락 또는 거부 하 고 추가할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 49bfca118e53bbe3e4287b2ce25e5baffa717175
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217332"
---
# <a name="manage-qna-maker-app"></a>QnA Maker 앱 관리

QnA Maker를 사용 하면 협력자의 역할에 따라 협력자 액세스를 제한 하는 기능을 제공 하 여 다양 한 작성자 및 콘텐츠 편집기와 공동 작업할 수 있습니다.
[QnA Maker 협력자 인증 개념](../Concepts/role-based-access-control.md)에 대해 자세히 알아보세요.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Azure 역할 기반 access control 추가 (Azure RBAC)

QnA Maker를 사용 하면 여러 사용자가 동일한 QnA Maker 리소스의 모든 기술 자료에 대해 공동 작업할 수 있습니다. 이 기능은 azure [RBAC (역할 기반 액세스 제어)](../../../role-based-access-control/role-assignments-portal.md)와 함께 제공 됩니다.

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker 리소스 수준에서 액세스

QnA Maker 서비스에서는 특정 기술 자료를 공유할 수 없습니다. 보다 세부적인 액세스 제어를 원하는 경우 여러 QnA Maker 리소스 간에 기술 자료를 배포한 다음 각 리소스에 역할을 추가 하는 것이 좋습니다.

## <a name="add-a-role-to-a-resource"></a>리소스에 역할 추가

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>QnA Maker 리소스에 사용자 계정 추가

다음 단계는 협력자 역할을 사용 하지만 이러한 단계를 사용 하 여 역할을 추가할 수 있습니다.

1. [Azure](https://portal.azure.com/) portal에 로그인 하 고 QnA Maker 리소스로 이동 합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. **액세스 제어(IAM)** 탭으로 이동합니다.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. **추가** 를 선택합니다.

    ![QnA Maker IAM 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. 다음 목록에서 역할을 선택 합니다.

    |역할|
    |--|
    |소유자|
    |참가자|
    |Cognitive Services QnA Maker 판독기|
    |Cognitive Services QnA Maker 편집기|
    |Cognitive Services 사용자|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="IAM 역할 추가를 QnA Maker 합니다.":::

1. 사용자의 전자 메일 주소를 입력 하 고 **저장** 을 누릅니다.

    ![QnA Maker IAM 메일 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker 기술 자료 보기

QnA Maker 서비스를 공유 하는 사람이 [QnA Maker 포털](https://qnamaker.ai)에 로그인 하면 해당 역할에 따라 해당 서비스의 모든 기술 자료를 볼 수 있습니다.

기술 자료를 선택 하면 해당 QnA Maker 리소스에 대 한 현재 역할이 기술 자료 이름 옆에 표시 됩니다.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="웹 페이지의 왼쪽 위 모서리에 있는 기술 자료 이름 옆의 괄호 안에 역할 이름을 사용 하 여 편집 모드에 있는 기술 자료의 스크린샷":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기](./manage-knowledge-bases.md)
