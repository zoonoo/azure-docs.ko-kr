---
title: "Azure 템플릿에 자식 리소스 정의 | Microsoft Docs"
description: "Azure Resource Manager 템플릿에서 자식 리소스의 리소스 유형 및 이름을 설정하는 방법을 보여 줍니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>Resource Manager 템플릿에서 자식 리소스의 이름 및 유형 설정
템플릿을 만들 때는 부모 리소스와 관련된 자식 리소스를 포함해야 하는 경우가 자주 있습니다. 예를 들어 템플릿에 SQL Server 및 데이터베이스가 포함될 수 있습니다. SQL Server는 부모 리소스이며 데이터베이스는 자식 리소스입니다. 

자식 리소스 유형의 형식은 다음과 같습니다. `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

자식 리소스 이름의 형식은 다음과 같습니다. `{parent-resource-name}/{child-resource-name}`

그러나 부모 리소스 내에 중첩되어 있는지, 자체적으로 최상위 수준에 있는지에 따라 템플릿에 종류와 이름을 다르게 지정합니다. 이 항목에서는 두 가지 방법을 처리하는 방법을 모두 보여 줍니다.

리소스에 대한 정규화된 참조를 생성할 때 형식과 이름으로 세그먼트를 결합하는 순서는 단순히 두 항목의 연결이 아닙니다.  대신, 네임스페이스 뒤에 구체성이 낮은 순으로 *형식/이름* 쌍의 시퀀스를 사용합니다.

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

예:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`는 올바릅니다. `Microsoft.Compute/virtualMachines/extensions/myVM/myExt`는 올바르지 않습니다.

## <a name="nested-child-resource"></a>중첩된 자식 리소스
자식 리소스를 정의하는 가장 쉬운 방법은 부모 리소스 내에 중첩시키는 것입니다. 다음 예제에서는 SQL Server 내에 중첩된 SQL Database를 보여 줍니다.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

자식 리소스의 유형은 `databases`로 설정되지만 전체 리소스 유형은 `Microsoft.Sql/servers/databases`입니다. `Microsoft.Sql/servers/`는 입력하지 않습니다. 부모 리소스 유형에서 유추되기 때문입니다. 자식 리소스 이름은 `exampledatabase`로 설정되지만 전체 이름에는 부모 이름이 포함됩니다. `exampleserver`는 입력하지 않습니다. 부모 리소스에서 유추되기 때문입니다.

## <a name="top-level-child-resource"></a>최상위 자식 리소스
최상위 수준에 자식 리소스를 정의할 수 있습니다. 부모 리소스가 동일한 템플릿에 배포되지 않은 경우 `copy`를 사용하여 여러 자식 리소스를 만들려는 경우 이 방법을 사용할 수 있습니다. 이 방법을 사용하는 경우 전체 리소스 유형을 입력하고 자식 리소스 이름에 부모 리소스 이름을 포함해야 합니다.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

데이터베이스는 템플릿의 동일한 수준에 정의되어 있더라도 서버의 자식 리소스입니다.

## <a name="next-steps"></a>다음 단계
* 템플릿 작성 방법에 대한 권장 사항은 [Azure Resource Manager 템플릿 생성 모범 사례](resource-manager-template-best-practices.md)를 참조하세요.
* 여러 자식 리소스를 만드는 예제는 [Azure Resource Manager 템플릿에서 리소스의 여러 인스턴스 배포](resource-group-create-multiple.md)를 참조하세요.
