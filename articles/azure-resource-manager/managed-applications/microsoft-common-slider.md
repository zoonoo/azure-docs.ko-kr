---
title: Slider UI 요소
description: Azure Portal의 Microsoft.Common.Slider UI 요소에 대해 설명합니다. 사용자가 옵션 범위에서 값을 설정할 수 있습니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098535"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft.Common.Slider UI 요소

슬라이더 컨트롤을 사용하면 사용자가 허용되는 값 범위에서 값을 선택할 수 있습니다.

## <a name="ui-sample"></a>UI 샘플

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft.Common.Slider":::

## <a name="schema"></a>스키마

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>샘플 출력

```json
26
```

## <a name="remarks"></a>설명

- `min` 및 `max` 값은 필수입니다. 이 값은 슬라이더의 시작점과 끝점을 설정합니다.
- `showStepMarkers` 속성은 기본적으로 true로 설정됩니다. 단계 마커는 min에서 max까지의 범위가 100 이하인 경우에만 표시됩니다.


## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
