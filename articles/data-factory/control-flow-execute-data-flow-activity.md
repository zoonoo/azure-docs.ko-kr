---
title: 데이터 흐름 활동
description: 데이터 팩터리 파이프라인 내에서 데이터 흐름을 실행하는 방법
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463053"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure 데이터 팩터리의 데이터 흐름 활동

데이터 흐름 활동을 사용하여 매핑 데이터 흐름을 통해 데이터를 변환하고 이동합니다. 데이터 흐름을 새로 접하는 경우 [데이터 흐름 매핑 개요를 참조하세요.](concepts-data-flow-overview.md)

## <a name="syntax"></a>구문

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>형식 속성

속성 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
Dataflow | 실행 중인 데이터 흐름에 대한 참조 | 데이터 흐름 참조 | yes
통합런타임 | 데이터 흐름이 실행되는 계산 환경입니다. 지정하지 않으면 자동 확인 Azure 통합 런타임이 사용됩니다. | 통합런타임 참조 | 예
계산.코어카운트 | 스파크 클러스터에 사용되는 코어 수입니다. 자동 확인 Azure 통합 런타임이 사용되는 경우에만 지정할 수 있습니다. | 8, 16, 32, 48, 80, 144, 272 | 예
계산.계산 유형 | 스파크 클러스터에 사용되는 계산 유형입니다. 자동 확인 Azure 통합 런타임이 사용되는 경우에만 지정할 수 있습니다. | "일반", "계산 최적화", "메모리 최적화" | 예
스테이징.링크서비스 | SQL DW 소스 또는 싱크를 사용하는 경우 PolyBase 스테이징에 사용되는 저장소 계정 | 링크된 서비스 참조 | 데이터 흐름이 SQL DW에 읽거나 쓰는 경우에만
스테이징.폴더패스 | SQL DW 소스 또는 싱크를 사용하는 경우 PolyBase 스테이징에 사용되는 Blob 저장소 계정의 폴더 경로 | String | 데이터 흐름이 SQL DW에 읽거나 쓰는 경우에만

![데이터 흐름 실행](media/data-flow/activity-data-flow.png "데이터 흐름 실행")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>런타임 시 동적으로 크기 조정 데이터 흐름 계산

코어 카운트 및 계산 유형 속성은 런타임시 들어오는 원본 데이터의 크기에 맞게 동적으로 설정할 수 있습니다. 원본 데이터 집합 데이터의 크기를 찾기 위해 조회 또는 메타데이터 받기와 같은 파이프라인 활동을 사용합니다. 그런 다음 데이터 흐름 활동 속성에서 동적 콘텐츠 추가를 사용합니다.

![동적 데이터 흐름](media/data-flow/dyna1.png "동적 데이터 흐름")

