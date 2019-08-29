---
title: Azure Data Factory의 유효성 검사 작업 | Microsoft Docs
description: 유효성 검사 작업은 사용자가 지정 하는 특정 조건에 따라 연결 된 데이터 집합의 유효성을 검사할 때까지 파이프라인의 실행을 계속 하지 않습니다.
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
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141598"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure Data Factory 유효성 검사 작업
파이프라인에서 유효성 검사를 사용 하 여 연결 된 데이터 집합 참조가 존재 하거나, 지정 된 조건을 충족 하 고, 시간 제한에 도달 하는 경우에만 파이프라인이 실행을 계속 하도록 할 수 있습니다.


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

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | ' 유효성 검사 ' 활동의 이름입니다. | String | 예 |
type | **유효성 검사**로 설정 되어야 합니다. | String | 예 |
데이터 세트 | 작업은이 데이터 집합 참조가 있고 지정 된 조건을 만족 하거나 시간 제한에 도달 하 여 유효성을 검사할 때까지 실행을 차단 합니다. 제공 된 데이터 집합은 "이상 크기" 또는 "ChildItems" 속성을 지원 해야 합니다. | 데이터 집합 참조 | 예 |
timeout | 작업 실행에 대한 시간 제한을 지정합니다. 값을 지정 하지 않으면 기본값은 7 일 ("7.00:00:00")입니다. 형식은 d. hh: mm: ss입니다. | String | 아니요 |
sleep | 유효성 검사 시도 간의 지연 시간 (초)입니다. 값을 지정 하지 않으면 기본값은 10 초입니다. | 정수 | 아니요 |
childItems | 폴더에 자식 항목이 있는지 여부를 확인 합니다. -True로 설정할 수 있습니다. 폴더가 있고 항목이 있는지 확인 합니다. 폴더에 하나 이상의 항목이 있거나 시간 제한 값에 도달할 때까지 차단 됩니다.-false: 폴더가 있고 비어 있는지 확인 합니다. 폴더가 비어 있거나 시간 제한 값에 도달할 때까지 차단 합니다. 값을 지정 하지 않으면 폴더가 존재 하거나 시간 제한에 도달할 때까지 활동이 차단 됩니다. | Boolean | 아니요 |
minimumSize | 파일의 최소 크기 (바이트)입니다. 값을 지정 하지 않으면 기본값은 0 바이트입니다. | 정수 | 아니요 |


## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
