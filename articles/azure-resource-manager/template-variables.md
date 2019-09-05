---
title: Azure Resource Manager 템플릿의 변수
description: Azure Resource Manager 템플릿에서 변수를 정의 하는 방법을 설명 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 5ed6bb58f2f45de557f2127fdc8abd5cdf2ef965
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384035"
---
# <a name="variables-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿의 변수

이 문서에서는 Azure Resource Manager 템플릿에서 변수를 정의 하 고 사용 하는 방법을 설명 합니다. 변수를 사용 하 여 템플릿을 단순화 합니다. 템플릿 전체에서 복잡 한 식을 반복 하는 대신 복잡 한 식을 포함 하는 변수를 정의 합니다. 그런 다음 템플릿 전체에서 필요에 따라 해당 변수를 참조 합니다.

리소스 관리자는 배포 작업을 시작 하기 전에 변수를 확인 합니다. 템플릿에서 변수를 사용 하는 모든 경우에는 리소스 관리자를 확인 된 값으로 바꿉니다.

## <a name="define-variable"></a>변수 정의

다음 예제는 변수 정의를 보여 줍니다. 스토리지 계정 이름의 문자열 값을 만듭니다. 여러 템플릿 함수를 사용 하 여 매개 변수 값을 가져오고이를 고유한 문자열에 연결 합니다.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Variables 섹션에는 [reference](resource-group-template-functions-resource.md#reference) 함수 또는 [list](resource-group-template-functions-resource.md#list) 함수를 사용할 수 없습니다. 이러한 함수는 리소스의 런타임 상태를 가져오며, 변수가 확인 될 때 배포 전에 실행할 수 없습니다.

## <a name="use-variable"></a>변수 사용

템플릿에서 [variables](resource-group-template-functions-deployment.md#variables) 함수를 사용 하 여 매개 변수의 값을 참조 합니다. 다음 예제에서는 리소스 속성에 대 한 변수를 사용 하는 방법을 보여 줍니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>구성 변수

환경을 구성 하는 데 관련 된 값을 포함 하는 변수를 정의할 수 있습니다. 변수를 값이 포함 된 개체로 정의 합니다. 다음 예제에서는 **테스트** 와 **prod**의 두 환경에 대 한 값을 보유 하는 개체를 보여 줍니다.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

매개 변수에서 사용할 구성 값을 나타내는 값을 만듭니다.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

지정 된 환경에 대 한 설정을 검색 하려면 변수와 매개 변수를 함께 사용 합니다.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>예제 템플릿

다음 예에서는 변수를 사용 하는 시나리오를 보여 줍니다.

|템플릿  |Description  |
|---------|---------|
| [변수 정의](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 다양한 변수 형식을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 변수 값을 구성하고 해당 값을 반환합니다. |
| [구성 변수](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 구성 값을 정의하는 변수의 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 변수 값을 구성하고 해당 값을 반환합니다. |
| [네트워크 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) 및 [매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 보안 규칙을 네트워크 보안 그룹에 할당하기 위한 올바른 형식으로 배열을 구성합니다. |

## <a name="next-steps"></a>다음 단계

* 변수에 사용할 수 있는 속성에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조 하세요.
* 변수를 만드는 방법에 대 한 권장 사항은 [모범 사례-변수](template-best-practices.md#variables)를 참조 하세요.
