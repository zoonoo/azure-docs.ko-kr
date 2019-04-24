---
title: Azure SizeSelector UI 요소 | Microsoft Docs
description: Azure Portal의 Microsoft.Compute.SizeSelector UI 요소에 대해 설명합니다.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e5be5635964ebeedc7be4d1d1f5403e4d281b55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251272"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI 요소
하나 이상의 가상 머신 인스턴스에 대한 크기를 선택하는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

사용자에게는 요소 정의의 기본 값을 포함한 선택기가 표시됩니다.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

컨트롤을 선택한 후에 사용자에게는 지원되는 크기의 확장된 보기가 표시됩니다.

![Microsoft.Compute.SizeSelector expanded](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>스키마
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>설명
- `recommendedSizes`에는 적어도 하나의 크기가 포함됩니다. 권장되는 첫 번째 크기가 기본값으로 사용됩니다. 지원되는 크기 목록은 권장되는 상태에 따라 정렬되지 않습니다. 사용자는 권장되는 상태별로 정렬할 열을 선택할 수 있습니다.
- 선택한 위치에서 권장되는 첫 번째 크기를 사용할 수 없는 경우 해당 크기를 자동으로 건너뜁니다. 대신 권장되는 다음 크기가 사용됩니다.
- `constraints.allowedSizes` 및 `constraints.excludedSizes`는 모두 선택 사항이지만 동시에 사용할 수는 없습니다. 사용 가능한 크기 목록은 [구독에 사용 가능한 가상 머신 크기 목록](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)을 호출하여 확인할 수 있습니다. `constraints.allowedSizes`에 지정되지 않은 크기는 숨겨지며 `constraints.excludedSizes`에 지정되지 않은 크기가 표시됩니다.
- `osPlatform`을 지정해야 하며 **Windows** 또는 **Linux**일 수 있습니다. 가상 머신의 하드웨어 비용을 결정하는 데 사용됩니다.
- `imageReference`은 자사 이미지에 대해 생략되지만, 타사 이미지에 대해서는 제공됩니다. 가상 머신의 소프트웨어 비용을 결정하는 데 사용됩니다.
- `count`는 요소에 적절한 승수를 설정하는 데 사용됩니다. **2**와 같은 정적 값 또는 `[steps('step1').vmCount]`와 같은 다른 요소의 동적 값을 지원합니다. 기본값은 **1**합니다.
- `numAvailabilityZonesRequired`는 1, 2 또는 3일 수 있습니다.
- 기본적으로 `hideDiskTypeFilter`는 **false**입니다. 디스크 형식 필터를 사용하면 모든 디스크 형식 또는 SSD만을 볼 수 있습니다.

## <a name="sample-output"></a>샘플 출력
```json
"Standard_D1"
```

## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
