---
title: 예제 발화의 엔터티에 레이블 지정
titleSuffix: Azure Cognitive Services
description: LUIS 포털의 의도 세부 정보 페이지에서 예제 발화의 하위 구성 요소를 사용하여 기계 학습된 엔터티에 레이블을 지정하는 방법에 대해 알아봅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: diberry
ms.openlocfilehash: 58e813d30273db4e011039aa43cd59c61507895e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74383697"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>예제 발화의 기계 학습된 엔터티에 레이블 지정

발화 예제의 엔터티에 레이블을 지정하면 해당 엔터티의 콘텐츠와 해당 엔터티가 발화에서 표시될 수 있는 위치에 대한 예제가 LUIS에 제공됩니다. 

## <a name="labeling-machine-learned-entity"></a>기계 학습된 엔터티에 레이블 지정

`hi, please I want a cheese pizza in 20 minutes` 구문을 살펴보겠습니다. 

1. 엔터티에 있는 가장 왼쪽의 텍스트와 가장 오른쪽의 텍스트를 차례로 선택한 다음, 레이블(여기서는 '주문 완료')을 지정하려는 엔터티를 선택합니다. 다음 이미지에서 _주문 완료_에 레이블이 지정됩니다.

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 완료 레이블 지정](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. 팝업 창에서 엔터티를 선택합니다. 레이블이 지정된 피자 주문 완료 엔터티에는 레이블이 지정된 모든 단어(영어로 왼쪽에서 오른쪽)가 포함되어 있습니다. 

## <a name="review-labeled-text"></a>레이블이 지정된 텍스트 검토

레이블이 지정되면 발화 예제를 검토하고 선택한 텍스트 범위에서 선택한 엔터티에 밑줄이 그어져 있는지 확인합니다. 실선은 텍스트에 레이블이 지정되었음을 나타냅니다. 

> [!div class="mx-imgBorder"]
> ![기계 학습된 엔터티에 완료 레이블 지정됨](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>예측된 엔터티 확인

텍스트 범위 주위에 점선으로 표시된 상자가 있고 엔터티 이름이 발화 위에 있으면 텍스트가 예측되었지만 _레이블이 아직 지정되지 않았음_을 나타냅니다. 예측을 레이블로 전환하려면 발화 행을 선택한 다음, **엔터티 예측 확인**을 선택합니다.

> [!div class="mx-imgBorder"]
> ![기계 학습된 엔터티에 완료 예측](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

또는 텍스트 위에서 엔터티 이름을 선택한 다음, 표시되는 메뉴에서 **예측 확인**을 선택할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![메뉴를 사용한 기계 학습된 엔터티 예측 확인](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>엔터티 팔레트 커서로 그려서 엔터티 레이블 지정

엔터티 팔레트는 이전 레이블 지정 환경에 대한 대안을 제공합니다. 텍스트 위를 브러시하여 엔터티에 레이블을 즉시 지정할 수 있습니다.

1. 발화 테이블의 오른쪽 위에 있는 형광펜 아이콘을 선택하여 엔터티 팔레트를 엽니다. 

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. 엔터티 구성 요소를 선택합니다. 이 작업은 새 커서로 시각적으로 표시됩니다. 커서는 포털에서 이동하는 대로 마우스를 따라갑니다. 

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. 예제 발화에서 커서를 사용하여 엔터티를 _그립니다_.

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>기계 학습된 엔터티의 하위 구성 요소 레이블 지정

엔터티의 하위 구성 요소에는 최상위 엔터티와 똑같은 방식으로 레이블이 지정됩니다. 텍스트를 선택하는 경우 팝업 창에서 사용할 수 있는 엔터티는 텍스트가 표시되는 컨텍스트를 기준으로 합니다. 예를 들어 수준 5 기계 학습된 엔터티가 있고 수준 1 및 수준 2의 레이블이 지정된 텍스트(발화 예제 아래에서 레이블이 지정된 엔터티 이름으로 표시됨)를 선택하는 경우 팝업 창에서 사용할 수 있는 엔터티는 수준 3 구성 요소의 컨텍스트로 제한됩니다. 다른 엔터티를 사용하여 텍스트에 레이블을 지정하려면 **다른 엔터티로 레이블 지정** 옵션을 선택합니다.

> [!div class="mx-imgBorder"]
> ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

부모 구성 요소에도 레이블이 지정된 경우에만 하위 구성 요소에 레이블을 지정할 수 있습니다. 

## <a name="unlabel-entities"></a>엔터티 레이블 지정 해제

엔터티의 레이블 지정을 해제하려면 텍스트 아래에서 엔터티 이름을 선택하고 **레이블 지정 해제**를 선택합니다. 레이블 지정을 해제하려는 엔터티에 레이블이 지정된 하위 구성 요소가 있는 경우 먼저 하위 구성 요소의 레이블 지정을 해제해야 합니다. 

## <a name="editing-labels-using-the-entity-palette"></a>엔터티 팔레트를 사용하여 레이블 편집

레이블을 지정하면서 실수하는 경우 엔터티 팔레트는 빠른 편집을 수행할 수 있는 손쉬운 도구입니다. 예를 들어 엔터티 레이블이 실수로 추가 단어 범위에 걸쳐 있고 이미 하위 구성 요소에 레이블이 지정된 경우 엔터티 팔레트를 사용하여 필요한 더 짧은 단어 범위 위를 브러시할 수 있습니다.

예:

1. 피자 유형 하위 구성 요소는 "cheese pizza with"에 걸쳐 있고, 여기에는 잘못된 "with" 단어가 추가로 포함되어 있습니다.

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/edit-label-with-palette-1.png)

2. 엔터티 팔레트를 사용하여 피자 유형을 선택하고 "cheese pizza" 위를 브러시합니다. 결과적으로 cheese pizza만 피자 유형 레이블로 지정됩니다.

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/edit-label-with-palette-2.png)


## <a name="labels-for-matching-text-entities"></a>텍스트 일치 엔터티에 대한 레이블

텍스트 일치 엔터티에는 미리 작성된 엔터티, 정규식 엔터티, 목록 엔터티 및 패턴 엔터티가 포함됩니다. 이러한 엔터티는 LUIS에서 자동으로 레이블을 지정하므로 사용자가 수동으로 레이블을 지정할 필요가 없습니다.

## <a name="entity-prediction-errors"></a>엔터티 예측 오류

엔터티 예측 오류는 예측된 엔터티가 레이블이 지정된 엔터티와 일치하지 않음을 나타냅니다. 이는 발화 옆에 주의 표시기를 사용하여 시각화됩니다.

> [!div class="mx-imgBorder"]
> ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>다음 단계

[대시보드](luis-how-to-use-dashboard.md)를 사용하고 [엔드포인트 발화를 검토](luis-how-to-review-endpoint-utterances.md)하여 앱의 예측 품질을 개선합니다.