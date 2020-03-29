---
title: 설명자 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)를 사용하여 의도 및 엔터티의 감지 또는 예측을 향상시킬 수 있는 앱 기능을 해당 범주 및 패턴에 추가
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74123129"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>설명자 사용으로 단어 목록의 신호 향상

정확성을 개선하기 위해 LUIS 앱에 기능을 추가할 수 있습니다. 기능은 특정 단어와 구가 앱 도메인 용어의 일부라는 힌트를 제공하여 LUIS를 지원합니다. 

[설명자(구](luis-concept-feature.md) 목록)에는 동일한 클래스에 속하며 유사하게 처리해야 하는 값 그룹(단어 또는 구)이 포함됩니다(예: 도시 또는 제품 이름). LUIS가 이러한 값 중 하나에 대해 학습하는 내용이 다른 값에도 자동으로 적용됩니다. 이 목록은 일치하는 단어의 [목록 엔터티(정확한](reference-entity-list.md) 텍스트 일치)와 동일하지 않습니다.

설명자는 해당 단어에 대한 LUIS에 두 번째 신호로 앱 도메인의 어휘에 추가합니다.

[기능 개념을](luis-concept-feature.md) 검토하여 설명기를 사용해야 하는 시기와 이유를 파악합니다. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>설명자 추가

1. **내 앱** 페이지에서 이름을 클릭하여 앱을 연 다음 **빌드를**클릭한 다음 앱의 왼쪽 패널에서 **설명자 만들기를 클릭합니다.** 

1. 설명자 페이지에서 **+ 설명자 추가 를** **클릭합니다.** 
 
1. 새 **구 목록 설명자 설명자** 대화 상자만들기에서 `Cities` 설명자 에 대한 이름을 입력합니다. **값** 상자에 `Seattle`와 같은 설명자의 값을 입력합니다. 한 번에 하나의 값을 입력하거나 쉼표로 구분해서 여러 개의 값을 입력한 후 **Enter** 키를 누릅니다.

    > [!div class="mx-imgBorder"]
    > ![설명자 도시 추가](./media/luis-add-features/add-phrase-list-cities.png)

    LUIS에 대해 충분한 값을 입력하면 제안이 나타납니다. 당신은 + 제안 된 **값을 모두 추가하거나** 개별 용어를 선택할 수 있습니다.

1. Keep **이러한 값은** 추가된 설명자 값이 상호 교환적으로 사용할 수 있는 대안인 경우 교환 가능한 검사를 선택합니다.

1. **완료를 선택합니다.** 새 설명자가 설명자 페이지에 **추가됩니다.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 설명자 페이지의 컨텍스트 도구 모음에서 설명자를 삭제하거나 비활성화할 수 **있습니다.**

## <a name="next-steps"></a>다음 단계

설명자를 추가, 편집, 삭제 또는 비활성화한 후 앱을 다시 [학습및 테스트하여](luis-interactive-test.md) 성능이 향상되는지 확인합니다.
