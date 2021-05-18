---
title: 사용자 발화 검토 - LUIS
titleSuffix: Azure Cognitive Services
description: 활성 학습에서 캡처한 발화를 검토하여 실제 발화에 대한 의도를 선택하고 엔터티를 표시합니다. 변경 내용을 수락하고 학습 및 게시합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: ea2b44d05d25756a16b6b84f0734966b1f579848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97007605"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>엔드포인트 발화를 검토하여 LUIS 앱을 개선하는 방법

올바른 예측을 위해 엔드포인트 발화를 검토하는 프로세스를 [활성 학습](luis-concept-review-endpoint-utterances.md)이라고 합니다. 활성 학습은 엔드포인트 쿼리를 캡처하고 확실하지 않은 사용자의 엔드포인트 발화를 선택합니다. 이러한 발화를 검토하여 실제 발화에 대해 의도를 선택하고 엔터티를 표시합니다. 예제 발화에 이러한 변경 내용을 적용한 다음 학습 및 게시합니다. 그러면 LUIS가 발화를 보다 정확하게 식별합니다.

## <a name="log-user-queries-to-enable-active-learning"></a>사용자 쿼리를 기록하여 활성 학습 사용

활성 학습을 사용하도록 설정하려면 사용자 쿼리를 기록해야 합니다. 이렇게 하려면 `log=true` 쿼리 문자열 매개 변수 및 값을 사용하여 [엔드포인트 쿼리](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)를 호출합니다.

LUIS 포털을 사용하여 올바른 엔드포인트 쿼리를 생성합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **관리** 섹션으로 이동한 다음 **Azure 리소스** 를 선택합니다.
1. 할당된 예측 리소스의 경우 **쿼리 매개 변수 변경** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![쿼리 매개 변수 변경 링크를 보여 주는 스크린샷](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. **로그 저장** 을 전환하고 **완료** 를 선택하여 저장합니다.

    > [!div class="mx-imgBorder"]
    > ![LUIS 포털을 사용하여 활성 학습에 필요한 로그를 저장합니다.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     이 작업은 `log=true` 쿼리 문자열 매개 변수를 추가하여 예제 URL을 변경합니다. 런타임 엔드포인트에 예측 쿼리를 만들 때 변경된 예제 쿼리 URL을 복사하여 사용합니다.

## <a name="correct-intent-predictions-to-align-utterances"></a>발화를 맞추기 위한 올바른 의도 예측

각 발화에는 **맞춤 의도** 열에 표시되는 제안된 의도가 포함됩니다.

> [!div class="mx-imgBorder"]
> [![LUIS가 알 수 없는 엔드포인트 발화 검토](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

해당 의도에 동의하는 경우 확인 표시를 선택합니다. 제안에 동의하지 않는 경우, 맞춤 의도 드롭다운 목록에서 올바른 의도를 선택한 다음, 맞춤 의도 오른쪽에 있는 확인 표시를 선택합니다. 확인 표시를 선택한 후 발화는 의도로 이동되고 **엔드포인트 발화 검토** 목록에서 제거됩니다.

> [!TIP]
> 의도 세부 정보 페이지로 이동하여 **엔드포인트 발화 검토** 목록에서 모든 예제 발화의 엔터티 예측을 검토하고 수정해야 합니다.

## <a name="delete-utterance"></a>발화 삭제

검토 목록에서 각 발화를 삭제할 수 있습니다. 삭제되면 목록에 다시 나타나지 않습니다. 이는 사용자가 엔드포인트에서 동일한 발화를 입력하는 경우에도 적용됩니다.

발화를 삭제해야 하는지 확실하지 않은 경우 해당 발화를 없음 의도로 이동하거나 `miscellaneous`와 같은 새 의도를 만들고 발화를 해당 의도로 이동합니다.

## <a name="disable-active-learning"></a>활성 학습 사용 안 함

활성 학습을 사용하지 않도록 설정하려면 사용자 쿼리를 기록하지 않습니다. 이렇게 하려면 `log=false` 쿼리 문자열 매개 변수 및 값을 사용하여 [엔드포인트 쿼리](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)를 설정하거나 기본값이 false이므로 쿼리 문자열 값을 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

제안된 발화에 레이블을 지정한 후 성능이 얼마나 향상되는지 테스트하려면 위쪽 패널에서 **테스트** 를 선택하여 테스트 콘솔에 액세스할 수 있습니다. 테스트 콘솔을 사용하여 앱을 테스트하는 방법에 대한 자세한 내용은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.
