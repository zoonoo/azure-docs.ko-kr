---
title: Azure Data Factory의 변수 설정 작업
description: 변수 설정 작업을 사용하여 Data Factory 파이프라인에 정의된 기존 변수의 값을 설정하는 방법 알아보기
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879271"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure 데이터 팩터리에서 변수 활동 설정

Data Factory 파이프라인에서 정의된 String, Bool 또는 Array 형식의 기존 변수 값을 설정하려면 변수 설정 작업을 사용합니다.

## <a name="type-properties"></a>형식 속성

속성 | Description | 필수
-------- | ----------- | --------
name | 파이프라인의 작업 이름 | 예
description | 작업이 어떤 일을 수행하는지 설명하는 텍스트 | 아니요
type | **Set변수로** 설정해야 합니다. | 예
값 | 변수가 할당될 문자열 리터럴 또는 식 개체 값 | 예
variableName | 이 작업에 의해 설정되는 변수의 이름입니다. | 예

## <a name="incrementing-a-variable"></a>변수 증분

Azure Data Factory에서 변수와 관련된 일반적인 시나리오는 때까지 또는 foreach 활동 내에서 변수를 이터레이터로 사용하는 것입니다. 집합 변수 활동에서는 필드에 설정되는 변수를 `value` 참조할 수 없습니다. 이 제한을 해결하려면 임시 변수를 설정한 다음 두 번째 집합 변수 활동을 만듭니다. 두 번째 집합 변수 활동은 이터레이터값을 임시 변수로 설정합니다. 

다음은 이 패턴의 예입니다.

![변수 증가](media/control-flow-set-variable-activity/increment-variable.png "변수 증가")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 관련 제어 흐름 작업에 대해 알아봅니다. 

- [변수 작업 추가](control-flow-append-variable-activity.md)
