---
title: Azure Synapse Analytics(이전의 SQL DW)란?
description: Azure Synapse Analytics(이전의 SQL DW)는 엔터프라이즈 데이터 웨어하우징과 빅 데이터 분석을 결합한 무제한 분석 서비스입니다.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 68d39b4f363794d50fd05c2067502fc55d5d0170
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153250"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics(이전의 SQL DW)란?

Azure Synapse는 엔터프라이즈 데이터 웨어하우징과 빅 데이터 분석을 결합한 무제한 분석 서비스입니다. 또한 서버리스 주문형 리소스 또는 프로비저닝된 리소스를 규모에 맞게 사용하여 사용자의 용어로 데이터를 자유롭게 쿼리할 수 있습니다. Azure Synapse는 통합된 환경으로 이 두 세계를 결합하여 BI 및 기계 학습에 대한 즉각적인 요구에 따라 데이터를 수집, 준비, 관리 및 제공합니다.

Azure Synapse에는 다음 네 가지 구성 요소가 있습니다.
- SQL Analytics: 전체 T-SQL 기반 분석 – 일반 공급
    - SQL 풀(프로비저닝되는 DWU당 요금 지불) 
    - SQL 주문형(처리되는 TB당 요금 지불) – (미리 보기)
- Spark: 긴밀하게 통합된 Apache Spark(미리 보기) 
- 데이터 통합: 하이브리드 데이터 통합(미리 보기)
- 스튜디오: 통합 사용자 환경.  (미리 보기)

> [!NOTE]
> Azure Synapse의 미리 보기 기능에 액세스하려면 [여기](https://aka.ms/synapsepreview)에서 액세스를 요청합니다. Microsoft에서 모든 요청을 심사하고 최대한 빨리 응답합니다.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Azure Synapse의 SQL Analytics 및 SQL 풀

SQL Analytics는 Azure Synapse에서 일반적으로 사용할 수 있는 엔터프라이즈 데이터 웨어하우징 기능을 나타냅니다. 

SQL 풀은 SQL Analytics를 사용할 때 프로비저닝되는 분석 리소스의 컬렉션을 나타냅니다. SQL 풀의 크기는 DWU(Data Warehousing Unit)로 결정됩니다.

간단한 [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL 쿼리를 사용하여 빅 데이터를 가져온 다음, MPP 기능을 사용하여 고성능 분석을 실행합니다. 통합하고 분석할 때 SQL Analytics는 비즈니스가 더 빠르고 강력한 정보를 위해 신뢰할 수 있는 단일 버전의 정보입니다.  

## <a name="key-component-of-a-big-data-solution"></a>빅 데이터 솔루션의 핵심 구성 요소

데이터 웨어하우징은 클라우드를 기반으로 하는 엔드투엔드 빅 데이터 솔루션의 핵심 구성 요소입니다.

![데이터 웨어하우스 솔루션](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

클라우드 데이터 솔루션에서 데이터는 다양한 원본의 빅 데이터 저장소에 수집됩니다. 빅 데이터 저장소에서는 Hadoop, Spark 및 Machine Learning 알고리즘을 통해 데이터를 준비하고 학습합니다. 복잡한 분석에 대한 데이터가 준비되면 SQL Analytics는 PolyBase를 사용하여 빅 데이터 저장소를 쿼리합니다. PolyBase는 표준 T-SQL 쿼리를 사용하여 SQL Analytics 테이블로 데이터를 가져옵니다.
 
SQL Analytics는 열 형식 스토리지를 사용하여 관계형 테이블에 데이터를 저장합니다. 이 형식으로 인해 데이터 스토리지 비용이 상당히 감소하고 쿼리 성능이 향상됩니다. 데이터가 저장되면 대규모로 분석을 실행할 수 있습니다. 기존 데이터베이스 시스템에 비해 분석 쿼리는 몇 분이 아닌 몇 초, 몇 일이 아닌 몇 시간 안에 완료됩니다. 

분석 결과는 전 세계 보고 데이터베이스 또는 애플리케이션으로 이동할 수 있습니다. 그러면 비즈니스 분석가가 합리적인 비즈니스 결정을 내리기 위한 정보를 파악할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse 아키텍처](massively-parallel-processing-mpp-architecture.md) 살펴보기
- 빠르게 [SQL 풀 만들기](create-data-warehouse-portal.md)
- [샘플 데이터 로드](sql-data-warehouse-load-sample-databases.md)
- [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse) 살펴보기

또는 다음과 같은 기타 Azure Synapse 리소스를 살펴봅니다.  
* [블로그](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) 검색
* [기능 요청](https://feedback.azure.com/forums/307516-sql-data-warehouse) 제출
* [지원 티켓 만들기](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse) 검색
* [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw) 검색