---
title: QnA Maker 앱 관리-QnA Maker
description: QnA Maker를 사용하면 여러 사용자가 기술 자료를 공동으로 작업할 수 있습니다. QnA Maker은 활성 학습을 사용 하 여 기술 자료의 품질을 향상 시키는 기능을 제공 합니다. 하나는 기존 질문을 제거 하거나 변경 하지 않고도 검토, 수락 또는 거부 하 고 추가할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 77290d271709db36f9c62e165b0b4070783b3ec6
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128479"
---
# <a name="manage-qna-maker-app"></a>QnA Maker 앱 관리

QnA Maker를 사용 하면 협력자의 역할에 따라 협력자 액세스를 제한 하는 기능을 제공 하 여 다양 한 작성자 및 콘텐츠 편집기와 공동 작업할 수 있습니다.
[QnA Maker 협력자 인증 개념](../Concepts/role-based-access-control.md)에 대해 자세히 알아보세요.

[활성 학습](../Concepts/active-learning-suggestions.md)을 통해 대체 질문을 제안 하 여 기술 자료의 품질을 향상 시킬 수도 있습니다. 사용자 제출을 고려 하 여 대체 질문 목록에서 제안 사항으로 표시 됩니다. 이러한 제안 사항을 대체 질문으로 추가 하거나 거부할 수 있는 유연성이 있습니다.

기술 자료가 자동으로 변경되지는 않습니다. 모든 변경 내용을 적용 하려면 제안에 동의 해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Azure 역할 기반 access control 추가 (Azure RBAC)

QnA Maker를 사용 하면 여러 사용자가 동일한 QnA Maker 리소스의 모든 기술 자료에 대해 공동 작업할 수 있습니다. 이 기능은 azure [RBAC (역할 기반 액세스 제어)](../../../active-directory/role-based-access-control-configure.md)와 함께 제공 됩니다.

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker 리소스 수준에서 액세스

QnA Maker 서비스에서는 특정 기술 자료를 공유할 수 없습니다. 보다 세부적인 액세스 제어를 원하는 경우 여러 QnA Maker 리소스 간에 기술 자료를 배포한 다음 각 리소스에 역할을 추가 하는 것이 좋습니다.

## <a name="add-a-role-to-a-resource"></a>리소스에 역할 추가

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>QnA Maker 리소스에 사용자 계정 추가

다음 단계는 협력자 역할을 사용 하지만 이러한 단계를 사용 하 여 [역할](../reference-role-based-access-control.md) 을 추가할 수 있습니다.

1. [Azure](https://portal.azure.com/) portal에 로그인 하 고 QnA Maker 리소스로 이동 합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. **액세스 제어(IAM)** 탭으로 이동합니다.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. **추가**를 선택합니다.

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

1. 사용자의 전자 메일 주소를 입력 하 고 **저장**을 누릅니다.

    ![QnA Maker IAM 메일 추가](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker 기술 자료 보기

QnA Maker 서비스를 공유 하는 사람이 [QnA Maker 포털](https://qnamaker.ai)에 로그인 하면 해당 역할에 따라 해당 서비스의 모든 기술 자료를 볼 수 있습니다.

기술 자료를 선택 하면 해당 QnA Maker 리소스에 대 한 현재 역할이 기술 자료 이름 옆에 표시 됩니다.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="IAM 역할 추가를 QnA Maker 합니다.":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>활성 학습을 사용 하도록 런타임 버전 업그레이드

활성 학습은 런타임 버전 4.4.0 이상에서 지원됩니다. 기술 자료가 이전 버전에서 작성된 경우 이 기능을 사용하려면 [런타임을 업그레이드](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)합니다.

## <a name="turn-on-active-learning-for-alternate-questions"></a>대체 질문에 대해 활성 학습 설정

활성 학습은 기본적으로 해제되어 있습니다. 제안된 질문을 확인하려면 활성 학습을 설정합니다. 활성 학습을 켠 후에는 클라이언트 앱의 정보를 QnA Maker으로 보내야 합니다. 자세한 내용은 [GenerateAnswer를 사용 하는 아키텍처 흐름 및 봇에서 Api 학습](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)을 참조 하세요.

1. **게시** 를 선택 하 여 기술 자료를 게시 합니다. 활성 학습 쿼리는 GenerateAnswer API 예측 끝점 에서만 수집 됩니다. QnA Maker 포털에서 테스트 창에 대 한 쿼리는 활성 학습에 영향을 주지 않습니다.

1. QnA Maker 포털에서 활성 학습을 설정 하려면 오른쪽 위 모서리로 이동 하 여 사용자의 **이름을**선택 하 고 [**서비스 설정**](https://www.qnamaker.ai/UserSettings)으로 이동 합니다.

    ![서비스 설정 페이지에서 활성 학습의 제안 된 질문을 사용 하도록 설정 합니다. 오른쪽 위에 있는 메뉴에서 사용자 이름을 선택한 다음 서비스 설정을 선택 합니다.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker 서비스를 찾은 다음 **활성 학습**을 설정 상태로 전환합니다.

    > [!div class="mx-imgBorder"]
    > [![서비스 설정 페이지에서 활성 학습 기능을 설정/해제 합니다. 기능을 설정/해제할 수 없는 경우 서비스를 업그레이드 해야 할 수 있습니다.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > 이전 이미지의 정확한 버전은 예제로만 표시 됩니다. 버전이 다를 수 있습니다.
    **활성 학습** 을 사용 하도록 설정 하면 기술 자료에서 사용자가 제출한 질문에 따라 정기적인 간격으로 새로운 질문을 제안 합니다. 설정을 다시 전환하여 **활성 학습**을 사용하지 않도록 설정할 수 있습니다.

## <a name="review-suggested-alternate-questions"></a>제안 된 대체 질문 검토

각 기술 자료의 **편집** 페이지에서 [제안 된 대체 질문을 검토](improve-knowledge-base.md) 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기](./manage-knowledge-bases.md)