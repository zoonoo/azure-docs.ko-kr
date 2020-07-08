---
title: 사용자 길이 발언 검토-LUIS
titleSuffix: Azure Cognitive Services
description: 활성 학습에서 캡처한 길이 발언을 검토 하 여 의도를 선택 하 고 엔터티를 읽기 전용 길이 발언로 표시 합니다. 변경 내용 적용, 학습 및 게시
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 58674bf38ec1efb970fa8e79f7d970ecefb36712
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055616"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>엔드포인트 길이 발언을 검토 하 여 LUIS 앱을 개선 하는 방법

올바른 예측에 대 한 끝점 길이 발언를 검토 하는 프로세스를 [활성 학습](luis-concept-review-endpoint-utterances.md)이라고 합니다. 활성 학습은 끝점 쿼리를 캡처하고 사용자의 끝점 길이 발언를 선택 합니다. 이러한 길이 발언를 검토 하 여 이러한 실제 길이 발언에 대 한 의도 및 표시 엔터티를 선택 합니다. 예제에 이러한 변경 내용을 적용 한 다음 학습 및 게시 길이 발언. LUIS는 길이 발언를 보다 정확 하 게 식별 합니다.

## <a name="enable-active-learning"></a>활성 학습 사용

활성 학습을 사용 하도록 설정 하려면 사용자 쿼리를 기록해 야 합니다. 이렇게 하려면 querystring 매개 변수 및 값을 사용 하 여 [끝점 쿼리](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) 를 호출 `log=true` 합니다.

LUIS 포털을 사용 하 여 올바른 끝점 쿼리를 생성 합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스**를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택 하 여 앱을 엽니다.
1. **관리** 섹션으로 이동한 다음 **Azure 리소스**를 선택 합니다.
1. 할당 된 예측 리소스의 경우 **쿼리 매개 변수 변경**을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![LUIS 포털을 사용 하 여 활성 학습에 필요한 로그를 저장 합니다.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. **로그 저장** 을 전환 하 고 **완료**를 선택 하 여 저장 합니다.

    > [!div class="mx-imgBorder"]
    > ![LUIS 포털을 사용 하 여 활성 학습에 필요한 로그를 저장 합니다.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     이 작업은 `log=true` 쿼리 문자열 매개 변수를 추가하여 예제 URL을 변경합니다. 런타임 엔드포인트에 예측 쿼리를 만들 때 변경된 예제 쿼리 URL을 복사하여 사용합니다.

## <a name="correct-intent-predictions-to-align-utterances"></a>길이 발언를 맞추기 위한 올바른 의도 예측

각 발화에는 **맞춤 의도** 열에 표시되는 제안된 의도가 포함됩니다.

> [!div class="mx-imgBorder"]
> [![LUIS가 확실 하지 않은 끝점 길이 발언을 검토 합니다.](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

해당 의도에 동의 하면 확인 표시를 선택 합니다. 제안에 동의하지 않는 경우, 맞춤 의도 드롭다운 목록에서 올바른 의도를 선택한 다음, 맞춤 의도 오른쪽에 있는 확인 표시를 선택합니다. 확인 표시를 선택한 후에는 utterance이 의도 한 것으로 이동 하 고 **검토 끝점 길이 발언** 목록에서 제거 됩니다.

> [!TIP]
> **길이 발언 검토** 목록에서 모든 예제 길이 발언의 엔터티 예측을 검토 하 고 수정 하려면 의도 세부 정보 페이지로 이동 하는 것이 중요 합니다.

## <a name="delete-utterance"></a>발화 삭제

검토 목록에서 각 발화를 삭제할 수 있습니다. 삭제되면 목록에 다시 나타나지 않습니다. 이는 사용자가 엔드포인트에서 동일한 발화를 입력하는 경우에도 적용됩니다.

Utterance를 삭제 해야 하는지 확실 하지 않은 경우이를 None으로 이동 하거나와 같은 새 의도를 만들어 `miscellaneous` utterance을 해당 의도로 이동 합니다.

## <a name="disable-active-learning"></a>활성 학습 사용 안 함

활성 학습을 사용 하지 않도록 설정 하려면 사용자 쿼리를 기록 하지 않습니다. 이렇게 [endpoint query](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) `log=false` 하려면 기본값은 false 이므로 querystring 매개 변수 및 값을 사용 하 여 끝점 쿼리를 설정 하거나 querystring 값을 사용 하지 않고이를 수행 합니다.

## <a name="next-steps"></a>다음 단계

제안된 발화에 레이블을 지정한 후 성능이 얼마나 향상되는지 테스트하려면 위쪽 패널에서 **테스트**를 선택하여 테스트 콘솔에 액세스할 수 있습니다. 테스트 콘솔을 사용하여 앱을 테스트하는 방법에 대한 자세한 내용은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.
