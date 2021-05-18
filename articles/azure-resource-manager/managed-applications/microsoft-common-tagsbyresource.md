---
title: TagsByResource UI 요소
description: Azure Portal의 Microsoft.Common.TagsByResource UI 요소에 대해 설명합니다. 배포 중 리소스에 태그를 적용하는 데 사용하세요.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: e730201812005a9b469a964e4acd081ebe86b100
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063948"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource UI 요소

배포의 리소스에 [태그](../management/tag-resources.md)를 연결하기 위한 제어입니다.

## <a name="ui-sample"></a>UI 샘플

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft-common-tagsbyresource.png)

## <a name="schema"></a>스키마

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>샘플 출력

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>설명

- `resources`배열에서 하나 이상의 항목을 지정해야 합니다.
- `resources`의 각 요소는 정규화된 리소스 종류여야 합니다. 이러한 요소는 **리소스** 드롭다운에 표시되며 사용자가 태그를 지정할 수 있습니다.
- 제어 출력은 Azure Resource Manager 템플릿에서 태그 값을 쉽게 할당하기 위해 포맷됩니다. 템플릿에서 제어 출력을 수신하려면 다음 예와 같이 템플릿에 매개 변수를 포함하세요.

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  태그를 지정할 수 있는 각 리소스에 대해 해당 리소스 종류의 매개 변수 값에 태그 속성을 할당하세요.

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- tagByResource 매개 변수에 액세스할 때 [if](../templates/template-functions-logical.md#if) 함수를 사용하세요. 지정된 리소스 종류에 태그가 할당되지 않은 경우 빈 개체를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
