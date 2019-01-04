---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 12월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 21baa89293c74ec49720bffc2506e20789fe9e55
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411382"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 12월
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 12월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다.

## <a name="virtual-network-service-endpoints-generally-available"></a>Virtual Network 서비스 엔드포인트 일반 공급
이 릴리스에는 모든 Azure 지역의 Azure SQL Data Warehouse에 대한 VNet(가상 네트워크) 서비스 엔드포인트의 일반 공급 버전이 포함됩니다. VNet 서비스 엔드포인트를 사용하면 가상 네트워크 내 지정된 서브넷 또는 서브넷 세트의 논리 서버 연결을 격리할 수 있습니다. VNet에서 Azure SQL Data Warehouse로 전송되는 트래픽은 항상 Azure 백본 네트워크에 유지됩니다. 가상 어플라이언스나 온-프레미스를 통해 인터넷 트래픽을 가져오는 특정 경로보다 이러한 직접 경로를 사용하는 것이 좋습니다. 서비스 엔드포인트를 통한 가상 네트워크 액세스에 대해서는 추가 요금이 부과되지 않습니다. [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)에 대한 현재 가격 책정 모델은 있는 그대로 적용됩니다.

이번 릴리스에서는 [ABFS(Azure Blob FileSystem)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) 드라이버를 통해 [ADLS(Azure Data Lake Storage Gen2)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)로의 PolyBase 연결도 활성화되었습니다. Azure Data Lake Storage Gen2는 분석 데이터의 전체 수명 주기에 필요한 모든 특성을 Azure Storage에 제공합니다. 두 개의 기존 Azure Storage 서비스인 Azure Blob Storage 및 Azure Data Lake Storage Gen1 기능이 통합됩니다. 파일 시스템 의미 체계, 파일 수준 보안 및 크기 조정과 같은 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)의 기능이 [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)의 낮은 비용, 계층화된 스토리지, 고가용성/재해 복구 기능과 결합됩니다. 

PolyBase를 사용하면 VNet에서 보안이 적용된 Azure Storage에서 Azure SQL Data Warehouse로 데이터를 가져올 수 있습니다. 마찬가지로 Polybase를 통해 Azure SQL Data Warehouse로부터 VNet에서 보안이 적용된 Azure Storage로 데이터를 내보낼 수도 있습니다. 

Azure SQL Data Warehouse의 VNet 서비스 엔드포인트에 대한 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) 또는 [설명서](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)를 참조하세요.

## <a name="automatic-performance-monitoring-preview"></a>자동 성능 모니터링(미리 보기)
[쿼리 저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)는 이제 Azure SQL Data Warehouse에 대한 미리 보기로 제공됩니다. 쿼리 저장소는 데이터 웨어하우스의 작업 및 성능을 모니터링하기 위해 쿼리, 쿼리 계획, 런타임 통계 및 쿼리 기록을 추적함으로써 쿼리 성능 문제를 해결하는 데 도움을 주도록 디자인되었습니다. 쿼리 저장소는 다음을 지원하는 내부 저장소 및 DMV(동적 관리 뷰) 세트입니다.

- 상위 리소스 소비 쿼리 식별 및 조정
- 임시 워크로드 식별 및 개선
- 쿼리 성능과 통계, 인덱스 또는 시스템 크기(DWU 설정) 변경 사항이 계획에 미치는 영향 평가
- 실행되는 모든 쿼리에 대한 전체 쿼리 텍스트 참조

쿼리 저장소에는 다음과 같은 3가지 실제 저장소가 포함되어 있습니다.  
- 실행 계획 정보를 유지하기 위한 계획 저장소 
- 실행 통계 정보를 지속하기 위한 런타임 통계 저장소
- 대기 통계 정보를 지속하기 위한 대기 통계 저장소 

SQL Data Warehouse는 이러한 저장소를 자동으로 관리하며, 추가 비용 없이 지난 7일 동안 확인된 무제한 수의 쿼리를 제공합니다. 쿼리 저장소를 사용하도록 설정하는 것은 ALTER DATABASE T-SQL 문을 실행하는 것만큼 간단합니다.

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Azure SQL Data Warehouse의 쿼리 저장소에 대한 자세한 내용은 [쿼리 저장소를 사용한 성능 모니터링](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) 문서와 쿼리 저장소 DMV(예: [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017))를 참조하세요. 다음은 출시 소식을 발표하는 [블로그 게시물](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)입니다.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Azure SQL Data Warehouse Gen2용 하위 컴퓨팅 계층
이제 Azure SQL Data Warehouse Gen2는 하위 컴퓨팅 계층을 지원합니다. 고객은 100 cDWU([데이터 웨어하우스 단위](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu))로 시작해서 Azure SQL Data Warehouse의 최고 성능, 유연성 및 보안 기능을 경험할 수 있으며, 몇 분 안에 30,000 cDWU까지 확장할 수 있습니다. 2018년 12월 중순부터, 고객은 [지역](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions)의 하위 컴퓨팅 계층에 대한 Gen2 성능 및 유연성을 누릴 수 있으며, 나머지 지역의 경우 2019년 중에 서비스가 지원될 예정입니다.

Microsoft는 차세대 데이터 웨어하우징에 대한 진입점을 낮춰 최적의 평가판 환경이 무엇인지 추측하지 않고 안전한 고성능 데이터 웨어하우스의 모든 이점을 평가하려는 가치 중심 고객에게 문을 열어주고 있습니다. 고객은 현재 500 cDWU 진입점에서 최저 100 cDWU로 시작할 수 있습니다. SQL Data Warehouse Gen2는 일시 중지 및 다시 시작 작업을 계속 지원하며 계산 시 뛰어난 유연성을 제공합니다. 또한 Gen2에서는 쿼리당 2.5배 더 많은 메모리, 최대 128개의 동시 쿼리 및 [적응형 캐싱](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) 기능과 함께 제한없는 columnstore 스토리지 용량을 지원합니다. 평균적으로 이러한 기능은 같은 가격으로 제공되는 Gen1의 동일한 데이터 웨어하우스 단위에 비해 5배 더 많은 성능을 보장합니다. 지역 중복 백업은 기본 제공되는 데이터 보호 기능과 함께 Gen2 표준입니다. Azure SQL Data Warehouse Gen2는 필요 시 확장 가능합니다.

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 학습하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

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
