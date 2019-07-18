---
title: 문구 목록
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS(Language Understanding)를 사용하여 의도 및 엔터티의 감지 또는 예측을 향상시킬 수 있는 앱 기능을 해당 범주 및 패턴에 추가
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: 0723c3730ca0ae6325d828fbb5f41698cb807dd3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60195933"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>단어 목록의 신호를 강화하는 구 사용

정확성을 개선하기 위해 LUIS 앱에 기능을 추가할 수 있습니다. 기능은 특정 단어와 구가 앱 도메인 용어의 일부라는 힌트를 제공하여 LUIS를 지원합니다. 

[구문 목록](luis-concept-feature.md)에는 동일한 클래스에 속하고 비슷하게 처리되어야 하는 값(단어 또는 구문) 그룹이 포함됩니다(예: 도시 또는 제품의 이름). LUIS가 이러한 값 중 하나에 대해 학습하는 내용이 다른 값에도 자동으로 적용됩니다. 이 목록은 일치하는 단어의 닫힌 목록 엔터티(정확한 텍스트 일치)가 아닙니다.

구문 목록은 해당 단어와 관련된 LUIS에 대한 두 번째 신호로 앱 도메인의 어휘에 추가됩니다.

## <a name="add-phrase-list"></a>구 목록 추가

LUIS는 앱당 최대 10개의 구 목록을 허용합니다. 

1. **내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 **빌드**를 클릭한 후 앱 왼쪽 패널에서 **구 목록**을 클릭합니다. 

2. **구 목록** 페이지에서 **새 구 목록 만들기**를 클릭합니다. 
 
3. **구 목록 추가** 대화 상자에서 구 목록 이름으로 "Cities"를 입력합니다. **값** 상자에 구 목록의 값을 입력합니다. 한 번에 하나의 값을 입력하거나 쉼표로 구분해서 여러 개의 값을 입력한 후 **Enter** 키를 누릅니다.

    ![구 목록 Cities 추가](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS는 구 목록에 추가할 관련 값을 제안할 수 있습니다. **권장**을 클릭하여 추가된 값과 의미 체계가 관련된 제안된 값 그룹을 표시합니다. 제안된 값 중 하나를 클릭하거나 **모두 추가**를 클릭하여 모두 추가할 수 있습니다.

    ![구 목록 제안된 값 - 모두 추가](./media/luis-add-features/related-values.png)

5. 추가된 구 목록 값이 서로 교환해서 사용할 수 있는 대체 값이면 **These values are interchangeable**(서로 교환 가능한 값)을 클릭합니다.

    ![구 목록 제안된 값 - 교환 가능한 상자 선택](./media/luis-add-features/interchangeable.png)

6. **저장**을 클릭합니다. "Cities" 구 목록이 **구 목록** 페이지에 추가됩니다.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> **구 목록** 페이지의 상황에 맞는 도구 모음에서 구 목록을 삭제하거나 비활성화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

구 목록을 추가, 편집, 삭제 또는 비활성화한 후에 다시 [앱을 학습하고 테스트](luis-interactive-test.md)하여 성능이 개선되는지 확인합니다.
