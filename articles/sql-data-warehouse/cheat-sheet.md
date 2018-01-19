---
title: "Azure SQL Data Warehouse 치트 시트 | Microsoft Docs"
description: "Azure SQL Data Warehouse 솔루션을 신속하게 빌드하는 모범 사례 링크를 찾습니다."
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: 2d17385ff255ddf7b85baa81600a2af60d015540
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대한 치트 시트
이 페이지는 데이터 웨어하우스 솔루션에 대한 주요 사용 사례를 디자인하는 데 유용합니다. 이 치트 시트는 세계 수준의 데이터 웨어하우스를 빌드하는 과정에 매우 유용합니다. 하지만 세부 사항의 각 단계를 더 자세히 알아보는 것도 매우 중요합니다. 우선 훌륭한 문서를 통해 무엇이 SQL Data Warehouse**[인지 아닌지]**에 대해 알아보는 것이 좋습니다.

다음은 SQL Data Warehouse 디자인을 시작할 때 수행해야 하는 과정의 스케치입니다.

![스케치]

## <a name="queries-and-operations-across-tables"></a>테이블에서 쿼리 및 작업

가장 중요한 것은 데이터 웨어하우스에서 발생하는 가장 중요한 작업 및 쿼리를 미리 아는 것입니다. 데이터 웨어하우스 아키텍처는 이러한 작업에 대한 우선 순위가 지정되어야 합니다. 작업의 일반적인 예는 다음과 같습니다.
* 하나 또는 두 개의 팩트 테이블을 차원 테이블과 조인, 일정 기간 동안 이 테이블을 필터링 및 데이터 마트에 결과를 추가
* 팩트 영업에 크고 작은 업데이트 수행
* 데이터만 테이블에 추가하기

작업의 형식을 알고 있으면 테이블의 디자인을 최적화하는 데 유용합니다.

## <a name="data-migration"></a>데이터 마이그레이션

데이터를 먼저 **[ADLS]** 또는 Azure Blob Storage에 로드하는 것이 좋습니다. 그런 다음 Polybase를 사용하여 준비 테이블의 SQL Data Warehouse로 데이터를 로드해야 합니다. 다음 구성이 권장됩니다.

| 디자인 | 권장 사항 |
|:--- |:--- |
| 배포 | 라운드 로빈 |
| 인덱싱 | 힙 |
| 분할 | 없음 |
| 리소스 클래스 | largerc 또는 xlargerc |

**로딩에 대한 [더 자세한 지침]**과 함께 **[데이터 마이그레이션], [데이터 로드]**에 대해 자세히 알아보세요. 

## <a name="distributed-or-replicated-tables"></a>분산 또는 복제된 테이블

테이블 속성에 따라 다음 전략이 권장됩니다.

| type | 적합한 대상 | 유의해야 하는 경우|
|:--- |:--- |:--- |
| 복제됨 | • 압축 후 2GB 저장소 이하 별모양 스키마의 소형 차원 테이블(~5배 압축) |• 대부분 트랜잭션을 테이블에 기록(예: insert, upsert, delete, update)<br></br>• DWU(데이터 웨어하우스 단위) 프로비전이 자주 변경<br></br>• 2-3개 열만 사용하나 테이블에 많은 열이 있음<br></br>• 복제된 테이블을 인덱싱 |
| 라운드 로빈(기본값) | • 임시/준비 테이블<br></br> • 명백한 조인 키 또는 양호한 후보 열이 없음 |• 데이터 이동으로 인해 느려진 성능 |
| 해시 | • 팩트 테이블<br></br>• 대형 차원 테이블 |• 배포 키를 업데이트할 수 없음 |

**팁:**
* 라운드 로빈으로 시작하되 대규모 병렬 아키텍처를 활용하는 해시 배포 전략을 목표로 함
* 일반적인 해시 키에 동일한 데이터 형식이 있는지 확인
* varchar 형식에 배포 금지
* 조인 작업이 빈번한 팩트 테이블에 대한 공동 해시 키가 있는 차원 테이블이 해시 분산될 수 있음
* *[sys.dm_pdw_nodes_db_partition_stats]*를 사용하여 데이터의 모든 왜곡도를 분석
* *[sys.dm_pdw_request_steps]*를 사용하여 쿼리 뒤 데이터 이동 분석, 시간 브로드캐스트 모니터링 및 작업 순서 섞기를 수행합니다. 배포 전략을 검토하는 데 유용합니다.