[다음은이 기술을 설명하는 간단한 비디오 자습서입니다.](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>데이터 흐름 통합 런타임

데이터 흐름 활동 실행에 사용할 통합 런타임을 선택합니다. 기본적으로 Data Factory는 4개의 작업자 코어가 있는 자동 해결 Azure 통합 런타임을 사용하고 TTL(라이브 시간)을 사용할 시간이 없습니다. 이 IR은 범용 계산 유형을 가지며 공장과 동일한 지역에서 실행됩니다. 데이터 흐름 활동 실행에 대한 특정 지역, 계산 유형, 코어 수 및 TTL을 정의하는 고유한 Azure 통합 런타임을 만들 수 있습니다.

파이프라인 실행의 경우 클러스터는 작업 클러스터로 실행을 시작하기 전에 시작하는 데 몇 분 정도 걸립니다. TTL을 지정하지 않으면 모든 파이프라인 실행에 이 시작 시간이 필요합니다. TTL을 지정하면 웜 클러스터 풀은 마지막 실행 후 지정된 시간 동안 활성 상태로 유지되어 시작 시간이 단축됩니다. 예를 들어 TTL이 60분이고 한 시간에 한 번 데이터 흐름을 실행하는 경우 클러스터 풀은 활성 상태로 유지됩니다. 자세한 내용은 [Azure 통합 런타임](concepts-integration-runtime.md)을 참조하십시오.

![Azure 통합 런타임](media/data-flow/ir-new.png "Azure 통합 런타임")

> [!NOTE]
> 데이터 흐름 활동의 통합 런타임 선택은 파이프라인의 *트리거된 실행에만* 적용됩니다. 데이터 흐름으로 파이프라인을 디버깅하는 것은 디버그 세션에 지정된 클러스터에서 실행됩니다.

### <a name="polybase"></a>PolyBase

Azure SQL 데이터 웨어하우스를 싱크 또는 원본으로 사용하는 경우 PolyBase 일괄 처리 로드에 대한 스테이징 위치를 선택해야 합니다. PolyBase는 데이터 행별로 로드하는 대신 일괄 로딩을 허용합니다. PolyBase는 SQL DW에 대한 로드 시간을 크게 줄입니다.

## <a name="parameterizing-data-flows"></a>데이터 흐름 매개 변수화

### <a name="parameterized-datasets"></a>매개 변수화된 데이터 집합

데이터 흐름에서 매개 변수화된 데이터 집합을 사용하는 경우 **설정** 탭에서 매개 변수 값을 설정합니다.

![데이터 흐름 매개 변수 실행](media/data-flow/params.png "매개 변수")

### <a name="parameterized-data-flows"></a>매개 변수화된 데이터 흐름

데이터 흐름이 매개 변수화된 경우 매개 변수 탭에서 데이터 흐름 매개 변수의 동적 값을 **설정합니다.** ADF 파이프라인 표현언어(문자열 형식에만) 또는 데이터 흐름 식 언어를 사용하여 동적 또는 리터럴 매개 변수 값을 할당할 수 있습니다. 자세한 내용은 [데이터 흐름 매개 변수 를](parameters-data-flow.md)참조하십시오.

![데이터 흐름 매개 변수 예제 실행](media/data-flow/parameter-example.png "매개 변수 예")

### <a name="parameterized-compute-properties"></a>매개 변수화된 계산 속성입니다.

자동 확인 Azure 통합 런타임을 사용하고 compute.coreCount 및 compute.computeType에 대한 값을 지정하는 경우 코어 수 또는 계산 유형을 매개 변수화할 수 있습니다.

![데이터 흐름 매개 변수 예제 실행](media/data-flow/parameterize-compute.png "매개 변수 예")

## <a name="pipeline-debug-of-data-flow-activity"></a>데이터 흐름 활동의 파이프라인 디버그

데이터 흐름 활동으로 디버그 파이프라인 실행을 실행하려면 상단 막대의 데이터 **흐름 디버그** 슬라이더를 통해 데이터 흐름 디버그 모드를 전환해야 합니다. 디버그 모드를 사용하면 활성 Spark 클러스터에 대해 데이터 흐름을 실행할 수 있습니다. 자세한 내용은 [디버그 모드를](concepts-data-flow-debug-mode.md)참조하십시오.

![디버그 버튼](media/data-flow/debugbutton.png "디버그 버튼")

디버그 파이프라인은 데이터 흐름 활동 설정에 지정된 통합 런타임 환경이 아닌 활성 디버그 클러스터에 대해 실행됩니다. 디버그 모드를 시작할 때 디버그 계산 환경을 선택할 수 있습니다.

## <a name="monitoring-the-data-flow-activity"></a>데이터 흐름 활동 모니터링

데이터 흐름 활동에는 분할, 스테이지 시간 및 데이터 계보 정보를 볼 수 있는 특별한 모니터링 환경이 있습니다. **작업**아래의 안경 아이콘을 통해 모니터링 창을 엽니다. 자세한 내용은 [데이터 흐름 모니터링](concepts-data-flow-monitoring.md)을 참조하십시오.

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>데이터 흐름 활동 사용으로 후속 활동이 발생합니다.

데이터 흐름 활동은 각 싱크에 기록된 행 수와 각 소스에서 읽은 행수에 대한 메트릭을 출력합니다. 이러한 결과는 활동 `output` 실행 결과 섹션에서 반환됩니다. 반환되는 메트릭은 아래 json 의 형식입니다.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

예를 들어 'dataflowActivity'라는 활동에서 'sink1'이라는 싱크에 기록된 행 수를 `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`얻으려면 을 사용합니다.

해당 싱크에 사용된 'source1'이라는 소스에서 읽은 행 수를 얻으려면 `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`을 사용합니다.

> [!NOTE]
> 싱크에 0개의 행이 기록된 경우 메트릭에 표시되지 않습니다. 이 함수를 `contains` 사용하여 존재를 확인할 수 있습니다. 예를 들어, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` 싱크에 기록 된 행을 확인 합니다1.

## <a name="next-steps"></a>다음 단계

데이터 팩터리에서 지원하는 제어 흐름 활동 보기: 

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 활동 실행](control-flow-execute-pipeline-activity.md)
- [각 활동에 대해](control-flow-for-each-activity.md)
- [메타데이터 활동 받기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 활동](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
