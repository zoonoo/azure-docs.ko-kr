---
title: 서버를 사용 하지 않는 SQL 풀에 대 한 비용 관리
description: 이 문서에서는 서버 리스 SQL 풀의 비용을 관리 하는 방법 및 Azure storage에서 데이터를 쿼리할 때 처리 되는 데이터를 계산 하는 방법을 설명 합니다.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491572"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀에 대 한 Cost management

이 문서에서는 Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀의 비용을 예측 하 고 관리 하는 방법을 설명 합니다.
- 쿼리를 실행 하기 전에 처리 된 데이터의 예상 작업량
- 비용 제어 기능을 사용 하 여 예산 설정

Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀의 비용은 Azure 청구서의 월간 비용 중 일부일 뿐입니다. 다른 Azure 서비스를 사용 하는 경우 타사 서비스를 포함 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대 한 요금이 청구 됩니다. 이 문서에서는 Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀의 비용을 계획 하 고 관리 하는 방법을 설명 합니다.

## <a name="data-processed"></a>처리된 데이터

*처리 된 데이터* 는 쿼리가 실행 되는 동안 시스템이 일시적으로 저장 하는 데이터의 양입니다. 처리 된 데이터는 다음 수량으로 구성 됩니다.

- 저장소에서 읽은 데이터의 양입니다. 이 크기는 다음과 같습니다.
  - 데이터를 읽는 동안 데이터를 읽었습니다.
  - 메타 데이터를 읽는 동안 읽은 데이터입니다 (예: Parquet와 같은 메타 데이터를 포함 하는 파일 형식의 경우).
- 중간 결과의 데이터 양입니다. 이 데이터는 쿼리가 실행 되는 동안 노드 간에 전송 됩니다. 여기에는 압축 되지 않은 형식으로 끝점에 대 한 데이터 전송이 포함 됩니다. 
- 저장소에 기록 된 데이터의 양입니다. CETAS를 사용 하 여 결과 집합을 저장소로 내보내는 경우 작성 된 데이터의 양은 CETAS의 SELECT 부분에 대해 처리 되는 데이터의 양에 추가 됩니다.

저장소에서 파일을 읽는 것은 매우 최적화 되어 있습니다. 이 프로세스는 다음을 사용 합니다.

- 프리페치-읽은 데이터 양에 약간의 오버 헤드를 추가할 수 있습니다. 쿼리에서 전체 파일을 읽는 경우 오버 헤드가 발생 하지 않습니다. 상위 N 개 쿼리와 같이 파일을 부분적으로 읽을 경우 프리페치를 사용 하 여 좀 더 많은 데이터를 읽습니다.
- 최적화 된 CSV (쉼표로 구분 된 값) 파서입니다. PARSER_VERSION = ' 2.0 '을 사용 하 여 CSV 파일을 읽으면 저장소에서 읽은 데이터 양이 약간 늘어납니다. 최적화 된 CSV 파서는 동일한 크기의 청크로 파일을 병렬로 읽습니다. 청크는 전체 행을 포함할 필요가 없습니다. 모든 행을 구문 분석 하기 위해 최적화 된 CSV 파서는 인접 청크의 작은 조각도 읽습니다. 이 프로세스는 적은 양의 오버 헤드를 추가 합니다.

## <a name="statistics"></a>통계

서버를 사용 하지 않는 SQL 풀 쿼리 최적화 프로그램은 통계를 기반으로 최적의 쿼리 실행 계획을 생성 합니다. 통계를 수동으로 만들 수 있습니다. 그렇지 않으면 서버를 사용 하지 않는 SQL 풀에서 자동으로 만듭니다. 어느 쪽이 든, 제공 된 샘플 주기에 특정 열을 반환 하는 별도의 쿼리를 실행 하 여 통계가 생성 됩니다. 이 쿼리에는 처리 된 데이터 양이 있습니다.