**[복제된 테이블] 및 [배포된 테이블]**에 대해 자세히 알아보세요.

## <a name="indexing-your-table"></a>테이블 인덱싱

인덱싱은 테이블을 빠르게 읽기에 **좋습니다**. 필요에 따라 사용할 수 있는 고유한 기술 집합이 있습니다.

| type | 적합한 대상 | 유의해야 하는 경우|
|:--- |:--- |:--- |
| 힙 | • 준비/임시 테이블<br></br>• 작은 조회를 통한 소형 테이블 |• 전체 테이블을 검색하는 모두 조회 |
| 클러스터형 인덱스 | • 최대 1억 개 행 테이블<br></br>• 1-2개 열만 많이 사용하는 대형 테이블(1억 개 이상의 행) |• 복제 테이블에서 사용됨<br></br>• 여러 Join, Group By 작업을 포함하는 복잡한 쿼리<br></br>• 인덱싱된 열에서 업데이트 수행, 메모리 소요 |
| CCI(클러스터형 Columnstore 인덱스) (기본값) | • 대형 테이블(1억 개 이상의 행) | • 복제 테이블에서 사용됨<br></br>• 테이블에서 대량 업데이트 작업 수행<br></br>• 과도한 테이블 분할: 행 그룹은 다른 배포 노드 및 파티션으로 확장되지 않음 |

**팁:**
* 클러스터형 인덱스뿐 아니라 필터에 많이 사용되는 열에 비클러스터형 인덱스를 추가하고자 할 수도 있습니다. 
* CCI로 테이블에서 메모리를 관리하는 방법에 주의해야 합니다. 데이터를 로드할 때 사용자(또는 쿼리)가 큰 리소스 클래스의 이점을 사용하고자 합니다. 작은 압축 행 그룹을 많이 트리밍 및 생성하지 않도록 해야 합니다.
* CCI를 통한 계산 계층 견고성을 위한 최적화
* CCI의 경우 행 그룹의 잘못된 압축으로 인해 성능이 저하될 수 있어 CCI를 다시 빌드하거나 다시 구성하고자 할 수도 있습니다. 압축된 행 그룹당 적어도 10만 개 행이 있길 원합니다. 이상적인 것은 행 그룹에 100만 개 행입니다.
* 증분 로드 빈도 및 크기에 따라 인덱스를 다시 구성하거나 다시 빌드할 때 자동화하고자 합니다. 봄맞이 대청소는 항상 유용합니다.
* 행 그룹을 자르려고 할 때는 전략적으로 생각하세요. 열려 있는 행 그룹의 크기는? 앞으로 로드할 데이터의 양은?

**[인덱스]**에 대해 자세히 알아보세요.

## <a name="partitioning"></a>분할
큰 팩트 테이블이 있는 경우 사용자 테이블을 분할할 수도 있습니다(>1B 행 테이블). 99%의 경우 분할 키는 날짜를 기반으로 합니다. 과도한 분할은 특히 클러스터형 Columnstore 인덱스가 있는 경우 피해야 합니다.
ETL이 필요한 준비 테이블을 사용하면 분할의 이점을 활용할 수 있습니다. 데이터 수명 주기 관리가 용이해집니다.
특히 클러스터형 Columnstore 인덱스에서 데이터의 과도한 분할에 주의해야 합니다.

**[분할]**에 대해 자세히 알아보세요.

## <a name="incremental-load"></a>증분 로드

첫째, 데이터 로딩에 더 큰 리소스 클래스를 할당하는지 확인해야 합니다. ETL 파이프라인을 SQL DW로 자동화하는 데 Polybase 및 ADF V2를 사용하는 것이 좋습니다.

