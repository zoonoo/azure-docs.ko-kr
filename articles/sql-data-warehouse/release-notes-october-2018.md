---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 10월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 81096eeb1edcd6681bdc887d267ee477d78a8578
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472085"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 10월
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 10월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다.

## <a name="devops-for-data-warehousing"></a>데이터 웨어하우징을 위한 DevOps
많은 분들이 SQL DW(SQL Data Warehouse)에 요청하신 기능이 Visual Studio의 SSDT(SQL Server Data Tools)를 통해 미리 보기로 제공됩니다! 이제 개발자 팀에서는 버전이 제어되는 단일 코드 베이스를 통해 공동으로 작업하고 전 세계의 모든 인스턴스에 변경 내용을 신속하게 배포할 수 있습니다. 관심이 있으세요? 이 기능은 현재 미리 보기로 제공되고 있습니다! [SQL Data Warehouse Visual Studio SSDT(SQL Server Data Tools) - 미리 보기 등록 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)을 방문하여 등록할 수 있습니다. 수요가 많기 때문에 고객에게 최상의 환경을 유지하기 위해 미리 보기 사용 인원을 관리하고 있습니다. 미리 보기에 등록하시면 저희 쪽에서 7영업일 내에 상태를 확인할 것입니다.

## <a name="row-level-security-generally-available"></a>행 수준 보안 일반 공급
SQL DW(Azure SQL Data Warehouse)는 이제 RLS(행 수준 보안)를 지원하므로 중요한 데이터를 보호하는 강력한 기능이 추가됩니다. RLS가 도입된 덕분에, 행에 액세스할 수 있는 사람을 제어하는 방법과 마찬가지로 테이블의 행에 대한 액세스를 제어하는 보안 정책을 구현할 수 있습니다. RLS를 사용하면 데이터 웨어하우스를 다시 디자인할 필요 없이 세밀한 액세스 제어가 가능합니다. RLS는 액세스 제한 논리가 다른 애플리케이션의 데이터가 아닌 데이터베이스 계층 자체에 있으므로 보안 모델이 전체적으로 간소화됩니다. 또한 RLS는 액세스 제어 관리용 행을 필터링하는 보기를 도입할 필요가 없습니다. 모든 고객은 이 엔터프라이즈급 보안 기능을 추가 비용 없이 사용할 수 있습니다.

## <a name="advanced-advisors"></a>고급 Advisor
데이터 웨어하우스 권장 사항 및 메트릭이 추가되어 Azure SQL DW(SQL Data Warehouse)의 고급 튜닝이 간편해졌습니다. Azure Advisor를 통해 고객이 원하는 대로 사용할 수 있는 다음과 같은 고급 성능 권장 사항이 추가로 제공됩니다.
1.  적응형 캐시 - 캐시 사용률을 최적화하기 위해 확장할 때를 알려줍니다.
2.  테이블 배포 - 데이터 이동을 줄이고 워크로드 성능을 향상하기 위해 테이블을 복제할 때를 결정합니다. 
3.  Tempdb - tempdb 경합을 줄이기 위해 리소스 클래스를 확장하고 구성할 때를 파악합니다.

개요 블레이드의 거의 실시간 메트릭을 위한 향상된 사용 지정 가능한 모니터링 차트 등 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)와 데이터 웨어하우스 메트릭이 더욱 긴밀하게 통합되었습니다. 사용량을 모니터링하거나 데이터 웨어하우스 권장 사항의 유효성을 검사하고 이 권장 사항을 적용할 때 Azure Monitor 메트릭에 액세스하기 위해 더 이상 데이터 웨어하우스 개요 블레이드에서 나갈 필요가 없습니다. 또한 tempdb, 적응형 캐시 사용률 등 성능 권장 사항을 보완하는 데 사용할 수 있는 새로운 메트릭이 있습니다.

## <a name="advanced-tuning-with-integrated-advisors"></a>Advisor가 통합된 고급 튜닝
데이터 웨어하우스 권장 사항 및 메트릭을 추가하고 Azure Advisor와 Azure Monitor가 통합된 환경을 제공하는 포털 개요 블레이드를 다시 디자인하여 Azure SQL DW(SQL Data Warehouse)의 고급 튜닝이 간편해졌습니다.

