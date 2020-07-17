---
title: 예제 발화의 엔터티에 레이블 지정
description: LUIS 포털의 의도 세부 정보 페이지에서 utterance 예제에 있는 하위 엔터티를 사용 하 여 기계 학습 엔터티에 레이블을 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: f576f42f46930d81212ff754a14ee30c75b79624
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610492"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>예 utterance에서 기계 학습 엔터티 레이블

발화 예제의 엔터티에 레이블을 지정하면 해당 엔터티의 콘텐츠와 해당 엔터티가 발화에서 표시될 수 있는 위치에 대한 예제가 LUIS에 제공됩니다.

컴퓨터에서 학습 한 엔터티 및 하위 엔터티에 레이블을 지정할 수 있습니다.

정규식, 목록 또는 미리 작성 된 엔터티의 레이블을 지정할 수 없으므로 엔터티 또는 하위 엔터티를 만든 다음 엔터티 또는 하위 엔터티에 이러한 엔터티를 기능 (해당 하는 경우)으로 추가 합니다.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>의도 세부 정보 페이지의 레이블 예 길이 발언

Utterance 내에서 엔터티 예를 레이블 하려면 utterance의 의도를 선택 합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스**를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택 하 여 앱을 엽니다.
1. 엔터티를 사용 하 여 추출 하기 위해 레이블을 지정할 길이 발언 예제를 포함 하는 의도를 선택 합니다.
1. 레이블을 지정할 텍스트를 선택 하 고 엔터티를 선택 합니다.

## <a name="two-techniques-to-label-entities"></a>엔터티에 레이블을 적용할 수 있는 두 가지 방법

