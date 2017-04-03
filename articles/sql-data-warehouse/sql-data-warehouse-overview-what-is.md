---
title: "Azure SQL 데이터 웨어하우스란? | Microsoft Docs"
description: "페타바이트 볼륨의 관계형 및 비관계형 데이터를 처리할 수 있는 엔터프라이즈급 분산 데이터베이스입니다. 몇 초 만에 확장, 축소 및 일시 중지가 가능한 업계 최초의 클라우드 데이터 웨어하우스입니다."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;mausher;kevin;barbkess;elbutter
translationtype: Human Translation
ms.sourcegitcommit: bf73ad830226626ddf41cc4ae80e714abf8bcfc2
ms.openlocfilehash: 19e87c61493bd4620120b39a533e9e4b64517538
ms.lasthandoff: 03/01/2017


---
# <a name="what-is-azure-sql-data-warehouse"></a>Azure SQL 데이터 웨어하우스란?
Azure SQL Data Warehouse는 거대한 양의 데이터를 처리할 수 있는 MPP(Massively Parallel Processing) 클라우드 기반 규모 확장 관계형 데이터베이스입니다. 

SQL 데이터 웨어하우스:

* SQL Server 관계형 데이터베이스를 Azure 클라우드 규모 확장 기능을 결합합니다. 
* 계산에서 저장소를 분리합니다.
* 계산을 증가, 감소, 일시 중지 또는 다시 시작할 수 있습니다. 
* Azure Platform 전반에 걸쳐 통합합니다.
* SQL Server T-SQL(Transact-SQL) 및 도구를 활용합니다.
* SOC 및 ISO와 같은 다양한 법적 및 비즈니스 보안 요구 사항을 준수합니다.

이 문서는 SQL 데이터 웨어하우스의 주요 기능을 설명합니다.

## <a name="massively-parallel-processing-architecture"></a>대규모 병렬 처리 아키텍처
SQL 데이터 웨어하우스는 방대한 병렬 처리(MPP) 분산 데이터베이스 시스템입니다. 숨은 기능으로 SQL 데이터 웨어하우스는 데이터를 여러 비공유 저장소 및 처리 단위에 확산합니다. 데이터는 맨 위 계층에 동적으로 연결된 계산 노드가 쿼리를 실행하는 프리미엄 로컬 중복 저장소 계층에 저장됩니다. SQL Data Warehouse는 로드 및 복잡한 쿼리를 실행하는 데 "분할 정복법(divide and conquer approach)"을 사용합니다. 요청은 제어 노드에서 수신하고 배포에 최적화되며 계산 노드에 전달되어 병렬로 해당 작업을 수행합니다.

분리된 저장소 및 계산을 사용하여 SQL Data Warehouse는 다음을 수행할 수 있습니다.

* 계산과 독립적으로 저장소를 확장 또는 축소합니다.
* 데이터를 이동하지 않고 계산을 확장 또는 축소합니다.
* 데이터를 그대로 유지하고 저장소에 대한 비용만 부담하면서 계산 용량을 일시 중지합니다.
* 운영 시간 동안 계산 용량을 다시 시작합니다.

다음 다이어그램에서는 아키텍처를 자세히 보여 줍니다.

![SQL 데이터 웨어하우스 아키텍처][1]

**제어 노드:** 제어 노드는 쿼리를 관리하고 최적화합니다. 모든 응용 프로그램 및 연결과 상호 작용하는 프런트 엔드입니다. SQL 데이터 웨어하우스의 제어 노드는 SQL 데이터베이스에서 제공되며 여기에 연결하는 것은 느낌과 모양이 같습니다. 내부적으로는 제어 노드가 분산된 데이터에서 병렬 쿼리를 실행하는 데 필요한 모든 데이터 이동 및 계산을 조율합니다. SQL 데이터 웨어하우스에 T-SQL 쿼리를 제출하면 제어 노드가 이를 각 계산 노드에서 병렬로 실행할 별도의 쿼리로 변환합니다.

