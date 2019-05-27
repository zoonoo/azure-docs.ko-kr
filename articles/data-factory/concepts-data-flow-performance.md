---
title: Azure Data Factory의 데이터 흐름 성능 매핑 및 조정 가이드 | Microsoft Docs
description: 매핑 데이터 흐름을 사용 하는 경우 Azure Data Factory의 데이터 흐름의 성능에 영향을 주는 주요 요소에 알아봅니다.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172342"
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

## <a name="optimizing-for-azure-sql-database"></a>Azure SQL Database에 대 한 최적화

![원본 파트](media/data-flow/sourcepart2.png "일부 원본")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>Spark 데이터 원본 데이터베이스 분할에 분할 키를 기반으로 데이터베이스 테이블 열 소스 변환에서 일치 시킬 수 있습니다.

* "최적화"로 이동한 다음 "Source"를 선택 합니다. 쿼리에서 특정 테이블 열 또는 유형 중 하나를 설정 합니다.
* "열"을 선택한 경우 다음 파티션 열을 선택 합니다.
* 또한 Azure SQL DB에 연결의 최대 수 설정. 더 높은 설정 데이터베이스에 대 한 병렬 연결을 얻으려고 시도할 수 있습니다. 그러나 일부 경우 제한 된 수의 연결을 사용 하 여 성능이 향상 될 수 있습니다.

### <a name="set-batch-size-and-query-on-source"></a>원본에서 일괄 처리 크기 및 쿼리 설정

![Source](media/data-flow/source4.png "Source")

* 행 단위로 대신 메모리에 있는 집합의 데이터를 저장 하는 ADF 지시 일괄 처리 크기를 설정 합니다. 선택적 설정 이며 부족 해질 수 있습니다 리소스에서 계산 노드의 경우 제대로 조정 되지 않습니다.
* 쿼리 설정 도착 하기 전에 데이터 흐름에 대 한 더 빠른 초기 데이터 취득을 만들 수 있는 처리에 대 한 원본에서 행 오른쪽을 필터링 할 수 있습니다.
* 즉, READ UNCOMMITTED Azure SQL DB에 대 한 선택적 쿼리 힌트는 쿼리를 사용 하는 경우 추가할 수 있습니다.

### <a name="set-sink-batch-size"></a>싱크 일괄 처리 크기를 설정 합니다.

![싱크](media/data-flow/sink4.png "싱크")

* 사용자 데이터 floes 행 단위로 처리를 방지 하기 위해 Azure SQL DB에 대 한 싱크 설정에서 "일괄 처리 크기"를 설정 합니다. 이 ADF 데이터베이스 처리를 제공 하는 크기에 따라 일괄 처리의 기록 알려줍니다.

### <a name="set-partitioning-options-on-your-sink"></a>분할에 싱크에서 옵션 설정

* 대상 Azure SQL DB 테이블의 분할 된 데이터에 없는 경우에 최적화 탭으로 이동 하 고 분할을 설정 합니다.
* 자주, 라운드 로빈 단일 노드/파티션에서 모든 연결을 강제 적용 하는 대신 로드 하는 훨씬 더 빠르게 데이터를 Spark 실행 클러스터에서 분할을 사용 하는 ADF 지시 하기만 하면 됩니다.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Azure 통합 런타임의 계산 엔진의 크기를 늘리십시오.

![새 IR](media/data-flow/ir-new.png "새 IR")

* 노드 수가 증가 하 고 Azure SQL DB에 쓰고 쿼리 처리 성능이 더 제공 코어의 수를 늘립니다.
* 더 많은 리소스를 계산 노드에 적용할 "계산 최적화" 및 "메모리 액세스에 최적화 된" 옵션을 시도 합니다.

### <a name="disable-indexes-on-write"></a>쓰기에 대해 인덱스를 사용 하지 않도록 설정
* 프로그램 싱크에서 기록 됩니다.는 대상 테이블의 인덱스를 사용 하지 않도록 설정 하는 데이터 흐름 작업 전에 ADF 파이프라인 저장 프로시저 작업을 사용 합니다.
* 데이터 흐름 작업을 한 후 해당 인덱스를 사용 하도록 설정 하는 다른 저장된 프로시저 활동을 추가 합니다.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Azure SQL DB의 크기 늘리기
* 원본 크기 조정 예약 하 고 처리량을 증가 시키려면 DTU에 도달 하면 Azure 제한을 최소화 하 여 파이프라인을 제한 하 여 실행 하기 전에 Azure SQL DB를 싱크 키를 누릅니다.
* 파이프라인 실행 완료 되 면 데이터베이스는 일반 실행된 속도를 다시 조정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다른 데이터 흐름 문서를 참조 하세요.

- [데이터 흐름 개요](concepts-data-flow-overview.md)
- [데이터 흐름 작업](control-flow-execute-data-flow-activity.md)

