---
title: 사용자 발언 검토 - LUIS
titleSuffix: Azure Cognitive Services
description: 활성 학습에 의해 캡처된 발언을 검토하여 의도를 선택하고 읽기-월드 발언에 대한 엔터티를 표시합니다. 변경 사항을 수락하고, 교육하고, 게시할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221304"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>끝점 발언을 검토하여 LUIS 앱을 개선하는 방법

올바른 예측을 위해 끝점 발언을 검토하는 프로세스를 [활성 학습이라고](luis-concept-review-endpoint-utterances.md)합니다. 활성 학습은 끝점 쿼리를 캡처하고 확실하지 않은 사용자의 끝점 발언을 선택합니다. 이러한 발언을 검토하여 의도를 선택하고 이러한 읽기 월드 발언에 대한 엔터티를 표시합니다. 이러한 변경 내용을 예제 발언에 수락한 다음 학습하고 게시합니다. 그런 다음 LUIS는 발언을 보다 정확하게 식별합니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>능동 학습 지원

활성 학습을 사용하려면 사용자 쿼리를 기록해야 합니다. 이 작업은 쿼리 문자열 매개 변수 `log=true` 및 값으로 [끝점 쿼리를](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) 호출하여 수행됩니다.

LUIS 포털을 사용하여 올바른 끝점 쿼리를 생성합니다.

1. LUIS [미리 보기 포털에서](https://preview.luis.ai/)앱 목록에서 앱을 선택합니다.
1. **관리** 섹션으로 이동한 다음 **Azure 리소스를**선택합니다.
1. 할당된 예측 리소스의 경우 **쿼리 매개 변수 변경을 선택합니다.**

    > [!div class="mx-imgBorder"]
    > ![LUIS 포털을 사용하여 활성 학습에 필요한 로그를 저장합니다.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. **로그 저장을** 전환한 다음 **완료**를 선택하여 저장합니다.

    > [!div class="mx-imgBorder"]
    > ![LUIS 포털을 사용하여 활성 학습에 필요한 로그를 저장합니다.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     이 작업은 `log=true` 쿼리 문자열 매개 변수를 추가하여 예제 URL을 변경합니다. 런타임 엔드포인트에 예측 쿼리를 만들 때 변경된 예제 쿼리 URL을 복사하여 사용합니다.

## <a name="correct-intent-predictions-to-align-utterances"></a>의도 예측을 수정하여 발언을 정렬합니다.

각 발화에는 **맞춤 의도** 열에 표시되는 제안된 의도가 포함됩니다.

> [!div class="mx-imgBorder"]
> [![LUIS가 확실하지 않은 끝점 발언을 검토합니다.](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

해당 의도에 동의하면 확인 표시를 선택합니다. 제안에 동의하지 않는 경우, 맞춤 의도 드롭다운 목록에서 올바른 의도를 선택한 다음, 맞춤 의도 오른쪽에 있는 확인 표시를 선택합니다. 확인 표시를 선택하면 발언이 의도로 이동되고 끝점 발언 검토 목록에서 **제거됩니다.**

> [!TIP]
> 의도 세부 정보 페이지로 이동하여 **끝점 발언 검토** 목록의 모든 예제 발언에서 엔터티 예측을 검토하고 수정하는 것이 중요합니다.

## <a name="delete-utterance"></a>발화 삭제

검토 목록에서 각 발화를 삭제할 수 있습니다. 삭제되면 목록에 다시 나타나지 않습니다. 이는 사용자가 엔드포인트에서 동일한 발화를 입력하는 경우에도 적용됩니다.

발언을 삭제해야 하는지 확실하지 않은 경우 발언을 None 의도로 이동하거나 발언과 `miscellaneous` 같은 새 의도를 만들고 해당 의도로 이동합니다.

## <a name="disable-active-learning"></a>활성 학습 사용 안 함

활성 학습을 사용하지 않도록 설정하려면 사용자 쿼리를 기록하지 마십시오. 이 작업은 `log=false` 쿼리 문자열 매개 변수 및 값으로 [끝점 쿼리를](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) 설정하거나 기본값이 false이기 때문에 쿼리 문자열 값을 사용하지 않음으로써 수행됩니다.

## <a name="next-steps"></a>다음 단계

제안된 발화에 레이블을 지정한 후 성능이 얼마나 향상되는지 테스트하려면 위쪽 패널에서 **테스트**를 선택하여 테스트 콘솔에 액세스할 수 있습니다. 테스트 콘솔을 사용하여 앱을 테스트하는 방법에 대한 자세한 내용은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.