**계산 노드:** 계산 노드는 SQL 데이터 웨어하우스 뒤에 있는 기능으로 제공됩니다. 이들은 데이터를 저장하고 쿼리를 처리하는 SQL 데이터베이스입니다. 데이터를 추가하면 SQL 데이터 웨어하우스는 계산 노드에 행을 배포합니다. 계산 노드는 데이터에서 병렬 쿼리를 실행하는 작업자입니다. 처리 후에 제어 노드에 다시 결과를 전달합니다. 쿼리를 완료하기 위해 제어 노드는 결과를 집계하고 최종 결과를 반환합니다.

**저장소:** 데이터는 Azure Blob 저장소에 저장됩니다. 계산 노드가 데이터와 상호 작용할 때 Blob 저장소 간에 데이터를 직접 쓰고 읽습니다. Azure Storage가 투명하고 크게 확장하므로 SQL Data Warehouse는 동일하게 수행할 수 있습니다. 계산 및 저장소는 독립적이므로 SQL 데이터 웨어하우스는 저장소 크기를 계산 확장과 별도로 자동으로 조정할 수 있으며 그 반대도 마찬가지 입니다. Azure Blob 저장소도 완전한 내결함성을 포함하며 백업 및 복원 프로세스를 간소화합니다.

**데이터 이동 서비스:** 데이터 이동 서비스(DMS)는 노드 간에 데이터를 이동합니다. DMS는 조인 및 집계에 필요한 데이터에 대한 계산 노드 액세스를 부여합니다. DMS는 Azure 서비스가 아닙니다. 모든 노드에서 SQL 데이터베이스와 함께 실행되는 Windows 서비스입니다. DMS는 직접 상호 작용할 수 없는 백그라운드 프로세스입니다. 그러나 쿼리 계획을 보면 각 쿼리를 병렬로 실행하기 위해 데이터 이동이 필요하므로 DMS 작업이 발생하는 경우에 확인할 수 있습니다.

## <a name="optimized-for-data-warehouse-workloads"></a>데이터 웨어하우스 워크로드에 최적화
MPP 방식은 다음을 포함하여 다양한 데이터 웨어하우징 특유의 성능 최적화를 통해 지원됩니다.

* 모든 데이터 전반에 대한 분산 쿼리 최적화 프로그램 및 복잡한 통계 집합. 데이터 크기 및 분포에 대한 정보를 사용하면, 서비스는 특정 분산 쿼리 작업에 대한 비용 산정을 기반으로 쿼리를 최적화할 수 있습니다.
* 컴퓨팅 리소스 사이에서 쿼리 수행에 필요한 대로 데이터를 효율적으로 이동해주는 데이터 이동 프로세스에 통합된 고급 알고리즘 및 기술도 제공합니다. 이러한 데이터 이동 작업은 기본 제공되며 데이터 이동 서비스에 대한 모든 최적화가 자동으로 수행됩니다.
* 기본적으로 클러스터형 **columnstore** 인덱스. SQL 데이터 웨어하우스는 열 기반 저장소를 사용하여 기존의 행 기반 저장소와 비교하여 평균 5배 높은 압축 향상, 최대 10배 이상의 쿼리 성능 향상을 달성합니다. 많은 수의 행을 스캔해야 하는 분석 쿼리가 Columnstore 인덱스에서 잘 작동합니다.


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>데이터 웨어하우스 단위를 사용한 예측 가능하고 확장 가능한 성능
SQL Data Warehouse는 사용자가 분석 쿼리에 대해 일관적이고 예측 가능한 성능을 예상할 수 있도록 SQL Database와 유사한 기술로 빌드됩니다. 계산 노드를 사용자가 추가하거나 빼면 사용자는 성능 규모를 선형으로 볼 수 있어야 합니다. SQL 데이터 웨어하우스에 대한 리소스 할당은 DWU(데이터 웨어하우스 단위)로 측정됩니다. DWU는 CPU, 메모리, IOPS와 같은 기본 리소스를 측정하며 SQL 데이터 웨어하우스에 할당됩니다. DWU 수가 증가하면 리소스 및 성능이 증가합니다. 특히, DWU는 다음을 보장합니다.

