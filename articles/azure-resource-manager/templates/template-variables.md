---
title: 템플릿의 변수
description: Azure 리소스 관리자 템플릿에서 변수를 정의하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483818"
---
# <a name="variables-in-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿의 변수

이 문서에서는 Azure 리소스 관리자 템플릿에서 변수를 정의하고 사용하는 방법을 설명합니다. 변수를 사용하여 템플릿을 단순화합니다. 템플릿 전체에서 복잡한 식을 반복하는 대신 복잡한 식이 포함된 변수를 정의합니다. 그런 다음 템플릿 전체에서 필요에 따라 해당 변수를 참조합니다.

리소스 관리자는 배포 작업을 시작하기 전에 변수를 해결합니다. 템플릿에서 변수가 사용되는 모든 위치에서 리소스 관리자는 변수를 해결된 값으로 바꿉습니다.

## <a name="define-variable"></a>변수 정의

다음 예제는 변수 정의를 보여 줍니다. 스토리지 계정 이름의 문자열 값을 만듭니다. 여러 템플릿 함수를 사용하여 매개 변수 값을 얻고 고유한 문자열에 연결합니다.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

변수 섹션의 [참조](template-functions-resource.md#reference) 함수 또는 [목록](template-functions-resource.md#list) 함수는 사용할 수 없습니다. 이러한 함수는 리소스의 런타임 상태를 가지며 변수가 해결될 때 배포 전에 실행할 수 없습니다.

## <a name="use-variable"></a>변수 사용

템플릿에서 변수 함수를 사용하여 매개 변수의 값을 [참조합니다.](template-functions-deployment.md#variables) 다음 예제에서는 리소스 속성에 대 한 변수를 사용 하는 방법을 보여 주어 있습니다.

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

환경 구성에 관련 값을 포함하는 변수를 정의할 수 있습니다. 변수를 값이 있는 개체로 정의합니다. 다음 예제에서는 **테스트** 및 prod - 두 환경에 대한 값을 보유하는 **개체를**보여 줍니다.

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

지정된 환경에 대한 설정을 검색하려면 변수와 매개 변수를 함께 사용합니다.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 변수를 사용하는 시나리오를 보여 줍니다.

|템플릿  |설명  |
|---------|---------|
| [변수 정의](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 다양한 변수 형식을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 변수 값을 구성하고 해당 값을 반환합니다. |
| [구성 변수](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 구성 값을 정의하는 변수의 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 변수 값을 구성하고 해당 값을 반환합니다. |
| [네트워크 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) 및 [매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 보안 규칙을 네트워크 보안 그룹에 할당하기 위한 올바른 형식으로 배열을 구성합니다. |

## <a name="next-steps"></a>다음 단계

* 변수에 사용할 수 있는 속성에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해를](template-syntax.md)참조하십시오.
* 변수 를 만드는 것에 대한 권장 사항은 [모범 사례 - 변수를](template-best-practices.md#variables)참조하십시오.
