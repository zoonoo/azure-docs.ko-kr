---
title: 전용 SQL 풀(이전 SQL DW) 자주 묻는 질문
description: 이 문서에서는 고객 및 개발자가 Azure Synapse Analytics 전용 SQL 풀(이전 SQL DW)에 대해 자주 묻는 질문을 나열합니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460518"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>Azure Synapse Analytics 전용 SQL 풀(이전 SQL DW) 자주 묻는 질문

## <a name="general"></a>일반

17. Azure Synapse란?

A. Azure Synapse는 데이터 웨어하우징과 빅 데이터 분석을 함께 제공하는 분석 서비스입니다. Azure Synapse는 BI 및 기계 학습 요구 사항에 대한 데이터를 수집, 준비, 관리 및 제공하도록 통합된 환경과 함께 이러한 두 가지 환경을 제공합니다. 자세한 정보는 [Azure Synapse Analytics란?](sql-data-warehouse-overview-what-is.md)을 참조하세요.

17. Azure SQL Data Warehouse는 어떻게 되었나요?

A. Azure Synapse는 Azure SQL Data Warehouse이 발전된 형태입니다. 동일한 업계 최고의 데이터 웨어하우스에 완전히 새로운 수준의 성능 및 기능을 적용했습니다. 프로덕션에서 Azure Synapse 전용 SQL 풀(이전 SQL DW)을 사용하여 기존 데이터 웨어하우스 워크로드를 계속 실행할 수 있습니다. 자세한 내용은 [Azure Synapse Analytics란?](sql-data-warehouse-overview-what-is.md)을 참조하세요.

17. Azure Synapse Analytics의 전용 SQL 풀(이전 SQL DW)이란?

A. 전용 SQL 풀(이전 SQL DW)은 Azure Synapse에서 일반적으로 사용할 수 있는 엔터프라이즈 데이터 웨어하우징 기능을 나타냅니다. 자세한 정보는 [Azure Synapse Analytics란?](sql-data-warehouse-overview-what-is.md)을 참조하세요.

17. Azure Synapse를 어떻게 시작하나요?

A. [Azure 무료 계정](https://azure.microsoft.com/free/sql-data-warehouse/)으로 시작하거나 [영업 담당자에게 자세한 내용을 문의](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)할 수 있습니다.

17. 데이터 보안을 위해 Azure Synapse가 제공하는 것은 무엇인가요?

A. Azure Synapse는 TDE 및 감사와 같이 데이터를 보호하기 위한 몇 가지 솔루션을 제공합니다. 자세한 내용은 [보안](sql-data-warehouse-overview-manage-security.md)을 참조하세요.

17. Azure Synapse가 준수하는 법적 또는 비즈니스 표준은 어디에서 확인할 수 있나요?

A. SOC 및 ISO와 같은 제품에 대한 다양한 규정 준수 제안에 대해서는 [Microsoft 규정 준수](https://www.microsoft.com/trustcenter/compliance/complianceofferings) 페이지를 방문하세요. 먼저 규정 준수 제목으로 선택합니다. 그런 다음 페이지 오른쪽에 있는 Microsoft 범위 내 클라우드 서비스 섹션에서 Azure를 확장하여 Azure Synapse 규정을 준수하는 서비스를 확인합니다.

17. Power BI를 연결할 수 있나요?

A. 예 Power BI는 Azure Synapse와 직접 쿼리하지만 많은 수의 사용자 또는 실시간 데이터를 위한 것은 아닙니다. Power BI 성능을 더욱 최적화하려면 Azure Analysis Services 또는 Analysis Service IaaS에 Power BI를 추가하여 사용하는 것이 좋습니다.

17. 전용 SQL 풀(이전 SQL DW) 용량 제한은 무엇인가요?

A. 현재 [용량 제한](sql-data-warehouse-service-capacity-limits.md) 페이지를 참조하세요.

17. 크기 조정/일시 중지/계속 작업이 너무 오래 걸리는 이유는 무엇인가요?

A. 컴퓨팅 관리 작업 시간에 영향을 줄 수 있는 몇 가지 요인이 있습니다. 장기 실행 작업의 일반적인 사례는 트랜잭션 롤백입니다. 크기 조정 또는 일시 중지 작업이 시작되면 들어오는 모든 세션이 차단되고 쿼리가 해제됩니다. 시스템을 안정된 상태로 유지하기 위해서는 작업을 시작하기 전에 트랜잭션을 롤백해야 합니다. 트랜잭션의 수가 많고 로그 크기가 클수록 시스템을 안정된 상태로 복원하기 위해 작업이 더 오래 중지됩니다.

## <a name="user-support"></a>사용자 지원

17. 기능 요청이 있는 경우 어디에 제출하나요?

A. 기능 요청이 있는 경우 [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지에 제출하세요.

17. 어떻게 하면 될까요?

A. Azure Synapse 개발과 관련하여 도움이 필요할 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) 페이지에 질문할 수 있습니다.

17. 지원 티켓을 제출하려면 어떻게 해야 하나요?

A. [지원 티켓](sql-data-warehouse-get-started-create-support-ticket.md)은 Azure Portal을 통해 정리할 수 있습니다.

## <a name="sql-languagefeature-support"></a>SQL 언어/기능 지원

17. 어떤 데이터 형식이 지원됩니까?

A. [데이터 형식](sql-data-warehouse-tables-data-types.md)을 참조하세요.

17. 어떤 테이블 기능이 지원되나요?

A. 많은 기능이 지원됩니다. 지원되지 않는 기능은 [지원되지 않는 테이블 기능](sql-data-warehouse-tables-data-types.md)에서 찾을 수 있습니다.

## <a name="tooling-and-administration"></a>도구 및 관리

17. 전용 SQL 풀(이전의 SQL DW)은 REST API를 지원하나요?

A. 예. SQL Database와 함께 사용할 수 있는 대부분의 REST 기능은 전용 SQL 풀(이전 SQL DW)에서도 사용할 수 있습니다. REST 설명서 페이지 또는 [데이터베이스](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)에서 이 API 정보를 찾을 수 있습니다.

## <a name="loading"></a>로드

17. 어떤 클라이언트 드라이버가 지원되나요?

A. 전용 SQL 풀(이전 SQL DW)에 대한 드라이버 지원은 [연결 문자열](sql-data-warehouse-connection-strings.md) 페이지에서 찾을 수 있습니다

Q: PolyBase에서 지원되는 파일 형식은 무엇입니까?

A: Orc, RC, Parquet 및 일반 구분 텍스트

Q: PolyBase를 사용하여 연결할 수 있는 데이터 소스는 무엇인가요?

A: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) 및 [Azure Storage Blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)입니다

Q: Azure Storage Blob 또는 ADLS에 연결할 때 계산 푸시다운이 가능한가요?

A: 아니요, PolyBase는 스토리지 구성 요소와 유일하게 상호 작용합니다.

Q: HDI에 연결할 수 있나요?

A: HDI는 HDFS 계층으로 ADLS 또는 WASB를 사용할 수 있습니다. 둘 중 하나를 HDFS 계층으로 사용하는 경우 해당 데이터를 전용 SQL 풀(이전 SQL DW)에 로드할 수 있습니다. 그러나 HDI 인스턴스로의 푸시다운 계산을 생성할 수 없습니다.

## <a name="next-steps"></a>다음 단계

Azure Synapse의 전용 SQL 풀 (이전 SQL DW)에 대한 자세한 내용은 [개요](sql-data-warehouse-overview-what-is.md) 페이지를 참조 하세요.
