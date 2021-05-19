---
title: 예제 발화의 엔터티에 레이블 지정
description: LUIS 포털의 의도 세부 정보 페이지에서 예제 발화의 하위 엔터티를 사용하여 기계 학습 엔터티에 레이블을 지정하는 방법에 대해 알아봅니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96019738"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>예제 발화의 기계 학습 엔터티에 레이블 지정

발화 예제의 엔터티에 레이블을 지정하면 해당 엔터티의 콘텐츠와 해당 엔터티가 발화에서 표시될 수 있는 위치에 대한 예제가 LUIS에 제공됩니다.

기계 학습 엔터티 및 하위 엔터티에 레이블을 지정할 수 있습니다.

정규식, 목록 또는 미리 빌드된 엔터티의 레이블을 지정할 수 없으므로 엔터티 또는 하위 엔터티를 만든 다음 엔터티 또는 하위 엔터티에 이러한 엔터티를 기능으로 추가합니다(해당하는 경우).

## <a name="label-example-utterances-from-the-intent-detail-page"></a>의도 세부 정보 페이지에서 예제 발화 레이블 지정

발화 내에서 엔터티 예의 레이블을 지정하려면 발화의 의도를 선택합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. 엔터티를 사용하여 추출하기 위해 레이블을 지정할 예제 발화를 포함하는 의도를 선택합니다.
1. 레이블을 지정하려는 텍스트를 선택한 다음 엔터티를 선택합니다.

## <a name="two-techniques-to-label-entities"></a>엔터티에 레이블을 지정하는 두 가지 기법

