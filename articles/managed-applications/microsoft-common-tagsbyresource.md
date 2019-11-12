---
title: Azure TagsByResource UI 요소
description: Azure Portal에 대 한 TagsByResource UI 요소에 대해 설명 합니다. 배포 하는 동안를 사용 하 여 리소스에 태그를 적용 합니다.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 5711759666cd68dc93e5bfb67e51ec1cc0a48bd1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933143"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>TagsByResource UI 요소

배포의 리소스와 [태그](../azure-resource-manager/resource-group-using-tags.md) 를 연결 하기 위한 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

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

- `resources` 배열에서 하나 이상의 항목을 지정 해야 합니다.
- `resources`의 각 요소는 정규화 된 리소스 형식 이어야 합니다. 이러한 요소는 **리소스** 드롭다운에서 표시 되며 사용자가 taggable 합니다.
- 컨트롤의 출력은 Azure Resource Manager 템플릿에서 태그 값을 쉽게 할당할 수 있도록 형식이 지정 됩니다. 템플릿에서 컨트롤의 출력을 받으려면 다음 예제와 같이 템플릿에 매개 변수를 포함 합니다.

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  태그를 지정할 수 있는 각 리소스에 대해 태그 속성을 해당 리소스 형식의 매개 변수 값에 할당 합니다.

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- TagsByResource 매개 변수에 액세스할 때 [if](../azure-resource-manager/resource-group-template-functions-logical.md#if) 함수를 사용 합니다. 지정 된 리소스 형식에 할당 된 태그가 없는 경우 빈 개체를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
