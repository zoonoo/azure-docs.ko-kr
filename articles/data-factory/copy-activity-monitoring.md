---
title: 복사 작업 모니터
description: Azure Data Factory에서 복사 작업 실행을 모니터링 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: 92119709aa260f3180c503a77064f6e80dece6e6
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440612"
---
# <a name="monitor-copy-activity"></a>복사 작업 모니터

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 복사 작업 실행을 모니터링 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="monitor-visually"></a>시각적으로 모니터링

Azure Data Factory에서 파이프라인을 만들어 게시한 후에는 트리거와 연결하거나 임시 실행을 수동으로 시작할 수 있습니다. Azure Data Factory 사용자 환경에서 기본적으로 모든 파이프라인 실행을 모니터링할 수 있습니다. [시각적 모니터 Azure Data Factory](monitor-visually.md)에서 일반적인 Azure Data Factory 모니터링에 대해 알아봅니다.

복사 작업 실행을 모니터링 하려면 데이터 팩터리 **작성자 & 모니터링** UI로 이동 합니다. **모니터** 탭에서 파이프라인 실행 목록이 표시 되 면 파이프라인 **이름** 링크를 클릭 하 여 파이프라인 실행의 활동 실행 목록에 액세스 합니다.

![파이프라인 실행 모니터링](./media/copy-activity-overview/monitor-pipeline-run.png)

이 수준에서 복사 작업 입력, 출력 및 오류 (복사 작업 실행이 실패 한 경우)에 대 한 링크와 기간/상태와 같은 통계를 볼 수 있습니다. 복사 작업 이름 옆의 **자세히** 단추 (안경)를 클릭 하면 복사 작업 실행에 대 한 자세한 정보가 제공 됩니다. 

![복사 작업 실행 모니터링](./media/copy-activity-overview/monitor-copy-activity-run.png)

