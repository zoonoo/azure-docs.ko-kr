---
title: 설명자-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)를 사용하여 의도 및 엔터티의 감지 또는 예측을 향상시킬 수 있는 앱 기능을 해당 범주 및 패턴에 추가
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80631459"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>설명자를 사용 하 여 단어 목록 신호 상승

정확성을 개선하기 위해 LUIS 앱에 기능을 추가할 수 있습니다. 기능은 특정 단어와 구가 앱 도메인 용어의 일부라는 힌트를 제공하여 LUIS를 지원합니다.

[설명자](luis-concept-feature.md) (구 목록)에는 동일한 클래스에 속하고 유사한 (예: 도시 또는 제품의 이름)로 처리 해야 하는 값 (단어 또는 구)의 그룹이 포함 됩니다. LUIS가 이러한 값 중 하나에 대해 학습하는 내용이 다른 값에도 자동으로 적용됩니다. 이 목록은 일치 하는 단어의 [목록 엔터티](reference-entity-list.md) (정확히 일치 하는 텍스트)와 다릅니다.

설명자는 이러한 단어에 대해 LUIS 하기 위해 두 번째 신호로 앱 도메인의 어휘에 추가 합니다.

[기능 개념](luis-concept-feature.md) 을 검토 하 여 설명자를 사용 하는 시기 및 이유를 이해 합니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>설명자 추가

1. **내 앱** 페이지에서 해당 이름을 클릭 하 여 앱을 열고 **빌드**를 클릭 한 다음 앱의 왼쪽 패널에서 **설명자** 를 클릭 합니다.

1. **설명자** 페이지에서 **+ 설명자 추가**를 클릭 합니다.

1. **새 구 목록 설명자 만들기** 대화 상자에서 설명자 `Cities` 의 이름과 같은 이름을 입력 합니다. **값** 상자에 설명자의 값 (예:)을 입력 합니다 `Seattle`. 한 번에 하나의 값을 입력하거나 쉼표로 구분해서 여러 개의 값을 입력한 후 **Enter** 키를 누릅니다.

    > [!div class="mx-imgBorder"]
    > ![설명자 도시 추가](./media/luis-add-features/add-phrase-list-cities.png)

    LUIS에 대 한 충분 한 값을 입력 하면 제안이 표시 됩니다. 모든 제안 된 값을 **추가** 하거나 개별 단어를 선택할 수 있습니다.

1. 추가 된 설명자 값이 서로 바꿔 사용할 수 있는 대안이 면 **이러한 값을 교환** 가능으로 유지 합니다 .를 선택 합니다.

1. 구 목록은 **전역** 설정이 있는 전체 앱 또는 특정 모델 (의도 또는 엔터티)에 적용할 수 있습니다. 의도 또는 엔터티의 _설명자_ 로 문구 목록을 만드는 경우 toggle은 global이 아닌 것으로 설정 됩니다. 이 경우,이 경우에는 설명자가 해당 모델에 대 한 기능 이므로 응용 프로그램에 대 한 전역이 _아닙니다_ .

1. **완료**를 선택합니다. 새 설명자가 **설명자** 페이지에 추가 됩니다.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> **설명자 페이지의** 상황별 도구 모음에서 설명자를 삭제 하거나 비활성화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

설명자를 추가, 편집, 삭제 또는 비활성화 한 후에는 [앱을 학습 하 고 테스트](luis-interactive-test.md) 하 여 성능이 개선 되는지 확인 합니다.
