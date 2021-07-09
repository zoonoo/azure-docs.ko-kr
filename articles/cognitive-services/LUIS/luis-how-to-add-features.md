---
title: 기능을 사용하여 LUIS 단어 목록 개선
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)를 사용하여 의도 및 엔터티의 감지 또는 예측을 향상시킬 수 있는 앱 기능을 해당 범주 및 패턴에 추가
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c1a0fb15be92e25b576cd29e671eed3c6a5b948f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313269"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>단어 목록의 신호를 강화하는 기능 사용

정확성을 개선하기 위해 LUIS 앱에 기능을 추가할 수 있습니다. 기능은 특정 단어와 구가 앱 도메인 용어의 일부라는 힌트를 제공하여 LUIS를 지원합니다.

[개념](luis-concept-feature.md)을 검토하여 기능을 사용하는 시기 및 이유를 이해합니다.

## <a name="add-phrase-list-as-a-feature"></a>구문 목록 기능 추가

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **빌드** 를 선택한 다음, 앱의 왼쪽 패널에서 **기능** 을 선택합니다.

1. **기능** 페이지에서 **+ 만들기** 를 선택합니다.

1. **새 구문 목록 기능 만들기** 대화 상자에서 `Cities`와 같은 이름을 입력합니다. **값** 상자에 `Seattle`과 같은 도시 예를 입력합니다. 한 번에 하나의 값을 입력하거나 쉼표로 구분해서 여러 개의 값을 입력한 후 **Enter** 키를 누릅니다.

    > [!div class="mx-imgBorder"]
    > ![도시 기능(구문 목록)을 추가하는 스크린샷](./media/luis-add-features/add-phrase-list-cities.png)

    LUIS에 대한 충분한 값을 입력하면 제안이 표시됩니다. 제안된 값을 **+ 모두 추가** 하거나 개별 항목을 선택할 수 있습니다.

1. 구문을 서로 교환해서 사용할 수 있는 경우 **서로 교환 가능한 값** 을 선택한 상태로 유지합니다. 교환 가능한 구문 목록 기능은 학습용 동의어 목록으로 사용됩니다. 교환 불가능한 구문 목록은 학습을 위한 별도의 기능으로 사용됩니다(즉, 기능이 유사하지만 구문을 바꾸면 의도가 변경됨).

1. 구문 목록은 **전역** 설정을 사용하여 전체 앱에 적용하거나 특정 모델(의도 도는 엔터티)에 적용할 수 있습니다. 의도 또는 엔터티의 _기능_ 으로 구문 목록을 만드는 경우 토글은 전역에 대해 설정되지 않습니다. 이 경우 토글의 의미는 해당 기능이 해당 모델에 대해서만 로컬이므로 애플리케이션에 대한 _전역이 아니라_ 는 것입니다.

1. **완료** 를 선택합니다. 새 기능이 **ML 기능** 페이지에 추가됩니다.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> **ML 기능** 페이지의 상황에 맞는 도구 모음에서 구 목록을 삭제하거나 비활성화할 수 있습니다.

## <a name="global-phrase-list-applies-to-entire-app"></a>전체 앱에 적용되는 전역 구문 목록

구문 목록은 지원하려는 의도 또는 엔터티에 적용해야 하지만 전체 앱에 **전역 기능** 으로 적용해야 하는 경우가 있을 수 있습니다.

ML 기능 페이지에서 구문 목록을 선택하고 위쪽 상황별 도구 모음에서 **전역 설정** 을 선택합니다.

## <a name="model-as-a-feature"></a>기능으로서의 모델

엔터티는 [의도 또는 엔터티에 대한 기능](luis-concept-feature.md)일 수 있습니다.

의도에 엔터티 기능을 추가하려면 의도 페이지에서 의도를 선택한 다음 상황별 도구 모음 위에서 **+ 기능 추가** 를 선택합니다. 이 목록에는 기능으로 적용할 수 있는 모든 구문 목록과 엔터티가 포함됩니다.

다른 엔터티에 엔터티 기능을 추가하려면 의도 세부 정보 페이지에서 [엔터티 팔레트](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette)를 사용하여 기능을 추가하거나 엔터티 세부 정보 페이지에서 [기능을 추가](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

기능을 추가, 편집, 삭제 또는 비활성화한 후에 다시 [앱을 학습하고 테스트](luis-interactive-test.md)하여 성능이 개선되는지 확인합니다.