기록 데이터에서 큰 일괄 처리 업데이트의 경우 먼저 우려되는 데이터를 삭제하는 것이 좋습니다. 그런 다음 새 데이터의 대량 삽입을 수행할 수 있습니다. 이 2단계 접근 방법이 더 효율적입니다.

## <a name="maintain-statistics"></a>통계 유지 관리
자동 통계는 곧 일반 공급될 예정입니다. 그때까지 SQL Data Warehouse에서는 통계의 수동 유지 관리가 필요합니다. 데이터에 **중요한** 변경 내용이 있는 것 만큼 통계를 업데이트하는 것도 중요합니다. 이는 쿼리 계획을 최적화하는 데 도움이 됩니다. 모든 통계를 유지 관리하는 데 시간이 너무 오래 소요되는 경우 어떤 열에 통계가 있는지 더 선별적으로 시도해볼 수 있습니다. 또한 업데이트의 빈도를 정의할 수 있습니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트하려고 합니다. 조인에 포함된 열, WHERE 절에 사용된 열 및 GROUP BY에 있는 열에서 통계를 유지하면 가장 많은 이득을 획득할 수 있습니다.

**[통계]**에 대해 자세히 알아보세요.

## <a name="resource-class"></a>리소스 클래스
SQL Data Warehouse는 메모리를 쿼리에 할당하는 방법으로 리소스 그룹을 사용합니다. 쿼리 또는 로드 속도를 개선하기 위해 더 많은 메모리가 필요한 경우 더 높은 리소스 클래스를 할당해야 합니다. 다른 한편으로, 더 큰 리소스 클래스를 사용하면 동시성에 영향을 줍니다. 큰 리소스 클래스로 모든 사용자를 이동하기 전에 이를 고려하고자 합니다.

쿼리 시간이 오래 걸리는 경우 사용자가 큰 리소스 클래스에서 실행하지 않는지 검사합니다. 큰 리소스 클래스는 많은 동시성 슬롯을 사용합니다. 이는 다른 쿼리를 큐에 대기시킬 수 있습니다.

마지막으로 계산에 최적화된 계층을 사용하는 경우 각 리소스 클래스는 탄력적 최적화 계층에서 2.5배 더 많은 메모리를 가져옵니다.

**[리소스 클래스 및 동시성]**으로 작업하는 방법에 대해 자세히 알아보세요.

## <a name="lower-your-cost"></a>비용 절감
SQL Data Warehouse의 핵심 기능은 사용 중이 아닐 때 일시 중지하는 기능으로 계산 리소스의 비용이 발생되지 않도록 하는 것입니다. 또 다른 주요 기능은 리소스 규모를 조정하는 기능입니다. 일시 중지 및 크기 조정은 Azure 포털 또는 PowerShell 명령을 통해 수행할 수 있습니다.

이제 Azure Functions로 원하는 시간에 자동 크기 조정:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>성능에 대한 아키텍처를 최적화

허브 및 스포크 아키텍처에서 SQL 데이터베이스 및 Azure Analysis Services를 고려하는 것이 좋습니다. 해당 솔루션은 SLQ DB 및 Azure Analysis Services에서 일부 고급 보안 기능을 활용하면서 동시에 다른 사용자 그룹 간 워크로드 격리를 제공할 수 있습니다. 또한 사용자에게 무제한 동시성을 제공하는 방법이기도 합니다.

**[SQL DW를 활용하는 일반적인 아키텍처]**에 대해 자세히 알아보세요.

한 번의 클릭으로 SQL DW에서 SQL DB 데이터베이스의 사용자 스포크 배포:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[스케치]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[데이터 로드]:./design-elt-data-loading.md
[더 자세한 지침]: ./guidance-for-loading-data.md
[인덱스]:./sql-data-warehouse-tables-index.md
[분할]:./sql-data-warehouse-tables-partition.md
[통계]:./sql-data-warehouse-tables-statistics.md
[리소스 클래스 및 동시성]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[SQL DW를 활용하는 일반적인 아키텍처]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[인지 아닌지]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[데이터 마이그레이션]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[복제된 테이블]:https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[배포된 테이블]:https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[ADLS]: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
