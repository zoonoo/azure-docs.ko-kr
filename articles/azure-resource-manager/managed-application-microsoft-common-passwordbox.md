---
title: "Azure Managed Application PasswordBox UI 요소 | Microsoft Docs"
description: "Azure Managed Applications의 Microsoft.Common.PasswordBox UI 요소에 대해 설명합니다."
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
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 196a4b8f77145f83e46b4b23e148bb3a9dffc1b5
ms.contentlocale: ko-kr
ms.lasthandoff: 05/13/2017

---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI 요소
암호를 제공하고 확인하는 데 사용할 수 있는 컨트롤입니다. [Azure 관리되는 응용 프로그램을 만드는](managed-application-publishing.md) 경우 이 요소를 사용합니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>스키마
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>설명
- 이 요소는 `defaultValue` 속성을 지원하지 않습니다.
- `constraints`의 구현에 대한 자세한 내용은 [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)를 참조하세요.
- `options.hideConfirmation`을 **true**로 설정하면 사용자의 암호를 확인하기 위한 두 번째 텍스트 상자가 숨겨집니다. 기본값은 **false**입니다.

## <a name="sample-output"></a>샘플 출력
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>다음 단계
* 관리되는 응용 프로그램에 대한 소개는 [Azure Managed Application 개요](managed-application-overview.md)를 참조하세요.
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](managed-application-createuidefinition-elements.md)를 참조하세요.

