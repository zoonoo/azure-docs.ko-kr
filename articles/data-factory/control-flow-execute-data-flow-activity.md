---
title: Azure Data Factory에서 데이터 흐름 작업 실행 | Microsoft Docs
description: Data factory 파이프라인 내에서 데이터를 실행 하는 방법에서 이동 합니다.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: c33219eacb1d3bada5630a7792f98ba33dba824e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235855"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Azure Data Factory의 데이터 흐름 작업을 실행 합니다.
트리거된 파이프라인 실행 및 파이프라인 디버그 (샌드박스) 실행 ADF 데이터 흐름을 실행 하려면 실행 데이터 흐름 작업을 사용 합니다.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>구문

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>형식 속성

* ```dataflow``` 실행 하려는 데이터 흐름이 엔터티의 이름
* ```compute``` Spark 실행 환경을 설명합니다.
* ```coreCount``` 데이터 흐름의이 작업 실행에 할당할 코어 수

![데이터 흐름을 실행할](media/data-flow/activity-data-flow.png "데이터 흐름 실행")

### <a name="debugging-pipelines-with-data-flows"></a>데이터 흐름을 사용 하 여 파이프라인 디버깅

![디버그 단추](media/data-flow/debugbutton.png "디버그 단추")

데이터 흐름 디버깅을 사용 하 여 파이프라인 디버그 실행 데이터 흐름을 대화형으로 테스트용 warmed 클러스터를 활용 하 합니다. 파이프라인 내에서 데이터 흐름을 테스트 하려면 파이프라인 디버그 옵션을 사용 합니다.

### <a name="run-on"></a>실행

데이터 흐름 작업 실행에 대 한 사용 하는 Integration Runtime을 정의 하는 필수 필드입니다. 기본적으로 Data Factory는 기본 자동 해결 Azure 통합 런타임을 사용 됩니다. 그러나 특정 영역을 정의 데이터 흐름 작업 실행에 대 한 형식, 코어 수 및 TTL을 계산 하는 사용자 고유의 Azure Integration Runtime을 만들 수 있습니다.

데이터 흐름 실행에 대 한 기본 설정은 60 분의 TTL 사용 하 여 일반 계산 8 코어를입니다.

데이터 흐름의이 실행이에 대 한 계산 환경을 선택 합니다. 기본값은 자동으로 해결 기본 Azure Integration Runtime. 이 선택에는 데이터 팩터리와 동일한 지역에서 Spark 환경에서 데이터 흐름을 실행 됩니다. 계산 형식 즉, 계산 환경을 시작 하려면 몇 분 정도가 걸리기 작업 클러스터 됩니다.

데이터 흐름 작업에 대 한 Spark 실행 환경 하 게 제어를 해야합니다. 에 [Azure 통합 런타임은](concepts-integration-runtime.md) 설정이 작업자 코어 수 및 time-to-live 데이터 흐름 계산을 사용 하 여 실행 엔진에 맞게 계산 형식 (일반 목적, 메모리 액세스에 최적화 된, 및 compute에 최적화)를 설정 하려면 요구 사항입니다. 또한 TTL을 설정 하면 작업 실행에 대 한 즉시 사용할 수 있는 웜 클러스터를 유지 관리할 수 있습니다.

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure 통합 런타임")

> [!NOTE]
> 데이터 흐름 작업의 Integration Runtime 선택 영역에만 적용 됩니다 *트리거 실행* 파이프라인입니다. 디버그를 사용 하 여 데이터 흐름 사용 하 여 파이프라인을 디버깅 8 코어 기본 Spark 클러스터에 대해 실행 됩니다.

### <a name="staging-area"></a>준비 영역

Azure Data Warehouse로 데이터를 싱크는, Polybase 일괄 처리 부하에 대 한 스테이징 위치를 선택 해야 합니다.

## <a name="parameterized-datasets"></a>매개 변수가 있는 데이터 집합

매개 변수가 있는 데이터 집합을 사용 하는 경우에 매개 변수 값을 설정 해야 합니다.

![실행 데이터 흐름 매개](media/data-flow/params.png "매개 변수")

### <a name="debugging-parameterized-data-flows"></a>매개 변수가 있는 데이터 흐름 디버깅

실행 데이터 흐름 작업을 사용 하 여 실행할 파이프라인 디버그에서 매개 변수가 있는 데이터 집합을 사용 하 여 데이터 흐름만 디버깅할 수 있습니다. 현재 매개 변수가 있는 데이터 집합을 사용 하 여 ADF 데이터 흐름의 대화형 디버그 세션 작동 하지 않습니다. 파이프라인 실행 및 디버그 실행 매개 변수를 사용 하 여 작동 합니다.

디자인 타임에 사용할 수 있는 전체 메타 데이터 열 전파 갖도록 정적 데이터 집합을 사용 하 여 데이터 흐름을 작성 하는 것이 좋습니다. 데이터 흐름 파이프라인을 실제로 운영할 때는 정적 데이터 집합 동적 매개 변수가 있는 데이터 집합을 사용 하 여 다음 대체 합니다.

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
