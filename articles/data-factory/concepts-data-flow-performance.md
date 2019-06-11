---
title: Azure Data Factory의 데이터 흐름 성능 매핑 및 조정 가이드 | Microsoft Docs
description: 매핑 데이터 흐름을 사용 하는 경우 Azure Data Factory의 데이터 흐름의 성능에 영향을 주는 주요 요소에 알아봅니다.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 46be01c57be0e4f5fa74f8e8b0d91db3d78f441c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480421"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>매핑 데이터 흐름 성능 및 튜닝 가이드

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure 데이터 팩터리 매핑 데이터 흐름 디자인, 배포 및 대규모 데이터 변환을 오케스트레이션 하는 코드가 없는 브라우저 인터페이스를 제공 합니다.

> [!NOTE]
> 없는 경우 ADF 매핑 데이터 흐름에 익숙한 일반적을 참조 하세요 [데이터 흐름 개요](concepts-data-flow-overview.md) 이 문서를 읽기 전에 합니다.
>

> [!NOTE]
> 디자인 하 고 ADF UI에서 데이터 흐름을 테스트 하는 경우에 디버그 스위치 켜기에서 데이터 흐름을 실행할 수 있도록 실시간 클러스터 준비 될 때까지 기다리지 않고 해야 합니다.
>

![디버그 단추](media/data-flow/debugb1.png "디버그")

## <a name="monitor-data-flow-performance"></a>데이터 흐름 성능 모니터링

브라우저에서 흐르는 데이터 매핑을 디자인 하는 동안 하면 단위 테스트 각 개별 변환 각 변환에 대 한 아래쪽 설정 창에서 데이터 미리 보기 탭을 클릭 하 여 합니다. 수행 해야 하는 다음 단계에 데이터 흐름 엔드-투-엔드 파이프라인 디자이너에서 테스트 하는 것입니다. 데이터 흐름 실행 활동을 추가 하 고 디버그 단추를 사용 하 여 데이터 흐름의 성능을 테스트 합니다. 파이프라인 창의 아래쪽 창에서 "작업"에서 안경 닦는 아이콘이 표시 됩니다.

![데이터 흐름 모니터](media/data-flow/mon002.png "데이터 흐름 2 모니터링")

해당 아이콘을 클릭 하 여 실행 계획 및 데이터 흐름의 후속 성능 프로필 표시 됩니다. 다른 크기의 데이터 원본에 대 한 데이터 흐름의 성능을 예측에이 정보를 사용할 수 있습니다. 전반적인 성능 계산에서 클러스터 작업 실행 설치 시간 1 분을 가정할 수 있습니다 하 고 클러스터 스핀업 시간 5 분을 추가 해야 기본 Azure Integration Runtime을 사용 하는 경우 note 합니다.

![데이터 흐름 모니터링](media/data-flow/mon003.png "데이터 흐름 3 모니터")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL Database 및 Azure SQL Data Warehouse에 대 한 최적화

![원본 파트](media/data-flow/sourcepart2.png "일부 원본")

### <a name="partition-your-source-data"></a>원본 데이터를 분할 합니다.

* "최적화"로 이동한 다음 "Source"를 선택 합니다. 쿼리에서 특정 테이블 열 또는 유형 중 하나를 설정 합니다.
* "열"을 선택한 경우 다음 파티션 열을 선택 합니다.
* 또한 Azure SQL DB에 연결의 최대 수 설정. 더 높은 설정 데이터베이스에 대 한 병렬 연결을 얻으려고 시도할 수 있습니다. 그러나 일부 경우 제한 된 수의 연결을 사용 하 여 성능이 향상 될 수 있습니다.
* 원본 데이터베이스 테이블 분할 될 필요가 없습니다.
* 데이터베이스 테이블의 파티션 구성표와 일치 하는 프로그램 원본 변환 쿼리를 설정 하면 파티션 제거를 활용 하는 소스 데이터베이스 엔진입니다.
* 원본에 이미 분할 되지 않은 경우 ADF 데이터 원본 변환에서 선택 하는 키를 기반으로 Spark 변환 환경에서 분할을 계속 사용 합니다.

