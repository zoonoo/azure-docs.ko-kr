---
title: Azure Data Factory에서 데이터 흐름 성능 및 튜닝 가이드 매핑 Microsoft Docs
description: 데이터 흐름 매핑을 사용할 때 Azure Data Factory에서 데이터 흐름의 성능에 영향을 주는 주요 요소에 대해 알아봅니다.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 8eb244a0eff1569ac27feae68104db613373463a
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992337"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>데이터 흐름 매핑 성능 및 튜닝 가이드

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

데이터 흐름 매핑 Azure Data Factory는 데이터 변환을 대규모로 디자인, 배포 및 오케스트레이션 할 수 있는 코드 없는 브라우저 인터페이스를 제공 합니다.

> [!NOTE]
> 일반적인 ADF 매핑 데이터 흐름에 익숙하지 않은 경우이 문서를 읽기 전에 [데이터 흐름 개요](concepts-data-flow-overview.md) 를 참조 하세요.
>

> [!NOTE]
> ADF UI에서 데이터 흐름을 디자인 하 고 테스트 하는 경우에는 클러스터가 준비 될 때까지 기다리지 않고 실시간으로 데이터 흐름을 실행할 수 있도록 디버그 스위치를 설정 해야 합니다.
>

![디버그 단추](media/data-flow/debugb1.png "디버그")

## <a name="monitor-data-flow-performance"></a>데이터 흐름 성능 모니터링

브라우저에서 매핑 데이터 흐름을 디자인 하는 동안 각 변환에 대해 아래쪽 설정 창에서 데이터 미리 보기 탭을 클릭 하 여 각 개별 변환을 단위 테스트할 수 있습니다. 파이프라인 디자이너에서 종단 간 데이터 흐름을 테스트 하려면 다음 단계를 수행 해야 합니다. 데이터 흐름 실행 작업을 추가 하 고 디버그 단추를 사용 하 여 데이터 흐름의 성능을 테스트 합니다. 파이프라인 창의 아래쪽 창에는 "작업" 아래에 eyeglass 아이콘이 표시 됩니다.

![데이터 흐름 모니터](media/data-flow/mon002.png "데이터 흐름 모니터 2")

해당 아이콘을 클릭 하면 데이터 흐름의 실행 계획 및 후속 성능 프로필이 표시 됩니다. 이 정보를 사용 하 여 다양 한 크기의 데이터 원본에 대 한 데이터 흐름의 성능을 예측할 수 있습니다. 전체 성능 계산에서 1 분의 클러스터 작업 실행을 설정 하는 것으로 가정할 수 있으며, 기본 Azure Integration Runtime 사용 하는 경우 클러스터 스핀 시간을 5 분으로 추가 해야 할 수 있습니다.

