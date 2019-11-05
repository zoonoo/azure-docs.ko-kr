---
title: Azure Synapse Analytics (이전의 SQL DW) 질문과 대답 | Microsoft Docs
description: 이 문서에서는 고객과 개발자의 Azure Synapse Analytics (이전의 SQL DW)에 대 한 질문과 대답을 제공 합니다.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4db84ed69979713d613679e94d50cdb73475e2be
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520846"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (이전의 SQL DW) 질문과 대답

## <a name="general"></a>일반

Q. Azure Synapse 란?

A. Azure Synapse는 데이터 웨어하우징 및 빅 데이터 분석을 함께 제공 하는 무한 분석 서비스입니다. 서버를 사용 하지 않는 주문형 또는 프로 비전 된 리소스 (대규모)를 사용 하 여 사용자의 용어로 데이터를 자유롭게 쿼리할 수 있습니다. Azure Synapse는 즉시 BI 및 기계 학습 요구에 대 한 데이터를 수집 하 고, 준비 하 고, 관리 하 고, 제공 하기 위해 통합 된 환경과 함께 이러한 두 가지 환경을 제공 합니다. 자세한 내용은 [Azure Synapse Analytics 란?](sql-data-warehouse-overview-what-is.md)을 참조 하세요.

Q. Azure SQL Data Warehouse 어떻게 되었습니까?

A. Azure Synapse는 SQL DW (Azure SQL Data Warehouse)로 진화 하 고 있습니다. 우리는 동일한 업계 최고의 데이터 웨어하우스를 새로운 수준의 성능 및 기능으로 완전히 새로운 수준으로 만들었습니다. Azure Synapse를 사용 하 여 현재 프로덕션 환경에서 기존 데이터 웨어하우스 워크 로드를 계속 실행 하 고 미리 보기에 있는 새로운 기능을 자동으로 활용할 수 있습니다. 자세한 내용은 [Azure Synapse Analytics 란?](sql-data-warehouse-overview-what-is.md)을 참조 하세요.

Q. SQL Analytics 란?

A. SQL Analytics는 Azure Synapse에서 일반적으로 사용할 수 있는 엔터프라이즈 데이터 웨어하우징 기능을 나타냅니다. 자세한 내용은 [Azure Synapse Analytics 란?](sql-data-warehouse-overview-what-is.md)을 참조 하세요.

Q. Azure Synapse를 시작 어떻게 할까요??

