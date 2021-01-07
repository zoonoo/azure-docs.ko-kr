---
title: CheckBox UI 요소
description: Azure Portal에 대 한 Microsoft Common. CheckBox UI 요소에 대해 설명 합니다. 사용자가 옵션을 선택 하거나 선택 취소 하도록 선택할 수 있습니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098547"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Microsoft 일반. CheckBox UI 요소

CheckBox 컨트롤을 사용 하면 사용자가 옵션을 선택 하거나 선택 취소할 수 있습니다. 컨트롤이 선택 되 면 **true** 를 반환 하 고, 선택 하지 않으면 **false** 를 반환 합니다.

## <a name="ui-sample"></a>UI 샘플

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft. 일반. 확인란":::

## <a name="schema"></a>스키마

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>샘플 출력

```json
true
```

## <a name="remarks"></a>설명

**필수** 를 **true**로 설정 하면 사용자가 확인란을 선택 해야 합니다. 사용자가이 확인란을 선택 하지 않으면 유효성 검사 메시지가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