### <a name="set-batch-size-and-query-on-source"></a>원본에서 일괄 처리 크기 및 쿼리 설정

![Source](media/data-flow/source4.png "Source")

* 행 단위로 대신 메모리에 있는 집합의 데이터를 저장 하는 ADF 지시 일괄 처리 크기를 설정 합니다. 선택적 설정 이며 부족 해질 수 있습니다 리소스에서 계산 노드의 경우 제대로 조정 되지 않습니다.
* 쿼리 설정 도착 하기 전에 데이터 흐름에 대 한 더 빠른 초기 데이터 취득을 만들 수 있는 처리에 대 한 원본에서 행 오른쪽을 필터링 할 수 있습니다.
* 즉, READ UNCOMMITTED Azure SQL DB에 대 한 선택적 쿼리 힌트는 쿼리를 사용 하는 경우 추가할 수 있습니다.

### <a name="set-sink-batch-size"></a>싱크 일괄 처리 크기를 설정 합니다.

![싱크](media/data-flow/sink4.png "싱크")

* 데이터 흐름의 행 단위로 처리를 방지 하기 위해 Azure SQL DB에 대 한 싱크 설정에서 "일괄 처리 크기"를 설정 합니다. 이 ADF 데이터베이스 처리를 제공 하는 크기에 따라 일괄 처리의 기록 알려줍니다.

### <a name="set-partitioning-options-on-your-sink"></a>분할에 싱크에서 옵션 설정

* 대상 Azure SQL DB 테이블의 분할 된 데이터에 없는 경우에 최적화 탭으로 이동 하 고 분할을 설정 합니다.
* 자주, 라운드 로빈 단일 노드/파티션에서 모든 연결을 강제 적용 하는 대신 로드 하는 훨씬 더 빠르게 데이터를 Spark 실행 클러스터에서 분할을 사용 하는 ADF 지시 하기만 하면 됩니다.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Azure 통합 런타임의 계산 엔진의 크기를 늘리십시오.

![새 IR](media/data-flow/ir-new.png "새 IR")

* 노드 수가 증가 하 고 Azure SQL DB에 쓰고 쿼리 처리 성능이 더 제공 코어의 수를 늘립니다.
* 더 많은 리소스를 계산 노드에 적용할 "계산 최적화" 및 "메모리 액세스에 최적화 된" 옵션을 시도 합니다.

### <a name="unit-test-and-performance-test-with-debug"></a>단위 테스트 및 디버그를 사용 하 여 성능 테스트

* 데이터 흐름을 테스트 하는 단위를 "데이터 흐름 디버깅" 단추를 ON으로 설정 하면 됩니다.
* 데이터 흐름 디자이너 내에서 변환 논리의 결과 보려면 변환에 데이터 미리 보기 탭을 사용 합니다.
* 데이터 흐름 활동을 파이프라인 디자인에 배치 하 여 파이프라인 디자이너에서 데이터 이동 단위 테스트 캔버스 및 "Debug" 단추를 사용 하 여 테스트 합니다.
* 디버그 모드에서 테스트를 적시에 클러스터 스핀업을 기다릴 필요 없이 라이브 준비 클러스터 환경에 대해 작동 합니다.

### <a name="disable-indexes-on-write"></a>쓰기에 대해 인덱스를 사용 하지 않도록 설정
* 프로그램 싱크에서 기록 됩니다.는 대상 테이블의 인덱스를 사용 하지 않도록 설정 하는 데이터 흐름 작업 전에 ADF 파이프라인 저장 프로시저 작업을 사용 합니다.
* 데이터 흐름 작업을 한 후 해당 인덱스를 사용 하도록 설정 하는 다른 저장된 프로시저 활동을 추가 합니다.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Azure SQL DB의 크기 늘리기
* 원본 크기 조정 예약 하 고 처리량을 증가 시키려면 DTU에 도달 하면 Azure 제한을 최소화 하 여 파이프라인을 제한 하 여 실행 하기 전에 Azure SQL DB를 싱크 키를 누릅니다.
* 파이프라인 실행 완료 되 면 데이터베이스는 일반 실행된 속도를 다시 조정할 수 있습니다.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대 한 최적화

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>준비를 사용 하 여 데이터를 대량으로 Polybase 통해 로드

