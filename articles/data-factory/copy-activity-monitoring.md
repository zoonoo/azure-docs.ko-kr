---
title: 복사 작업 모니터
description: Azure 데이터 팩터리에서 복사 활동 실행을 모니터링하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 47824095e892ca3c919d2d871feb612758ab2308
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417847"
---
# <a name="monitor-copy-activity"></a>복사 작업 모니터

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 복사 활동 실행을 모니터링하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="monitor-visually"></a>시각적으로 모니터링

Azure Data Factory에서 파이프라인을 만들고 게시한 후에는 파이프라인을 트리거와 연결하거나 임시 실행을 수동으로 시작할 수 있습니다. Azure Data Factory 사용자 환경에서 기본적으로 실행되는 모든 파이프라인을 모니터링할 수 있습니다. 일반적으로 Azure 데이터 팩터리 모니터링에 대해 [시각적으로 Azure 데이터 팩터리](monitor-visually.md)에서 자세히 알아봅니다.

복사 활동 실행을 모니터링하려면 데이터 팩터리 **작성자 & 모니터** UI로 이동합니다. **모니터** 탭에서 파이프라인 실행 목록이 표시되며 **파이프라인 이름** 링크를 클릭하여 파이프라인 실행에서 실행되는 활동 목록에 액세스합니다.

![복사 활동 실행 모니터링](./media/copy-activity-overview/monitor-pipeline-run.png)

이 수준에서는 활동 입력, 출력 및 오류(복사 활동 실행이 실패한 경우)와 기간/상태와 같은 통계를 복사하는 링크를 볼 수 있습니다. 복사 활동 이름 옆에 있는 **세부 정보** 버튼(안경)을 클릭하면 복사 활동 실행에 대한 자세한 정보를 확인할 수 있습니다. 

![복사 활동 실행 모니터링](./media/copy-activity-overview/monitor-copy-activity-run.png)