## <a name="accelerated-database-recovery-adr"></a>ADR(가속 데이터베이스 복구)
Azure SQL Data Warehouse ADR(가속 데이터베이스 복구)이 공개 미리 보기로 제공됩니다. ADR은 현재 복구 프로세스를 처음부터 다시 설계하여 데이터베이스, 특히 장기 실행 트랜잭션이 있는 데이터베이스의 가용성을 크게 향상하는 새로운 SQL Server 엔진입니다. ADR의 가장 큰 장점은 빠르고 일관적인 데이터베이스 복구와 즉각적인 트랜잭션 롤백입니다.

## <a name="azure-monitor-diagnostics-logs"></a>Azure Monitor 진단 로그
이제 SQL DW(SQL Data Warehouse)는 Azure Monitor 진단 로그와 직접 통합하여 분석 워크로드에 대한 향상된 인사이트를 얻을 수 있습니다. 이 새 기능을 통해 개발자는 오랫동안 워크로드 동작을 분석하고 쿼리 최적화 또는 용량 관리에 대한 합리적 결정을 내릴 수 있습니다. [Azure Monitor 진단 로그](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)를 통해 데이터 웨어하우스 워크로드에 대한 추가 정보를 제공하는 외부 로깅 프로세스를 도입했습니다. 이제 단추를 한 번만 클릭하면 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries)를 사용하여 기록 쿼리 성능 문제 해결 기능에 대한 진단 로그를 구성할 수 있습니다. Azure Monitor 진단 로그는 감사용으로 스토리지 계정에 로그를 저장하는 방식을 통한 사용자 지정 가능 보존 기간, 실시간에 가까운 원격 분석 인사이트 확인을 위해 로그를 이벤트 허브로 스트리밍하는 기능, 그리고 Log Analytics에서 로그 쿼리를 사용하여 로그를 분석하는 기능을 지원합니다. 진단 로그는 SQL Data Warehouse의 가장 일반적으로 사용되는 성능 문제 해결 DMV에 상응하는 데이터 웨어하우스의 원격 분석 보기로 구성됩니다. 이 초기 릴리스의 경우 다음 시스템 동적 관리 뷰에 대한 보기가 설정되었습니다.

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Columnstore 메모리 관리
압축된 열 저장소 행 그룹의 수가 증가하면 이러한 행 그룹에 대한 내부 열 세그먼트 메타데이터를 관리하는 데 필요한 메모리도 증가합니다.  결과적으로 일부 Columnstore DMV(동적 관리 뷰)에 대해 실행되는 쿼리 및 쿼리 성능이 저하될 수 있습니다.  이번 릴리스에서는 이러한 경우에 대한 내부 메타데이터 크기가 최적화되어 이러한 쿼리의 환경 및 성능이 개선되도록 수정되었습니다. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Azure Data Lake Storage Gen2 통합(GA)
이제 Azure SQL DW(SQL Data Warehouse)가 Azure Data Lake Storage Gen2와 기본적으로 통합됩니다. 고객은 ABFS에서 외부 테이블을 사용하여 SQL DW로 데이터를 로드할 수 있습니다. 이 기능을 통해 고객은 데이터 레이크를 Data Lake Storage Gen2에 통합할 수 있습니다. 

## <a name="bug-fixes"></a>버그 수정

| 제목 | 설명 |
|:---|:---|
| **DW2000 등의 데이터 웨어하우스에 있는 소규모 리소스 클래스에서 Parquet에 대한 CETAS 오류** | 이 픽스는 Create External Table As to Parquet 코드 경로에서 null 참조를 올바르게 식별합니다. |
|**일부 CTAS 작업에서 ID 열 값이 손실될 수 있음** | 다른 테이블로 CTAS 할 때 ID 열 값이 유지되지 않을 수 있습니다. [https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/) 블로그에서 보고되었습니다. |
| **쿼리가 실행 중인 동안 세션이 종료되면 경우에 따라 내부 오류 발생** | 쿼리가 실행 중일 때 세션이 종료되면 이 픽스는 InvalidOperationException을 트리거합니다. |
| **(2018년 11월에 배포됨) Polybase를 사용하여 ADLS(Gen1)에서 여러 작은 파일을 로드하려고 하는 경우 고객에게 최적이 아닌 성능 문제가 발생했습니다.** | 시스템 성능은 AAD 보안 토큰 유효성 검사 동안 병목 상태가 되었습니다. 보안 토큰의 캐싱을 사용하여 성능 문제를 완화했습니다. |


## <a name="next-steps"></a>다음 단계
SQL Data Warehouse에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 발견하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [Stack Overflow 포럼]
* [Twitter]


[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[고객 성공 사례]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 포럼]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
