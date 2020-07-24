---
title: 기능을 사용 하 여 LUIS 단어 목록 개선
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)를 사용하여 의도 및 엔터티의 감지 또는 예측을 향상시킬 수 있는 앱 기능을 해당 범주 및 패턴에 추가
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 25698fe2b05cbfb564e441e488bfa93221a9618a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075197"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>기능을 사용 하 여 단어 목록 신호 상승

정확성을 개선하기 위해 LUIS 앱에 기능을 추가할 수 있습니다. 기능은 특정 단어와 구가 앱 도메인 용어의 일부라는 힌트를 제공하여 LUIS를 지원합니다.

[개념](luis-concept-feature.md) 을 검토 하 여 기능을 사용 하는 시기 및 이유를 이해 합니다.

## <a name="add-phrase-list-as-a-feature"></a>기능으로 문구 목록 추가

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스**를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택 하 여 앱을 엽니다.
1. **빌드**를 선택한 다음, 앱의 왼쪽 패널에서 **기능** 을 선택 합니다.

1. **기능** 페이지에서 **+ 만들기**를 선택 합니다.

1. **새 구 목록 기능 만들기** 대화 상자에서와 같은 이름을 입력 `Cities` 합니다. **값** 상자에와 같은 도시 예를 입력 `Seattle` 합니다. 한 번에 하나의 값을 입력하거나 쉼표로 구분해서 여러 개의 값을 입력한 후 **Enter** 키를 누릅니다.

    > [!div class="mx-imgBorder"]
    > ![기능 (구 목록) 도시 추가 스크린샷](./media/luis-add-features/add-phrase-list-cities.png)

    LUIS에 대 한 충분 한 값을 입력 하면 제안이 표시 됩니다. 모든 제안 된 값을 **추가** 하거나 개별 단어를 선택할 수 있습니다.

1. 문장을 교대로 사용할 수 있는 경우 **이러한 값을 상호 교환할** 수 있습니다.

1. 구 목록은 **전역** 설정이 있는 전체 앱 또는 특정 모델 (의도 또는 엔터티)에 적용할 수 있습니다. 의도 또는 엔터티의 _기능_ 으로 문구 목록을 만드는 경우 toggle은 global에 대해 설정 되지 않습니다. 이 경우 설정/해제의 의미는 해당 기능이 해당 모델에 대해서만 로컬 이며 응용 프로그램에 대 한 _전역이 아니라_ 는 것입니다.

1. **완료**를 선택합니다. 새 기능이 **ML 기능** 페이지에 추가 됩니다.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> **ML 기능** 페이지의 상황별 도구 모음에서 구 목록을 삭제 하거나 비활성화할 수 있습니다.

## <a name="global-phrase-list-applies-to-entire-app"></a>전체 앱에 적용 되는 글로벌 문구 목록

지원 하려는 의도 또는 엔터티에 문구 목록을 적용 해야 하지만 전체 앱에 전체 앱에 **대 한 문구** 목록을 적용 해야 하는 경우가 있을 수 있습니다.

ML 기능 페이지에서 구 목록을 선택 하 고 위쪽 상황별 도구 모음에서 **전역 설정** 을 선택 합니다.

## <a name="model-as-a-feature"></a>기능으로 서의 모델

엔터티는 [의도 또는 엔터티에 대 한 기능](luis-concept-feature.md)일 수 있습니다.

엔터티를 의도에 대 한 기능으로 추가 하려면 의도 페이지에서 의도를 선택한 다음 상황별 도구 모음 위에서 **+ 기능 추가** 를 선택 합니다. 이 목록에는 기능으로 적용할 수 있는 모든 구 목록과 엔터티가 포함 됩니다.

엔터티를 다른 엔터티에 기능으로 추가 하려면 [엔터티 색상표](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) 를 사용 하 여 의도 세부 정보 페이지에 기능을 추가 하거나 엔터티 세부 정보 페이지에 [기능을 추가할](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) 수 있습니다.

## <a name="next-steps"></a>다음 단계

기능을 추가, 편집, 삭제 또는 비활성화 한 후에는 [앱을 학습 하 고 테스트](luis-interactive-test.md) 하 여 성능이 개선 되는지 확인 합니다.
