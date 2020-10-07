---
title: 기술 자료를 사용 하 여 활성 학습 사용-QnA Maker
description: 활성 학습을 사용 하 여 기술 자료의 품질을 개선 하는 방법을 알아보세요. 기존 질문을 삭제하거나 변경하지 않고 검토, 수락, 거부 또는 추가합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: c67cde3062d88c48eb44a5135bf47ad3948cecc8
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776802"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>활성 학습을 사용하여 기술 자료 개선

[활성 학습](../Concepts/active-learning-suggestions.md) 을 통해 대체 질문을 제안 하 여 기술 자료의 품질을 향상 시킬 수 있습니다. 사용자 제출을 고려 하 여 대체 질문 목록에 제안 사항으로 표시 됩니다. 이러한 제안 사항을 대체 질문으로 추가 하거나 거부할 수 있는 유연성이 있습니다.

기술 자료가 자동으로 변경되지는 않습니다. 모든 변경 내용을 적용 하려면 제안에 동의 해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.


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