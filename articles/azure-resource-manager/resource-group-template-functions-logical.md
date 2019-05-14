---
title: Azure Resource Manager 템플릿 함수 - 논리 | Microsoft 문서
description: Azure Resource Manager 템플릿에서 논리 값을 확인하는 데 사용할 수 있는 함수에 대해 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: tomfitz
ms.openlocfilehash: 4684c38fe506ed912c6827f1e60b94b847024347
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405663"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿용 논리 함수

Resource Manager는 템플릿에서 비교를 수행하기 위한 몇 가지 함수를 제공합니다.

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [또는](#or)

## <a name="and"></a>및

`and(arg1, arg2, ...)`

모든 매개 변수 값이 True인지 확인합니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예. |부울 |true인지 확인할 첫 번째 값입니다. |
| arg2 |예. |부울 |true인지 확인할 두 번째 값입니다. |
| 추가 인수 |아닙니다. |부울 |True인지 확인할 추가 인수입니다. |

### <a name="return-value"></a>반환 값

모든 값이 True이면 **True**를 반환하고 그렇지 않으면 **False**를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | type | Value |
| ---- | ---- | ----- |
| andExampleOutput | 부울 | 거짓 |
| orExampleOutput | 부울 | True  |
| notExampleOutput | 부울 | 거짓 |

## <a name="bool"></a>bool

`bool(arg1)`

매개 변수를 부울로 변환합니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예. |문자열 또는 int |부울로 변환할 값입니다. |

### <a name="return-value"></a>반환 값
변환된 값의 부울입니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)에서는 문자열 또는 정수에 bool을 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | type | Value |
| ---- | ---- | ----- |
| trueString | 부울 | True  |
| falseString | 부울 | 거짓 |
| trueInt | 부울 | True  |
| falseInt | 부울 | 거짓 |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

조건이 true인지 아니면 false인지에 따라 값을 반환합니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| condition |예. |부울 |True 또는 false 인지 확인할 값입니다. |
| trueValue |예. | 문자열, 정수, 개체 또는 배열 |조건이 true이면 반환할 값입니다. |
| falseValue |예. | 문자열, 정수, 개체 또는 배열 |조건이 false이면 반환할 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 매개 변수가 **True**이면 두 번째 매개 변수를 반환하고 그렇지 않으면 세 번째 매개 변수를 반환합니다.

### <a name="remarks"></a>설명

조건이 **True**에 true 값만 평가 됩니다. 조건이 **False**, false 값만 평가 됩니다. 사용 하 여 합니다 **경우** 함수만 조건에 따라 사용할 수 있는 식을 포함할 수 있습니다. 예를 들어, 하나의 조건이 하지만 다른 조건이 아니었습니다 존재 하는 리소스를 참조할 수 있습니다. 조건에 따라 식 평가의 예는 다음 섹션에 표시 됩니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json)에서는 `if` 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | type | Value |
| ---- | ---- | ----- |
| yesOutput | String | 예 |
| noOutput | String | no |
| objectOutput | Object | { "test": "value1" } |

다음 [예제 템플릿](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) 만 조건에 따라 사용할 수 있는 식을 사용 하 여이 함수를 사용 하는 방법을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

부울 값을 반대 값으로 변환합니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예. |부울 |변환할 값입니다. |

### <a name="return-value"></a>반환 값

매개 변수가 **False**이면 **True**를 반환합니다. 매개 변수가 **True**이면 **False**를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | type | Value |
| ---- | ---- | ----- |
| andExampleOutput | 부울 | 거짓 |
| orExampleOutput | 부울 | True  |
| notExampleOutput | 부울 | 거짓 |

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)에서는 [equals](resource-group-template-functions-comparison.md#equals)에 **not**을 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

위 예제의 출력은 다음과 같습니다.

| 이름 | type | Value |
| ---- | ---- | ----- |
| checkNotEquals | 부울 | True  |

## <a name="or"></a>또는

`or(arg1, arg2, ...)`

매개 변수 값 중 하나가 True인지 확인합니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |부울 |true인지 확인할 첫 번째 값입니다. |
| arg2 |예. |부울 |true인지 확인할 두 번째 값입니다. |
| 추가 인수 |아닙니다. |부울 |True인지 확인할 추가 인수입니다. |

### <a name="return-value"></a>반환 값

True인 값이 하나라도 있으면 **True**를 반환하고 그렇지 않으면 **False**를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

위 예제의 출력은 다음과 같습니다.

| 이름 | type | Value |
| ---- | ---- | ----- |
| andExampleOutput | 부울 | 거짓 |
| orExampleOutput | 부울 | True  |
| notExampleOutput | 부울 | 거짓 |

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager 템플릿의 섹션에 대한 설명은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.
* 만든 템플릿을 배포하는 방법을 보려면 [Azure Resource Manager 템플릿을 사용하여 애플리케이션 배포](resource-group-template-deploy.md)를 참조하세요.

