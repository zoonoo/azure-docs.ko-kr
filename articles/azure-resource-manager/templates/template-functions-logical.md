---
title: 템플릿 기능 - 논리
description: Azure Resource Manager 템플릿에서 논리 값을 확인하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f058baa32e5f93a4177913287a5e9873fa7a9acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156313"
---
# <a name="logical-functions-for-arm-templates"></a>ARM 템플릿에 대한 논리 함수

리소스 관리자는 ARM(Azure 리소스 관리자) 템플릿에서 비교하기 위한 몇 가지 기능을 제공합니다.

* [및](#and)
* [bool](#bool)
* [if](#if)
* [하지 않습니다.](#not)
* [또는](#or)

## <a name="and"></a>and

`and(arg1, arg2, ...)`

모든 매개 변수 값이 True인지 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |yes |boolean |true인지 확인할 첫 번째 값입니다. |
| arg2 |yes |boolean |true인지 확인할 두 번째 값입니다. |
| 추가 인수 |예 |boolean |True인지 확인할 추가 인수입니다. |

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

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

매개 변수를 부울로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |yes |문자열 또는 int |부울로 변환할 값입니다. |

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

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

조건이 true인지 아니면 false인지에 따라 값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| condition(조건) |yes |boolean |true인지 거짓인지 확인하는 값입니다. |
| trueValue |yes | 문자열, 정수, 개체 또는 배열 |조건이 true이면 반환할 값입니다. |
| falseValue |yes | 문자열, 정수, 개체 또는 배열 |조건이 false이면 반환할 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 매개 변수가 **True**이면 두 번째 매개 변수를 반환하고 그렇지 않으면 세 번째 매개 변수를 반환합니다.

### <a name="remarks"></a>설명

조건이 **True이면**true 값만 평가됩니다. 조건이 **False이면**false 값만 평가됩니다. **if** 함수를 사용하면 조건부로만 유효한 식을 포함할 수 있습니다. 예를 들어 한 조건에서는 있지만 다른 조건에서는 없는 리소스를 참조할 수 있습니다. 조건부로 식을 평가하는 예제는 다음 섹션에 나와 있습니다.

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

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| yesOutput | String | 예 |
| noOutput | String | 아니요 |
| objectOutput | Object | { "test": "value1" } |

다음 [예제 템플릿에서는](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) 조건부로만 유효한 식과 함께 이 함수를 사용하는 방법을 보여 주며 있습니다.

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

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |yes |boolean |변환할 값입니다. |

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

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)에서는 [equals](template-functions-comparison.md#equals)에 **not**을 사용합니다.

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

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>또는

`or(arg1, arg2, ...)`

매개 변수 값 중 하나가 True인지 확인합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |yes |boolean |true인지 확인할 첫 번째 값입니다. |
| arg2 |yes |boolean |true인지 확인할 두 번째 값입니다. |
| 추가 인수 |예 |boolean |True인지 확인할 추가 인수입니다. |

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

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>다음 단계

* Azure 리소스 관리자 템플릿의 섹션에 대한 설명은 [Azure 리소스 관리자 템플릿 작성을](template-syntax.md)참조하십시오.
* 여러 템플릿을 병합하려면 [연결된 템플릿 사용을 Azure 리소스 관리자와](linked-templates.md)함께 참조합니다.
* 리소스 형식을 만들 때 지정된 횟수를 반복하려면 [Azure 리소스 관리자에서 여러 리소스 인스턴스 만들기를](copy-resources.md)참조하십시오.
* 만든 템플릿을 배포하는 방법을 보려면 [Azure Resource Manager 템플릿을 사용하여 애플리케이션 배포](deploy-powershell.md)를 참조하세요.