의도 세부 정보 페이지에는 두 가지 레이블 지정 기법이 지원됩니다.
* [엔터티 팔레트](#label-with-the-entity-palette-visible)에서 엔터티 또는 하위 엔터티를 선택하고 예제 발화 텍스트 내에서 선택합니다. 이 방법은 스키마에 따라 올바른 엔터티 또는 하위 엔터티를 사용하는지 시각적으로 확인할 수 있기 때문에 권장되는 방법입니다.
* 먼저 예제 발화 텍스트 내에서 선택합니다. 이렇게 하면 [레이블 선택 항목](#how-to-label-entity-from-in-place-menu)의 팝업 메뉴가 표시됩니다.

## <a name="label-with-the-entity-palette-visible"></a>엔터티 팔레트를 표시하여 레이블 지정

[엔터티를 사용하여 스키마를 계획](luis-how-plan-your-app.md)한 후 레이블을 지정하는 동안 **엔터티 팔레트** 를 계속 표시합니다. **엔터티 팔레트** 는 추출하려던 엔터티에 대한 미리 알림입니다.

**엔터티 팔레트** 에 액세스하려면 예제 발화 목록 위의 상황별 도구 모음에서 **@** 기호를 선택합니다.

> [!div class="mx-imgBorder"]
> ![의도 세부 정보 페이지의 엔터티 팔레트 스크린샷](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>엔터티 팔레트에서 엔터티의 레이블을 지정하는 방법

엔터티 팔레트는 이전 레이블 지정 환경에 대한 대안을 제공합니다. 텍스트 위를 브러시하여 엔터티에 레이블을 즉시 지정할 수 있습니다.

1. 발화 테이블의 오른쪽 위에 있는 **@** 기호를 선택하여 엔터티 팔레트를 엽니다.

2. 팔레트에서 레이블을 지정할 엔터티를 선택합니다. 이 작업은 새 커서로 시각적으로 표시됩니다. 커서는 LUIS 포털에서 이동하는 대로 마우스를 따라갑니다.

3. 예제 발화에서 커서를 사용하여 엔터티를 _그립니다_.

    > [!div class="mx-imgBorder"]
    > ![커서로 그린 엔터티를 보여 주는 스크린샷](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>엔터티 팔레트의 기능으로 엔터티 추가

엔터티 팔레트의 아래쪽 섹션에서는 현재 선택된 엔터티에 기능을 추가할 수 있습니다. 모든 기존 엔터티 및 구문 목록에서 선택하거나 새 구문 목록을 만들 수 있습니다.

> [!div class="mx-imgBorder"]
> ![엔터티를 기능으로 사용하는 엔터티 팔레트의 스크린샷](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>엔터티 역할 레이블 지정

엔터티 역할은 **엔터티 팔레트** 를 사용하여 레이블이 지정됩니다.

1. 의도 세부 정보 페이지의 컨텍스트 도구 모음에서 **엔터티 팔레트** 를 선택합니다.
1. 엔터티 팔레트가 열리면 엔터티 목록에서 엔터티를 선택합니다.
1. 엔터티 목록 아래에서 기존 역할을 선택합니다.
1. 예제 발화 텍스트에서 엔터티 역할을 사용하여 텍스트에 레이블을 지정합니다.

## <a name="how-to-label-entity-from-in-place-menu"></a>상황별 메뉴에서 엔터티의 레이블을 지정하는 방법

상황별 레이블 지정을 통해 발화 내에서 텍스트를 빠르게 선택하고 레이블을 지정할 수 있습니다. 레이블이 지정된 텍스트에서 기계 학습 엔터티 또는 목록 엔터티를 만들 수도 있습니다.

예제 발화 `hi, please I want a cheese pizza in 20 minutes`를 참조하세요.

엔터티에 있는 가장 왼쪽의 텍스트와 가장 오른쪽의 텍스트를 차례로 선택한 다음, 상황별 메뉴에서 레이블을 지정하려는 엔터티를 선택합니다.

> [!div class="mx-imgBorder"]
> ![기계 학습 엔터티에 완료 레이블 지정](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>레이블이 지정된 텍스트 검토

레이블이 지정되면 발화 예제를 검토하고 선택한 텍스트 범위에서 선택한 엔터티에 밑줄이 그어져 있는지 확인합니다. 실선은 텍스트에 레이블이 지정되었음을 나타냅니다.

> [!div class="mx-imgBorder"]
> ![기계 학습 엔터티에 완료 레이블이 지정됨](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>예측된 엔터티 확인

텍스트 범위 주위에 점선으로 된 상자가 있는 경우 텍스트를 예측했지만 _레이블이 아직 지정되지 않았음_ 을 나타냅니다. 예측을 레이블로 전환하려면 발화 행을 선택한 다음, 상황별 도구 모음에서 **엔터티 확인** 을 선택합니다.

## <a name="relabeling-over-existing-entities"></a>기존 엔터티에 대한 레이블 재지정

이미 레이블이 지정된 텍스트의 레이블을 재지정하는 경우 LUIS는 기존 레이블을 분할하거나 병합할 수 있습니다.

## <a name="labeling-for-punctuation"></a>문장 부호에 대한 레이블 지정

문장 부호에 대한 레이블을 지정할 필요가 없습니다. [애플리케이션 설정](luis-reference-application-settings.md)을 사용하여 문장 부호가 발화 예측에 영향을 주는 방식을 제어합니다.

## <a name="unlabel-entities"></a>엔터티 레이블 지정 해제

> [!NOTE]
> 기계 학습 엔터티만 레이블 지정을 해제할 수 있습니다. 정규식 엔터티, 목록 엔터티 또는 미리 빌드된 엔터티는 레이블을 지정하거나 해제할 수 없습니다.

엔터티의 레이블 지정을 해제하려면 엔터티를 선택하고 상황별 메뉴에서 **레이블 지정 해제** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![엔터티 레이블 지정 해제를 보여 주는 스크린샷](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>상위 및 하위 엔터티에 대한 자동 레이블 지정

상위 엔터티에 레이블을 지정하는 경우 현재 학습된 버전을 기반으로 예측할 수 있는 모든 하위 엔터티에 레이블이 지정됩니다.

하위 엔터티에 대한 레이블을 지정하는 경우 상위는 자동으로 레이블이 지정됩니다.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>비 기계 학습 엔터티에 대한 자동 레이블 지정

비 기계 학습 엔터티에는 미리 빌드된 엔터티, 정규식 엔터티, 목록 엔터티 및 pattern.any 엔터티가 포함됩니다. 이러한 엔터티는 LUIS에서 자동으로 레이블을 지정하므로 사용자가 수동으로 레이블을 지정할 필요가 없습니다.

## <a name="intent-prediction-errors"></a>의도 예측 오류

의도 예측 오류는 현재 학습된 앱에서 예제 발화가 의도에 대해 예측되지 않음을 나타냅니다.

의도 세부 정보 페이지에서 [이러한 오류를 확인](luis-how-to-add-intents.md#intent-prediction-errors)하는 방법에 대해 알아봅니다.

## <a name="entity-prediction-errors"></a>엔터티 예측 오류

엔터티 예측 오류는 예측된 엔터티가 레이블이 지정된 엔터티와 일치하지 않음을 나타냅니다. 이는 발화 옆에 주의 표시기를 사용하여 시각화됩니다.

> [!div class="mx-imgBorder"]
> ![기계 학습 엔터티에 대한 엔터티 팔레트](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>다음 단계

[대시보드](luis-how-to-use-dashboard.md)를 사용하고 [엔드포인트 발화를 검토](luis-how-to-review-endpoint-utterances.md)하여 앱의 예측 품질을 개선합니다.