생성 된 통계를 활용 하는 동일한 쿼리 또는 다른 쿼리를 실행 하는 경우 가능한 경우 통계가 다시 사용 됩니다. 통계 생성을 위해 처리 된 추가 데이터가 없습니다.

Parquet 열에 대 한 통계를 만들 때 파일에서 관련 열만 읽습니다. CSV 열에 대해 통계를 만들면 전체 파일을 읽고 구문 분석 합니다.

## <a name="rounding"></a>반올림

처리 되는 데이터의 양은 쿼리당 가장 가까운 MB로 반올림 됩니다. 각 쿼리에는 최소한 10mb의 처리 된 데이터가 있습니다.

## <a name="what-data-processed-doesnt-include"></a>처리 된 데이터에 포함 되지 않은 내용

- 서버 수준 메타 데이터 (예: 로그인, 역할 및 서버 수준 자격 증명).
- 끝점에서 만든 데이터베이스입니다. 이러한 데이터베이스에는 메타 데이터 (예: 사용자, 역할, 스키마, 뷰, 인라인 테이블 반환 함수 [Tvf], 저장 프로시저, 데이터베이스 범위 자격 증명, 외부 데이터 원본, 외부 파일 형식 및 외부 테이블)만 포함 됩니다.
  - 스키마 유추를 사용 하는 경우 파일 조각을 읽어 열 이름과 데이터 형식을 유추 하 고 읽은 데이터 양이 처리 되는 데이터의 양에 추가 됩니다.
- 지정 된 샘플 비율에 따라 저장소에서 데이터를 처리 하기 때문에 CREATE STATISTICS 문을 제외 하 고 DDL (데이터 정의 언어) 문
- 메타 데이터 전용 쿼리입니다.

## <a name="reducing-the-amount-of-data-processed"></a>처리 되는 데이터의 양 줄이기

처리 된 데이터의 쿼리당 크기를 최적화 하 고 데이터를 Parquet 같은 압축 된 열 기반 형식으로 분할 및 변환 하 여 성능을 향상 시킬 수 있습니다.

## <a name="examples"></a>예

3 개의 테이블을 가정 합니다.

- Population_csv 테이블은 5 TB CSV 파일에 의해 지원 됩니다. 파일은 크기가 같은 5 개의 열로 구성 됩니다.
- Population_parquet 테이블은 population_csv 테이블과 동일한 데이터를 포함 합니다. 1TB의 Parquet 파일에 의해 지원 됩니다. 데이터가 Parquet 형식으로 압축 되기 때문에이 테이블은 이전 테이블 보다 작습니다.
- Very_small_csv 테이블은 100 KB의 CSV 파일에 의해 지원 됩니다.

**쿼리 1** : POPULATION_CSV에서 합계 (채우기)를 선택 합니다.

이 쿼리는 전체 파일을 읽고 구문 분석 하 여 모집단 열의 값을 가져옵니다. 노드는이 테이블의 조각을 처리 하 고 각 조각의 모집단 합계는 노드 간에 전송 됩니다. 최종 합계가 끝점으로 전송 됩니다. 

이 쿼리는 5TB의 데이터를 처리 하 고 조각 합계를 전송 하기 위한 약간의 오버 헤드를 처리 합니다.

**쿼리 2** : POPULATION_PARQUET에서 합계 (채우기) 선택

Parquet와 같은 압축 및 열 기반 형식을 쿼리하면 쿼리 1 보다 더 작은 데이터를 읽을 수 있습니다. 서버를 사용 하지 않는 SQL 풀에서 전체 파일 대신 압축 된 단일 열을 읽기 때문에이 결과가 표시 됩니다. 이 경우 0.2 TB를 읽습니다. 동일한 크기의 열 5 개는 각각 0.2입니다. 노드는이 테이블의 조각을 처리 하 고 각 조각의 모집단 합계는 노드 간에 전송 됩니다. 최종 합계가 끝점으로 전송 됩니다. 

이 쿼리는 0.2 TB를 처리 하 고 조각 합계를 전송 하기 위한 약간의 오버 헤드를 처리 합니다.