의도 세부 정보 페이지에는 두 가지 레이블 지정 기술이 지원 됩니다.
* [엔터티 팔레트](#label-with-the-entity-palette-visible) 에서 엔터티 또는 하위 엔터티를 선택 하 고 예 utterance 텍스트를 선택 합니다. 이 방법은 스키마에 따라 올바른 엔터티 또는 하위 엔터티를 시각적으로 확인할 수 있기 때문에 권장 되는 방법입니다.
* 예 utterance 텍스트를 먼저 선택 합니다. 이렇게 하면 [레이블 선택 항목](#how-to-label-entity-from-in-place-menu) 의 팝업 메뉴가 표시 됩니다.

## <a name="label-with-the-entity-palette-visible"></a>엔터티 팔레트가 표시 된 레이블

[엔터티를 사용 하 여 스키마를 계획](luis-how-plan-your-app.md)한 후 레이블을 지정 하는 동안 **엔터티 팔레트** 를 계속 표시 합니다. **엔터티 팔레트** 는 추출 하려는 엔터티에 대 한 미리 알림입니다.

**엔터티 팔레트**에 액세스 하려면 **@** 예제 utterance 목록 위의 상황별 도구 모음에서 기호를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![의도 세부 정보 페이지에서 엔터티 팔레트의 스크린샷](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>엔터티 팔레트에서 엔터티를 레이블 하는 방법

엔터티 팔레트는 이전 레이블 지정 환경에 대한 대안을 제공합니다. 텍스트 위를 브러시하여 엔터티에 레이블을 즉시 지정할 수 있습니다.

1. **@** Utterance 테이블의 오른쪽 위에 있는 기호를 선택 하 여 엔터티 팔레트를 엽니다.

2. 레이블을 지정할 색상표에서 엔터티를 선택 합니다. 이 작업은 새 커서로 시각적으로 표시됩니다. 커서는 LUIS 포털에서 이동할 때 마우스를 따릅니다.

3. 예제 발화에서 커서를 사용하여 엔터티를 _그립니다_.

    > [!div class="mx-imgBorder"]
    > ![기계 학습 엔터티에 대 한 엔터티 팔레트](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>엔터티 팔레트의 기능으로 엔터티 추가

엔터티 팔레트의 아래쪽 섹션에서는 현재 선택 된 엔터티에 기능을 추가할 수 있습니다. 모든 기존 엔터티 및 구 목록에서 선택 하거나 새 구 목록을 만들 수 있습니다.

> [!div class="mx-imgBorder"]
> ![엔터티를 기능으로 사용 하는 엔터티 팔레트의 스크린샷](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>엔터티 역할 레이블 지정

엔터티 역할은 **엔터티 팔레트**를 사용 하 여 레이블이 지정 됩니다.

1. 의도 세부 정보 페이지의 컨텍스트 도구 모음에서 **엔터티 팔레트**를 선택합니다.
1. 엔터티 팔레트가 열리면 엔터티 목록에서 엔터티를 선택합니다.
1. 엔터티 목록 아래에서 기존 역할을 선택 합니다.
1. 예제 발화 텍스트에서 엔터티 역할을 사용하여 텍스트에 레이블을 지정합니다.

## <a name="how-to-label-entity-from-in-place-menu"></a>내부 메뉴에서 엔터티를 레이블 하는 방법

내부 레이블을 지정 하면 utterance 내에서 텍스트를 빠르게 선택 하 고 레이블을 지정할 수 있습니다. 레이블이 지정 된 텍스트에서 machine learning 엔터티 또는 목록 엔터티를 만들 수도 있습니다.

예 utterance를 참조 `hi, please I want a cheese pizza in 20 minutes` 하세요.

맨 왼쪽 텍스트를 선택한 다음 엔터티의 맨 오른쪽 텍스트를 선택 하 고 바로 이동 메뉴에서 레이블을 지정할 엔터티를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![전체 컴퓨터 레이블-학습 엔터티](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>레이블이 지정된 텍스트 검토

레이블이 지정되면 발화 예제를 검토하고 선택한 텍스트 범위에서 선택한 엔터티에 밑줄이 그어져 있는지 확인합니다. 실선은 텍스트에 레이블이 지정되었음을 나타냅니다.

> [!div class="mx-imgBorder"]
> ![레이블 전체 기계 학습 엔터티](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>예측된 엔터티 확인

텍스트 범위 주위에 점선으로 구분 된 상자가 있는 경우 텍스트를 예측 했지만 _레이블이 아직 지정 되지_않았음을 나타냅니다. 예측을 레이블로 전환 하려면 utterance 행을 선택 하 고 상황별 도구 모음에서 **엔터티 확인** 을 선택 합니다.

## <a name="relabeling-over-existing-entities"></a>기존 엔터티를 통해 레이블 재지정

이미 레이블이 지정 된 텍스트를 레이블 재지정 경우 LUIS는 기존 레이블을 분할 하거나 병합할 수 있습니다.

## <a name="labeling-for-punctuation"></a>문장 부호 레이블

문장 부호에 레이블을 추가할 필요가 없습니다. [응용 프로그램 설정을](luis-reference-application-settings.md) 사용 하 여 문장 부호가 utterance 예측에 영향을 주는 방식을 제어 합니다.

## <a name="unlabel-entities"></a>엔터티 레이블 지정 해제

> [!NOTE]
> 컴퓨터에서 배운 엔터티만 레이블 지정할 수 없습니다. 정규식 엔터티, 목록 엔터티 또는 미리 작성 한 엔터티를 레이블 또는 unlabel 수 없습니다.

엔터티를 unlabel 하려면 엔터티를 선택 하 고 내부 메뉴에서 **unlabel** 를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![레이블 없는 엔터티를 보여 주는 스크린샷](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>부모 및 자식 엔터티에 대 한 자동 레이블 지정

부모 엔터티에 레이블을 지정 하는 경우 현재 학습 된 버전을 기반으로 예측할 수 있는 하위 엔터티는 레이블이 지정 됩니다.

하위 엔터티에 대 한 레이블을 지정 하는 경우 부모는 자동으로 레이블이 지정 됩니다.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>비시스템 학습 엔터티에 대 한 자동 레이블 지정

비 기계어 학습 엔터티에는 미리 작성 된 엔터티, 정규식 엔터티, 목록 엔터티 및 패턴이 포함 됩니다. 모든 엔터티. 이러한 엔터티는 LUIS에서 자동으로 레이블을 지정하므로 사용자가 수동으로 레이블을 지정할 필요가 없습니다.

## <a name="intent-prediction-errors"></a>의도 예측 오류

의도 예측 오류는 현재 학습 된 앱이 있는 utterance 예제는 의도에 대해 예측 되지 않음을 나타냅니다.

의도 세부 정보 페이지에서 [이러한 오류를 확인](luis-how-to-add-intents.md#intent-prediction-errors) 하는 방법에 대해 알아봅니다.

## <a name="entity-prediction-errors"></a>엔터티 예측 오류

엔터티 예측 오류는 예측된 엔터티가 레이블이 지정된 엔터티와 일치하지 않음을 나타냅니다. 이는 발화 옆에 주의 표시기를 사용하여 시각화됩니다.

> [!div class="mx-imgBorder"]
> ![기계 학습 엔터티에 대 한 엔터티 팔레트](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>다음 단계

[대시보드](luis-how-to-use-dashboard.md)를 사용하고 [엔드포인트 발화를 검토](luis-how-to-review-endpoint-utterances.md)하여 앱의 예측 품질을 개선합니다.