이 그래픽 모니터링 보기에서 Azure Data Factory는 데이터 읽기/기록 볼륨, 원본에서 싱크로 복사된 데이터의 파일/행 수, 처리량, 복사 시나리오에 적용된 구성, 복사 활동이 해당 기간 및 세부 정보로 진행되는 단계 등을 포함하여 복사 활동 실행 정보를 제공합니다. 가능한 각 메트릭 및 자세한 설명에 대한 [이 표를](#monitor-programmatically) 참조하십시오. 

일부 시나리오에서는 데이터 팩터리에서 복사 활동을 실행하면 예제와 같이 복사 활동 모니터링 보기 상단에 **"성능 조정 팁"이** 표시됩니다. 팁은 특정 복사 실행에 대해 ADF에서 식별한 병목 현상과 복사 처리량을 높이기 위해 변경해야 할 사항에 대한 제안을 알려줍니다. [자동 성능 튜닝 팁에](copy-activity-performance-troubleshooting.md#performance-tuning-tips)대해 자세히 알아보십시오.

하단 **실행 세부 정보 및 기간은** 복사 작업이 진행되는 주요 단계를 설명하며, 복사 성능 문제를 해결하는 데 특히 유용합니다. 복사 실행의 병목 현상은 가장 긴 기간의 병목 현상입니다. 각 단계가 나타내는 내용과 자세한 문제 해결 지침에 대한 [문제 해결 복사 활동 성능을](copy-activity-performance-troubleshooting.md) 참조하십시오.

**예: 아마존 S3에서 Azure 데이터 레이크 스토리지 Gen2로 복사**

![복사 활동 실행 세부 정보 모니터링](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>프로그래밍 방식으로 모니터링

복사 활동 실행 세부 정보 및 성능 특성은 UI 모니터링 보기를 렌더링하는 데 사용되는 **복사 활동 실행 결과** > **출력** 섹션에서도 반환됩니다. 다음은 반환될 수 있는 속성의 전체 목록입니다. 복사 시나리오에 적용할 수 있는 속성만 표시됩니다. 일반적으로 프로그래밍 방식으로 실행되는 활동을 모니터링하는 방법에 대한 자세한 내용은 [프로그래밍 방식으로 Azure 데이터 팩터리 를 모니터링합니다.](monitor-programmatically.md)

| 속성 이름  | Description | 출력 단위 |
|:--- |:--- |:--- |
| DataRead | 원본에서 읽은 실제 데이터 양입니다. | INt64 값(바이트) |
| DataWritten | 싱크대에 기록/커밋된 데이터의 실제 마운트입니다. 크기는 각 데이터 `dataRead` 저장소가 데이터를 저장하는 방법과 관련이 있으므로 크기와 다를 수 있습니다. | INt64 값(바이트) |
| filesRead | 파일 기반 소스에서 읽은 파일 수입니다. | Int64 값(단위 없음) |
| filesWritten | 파일 기반 싱크에 기록/커밋된 파일 수입니다. | Int64 값(단위 없음) |
| 소스피크커넥션 | 복사 활동 실행 중에 원본 데이터 저장소에 설정된 최대 연결 수입니다. | Int64 값(단위 없음) |
| 싱크피크커넥션 | 복사 활동 실행 중에 싱크 데이터 저장소에 설정된 최대 연결 수입니다. | Int64 값(단위 없음) |
| 행읽기 | 소스에서 읽은 행 수입니다(이진 복사본에는 적용되지 않음). | Int64 값(단위 없음) |
| rowsCopied | 싱크에 복사된 행 수입니다(이진 복사본에는 적용되지 않음). | Int64 값(단위 없음) |
| rowsSkipped | 건너뛴 호환되지 않는 행의 수입니다. true로 설정하여 `enableSkipIncompatibleRow` 호환되지 않는 행을 건너뛸 수 있습니다. | Int64 값(단위 없음) |
| copyDuration | 복사본 실행 기간입니다. | Int32 값(초) |
| throughput | 데이터 전송 속도입니다. | 부동 소수점 번호( KBps) |
| 소스피크커넥션 | 복사 활동 실행 중에 원본 데이터 저장소에 설정된 최대 연결 수입니다. | Int32 값(단위 없음) |
| 싱크피크커넥션| 복사 활동 실행 중에 싱크 데이터 저장소에 설정된 최대 연결 수입니다.| Int32 값(단위 없음) |
| sqlDwPolyBase | 데이터가 SQL 데이터 웨어하우스에 복사될 때 PolyBase가 사용되는지 여부입니다. | 부울 |
| redshiftUnload | 데이터를 Redshift에서 복사할 때 언로드가 사용되는지 여부입니다. | 부울 |
| hdfsDistcp | HDFS에서 데이터를 복사할 때 DistCp가 사용되는지 여부입니다. | 부울 |
| effectiveIntegrationRuntime | 활동 `<IR name> (<region if it's Azure IR>)`실행에 전원을 공급하는 데 사용되는 통합 런타임(IR) 또는 런타임입니다. | 텍스트(문자열) |
| usedDataIntegrationUnits | 복사 중 효율적인 데이터 통합 단위입니다. | Int32 값 |
| usedParallelCopies | 복사 동안 유효한 parallelCopies입니다. | Int32 값 |
| redirectRowPath | 속성에서 구성한 Blob 저장소에서 건너뛴 호환되지 않는 `redirectIncompatibleRowSettings` 행의 로그에 대한 경로입니다. [내결함성](copy-activity-overview.md#fault-tolerance). | 텍스트(문자열) |
| executionDetails | 복사 활동이 진행되는 단계와 해당 단계, 기간, 구성 등에 대한 자세한 내용을 확인합니다. 변경될 수 있으므로 이 섹션을 구문 분석하지 않는 것이 좋습니다. 복사 성능을 이해하고 문제를 해결하는 데 어떻게 도움이 되는지 더 잘 이해하려면 [시각적으로 모니터](#monitor-visually) 섹션을 참조하십시오. | Array |
| 퍼프추천 | 성능 튜닝 팁을 복사합니다. 자세한 내용은 [성능 튜닝 팁을](copy-activity-performance-troubleshooting.md#performance-tuning-tips) 참조하세요. | Array |

**예제:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
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

\-[활동 개요 복사](copy-activity-overview.md)

\- [복사 작업 성능](copy-activity-performance.md)