---
title: 태그바이리소스 UI 요소
description: Azure 포털에 대한 Microsoft.Common.TagsByResource UI 요소에 대해 설명합니다. 배포 하는 동안 리소스에 태그를 적용 하는 데 사용 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652204"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>마이크로소프트.커먼.태그바이리소스 UI 요소

태그를 배포의 [리소스와](../management/tag-resources.md) 연결하기 위한 컨트롤입니다.

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

- 배열에 `resources` 하나 이상의 항목을 지정해야 합니다.
- 각 `resources` 요소는 정규화된 리소스 유형이어야 합니다. 이러한 요소는 **리소스** 드롭다운에 표시되며 사용자가 태그할 수 있습니다.
- Azure 리소스 관리자 템플릿에서 태그 값을 쉽게 할당할 수 위해 컨트롤의 출력이 서식이 지정됩니다. 템플릿에서 컨트롤의 출력을 받으려면 다음 예제와 같이 템플릿에 매개 변수를 포함하십시오.

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  태그지정할 수 있는 각 리소스에 대해 해당 리소스 형식의 매개 변수 값에 tags 속성을 할당합니다.

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- 태그ByResource 매개 변수에 액세스할 때 [if](../templates/template-functions-logical.md#if) 함수를 사용합니다. 지정된 리소스 유형에 태그가 할당되지 않은 경우 빈 개체를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
