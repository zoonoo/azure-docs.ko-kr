---
title: 템플릿 기능 - 날짜
description: Azure 리소스 관리자 템플릿에서 날짜를 사용할 기능에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 75234a142b9f2fbe61c337bfeb378b47534bac79
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986279"
---
# <a name="date-functions-for-arm-templates"></a>ARM 템플릿의 날짜 함수

리소스 관리자는 ARM(Azure 리소스 관리자) 템플릿에서 날짜작업을 위한 다음 기능을 제공합니다.

* [날짜시간 추가](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>날짜시간 추가

`dateTimeAdd(base, duration, [format])`

기본 날짜 시간 값에 시간 기간을 추가합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| base | 예 | 문자열 | 추가의 시작 날짜 시간 값입니다. [ISO 8601 타임스탬프 형식을](https://en.wikipedia.org/wiki/ISO_8601)사용합니다. |
| duration | 예 | 문자열 | 베이스에 추가할 시간 값입니다. 음수 값이 될 수 있습니다. [ISO 8601 기간 형식을](https://en.wikipedia.org/wiki/ISO_8601#Durations)사용합니다. |
| format | 예 | 문자열 | 날짜 시간 결과의 출력 형식입니다. 제공되지 않으면 기본 값의 형식이 사용됩니다. 표준 [형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 문자열을](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)사용합니다. |

### <a name="return-value"></a>반환 값

기간 값을 기준 값에 추가하여 발생하는 날짜 시간 값입니다.

### <a name="examples"></a>예

다음 예제 템플릿에서는 시간 값을 추가하는 다양한 방법을 보여 주며 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

이전 템플릿이 `2020-04-07 14:53:14Z`의 기본 시간으로 배포되면 출력은 다음과 됩니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| add3년 | String | 2023년 4월 7일 오후 2:53:14 |
| 빼기9일 | String | 2020년 3월 29일 오후 2:53:14 |
| 추가 1시간 | String | 2020년 4월 7일 오후 3:53:14 |

다음 예제 템플릿에서는 자동화 일정에 대한 시작 시간을 설정하는 방법을 보여 주며 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

지정된 형식으로 현재(UTC) 날짜 시간 값을 반환합니다. 형식이 제공되지 않으면 ISO 8601(yyyMMddHMmssZ) 형식이 사용됩니다. **이 함수는 매개 변수의 기본값에서만 사용할 수 있습니다.**

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| format |예 |문자열 |문자열로 변환할 URI 인코딩 값입니다. 표준 [형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 문자열을](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)사용합니다. |

### <a name="remarks"></a>설명

매개 변수의 기본값에 대해서만 이 함수를 식 내에서 사용할 수 있습니다. 템플릿의 다른 곳에서이 함수를 사용하면 오류가 반환됩니다. 함수는 호출될 때마다 다른 값을 반환하기 때문에 템플릿의 다른 부분에서는 허용되지 않습니다. 동일한 매개 변수를 사용 하 여 동일한 템플릿을 배포 하면 동일한 결과 안정적으로 생성 되지 않습니다.

이전 성공적인 [배포를 다시 배포하는 옵션을](rollback-on-error.md)사용하고 이전 배포에 utcNow를 사용하는 매개 변수가 포함되어 있으면 매개 변수가 다시 평가되지 않습니다. 대신 이전 배포의 매개 변수 값은 롤백 배포에서 자동으로 다시 사용됩니다.

기본값에 대 한 utcNow 함수에 의존 하는 서식 파일을 다시 배포 주의 해야 합니다. 매개 변수에 대한 값을 다시 배포하고 제공하지 않으면 함수가 다시 평가됩니다. 새 리소스를 만드는 대신 기존 리소스를 업데이트하려면 이전 배포에서 매개 변수 값을 전달합니다.

### <a name="return-value"></a>반환 값

현재 UTC 날짜 시간 값입니다.

### <a name="examples"></a>예

다음 예제 템플릿에서는 datetime 값에 대한 다양한 형식을 보여 주며 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

앞의 예제의 출력은 배포마다 다르지만 다음과 유사합니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| utc출력 | 문자열 | 20190305T175318Z |
| utc쇼트출력 | 문자열 | 2019/03/05 |
| utc사용자 출력 | 문자열 | 3 5 |

다음 예제에서는 태그 값을 설정할 때 함수에서 값을 사용하는 방법을 보여 주며 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```