**쿼리 3** : SELECT * FROM population_parquet

이 쿼리는 모든 열을 읽고 압축 되지 않은 형식으로 모든 데이터를 전송 합니다. 압축 형식이 5:1 인 경우 쿼리는 1TB를 읽고 압축 되지 않은 데이터의 5tb를 전송 하기 때문에 6TB를 처리 합니다.

**쿼리 4** : VERY_SMALL_CSV에서 COUNT (*)를 선택 합니다.

이 쿼리는 전체 파일을 읽습니다. 이 테이블에 대 한 저장소에 있는 파일의 총 크기는 100 KB입니다. 노드는이 테이블의 조각을 처리 하 고 각 조각의 합계는 노드 간에 전송 됩니다. 최종 합계가 끝점으로 전송 됩니다. 

이 쿼리는 100 KB 보다 약간 더 많은 데이터를 처리 합니다. 이 쿼리에 대해 처리 되는 데이터의 양은이 문서의 [반올림](#rounding) 섹션에 지정 된 대로 최대 10mb로 반올림 됩니다.

## <a name="cost-control"></a>비용 제어

서버를 사용 하지 않는 SQL 풀의 비용 제어 기능을 사용 하면 처리 되는 데이터의 양에 대 한 예산을 설정할 수 있습니다. 하루, 주 및 월에 처리 되는 데이터의 예산 (TB)을 설정할 수 있습니다. 동시에 하나 이상의 예산을 설정할 수 있습니다. 서버를 사용 하지 않는 SQL 풀에 대해 비용 제어를 구성 하려면 Synapse Studio 또는 T-sql을 사용 하면 됩니다.

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>Synapse Studio에서 서버를 사용 하지 않는 SQL 풀에 대 한 비용 제어 구성
 
Synapse Studio에서 서버를 사용 하지 않는 SQL 풀에 대 한 비용 제어를 구성 하려면 왼쪽의 메뉴에서 관리 항목으로 이동 하 고 분석 풀에서 SQL 풀 항목을 선택 합니다. 서버를 사용 하지 않는 SQL 풀을 가리키면 비용 제어 아이콘을 볼 수 있습니다 .이 아이콘을 클릭 하면 됩니다.

![비용 제어 탐색](./media/data-processed/cost-control-menu.png)

비용 제어 아이콘을 클릭 하면 다음과 같은 사이드 막대가 표시 됩니다.

![비용 제어 구성](./media/data-processed/cost-control-sidebar.png)

하나 이상의 예산을 설정 하려면 먼저 텍스트 상자에 정수 값을 입력 하는 것 보다 설정 하려는 예산에 대해 라디오 단추 사용을 클릭 합니다. 값의 단위는 TBs입니다. 예산을 구성한 후에는 양쪽 표시줄의 아래쪽에 있는 적용 단추를 클릭 합니다. 이제 예산이 설정 되었습니다.

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>T-sql에서 서버를 사용 하지 않는 SQL 풀에 대 한 비용 제어 구성

T-sql에서 서버를 사용 하지 않는 SQL 풀에 대 한 비용 제어를 구성 하려면 다음 저장 프로시저 중 하나 이상을 실행 해야 합니다.

```sql
sp_set_data_processed_limit
    @type = N'daily',
    @limit_tb = 1

sp_set_data_processed_limit
    @type= N'weekly',
    @limit_tb = 2

sp_set_data_processed_limit
    @type= N'monthly',
    @limit_tb = 3334
```

현재 구성을 보려면 다음 T-sql 문을 실행 합니다.

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

현재 날짜, 주 또는 월에 처리 된 데이터의 양을 확인 하려면 다음 T-sql 문을 실행 합니다.

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>다음 단계

성능을 위해 쿼리를 최적화 하는 방법을 알아보려면 서버를 사용 하지 않는 [SQL 풀에 대 한 모범 사례](best-practices-sql-on-demand.md)를 참조 하세요.
