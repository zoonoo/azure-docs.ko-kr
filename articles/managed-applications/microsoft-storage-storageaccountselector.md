---
title: Azure StorageAccountSelector UI 요소 | Microsoft Docs
description: Azure Portal의 Microsoft.Storage.StorageAccountSelector UI 요소에 대해 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: ca66b788af68699b4750e1e2826b6a6b104c72c7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI 요소
새 또는 기존 저장소 계정을 선택하는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

## <a name="schema"></a>스키마
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>설명
- 지정하는 경우 `defaultValue.name`의 고유성에 대한 유효성 검사가 자동으로 수행됩니다. 저장소 계정 이름이 고유하지 않으면 사용자가 다른 이름을 지정하거나 기존 저장소 계정을 선택해야 합니다.
- `defaultValue.type`의 기본값은 **Premium_LRS**입니다.
- `constraints.allowedTypes`에 지정되지 않은 형식은 숨겨지며, `constraints.excludedTypes`에 지정되지 않은 형식이 표시됩니다.
`constraints.allowedTypes`와 `constraints.excludedTypes`는 모두 선택 사항이지만 동시에 사용할 수는 없습니다.
- `options.hideExisting`이 **true**이면 사용자가 기존 저장소 계정을 선택할 수 없습니다. 기본값은 **false**입니다.


## <a name="sample-output"></a>샘플 출력
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