A. 자세한 내용은 [Azure 무료 계정](https://azure.microsoft.com/free/sql-data-warehouse/) 으로 시작 하거나 [영업 담당자에 게 문의할](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)수 있습니다. 

Q. Azure Synapse는 데이터 보안을 위해 어떤 기능을 제공 하나요?

A. Azure Synapse는 TDE 및 감사와 같은 데이터를 보호 하기 위한 여러 가지 솔루션을 제공 합니다. 자세한 내용은 [보안]을 참조하세요.

Q. Azure Synapse가 준수 하는 법적 또는 비즈니스 표준에 대 한 자세한 내용은 어디서 확인할 수 있나요?

A. SOC 및 ISO와 같은 제품에 대한 다양한 규정 준수 제안에 대해서는 [Microsoft 규정 준수] 페이지를 방문하세요. 먼저 준수 제목으로 선택 하 고 페이지 오른쪽의 Microsoft 범위 내 클라우드 서비스 섹션에서 Azure를 확장 하 여 Azure Synapse 규격의 서비스를 확인 합니다.

Q. Power BI 연결할 수 있나요?

A. 예! Power BI는 Azure Synapse를 사용 하 여 직접 쿼리를 지원 하지만 많은 수의 사용자 또는 실시간 데이터를 위한 것이 아닙니다. Power BI의 프로덕션 사용을 위해 Azure Analysis Services 또는 Analysis Services IaaS 위에 Power BI를 사용 하는 것이 좋습니다. 

Q. SQL Analytics 용량 제한은 무엇 인가요?

A. 현재 [용량 제한] 페이지를 참조하세요. 

Q. 크기 조정/일시 중지/계속 작업이 너무 오래 걸리는 이유는 무엇인가요?

A. 다양한 요인이 컴퓨팅 관리 작업 시간에 영향을 줄 수 있습니다. 장기 실행 작업의 일반적인 경우는 트랜잭션 롤백입니다. 크기 조정 또는 일시 중지 작업이 시작되면 들어오는 모든 세션이 차단되고 쿼리가 해제됩니다. 시스템을 안정된 상태로 유지하기 위해서는 작업을 시작하기 전에 트랜잭션을 롤백해야 합니다. 트랜잭션의 수가 많고 로그 크기가 클수록 시스템을 안정된 상태로 복원하기 위해 작업이 더 오래 중지됩니다.

## <a name="user-support"></a>사용자 지원

Q. 기능 요청이 있는 경우 어디에 제출하나요?

A. 기능 요청이 있는 경우 [UserVoice] 페이지에 제출하세요.

Q. 어떻게 하면 될까요?

A. Azure Synapse를 사용 하 여 개발 하는 방법에 대 한 도움말은 [Stack Overflow] 페이지에서 질문할 수 있습니다. 

Q. 지원 티켓을 제출하려면 어떻게 해야 하나요?

A. [지원 티켓]은 Azure Portal을 통해 정리할 수 있습니다.

## <a name="sql-languagefeature-support"></a>SQL 언어/기능 지원 

Q. 지원 되는 데이터 형식은 무엇입니까?

A. [데이터 형식]을 참조 하세요.

Q. 어떤 테이블 기능이 지원되나요?

A. 지원 되지 않는 많은 기능과 지원 되지 않는 [테이블 기능]에 문서화 되어 있습니다.

## <a name="tooling-and-administration"></a>도구 및 관리

Q. Visual Studio에서 데이터베이스 프로젝트가 지원되나요?

A. Visual Studio에서는 현재 데이터베이스 프로젝트를 지원 하지 않습니다. 이 기능 제공 요청에 참여하려면 사용자 의견 [데이터베이스 프로젝트 기능 요청]을 방문하세요.

Q. SQL Analytics는 REST Api를 지원 하나요?

A. 예. SQL Database와 함께 사용할 수 있는 대부분의 REST 기능은 SQL Analytics 에서도 사용할 수 있습니다. REST 설명서 페이지 또는 [MSDN]에서 이 API 정보를 찾을 수 있습니다.


## <a name="loading"></a>로드

Q. 어떤 클라이언트 드라이버가 지원되나요?

A. DW에 대한 드라이버 지원은 [연결 문자열] 페이지에서 찾을 수 있습니다.

Q: PolyBase에서 지원 되는 파일 형식은 무엇입니까?

A: Orc, RC, Parquet 및 일반 구분 텍스트

Q: PolyBase를 사용 하 여 연결할 수 있는 데이터 소스는 무엇 인가요? 

A: [Azure Data Lake Store] 및 [Azure Storage Blobs]

Q: Azure Storage Blob 또는 ADLS에 연결할 때 계산 푸시 다운을 사용할 수 있나요? 

A: 아니요, PolyBase는 저장소 구성 요소와만 상호 작용 합니다. 

Q: HDI에 연결할 수 있나요?

A: HDI는 HDFS 계층으로 ADLS 또는 WASB를 사용할 수 있습니다. 둘 중 하나를 HDFS 계층으로 사용하는 경우 해당 데이터를 SQL DW에 로드할 수 있습니다. 그러나 HDI 인스턴스로의 푸시다운 계산을 생성할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
전체적으로 Azure Synapse에 대 한 자세한 내용은 [개요] 페이지를 참조 하세요.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[연결 문자열]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[지원 티켓]: ./sql-data-warehouse-get-started-create-support-ticket.md
[보안]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft 규정 준수]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[용량 제한]: ./sql-data-warehouse-service-capacity-limits.md
[데이터 형식]: ./sql-data-warehouse-tables-data-types.md
[테이블 기능]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[데이터베이스 프로젝트 기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[개요]: ./sql-data-warehouse-overview-faq.md