* 기본 하드웨어 또는 소프트웨어에 대한 우려 없이 데이터 웨어하우스의 규모를 쉽게 조정할 수 있습니다.
* 데이터 웨어하우스의 계산을 변경하기 전에 DWU 수준에 대한 성능 향상을 예측할 수 있습니다.
* 인스턴스의 기본적인 하드웨어 및 소프트웨어는 워크로드 성능에 영향을 미치지 않고 변경되거나 이동될 수 있습니다.
* Microsoft는 워크로드 성능에 영향을 미치지 않고 서비스의 기본적인 아키텍처를 개선할 수 있습니다.
* Microsoft는 확장이 가능하고 시스템에 고르게 영향이 미치도록 SQL Data Warehouse에서 성능을 빠르게 개선할 수 있습니다.

데이터 웨어하우스 단위는 데이터 웨어하우징 워크로드 성능과 연관성이 높은 세 가지 측정 메트릭에 대한 단위를 제공합니다. 다음 키 워크로드 메트릭은 DWU를 사용하여 선형으로 규모를 조정할 수 있습니다.

**검색/집계:** 대량의 행을 검색한 후 복잡한 집계를 수행하는 표준 데이터 웨어하우징 쿼리입니다. 이것은 I/O와 CPU를 많이 사용하는 연산입니다.

**로드:** 데이터를 서비스로 수집할 수 있는 역량입니다. 로드는 Azure Data Lake 또는 Azure Storage Blob의 PolyBase를 통해 가장 잘 수행됩니다. 이 메트릭은 서비스의 네트워크 및 CPU 측면을 강조하기 위해서 설계되었습니다.

**Create Table As Select(CTAS):** CTAS는 테이블을 복사하는 기능을 측정합니다. 여기에는 데이터를 저장소에서 읽어오기, 어플라이언스의 노드 전체에 배포하기, 저장소에 다시 쓰기가 포함됩니다. 이것은 CPU, IO 및 네트워크를 많이 사용하는 연산입니다.

## <a name="built-on-sql-server"></a>SQL Server 기반
SQL 데이터 웨어하우스는 SQL Server 관계형 데이터베이스 엔진을 기반으로 하며, 엔터프라이즈 데이터 웨어하우스에 기대하는 다양한 기능들을 포함하고 있습니다. 이미 T-SQL에 대해 알고 있다면 해당 지식을 쉽게 SQL 데이터 웨어하우스에 전송할 수 있습니다. 고급 사용자이든 초보 사용자이든 상관 없이 설명서의 예제들이 시작하는 데 도움이 될 것입니다. 전반적으로, SQL 데이터 웨어하우스의 언어 요소를 구축하는 방식에 대한 기본적인 생각은 다음과 같습니다.

* SQL 데이터 웨어하우스는 많은 연산에 T-SQL 구문을 사용합니다. 또한 저장 프로시저, 사용자 정의 함수, 테이블 파티션, 인덱스, 데이터 정렬을 비롯한 기존의 SQL 구문을 폭넓게 지원합니다.
* SQL Data Warehouse에는 클러스터형 **Columnstore** 인덱스, PolyBase 통합 및 데이터 감사(위협 평가와 함께 완성되는)를 비롯한 새로운 SQL Server 기능이 많이 포함되어 있습니다.
* 데이터 웨어하우스 작업에 덜 일반적이거나 SQL Server에 새로운 특정 T-SQL 언어 요소는 현재 사용할 수 없습니다. 자세한 내용은 [마이그레이션 설명서][Migration documentation]를 참조하세요.

SQL Server, SQL 데이터 웨어하우스, SQL 데이터베이스 및 분석 플랫폼 시스템 간의 TRANSACT-SQL 및 기능 공통성을 통해 데이터 요구 사항에 맞는 솔루션을 개발할 수 있습니다. 성능, 보안, 규모 요구 사항을 고려하여 데이터를 보관할 곳을 결정한 후에 필요에 따라 다른 시스템 간에 데이터를 전송할 수 있습니다.