![데이터 흐름 모니터링](media/data-flow/mon003.png "데이터 흐름 모니터 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL Database 및 Azure SQL Data Warehouse 최적화

![원본 파트](media/data-flow/sourcepart3.png "원본 파트")

### <a name="partition-your-source-data"></a>원본 데이터 분할

* "최적화"로 이동 하 고 "원본"을 선택 합니다. 특정 테이블 열 또는 쿼리에서 유형을 설정 합니다.
* "열"을 선택한 경우 파티션 열을 선택 합니다.
* 또한 Azure SQL DB에 대 한 최대 연결 수를 설정 합니다. 더 높은 설정을 시도 하 여 데이터베이스에 대 한 병렬 연결을 확보할 수 있습니다. 그러나 일부 경우에는 제한 된 수의 연결을 사용 하 여 더 빠른 성능을 얻을 수 있습니다.
* 원본 데이터베이스 테이블을 분할할 필요가 없습니다.
* 원본 변환에서 데이터베이스 테이블의 파티션 구성표와 일치 하는 쿼리를 설정 하면 원본 데이터베이스 엔진에서 파티션 제거를 활용할 수 있습니다.
* 원본이 아직 분할 되지 않은 경우 ADF는 원본 변환에서 선택한 키에 따라 Spark 변환 환경에서 데이터 분할을 계속 사용 합니다.

### <a name="set-batch-size-and-query-on-source"></a>원본에서 일괄 처리 크기 및 쿼리 설정

![원본](media/data-flow/source4.png "원본")

* 일괄 처리 크기를 설정 하면 ADF가 행 단위 대신 메모리의 집합에 데이터를 저장 하도록 합니다. 이 옵션은 선택적 설정 이며 적절 한 크기를 지정 하지 않으면 계산 노드에서 리소스가 부족 한 것일 수 있습니다.
* 쿼리를 설정 하면 처리를 위해 데이터 흐름에 도달 하기 전에 원본에서 오른쪽으로 행을 필터링 하 여 초기 데이터 가져오기를 더 빠르게 수행할 수 있습니다.
* 쿼리를 사용 하는 경우 Azure SQL DB에 대 한 선택적 쿼리 힌트 (예: 커밋되지 않은 읽기)를 추가할 수 있습니다.

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>SQL 데이터 집합에 대 한 원본 변환 설정의 격리 수준 설정

* 커밋되지 않은 읽기는 원본 변환에서 더 빠른 쿼리 결과를 제공 합니다.

![격리 수준](media/data-flow/isolationlevel.png "격리 수준")

### <a name="set-sink-batch-size"></a>싱크 일괄 처리 크기 설정

![싱크](media/data-flow/sink4.png "싱크")

* 데이터 흐름의 행 단위 처리를 방지 하기 위해 Azure SQL DB에 대 한 싱크 설정에서 "일괄 처리 크기"를 설정 합니다. 그러면 ADF가 제공 된 크기에 따라 일괄 처리로 데이터베이스 쓰기를 처리 합니다.

### <a name="set-partitioning-options-on-your-sink"></a>싱크에 대 한 분할 옵션 설정

* 대상 Azure SQL DB 테이블에서 데이터가 분할 되지 않은 경우에도 최적화 탭으로 이동 하 여 분할을 설정 합니다.
* 경우에 따라 ADF가 Spark 실행 클러스터에서 라운드 로빈 분할을 사용 하도록 하면 단일 노드/파티션의 모든 연결을 강제 적용 하는 대신 훨씬 더 빠른 데이터 로드가 발생 합니다.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Azure Integration Runtime에서 계산 엔진 크기를 늘립니다.

![새 IR](media/data-flow/ir-new.png "새 IR")

* 코어 수를 늘려 노드 수를 늘리고 Azure SQL DB에 대 한 쿼리 및 쓰기를 위한 더 많은 처리 기능을 제공 합니다.
* 계산 노드에 더 많은 리소스를 적용 하려면 "계산 최적화" 및 "메모리 최적화" 옵션을 사용해 보세요.

### <a name="unit-test-and-performance-test-with-debug"></a>디버그를 사용 하 여 단위 테스트 및 성능 테스트

* 데이터 흐름을 단위 테스트 하는 경우 "데이터 흐름 디버그" 단추를 켜기로 설정 합니다.
* 데이터 흐름 디자이너 내에서 변환의 데이터 미리 보기 탭을 사용 하 여 변환 논리의 결과를 볼 수 있습니다.
* 파이프라인 디자인 캔버스에 데이터 흐름 작업을 배치 하 고 "디버그" 단추를 사용 하 여 파이프라인 디자이너에서 데이터 흐름 단위 테스트를 사용 하 여 테스트 합니다.
* 디버그 모드에서의 테스트는 just-in-time 클러스터 스핀을 기다릴 필요 없이 라이브 준비 클러스터 환경에 대해 작동 합니다.

### <a name="disable-indexes-on-write"></a>쓰기 시 인덱스 사용 안 함
* 데이터 흐름 작업 이전에 ADF 파이프라인 저장 프로시저 작업을 사용 하 여 싱크에 기록 되는 대상 테이블의 인덱스를 비활성화 합니다.
* 데이터 흐름 작업 후 해당 인덱스를 사용 하도록 설정 하는 다른 저장 프로시저 작업을 추가 합니다.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Azure SQL DB의 크기를 늘립니다.
* 사용자가 DTU 한도에 도달 하면 처리량을 늘리고 Azure 제한을 최소화 하기 위해 파이프라인을 실행 하기 전에 원본 및 싱크 Azure SQL DB의 크기 조정을 예약 합니다.
* 파이프라인 실행이 완료 된 후에는 데이터베이스를 다시 일반 실행 속도로 조정할 수 있습니다.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse 최적화

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>준비를 사용 하 여 Polybase를 통해 데이터를 대량으로 로드

* 데이터 흐름의 행 단위 처리를 방지 하려면 ADF가 Polybase를 활용 하 여 DW에 행 단위 삽입을 방지 하기 위해 싱크 설정에서 "준비" 옵션을 설정 합니다. 이렇게 하면 ADF를 사용 하 여 데이터를 대량으로 로드할 수 있습니다.
* 스테이징 기능이 설정 된 상태에서 파이프라인에서 데이터 흐름 활동을 실행할 경우 대량 로드를 위해 준비 데이터의 Blob 저장소 위치를 선택 해야 합니다.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Azure SQL DW의 크기를 늘립니다.

* 파이프라인을 실행 하기 전에 원본 및 싱크 Azure SQL DW의 크기를 조정 하 여 처리량을 늘리고 DWU 제한에 도달 하면 Azure 제한을 최소화 합니다.

* 파이프라인 실행이 완료 된 후에는 데이터베이스를 다시 일반 실행 속도로 조정할 수 있습니다.

## <a name="optimize-for-files"></a>파일 최적화

* ADF에서 사용할 파티션 수를 제어할 수 있습니다. 각 원본 & 싱크 변환 뿐만 아니라 각 개별 변환에서 파티션 구성표를 설정할 수 있습니다. 작은 파일의 경우 Spark에 작은 파일을 분할 하 라고 요청 하는 것 보다 "단일 파티션"을 선택 하는 것이 더 빠르고 빠르게 작동할 수 있습니다.
* 원본 데이터에 대 한 정보가 충분 하지 않은 경우 "라운드 로빈" 분할을 선택 하 고 파티션 수를 설정할 수 있습니다.
* 데이터를 탐색 하 고 적절 한 해시 키가 될 수 있는 열이 있는 경우 해시 분할 옵션을 사용 합니다.
* 데이터 미리 보기 및 파이프라인 디버그에서 디버그할 때 파일 기반 원본 데이터 집합의 제한 및 샘플링 크기는 읽은 행 수를 나타내는 것이 아니라 반환 되는 행 수에만 적용 됩니다. 이는 디버그 실행의 성능에 영향을 미칠 수 있으며 흐름이 실패 하는 원인이 될 수 있기 때문에 주의 해야 합니다.
* 디버그 클러스터는 기본적으로 작은 단일 노드 클러스터 이므로 디버깅에 임시 작은 파일을 사용 해야 합니다. 디버그 설정으로 이동 하 여 임시 파일을 사용 하 여 데이터의 작은 하위 집합을 가리킵니다.

![디버그 설정](media/data-flow/debugsettings3.png "디버그 설정")

### <a name="file-naming-options"></a>파일 명명 옵션

* ADF 매핑 데이터 흐름에서 변환 된 데이터를 작성 하는 기본 특성은 Blob 또는 ADLS 연결 된 서비스를 포함 하는 데이터 집합에 쓰는 것입니다. 명명 된 파일이 아닌 폴더 또는 컨테이너를 가리키도록 해당 데이터 집합을 설정 해야 합니다.
* 데이터 흐름은 실행에 Azure Databricks Spark를 사용 합니다. 즉, 기본 Spark 분할 또는 명시적으로 선택한 파티션 구성표를 기반으로 하 여 여러 파일에 출력을 분할 합니다.
* ADF 데이터 흐름에서 매우 일반적인 작업은 모든 출력 부분 파일이 단일 출력 파일에 병합 되도록 "단일 파일에 출력"을 선택 하는 것입니다.
* 그러나이 작업을 수행 하려면 출력이 단일 클러스터 노드의 단일 파티션으로 축소 되어야 합니다.
* 이 인기 있는 옵션을 선택할 때이 점을 염두에 두어야 합니다. 많은 수의 대량 원본 파일을 단일 출력 파일 파티션에 결합 하는 경우 클러스터 노드 리소스가 부족 합니다.
* 계산 노드 리소스의 발생을 방지 하기 위해 ADF에서 성능에 맞게 최적화 된 기본 또는 명시적 파티션 구성표를 유지 한 다음 출력 폴더의 모든 파트 파일을 새 단일 파일에 병합 하는 후속 복사 작업을 파이프라인에 추가할 수 있습니다. 파일과. 기본적으로이 기술은 파일 병합에서 변환 작업을 분리 하 고 "출력을 단일 파일로 설정" 하는 것과 동일한 결과를 얻을 수 있습니다.

### <a name="looping-through-file-lists"></a>파일 목록 반복

대부분의 경우 ADF의 데이터 흐름은 데이터 흐름 원본 변환이 여러 파일을 반복할 수 있도록 하는 파이프라인에서 더 효율적으로 실행 됩니다. 즉, 파이프라인에서 ForEach를 사용 하 여 많은 파일 목록을 반복 하 고 각 반복에서 데이터 흐름 실행을 호출 하는 것 보다는 데이터 흐름의 소스 내에서 와일드 카드 또는 파일 목록을 사용 하는 것이 좋습니다. 데이터 흐름 내에서 루프가 발생할 수 있도록 하 여 데이터 흐름 프로세스가 더 빠르게 실행 됩니다.

예를 들어 7 월 2019의 데이터 파일 목록이 Blob Storage 폴더에서 처리 하려는 경우 파이프라인에서 데이터 흐름 실행 작업을 한 번 호출 하 고 다음과 같이 소스에서 와일드 카드를 사용 하는 것이 더 나을 수 있습니다. :

```DateFiles/*_201907*.txt```

이는 내부에서 데이터 흐름 실행 작업을 사용 하 여 ForEach를 사용 하 여 일치 하는 모든 파일에서 반복 하는 파이프라인의 Blob 저장소에 대 한 조회 보다 성능이 더 우수 합니다.

## <a name="next-steps"></a>다음 단계

성능 관련 다른 데이터 흐름 문서를 참조 하세요.

- [데이터 흐름 최적화 탭](concepts-data-flow-optimize-tab.md)
- [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)
- [데이터 흐름 성능 모니터링](concepts-data-flow-monitoring.md)
