---
title: 기술 자료에 대 한 공동 작업-QnA Maker
description: QnA Maker를 사용하면 여러 사용자가 기술 자료를 공동으로 작업할 수 있습니다. 이 기능은 Azure 역할 기반 액세스 제어를 통해 제공됩니다.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650792"
---
# <a name="collaboration-with-authors-and-editors"></a>작성자 및 편집기와 공동 작업

공동 작업은 QnA Maker 리소스 수준에서 제공 되므로 협력자의 역할에 따라 협력자 액세스를 제한할 수 있습니다. QnA Maker 협력자 인증 [개념](../Concepts/role-based-access-control.md)에 대해 자세히 알아보세요.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>QnA Maker 리소스에 RBAC (역할 기반 액세스) 추가

QnA Maker를 사용 하면 여러 사용자가 동일한 QnA Maker 리소스의 모든 기술 자료에 대해 공동 작업할 수 있습니다. 이 기능은 Azure [역할 기반 액세스 제어](../../../active-directory/role-based-access-control-configure.md)를 통해 제공됩니다.

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker 리소스 수준에서 액세스

QnA Maker 서비스에서는 특정 기술 자료를 공유할 수 없습니다. 보다 세부적인 액세스 제어를 원하는 경우 여러 QnA Maker 리소스 간에 기술 자료를 배포한 다음 각 리소스에 역할을 추가 하는 것이 좋습니다.

## <a name="add-role-to-resource"></a>리소스에 역할 추가

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>QnA Maker 리소스에 사용자 계정 추가

다음 단계는 협력자 역할을 사용 하지만 이러한 단계를 사용 하 여 [역할](../reference-role-based-access-control.md) 을 추가할 수 있습니다.

1. [Azure](https://portal.azure.com/) portal에 로그인 하 고 QnA Maker 리소스로 이동 합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. **액세스 제어(IAM)** 탭으로 이동합니다.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. **추가**를 선택합니다.

    ![QnA Maker IAM 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. 다음 목록에서 역할을 선택 합니다.

    |역할|
    |--|
    |소유자|
    |참가자|
    |QnA Maker 판독기|
    |QnA Maker 편집기|
    |Cognitive Services 사용자|

    ![QnA Maker IAM 역할 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. 사용자의 전자 메일 주소를 입력 하 고 **저장**을 누릅니다.

    ![QnA Maker IAM 메일 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

QnA Maker 서비스를 공유 하는 사람이 [QnA Maker 포털](https://qnamaker.ai)에 로그인 하면 해당 역할에 따라 해당 서비스의 모든 기술 자료를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 테스트](./test-knowledge-base.md)

공동 작업에 대해 자세히 알아보세요.
* [Azure](../../../active-directory/role-based-access-control-configure.md) 역할 기반 액세스 제어
* 역할 기반 액세스 제어 [개념](../Concepts/role-based-access-control.md) QnA Maker