이 그래픽 모니터링 보기에서는 데이터 읽기/쓰기 볼륨, 원본에서 싱크로 복사 된 데이터의 파일/행 수, 처리량, 복사 시나리오에 적용 된 구성, 복사 작업이 해당 기간 및 세부 정보로 이동 하는 단계를 포함 하 여 복사 작업 실행 정보를 Azure Data Factory 표시 합니다. 가능한 각 메트릭 및 자세한 설명에서 [이 표](#monitor-programmatically) 를 참조 하세요. 

일부 시나리오에서는 Data Factory 복사 작업을 실행할 때 복사 작업 모니터링 보기의 맨 위에 예에 나와 있는 것 처럼 **"성능 튜닝 팁"**  이 표시 됩니다. 팁은 복사 처리량 향상을 위해 변경 해야 하는 사항에 대 한 제안 사항을 비롯 하 여 특정 복사 실행에 대해 ADF에서 식별 한 병목 상태를 알려 줍니다. [자동 성능 튜닝 팁](copy-activity-performance-troubleshooting.md#performance-tuning-tips)에 대해 자세히 알아보세요.

아래쪽 **실행 세부 정보 및 기간은** 복사 작업을 수행 하는 주요 단계를 설명 하며,이는 복사 성능 문제를 해결 하는 데 특히 유용 합니다. 복사 실행에 대 한 병목 상태는 기간이 가장 긴 것입니다. 각 스테이지가 나타내는 내용과 자세한 문제 해결 지침은에서 [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md) 을 참조 하세요.

**예: Amazon s 3에서 Azure Data Lake Storage Gen2로 복사**

![복사 작업 실행 세부 정보 모니터링](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>프로그래밍 방식으로 모니터링

복사 작업 실행 세부 정보 및 성능 특성도 UI 모니터링 보기를 렌더링 하는 데 사용 되는 **복사 작업 실행 결과**  >  **출력** 섹션에도 반환 됩니다. 다음은 반환 될 수 있는 속성의 전체 목록입니다. 복사 시나리오에 적용 되는 속성만 볼 수 있습니다. 일반적인 방법으로 작업 실행을 모니터링 하는 방법에 대 한 자세한 내용은 [Azure data factory를 프로그래밍 방식으로 모니터링](monitor-programmatically.md)을 참조 하세요.

| 속성 이름  | 설명 | 출력 단위 |
|:--- |:--- |:--- |
| DataRead | 원본에서 읽은 실제 데이터 양입니다. | Int64 값 (바이트) |
| DataWritten | 싱크에 기록/커밋된 데이터의 실제 탑재입니다. 크기는 `dataRead` 각 데이터 저장소에서 데이터를 저장 하는 방법과 관련 되므로 크기와 다를 수 있습니다. | Int64 값 (바이트) |
| filesRead | 파일 기반 소스에서 읽은 파일 수입니다. | Int64 값(단위 없음) |
| filesWritten | 파일 기반 싱크에 대해 작성/커밋된 파일 수입니다. | Int64 값(단위 없음) |
| filesSkipped | 파일 기반 소스에서 건너뛴 파일 수입니다. | Int64 값(단위 없음) |
| dataConsistencyVerification | 원본 및 대상 저장소 간에 복사 된 데이터가 일관 되 게 확인 되었는지 확인할 수 있는 데이터 일관성 확인에 대 한 세부 정보입니다. [이 문서](copy-activity-data-consistency.md#monitoring)에서 자세히 알아보세요. | 배열 |
| sourcePeakConnections | 복사 작업을 실행 하는 동안 원본 데이터 저장소에 설정 된 최대 동시 연결 수입니다. | Int64 값(단위 없음) |
| sinkPeakConnections | 복사 작업을 실행 하는 동안 싱크 데이터 저장소에 설정 된 최대 동시 연결 수입니다. | Int64 값(단위 없음) |
| rowsRead | 원본에서 읽은 행 수입니다. 원본 및 싱크 데이터 집합이 이진 형식 형식 이거나 설정이 동일한 다른 형식 형식인 경우를 제외 하 고 파일을 있는 그대로 복사 하는 경우에는이 메트릭이 적용 되지 않습니다. | Int64 값(단위 없음) |
| rowsCopied | 싱크로 복사 된 행의 수입니다. 원본 및 싱크 데이터 집합이 이진 형식 형식 이거나 설정이 동일한 다른 형식 형식인 경우를 제외 하 고 파일을 있는 그대로 복사 하는 경우에는이 메트릭이 적용 되지 않습니다.  | Int64 값(단위 없음) |
| rowsSkipped | 건너뛴 호환 되지 않는 행의 수입니다. 을 true로 설정 하 여 호환 되지 않는 행을 건너뛰도록 설정할 수 있습니다 `enableSkipIncompatibleRow` . | Int64 값(단위 없음) |
| copyDuration | 복사 실행의 지속 시간입니다. | Int32 값 (초) |
| throughput | 데이터 전송 률입니다. | 부동 소수점 수 (KBps) |
| sourcePeakConnections | 복사 작업을 실행 하는 동안 원본 데이터 저장소에 설정 된 최대 동시 연결 수입니다. | Int32 값 (단위 없음) |
| sinkPeakConnections| 복사 작업을 실행 하는 동안 싱크 데이터 저장소에 설정 된 최대 동시 연결 수입니다.| Int32 값 (단위 없음) |
| sqlDwPolyBase | 데이터가 Azure Synapse Analytics로 복사 될 때 (이전에 SQL Data Warehouse) PolyBase를 사용할지 여부입니다. | 부울 |
| redshiftUnload | Redshift에서 데이터를 복사할 때 언로드가 사용 되는지 여부입니다. | 부울 |
| hdfsDistcp | HDFS에서 데이터를 복사할 때 DistCp를 사용할지 여부를 지정 합니다. | 부울 |
| effectiveIntegrationRuntime | 작업을 실행 하는 데 사용 되는 IR (통합 런타임) 또는 런타임 형식 `<IR name> (<region if it's Azure IR>)` 입니다. | 텍스트(문자열) |
| usedDataIntegrationUnits | 복사 중 효율적인 데이터 통합 단위입니다. | Int32 값 |
| usedParallelCopies | 복사 동안 유효한 parallelCopies입니다. | Int32 값 |
| logPath | Blob 저장소에서 건너뛴 데이터의 세션 로그 경로입니다. [내결함성](copy-activity-overview.md#fault-tolerance)을 참조 하세요. | 텍스트(문자열) |
| executionDetails | 복사 작업을 수행 하는 단계 및 해당 단계, 기간, 구성 등에 대해 자세히 설명 합니다. 이 섹션은 변경 될 수 있으므로이 섹션을 구문 분석 하지 않는 것이 좋습니다. 복사 성능을 이해 하 고 문제를 해결 하는 데 도움이 되는 방법을 더 잘 이해 하려면 [시각적 개체 모니터링](#monitor-visually) 섹션을 참조 하세요. | 배열 |
| perfRecommendation 사항 | 성능 튜닝 팁을 복사 합니다. 자세한 내용은 [성능 튜닝 팁](copy-activity-performance-troubleshooting.md#performance-tuning-tips) 을 참조 하세요. | 배열 |
| billingReference | 지정 된 실행에 대 한 청구 사용량입니다. [활동 실행 수준에서 모니터 사용에](plan-manage-costs.md#monitor-consumption-at-activity-run-level)대해 자세히 알아보세요. | Object |
| durationInQueue | 복사 작업 실행을 시작 하기 전의 대기 시간 (초)입니다. | Object |

**예:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

\- [복사 작업 개요](copy-activity-overview.md)

\- [복사 작업 성능](copy-activity-performance.md)