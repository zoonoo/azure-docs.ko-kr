---
title: 사용자 길이 발언 검토-LUIS
titleSuffix: Azure Cognitive Services
description: 활성 학습은 끝점 쿼리를 캡처하고 사용자의 끝점 길이 발언를 선택 합니다. 이러한 길이 발언를 검토 하 여 이러한 읽기 전용 길이 발언에 대 한 의도 및 표시 엔터티를 선택 합니다. 예제에 이러한 변경 내용을 적용 한 다음 학습 및 게시 길이 발언. LUIS는 길이 발언를 보다 정확 하 게 식별 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d5652857f2f35e392d3f512001044fd06bc0a0c9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499064"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>활성 학습을 위해 LUIS 포털에서 엔드포인트 길이 발언을 검토 하는 방법

[활성 학습](luis-concept-review-endpoint-utterances.md) 은 끝점 쿼리를 캡처하고 사용자의 끝점 길이 발언를 선택 합니다. 이러한 길이 발언를 검토 하 여 이러한 읽기 전용 길이 발언에 대 한 의도 및 표시 엔터티를 선택 합니다. 예제에 이러한 변경 내용을 적용 한 다음 학습 및 게시 길이 발언. LUIS는 길이 발언를 보다 정확 하 게 식별 합니다.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>활성 학습 사용

활성 학습을 사용 하도록 설정 하려면 사용자 쿼리를 로깅합니다. 이는 `log=true` querystring 매개 변수 및 값을 사용 하 여 [끝점 쿼리](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) 를 설정 하 여 수행 합니다.

## <a name="disable-active-learning"></a>활성 학습 사용 안 함

활성 학습을 사용 하지 않도록 설정 하려면 사용자 쿼리를 기록 하지 않습니다. 이는 `log=false` querystring 매개 변수 및 값을 사용 하 여 [끝점 쿼리](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) 를 설정 하 여 수행 합니다.

## <a name="filter-utterances"></a>발화 필터링

1. **내 앱** 페이지에서 이름을 선택하여 앱(예: TravelAgent)을 열고 위쪽 막대에서 **빌드**를 선택합니다.

1. **앱 성능 개선**에서 **엔드포인트 발화 검토**를 선택합니다.

1. **엔드포인트 발화 검토** 페이지에서 **의도 또는 엔터티별 목록 필터링** 텍스트 상자를 선택합니다. 이 드롭다운 목록에서 **의도** 아래에 모든 의도가 포함되고 **엔터티** 아래에 모든 엔터티가 포함됩니다.

    ![발화 필터](./media/label-suggested-utterances/filter.png)

1. 드롭다운 목록에서 범주(의도 또는 엔터티)를 선택하고 발화를 검토합니다.

    ![의도 발화](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>엔터티 레이블 지정
LUIS는 엔터티 토큰(단어)을 파란색으로 강조 표시된 엔터티 이름으로 바꿉니다. 레이블이 없는 엔터티가 발화에 있는 경우 발화에서 엔터티에 레이블을 지정합니다. 

1. 발화에서 단어를 선택합니다. 

1. 목록에서 엔터티를 선택합니다.

    ![엔터티 레이블 지정](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>단일 발화 맞춤

각 발화에는 **맞춤 의도** 열에 표시되는 제안된 의도가 포함됩니다. 

1. 해당 제안에 동의하는 경우 확인 표시를 선택합니다.

    ![맞춤 의도 유지](./media/label-suggested-utterances/align-intent-check.png)

1. 제안에 동의하지 않는 경우, 맞춤 의도 드롭다운 목록에서 올바른 의도를 선택한 다음, 맞춤 의도 오른쪽에 있는 확인 표시를 선택합니다. 

    ![의도 맞춤](./media/label-suggested-utterances/align-intent.png)

1. 확인 표시를 선택한 후에는 발화가 목록에서 제거됩니다. 

## <a name="align-several-utterances"></a>여러 발화 맞춤

여러 발화를 맞추려면 발화 왼쪽의 상자를 선택한 다음, **선택한 항목 추가** 단추를 선택합니다. 

![여러 항목 맞춤](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>맞춤 의도 확인

**의도** 페이지로 이동하고 의도 이름을 선택하고 발화를 검토하여 발화가 올바른 의도에 맞춰졌는지 확인할 수 있습니다. **엔드포인트 발화 검토**의 발화가 목록에 있습니다.

## <a name="delete-utterance"></a>발화 삭제

검토 목록에서 각 발화를 삭제할 수 있습니다. 삭제되면 목록에 다시 나타나지 않습니다. 이는 사용자가 엔드포인트에서 동일한 발화를 입력하는 경우에도 적용됩니다. 

발화를 삭제해야 하는지 확실하지 않은 경우 해당 발화를 없음 의도로 이동하거나 “기타”와 같은 새 의도를 만들고 발화를 해당 의도로 이동합니다. 

## <a name="delete-several-utterances"></a>여러 발화 삭제

여러 발화를 삭제하려면 각 항목을 선택하고 **선택한 항목 추가** 단추 오른쪽에 있는 휴지통을 선택합니다.

![여러 항목 삭제](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>다음 단계

제안된 발화에 레이블을 지정한 후 성능이 얼마나 향상되는지 테스트하려면 위쪽 패널에서 **테스트**를 선택하여 테스트 콘솔에 액세스할 수 있습니다. 테스트 콘솔을 사용하여 앱을 테스트하는 방법에 대한 자세한 내용은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.
