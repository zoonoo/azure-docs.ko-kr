---
title: 엔터티 검색을 향상시키는 구 목록
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)를 사용하여 의도 및 엔터티의 감지 또는 예측을 향상시킬 수 있는 앱 기능을 해당 범주 및 패턴에 추가
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 32ef8ba2f6416e1b59fc98595f1b204e94bd2ead
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830993"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>단어 목록의 신호를 강화하는 구 사용

정확성을 개선하기 위해 LUIS 앱에 기능을 추가할 수 있습니다. 기능은 특정 단어와 구가 앱 도메인 용어의 일부라는 힌트를 제공하여 LUIS를 지원합니다. 

## <a name="add-phrase-list"></a>구 목록 추가

1. **내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 **빌드**를 클릭한 후 앱 왼쪽 패널에서 **구 목록**을 클릭합니다. 

2. **구 목록** 페이지에서 **새 구 목록 만들기**를 클릭합니다. 
 
3. **구 목록 추가** 대화 상자에서 구 목록 이름으로 "Cities"를 입력합니다. **값** 상자에 구 목록의 값을 입력합니다. 한 번에 하나의 값을 입력하거나 쉼표로 구분해서 여러 개의 값을 입력한 후 **Enter** 키를 누릅니다.

    ![구 목록 Cities 추가](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS는 구 목록에 추가할 관련 값을 제안할 수 있습니다. **권장**을 클릭하여 추가된 값과 의미 체계가 관련된 제안된 값 그룹을 표시합니다. 제안된 값 중 하나를 클릭하거나 **모두 추가**를 클릭하여 모두 추가할 수 있습니다.

    ![구 목록 제안된 값](./media/luis-add-features/related-values.png)

5. 추가된 구 목록 값이 서로 교환해서 사용할 수 있는 대체 값이면 **These values are interchangeable**(서로 교환 가능한 값)을 클릭합니다.

    ![구 목록 제안된 값](./media/luis-add-features/interchangeable.png)

6. **저장**을 클릭합니다. "Cities" 구 목록이 **구 목록** 페이지에 추가됩니다.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> **구 목록** 페이지의 상황에 맞는 도구 모음에서 구 목록을 삭제하거나 비활성화할 수 있습니다.

## <a name="pattern-regular-expression-feature"></a>패턴(정규식) 기능 
**이 기능은 더 이상 사용되지 않습니다**. 새 패턴 기능은 LUIS에 추가할 수 없습니다. 모든 기존 패턴 기능은 2018년 5월까지 지원됩니다. PR을 사용한 표준 LUIS 정규식 일치의 경우 [Recognizers-Text Github 리포지토리](https://github.com/Microsoft/Recognizers-Text)에서 도움을 주세요. 

## <a name="next-steps"></a>다음 단계

구 목록을 추가, 편집, 삭제 또는 비활성화한 후에 다시 [앱을 학습하고 테스트](luis-interactive-test.md)하여 성능이 개선되는지 확인합니다.
