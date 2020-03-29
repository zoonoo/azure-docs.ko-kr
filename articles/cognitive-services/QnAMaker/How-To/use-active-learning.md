---
title: 기술 자료 개선 - QnA Maker
description: 적극적인 학습을 통해 지식 기반의 품질을 향상시킵니다. 기존 질문을 제거하거나 변경하지 않고 검토, 수락 또는 거부, 추가합니다.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071232"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>활성 학습을 사용하여 기술 자료 개선

[적극적인 학습을](../Concepts/active-learning-suggestions.md) 통해 다른 질문을 제안하여 지식 자료의 품질을 향상시킬 수 있습니다. 사용자 제출은 고려되고 대체 질문 목록에 제안으로 표시됩니다. 이러한 제안을 대체 질문으로 추가하거나 거부할 수 있는 유연성이 있습니다.

기술 자료가 자동으로 변경되지는 않습니다. 변경 사항이 적용되기 위해서는 제안을 수락해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>활성 학습을 사용하도록 런타임 버전을 업그레이드

활성 학습은 런타임 버전 4.4.0 이상에서 지원됩니다. 기술 자료가 이전 버전에서 작성된 경우 이 기능을 사용하려면 [런타임을 업그레이드](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)합니다.

## <a name="turn-on-active-learning-for-alternate-questions"></a>대체 질문에 대한 활성 학습 켜기

활성 학습은 기본적으로 해제되어 있습니다. 제안된 질문을 확인하려면 활성 학습을 설정합니다. 활성 학습을 켜면 클라이언트 앱에서 QnA Maker로 정보를 보내야 합니다. 자세한 내용은 [생성 응답 및 봇에서 API 를 학습하기 위한 아키텍처 흐름을](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)참조하십시오.

1. 기술 자료 게시를 위해 **게시를** 선택합니다. 활성 학습 쿼리는 생성Answer API 예측 끝점에서만 수집됩니다. QnA Maker 포털의 테스트 창에 대한 쿼리는 활성 학습에 영향을 미치지 않습니다.

1. QnA Maker 포털에서 활성 학습을 켜려면 오른쪽 상단 모서리로 이동하여 **이름**선택, [**서비스 설정으로**](https://www.qnamaker.ai/UserSettings)이동합니다.

    ![서비스 설정 페이지에서 활성 학습의 권장 질문 대안을 켭니다. 오른쪽 상단 메뉴에서 사용자 이름을 선택한 다음 서비스 설정을 선택합니다.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker 서비스를 찾은 다음 **활성 학습**을 설정 상태로 전환합니다.

    > [!div class="mx-imgBorder"]
    > [![서비스 설정 페이지에서 활성 학습 기능을 전환합니다. 기능을 전환할 수 없는 경우 서비스를 업그레이드해야 할 수 있습니다.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 앞의 이미지의 정확한 버전은 예제로만 표시됩니다. 버전이 다를 수 있습니다.

    **활성 학습이** 활성화되면 기술 자료는 사용자가 제출한 질문에 따라 정기적으로 새로운 질문을 제안합니다. 설정을 다시 전환하여 **활성 학습**을 사용하지 않도록 설정할 수 있습니다.

## <a name="review-suggested-alternate-questions"></a>제안된 대체 질문 검토

각 기술 자료의 **편집** 페이지에서 [제안된 대체 질문을 검토합니다.](improve-knowledge-base.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기](./manage-knowledge-bases.md)