## <a name="data-protection"></a>데이터 보호
SQL 데이터 웨어하우스는 Azure 프리미엄 로컬 중복 저장소에 모든 데이터를 저장합니다. 데이터의 여러 동기 복사본은 지역화된 오류 발생 시 투명한 데이터 보호를 보장하기 위해 로컬 데이터 센터에 유지됩니다. 또한 SQL Data Warehouse는 Azure Storage 스냅숏을 사용하여 일정한 간격으로 자동으로 (일시 중지 해제된) 활성 데이터베이스를 백업합니다. 작업을 백업하고 복원하는 방법에 대해 자세히 알아보려면 [백업 및 복원 개요][Backup and restore overview]를 참조하세요.

## <a name="integrated-with-microsoft-tools"></a>Microsoft 도구와 통합
SQL 데이터 웨어하우스는 SQL Server 사용자가 잘 알고 있는 다양한 도구를 통합합니다. 이러한 도구로는 다음이 있습니다.

**기존 SQL Server 도구:** SQL 데이터 웨어하우스는 SQL Server Analysis Services, Integration Services 및 Reporting Services와 완전히 통합됩니다.

**클라우드 기반 도구:** SQL Data Warehouse는 데이터 팩터리, Stream Analytics, Machine Learning 및 Power BI를 포함하여 Azure의 수많은 신규 도구와 통합할 수 있습니다. 자세한 전체 목록은 [통합된 도구 개요][Integrated tools overview]를 참조하세요.

**타사 도구:** 많은 타사 도구 공급자들이 자사의 도구와 SQL Data Warehouse의 통합을 보증하였습니다. 전체 목록은 [SQL Data Warehouse 솔루션 파트너][SQL Data Warehouse solution partners](영문)를 참조하세요.

## <a name="hybrid-data-sources-scenarios"></a>하이브리드 데이터 원본 시나리오
Polybase는 친숙한 T-SQL 명령을 사용하여 다양한 원본에서 데이터를 활용할 수 있게 해줍니다. Polybase를 통해 일반 테이블처럼 Azure blob 저장소에 보관 중인 비관계형 데이터를 쿼리할 수 있습니다. Polybase를 사용하여 비관계형 데이터를 쿼리하거나 비관계형 데이터를 SQL 데이터 웨어하우스로 가져옵니다.

* PolyBase는 외부 테이블을 사용하여 비관계형 데이터에 액세스합니다. 테이블 정의는 SQL 데이터 웨어하우스에 저장되며, SQL 및 일반적인 관계형 데이터에 액세스하는 도구를 사용하여 액세스할 수 있습니다.
* Polybase는 통합 시 중립적입니다. 지원되는 모든 원본에 동일한 기능을 노출합니다. Polybase에서 읽는 데이터는 구분된 파일 또는 ORC 파일 등 다양한 형식일 수 있습니다.
* PolyBase는 HDInsight 클러스터에 대한 저장소로도 사용되고 있는 Blob Storage에 액세스하는 데 사용할 수 있습니다. 그러면 관계형 및 비관계형 도구와 동일한 데이터에 액세스할 수 있습니다.

## <a name="sla"></a>SLA
SQL Data Warehouse는 Microsoft 온라인 서비스 SLA의 일부로 제품 수준 SLA(서비스 수준 계약)를 제공합니다. 자세한 내용은 [SQL Data Warehouse에 대한 SLA][SLA for SQL Data Warehouse]를 방문하세요. 다른 모든 제품에 대한 SLA 정보에 대해서는 [서비스 수준 계약] Azure 페이지를 방문하거나 [볼륨 라이선스][Volume Licensing](영문) 페이지에서 다운로드할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
SQL 데이터 웨어하우스에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse] 및 [샘플 데이터 로드][load sample data]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 발견하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [지원 티켓 만들기]
* [MSDN 포럼]
* [Stack Overflow 포럼]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[지원 티켓 만들기]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[고객 성공 사례]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 포럼]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow 포럼]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[서비스 수준 계약]: https://azure.microsoft.com/en-us/support/legal/sla/

