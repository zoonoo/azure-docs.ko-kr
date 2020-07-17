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
ms.openlocfilehash: a0b5fa16658d3e354bcb4f90ad998997fc844a84
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832791"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure Data Factory의 변수 설정 작업
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data Factory 파이프라인에서 정의된 String, Bool 또는 Array 형식의 기존 변수 값을 설정하려면 변수 설정 작업을 사용합니다.

## <a name="type-properties"></a>형식 속성

속성 | Description | 필수
-------- | ----------- | --------
name | 파이프라인의 작업 이름 | 예
description | 작업이 어떤 일을 수행하는지 설명하는 텍스트 | 아니요
type | **SetVariable**로 설정해야 합니다. | 예
값 | 변수가 할당된 문자열 리터럴 또는 식 개체 값 | 예
variableName | 이 작업에 의해 설정된 변수의 이름 | 예

## <a name="incrementing-a-variable"></a>변수 증분

Azure Data Factory의 변수와 관련된 일반적인 시나리오는 until 또는 foreach 활동 내에서 변수를 반복기로 사용하는 것입니다. 변수 설정 작업에서는 `value` 필드에 설정 중인 변수를 참조할 수 없습니다. 이 제한을 해결하려면 임시 변수를 설정한 다음, 두 번째 변수 설정 작업을 만듭니다. 두 번째 변수 설정 작업은 반복기의 값을 임시 변수로 설정합니다. 

이 패턴의 예는 다음과 같습니다.

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

- [변수 추가 작업](control-flow-append-variable-activity.md)
