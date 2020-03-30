---
title: Azure 데이터 팩터리의 유효성 검사 활동
description: 유효성 검사 작업은 사용자가 지정하는 특정 조건으로 연결된 데이터 집합의 유효성을 검사할 때까지 파이프라인 실행을 계속하지 않습니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678361"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure 데이터 팩터리의 유효성 검사 활동
파이프라인에서 유효성 검사를 사용하여 연결된 데이터 집합 참조가 있는지, 지정된 조건을 충족하거나 시간 지정에 도달한 경우에만 파이프라인이 실행을 계속하도록 할 수 있습니다.


## <a name="syntax"></a>구문

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>형식 속성

속성 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | '유효성 검사' 활동의 이름 | String | yes |
type | **유효성 검사로**설정해야 합니다. | String | yes |
데이터 세트 | 활동은 이 데이터 집합 참조가 존재하고 지정된 기준을 충족하거나 시간 초과에 도달할 때까지 실행을 차단합니다. 제공된 데이터 집합은 "MinimumSize" 또는 "ChildItems" 속성을 지원해야 합니다. | 데이터 집합 참조 | yes |
시간 제한 | 작업 실행에 대한 시간 제한을 지정합니다. 값을 지정하지 않으면 기본값은 7일입니다("7.00:00:00"). 형식은 d.hh:mm:ss | String | 예 |
sleep | 유효성 검사 시도 사이의 초 지연입니다. 값을 지정하지 않으면 기본값은 10초입니다. | 정수 | 예 |
childItems | 폴더에 하위 항목이 있는지 확인합니다. true로 설정할 수 있습니다: 폴더가 존재하고 항목이 있는지 확인합니다. 폴더에 하나 이상의 항목이 있거나 시간 시간 값에 도달할 때까지 블록.-false: 폴더가 존재하고 비어 있는지 확인합니다. 폴더가 비어 있거나 시간 시간 값에 도달할 때까지 차단합니다. 값을 지정하지 않으면 폴더가 존재하거나 시간 초과에 도달할 때까지 활동이 차단됩니다. | 부울 | 예 |
최소크기 | 바이트로 파일의 최소 크기입니다. 값을 지정하지 않으면 기본값은 0바이트입니다. | 정수 | 예 |


## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 활동 실행](control-flow-execute-pipeline-activity.md)
- [각 활동에 대해](control-flow-for-each-activity.md)
- [메타데이터 활동 받기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 활동](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
