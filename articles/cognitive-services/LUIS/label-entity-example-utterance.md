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
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134202"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>예제 발화의 기계 학습된 엔터티에 레이블 지정

예제 발화의 엔터티에 레이블을 지정하여 LUIS에 엔터티의 예제 및 엔터티가 발화에서 표시될 수 있는 위치를 알려줍니다. 

## <a name="labeling-machine-learned-entity"></a>기계 학습된 엔터티에 레이블 지정

`hi, please I want a cheese pizza in 20 minutes` 구문을 살펴보겠습니다. 

1. 맨 왼쪽 텍스트를 선택한 다음, 엔터티의 맨 오른쪽 텍스트를 선택합니다. 다음 이미지에서 _주문 완료_에 레이블이 지정됩니다.

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 완료 레이블 지정](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. 팝업 창에서 엔터티를 선택합니다. 레이블이 지정된 피자 주문 완료 엔터티에는 레이블이 지정된 모든 단어(영어로 왼쪽에서 오른쪽)가 포함되어 있습니다. 

> [!TIP]
> 팝업 창에서 사용할 수 있는 엔터티는 텍스트가 표시되는 컨텍스트와 관련이 있습니다. 예를 들어 5단계 기계 학습된 엔터티가 있는데 3단계 텍스트를 선택하는 경우(예제 발화에서 레이블이 지정된 엔터티 이름으로 표시됨) 팝업 창에서 사용할 수 있는 엔터티는 3단계 하위 구성 요소의 컨텍스트로 제한됩니다(4단계 하위 구성 요소). 

## <a name="review-labeled-text"></a>레이블이 지정된 텍스트 검토

레이블을 지정한 후 예제 발화를 검토합니다. LUIS는 레이블을 지정한 후 예제 발화에 현재 모델을 적용합니다. 실선은 텍스트에 레이블이 지정되었음을 나타냅니다. 

> [!div class="mx-imgBorder"]
> ![기계 학습된 엔터티에 완료 레이블 지정됨](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>학습 시기

현재 모델에서 레이블이 지정된 엔터티를 지원해야 하지만 예제 발화는 계속해서 텍스트를 계속 표시하지만 레이블이 지정되지 않은 경우에는 앱을 학습시킵니다.  

## <a name="confirm-predicted-entity"></a>예측된 엔터티 확인

시각적 표시기가 발화 위에 있으면 텍스트를 예측했지만 _아직 레이블이 지정되지 않았음_을 나타냅니다. 예측을 레이블로 전환하려면 발화를 선택한 다음, **엔터티 예측 확인**을 선택합니다.

> [!div class="mx-imgBorder"]
> ![기계 학습된 엔터티에 완료 예측](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>엔터티 팔레트 커서로 그려서 하위 구성 요소 엔터티에 레이블 지정

1. 예측(예제 발화 위에 표시되는 엔터티)을 수정하려면 엔터티 팔레트를 엽니다. 

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. 엔터티 하위 구성 요소를 선택합니다. 이 작업은 새 커서로 시각적으로 표시됩니다. 커서는 포털에서 이동하는 대로 마우스를 따라갑니다. 

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. 예제 발화에서 커서를 사용하여 엔터티를 _그립니다_. 

    > [!div class="mx-imgBorder"]
    > ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>기계 학습된 엔터티에 일치하는 텍스트 엔터티 레이블 지정

일치하는 텍스트 엔터티는 미리 빌드된 엔터티, 정규식 엔터티 및 목록 엔터티를 포함합니다. 이를 기계 학습된 엔터티를 만들거나 편집할 때 하위 구성 요소에 대한 제약 조건으로 기계 학습된 엔터티에 추가합니다. 

**이러한 제약 조건이 추가되면 예제 발화에서 일치하는 텍스트에 레이블을 지정할 필요가 없습니다.**

## <a name="entity-prediction-errors"></a>엔터티 예측 오류

엔터티 예측 오류는 주의 표시기를 표시합니다. 이는 예측된 엔터티가 레이블이 지정된 엔터티와 일치하지 않음을 나타냅니다. 

> [!div class="mx-imgBorder"]
> ![기계 학습된 엔터티에 대한 엔터티 팔레트](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>다음 단계

[대시보드](luis-how-to-use-dashboard.md)를 사용하고 [엔드포인트 발화를 검토](luis-how-to-review-endpoint-utterances.md)하여 앱의 예측 품질을 개선합니다.