* 데이터 흐름의 행 단위로 처리를 방지 하기 위해 ADF DW로-행 삽입을 방지 하려면 Polybase를 활용할 수 있도록에 싱크 설정에서 "스테이징" 옵션을 설정 합니다. 이 데이터를 대량에서 로드할 수 있도록 Polybase를 사용 하는 ADF 지시 합니다.
* 데이터 흐름 작업을 실행 하는 경우 스테이징을 사용 하 여 파이프라인 설정, 대량 로드에 대 한 준비 데이터의 Blob 저장소 위치를 선택 해야 합니다.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Azure SQL DW의 크기 늘리기

* 원본 크기 조정 예약 하 고 처리량을 늘리려면 DWU 제한에 도달 하면 Azure 제한을 최소화 하 여 파이프라인을 실행 하기 전에 Azure SQL DW 싱크 합니다.

* 파이프라인 실행 완료 되 면 데이터베이스는 일반 실행된 속도를 다시 조정할 수 있습니다.

## <a name="optimize-for-files"></a>파일에 대 한 최적화

* ADF를 사용 하는 파티션 수를 제어할 수 있습니다. 각 원본 및 싱크 변환 뿐만 아니라 각 개별 변환, 파티션 구성표를 설정할 수 있습니다. 작은 파일에 대 한 "단일 파티션"를 선택 합니다. 경우에 따라 해도 원활 하 고 작은 파일을 분할 하는 Spark를 요청 하는 보다 빠르게 찾을 수 있습니다.
* 원본 데이터에 대 한 충분 한 정보가 없는 경우 "라운드 로빈" 분할 및 파티션 수를 설정할 수도 있습니다.
* 데이터를 탐색 하 고 좋은 해시 키 수 있는 열을 발견할 경우 해시 분할 옵션을 사용 합니다.

### <a name="file-naming-options"></a>파일 이름 지정 옵션

* ADF 매핑 데이터 흐름에서 변환 된 데이터를 작성 하는 기본 특성에 Blob 또는 ADLS 연결 된 서비스는 데이터 집합을 작성 하는 것입니다. 명명 된 파일이 아니라 폴더 또는 컨테이너를 가리키도록 해당 데이터 집합을 설정 해야 합니다.
* 데이터 흐름 사용 하 여 Azure Databricks 용 Spark는 출력에 따라 여러 파일에 대해 분할 됩니다 것을 의미 하는 실행 하거나 분할 하는 Spark를 기본 또는 분할 구성표를 명시적으로 선택 했습니다.
* ADF 데이터 흐름에서 매우 일반적인 작업은 출력 파트 파일의 모든 값은 단일 출력 파일에 함께 병합 됩니다 있도록 "단일 파일을 출력"을 선택 하는 것입니다.
* 그러나이 작업 출력을 단일 클러스터 노드에서 단일 파티션으로 줄어든다는 필요 합니다.
* 이 인기 있는 옵션을 선택할 때이 점에 염두 해야 합니다. 단일 출력 파일 파티션으로 많은 큰 소스 파일을 결합 하는 경우 클러스터 노드에 리소스가 부족할 수 있습니다.
* 계산 노드 리소스 소진을 방지 하려면 성능에 대 한 단위를 최적화 하는 ADF에 기본값 또는 명시적인 파티션 구성표를 유지 및 추가한 다음 새 단일 출력 폴더에서 파일을 파트의 모든 병합 하는 파이프라인의 후속 복사 작업 수 있습니다. 파일입니다. 기본적으로,이 기술은 파일 병합에서 변환의 작업을 나누고 "단일 파일에 출력을" 설정으로 동일한 결과 달성 합니다.

## <a name="next-steps"></a>다음 단계
다른 데이터 흐름 문서를 참조 하세요.

- [데이터 흐름 개요](concepts-data-flow-overview.md)
- [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)
- [데이터 흐름 성능 모니터링](concepts-data-flow-monitoring.md)
