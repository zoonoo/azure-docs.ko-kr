---
title: 템플릿 함수-논리적
description: Azure Resource Manager 템플릿에서 논리 값을 확인하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: ede41bd6c03eb7a01ae63526810d0310f31e4014
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978512"
---
# <a name="logical-functions-for-arm-templates"></a>ARM 템플릿에 대 한 논리 함수

리소스 관리자는 ARM (Azure Resource Manager) 템플릿에서 비교를 수행 하기 위한 여러 함수를 제공 합니다.

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

## <a name="and"></a>그리고

`and(arg1, arg2, ...)`

모든 매개 변수 값이 True인지 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |boolean |true인지 확인할 첫 번째 값입니다. |
| arg2 |예 |boolean |true인지 확인할 두 번째 값입니다. |
| 추가 인수 |예 |boolean |True인지 확인할 추가 인수입니다. |

### <a name="return-value"></a>반환 값

모든 값이 True이면 **True**를 반환하고 그렇지 않으면 **False**를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | 유형 | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | 거짓 |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | 거짓 |

## <a name="bool"></a>bool

`bool(arg1)`

매개 변수를 부울로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열 또는 int |부울로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

변환된 값의 부울입니다.

### <a name="remarks"></a>설명

[True ()](#true) 및 [false ()](#false) 를 사용 하 여 부울 값을 가져올 수도 있습니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)에서는 문자열 또는 정수에 bool을 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | 거짓 |
| trueInt | Bool | True |
| falseInt | Bool | 거짓 |

## <a name="false"></a>false

`false()`

false를 반환합니다.

### <a name="parameters"></a>매개 변수

False 함수는 매개 변수를 허용 하지 않습니다.

### <a name="return-value"></a>반환 값

항상 false 인 부울입니다.

### <a name="example"></a>예제

다음 예에서는 false 출력 값을 반환 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "falseOutput": {
            "value": "[false()]",
            "type" : "bool"
        }
    }
}
```

위 예제의 출력은 다음과 같습니다.

| Name | 유형 | 값 |
| ---- | ---- | ----- |
| falseOutput | Bool | 거짓 |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

조건이 true인지 아니면 false인지에 따라 값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| condition(조건) |예 |boolean |True 인지 false 인지 확인 하는 값입니다. |
| trueValue |예 | 문자열, 정수, 개체 또는 배열 |조건이 true이면 반환할 값입니다. |
| falseValue |예 | 문자열, 정수, 개체 또는 배열 |조건이 false이면 반환할 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 매개 변수가 **True**이면 두 번째 매개 변수를 반환하고 그렇지 않으면 세 번째 매개 변수를 반환합니다.

### <a name="remarks"></a>설명

조건이 **true**이면 true 값만 평가 됩니다. 조건이 **false**이면 false 값만 평가 됩니다. **If** 함수를 사용 하 여 조건적 으로만 유효한 식을 포함할 수 있습니다. 예를 들어 한 조건에 있는 리소스를 참조할 수 있지만 다른 조건에는 없는 리소스를 참조할 수 있습니다. 조건에 따라 식을 평가 하는 예는 다음 섹션에 나와 있습니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json)에서는 `if` 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | 유형 | 값 |
| ---- | ---- | ----- |
| yesOutput | String | 예 |
| noOutput | String | 아니요 |
| objectOutput | Object | { "test": "value1" } |

다음 [예제 템플릿에서는](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) 조건부로 유효한 식으로이 함수를 사용 하는 방법을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |boolean |변환할 값입니다. |

### <a name="return-value"></a>반환 값

매개 변수가 **False**이면 **True**를 반환합니다. 매개 변수가 **True**이면 **False**를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | 유형 | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | 거짓 |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | 거짓 |

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)에서는 [equals](template-functions-comparison.md#equals)에 **not**을 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

위 예제의 출력은 다음과 같습니다.

| Name | 유형 | 값 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>또는

`or(arg1, arg2, ...)`

매개 변수 값 중 하나가 True인지 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |boolean |true인지 확인할 첫 번째 값입니다. |
| arg2 |예 |boolean |true인지 확인할 두 번째 값입니다. |
| 추가 인수 |예 |boolean |True인지 확인할 추가 인수입니다. |

### <a name="return-value"></a>반환 값

True인 값이 하나라도 있으면 **True**를 반환하고 그렇지 않으면 **False**를 반환합니다.

### <a name="examples"></a>예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)에서는 논리 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Name | 유형 | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | 거짓 |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | 거짓 |

## <a name="true"></a>true

`true()`

true를 반환합니다.

### <a name="parameters"></a>매개 변수

True 함수는 매개 변수를 허용 하지 않습니다.

### <a name="return-value"></a>반환 값

항상 true 인 부울입니다.

### <a name="example"></a>예제

다음 예에서는 실제 출력 값을 반환 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueOutput": {
            "value": "[true()]",
            "type" : "bool"
        }
    }
}
```

위 예제의 출력은 다음과 같습니다.

| Name | 유형 | 값 |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager 템플릿의 섹션에 대 한 설명은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.

