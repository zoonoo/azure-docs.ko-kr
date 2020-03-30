---
title: Azure 시냅스 분석(이전 SQL DW) 자주 묻는 질문
description: 이 문서에서는 고객 및 개발자의 Azure Synapse Analytics(이전의 SQL DW)에 대해 자주 묻는 질문을 나열합니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 766615a92e616a254cca4b2b3ce1ccde40118275
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350241"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure 시냅스 분석(이전 SQL DW) 자주 묻는 질문

## <a name="general"></a>일반

17. Azure Synapse란?

A. Azure Synapse는 데이터 웨어하우징 및 빅 데이터 분석을 통합하는 무한한 분석 서비스입니다. 서버리스 온디맨드 또는 프로비저닝된 리소스를 대규모로 사용하여 조건에 따라 데이터를 쿼리할 수 있습니다. Azure Synapse는 이러한 두 가지 환경을 통합된 환경과 결합하여 즉각적인 BI 및 기계 학습 요구에 맞게 데이터를 수집, 준비, 관리 및 제공합니다. 자세한 내용은 Azure [시냅스 분석](sql-data-warehouse-overview-what-is.md)입니다.

17. Azure SQL 데이터 웨어하우스는 어떻게 되었습니까?

A. Azure 시냅스는 SQL DW(Azure SQL 데이터 웨어하우스)를 발전시켰습니다. 업계 를 선도하는 동일한 데이터 웨어하우스를 완전히 새로운 차원의 성능과 기능으로 끌어올렸습니다. Azure Synapse를 사용하여 프로덕션 환경에서 기존 데이터 웨어하우스 워크로드를 계속 실행하고 미리 보기중인 새로운 기능의 이점을 자동으로 활용할 수 있습니다. 자세한 내용은 [Azure 시냅스 분석](sql-data-warehouse-overview-what-is.md)기능을 참조하십시오.

17. SQL 분석이란 무엇입니까?

A. SQL Analytics는 Azure Synapse에서 일반적으로 사용할 수 있는 엔터프라이즈 데이터 웨어하우징 기능을 가리킵니다. 자세한 내용은 Azure [시냅스 분석](sql-data-warehouse-overview-what-is.md)입니다.

17. Azure 시냅스로 시작하려면 어떻게 해야 합니까?

A. 자세한 내용은 Azure [무료 거래처를](https://azure.microsoft.com/free/sql-data-warehouse/) 시작하거나 [판매에 문의할](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)수 있습니다. 

17. Azure Synapse는 데이터 보안을 위해 무엇을 제공합니까?

A. Azure Synapse는 TDE 및 감사와 같은 데이터를 보호하기 위한 몇 가지 솔루션을 제공합니다. 자세한 내용은 [보안](sql-data-warehouse-overview-manage-security.md)을 참조하세요.

17. Azure Synapse가 준수하는 법적 또는 비즈니스 표준은 어디에서 확인할 수 있습니까?

A. SOC 및 ISO와 같은 제품에 대한 다양한 규정 준수 제안에 대해서는 [Microsoft 규정 준수](https://www.microsoft.com/trustcenter/compliance/complianceofferings) 페이지를 방문하세요. 먼저 규정 준수 제목을 선택합니다. 그런 다음 페이지 오른쪽에 있는 Microsoft 범위 내 클라우드 서비스 섹션에서 Azure를 확장하여 Azure Synapse를 준수하는 서비스를 확인합니다.

17. Power BI를 연결할 수 있습니까?

A. 예! Power BI는 Azure Synapse를 사용하여 직접 쿼리를 지원하지만 많은 수의 사용자 또는 실시간 데이터를 위한 것은 아닙니다. Power BI 성능을 추가로 최적화하려면 Azure 분석 서비스 또는 분석 서비스 IaaS 위에 Power BI를 사용하는 것이 좋습니다.

17. SQL 분석 용량 제한이란 무엇입니까?

A. 현재 [용량 제한](sql-data-warehouse-service-capacity-limits.md) 페이지를 참조하세요. 

17. 크기 조정/일시 중지/계속 작업이 너무 오래 걸리는 이유는 무엇인가요?

A. 여러 가지 요인이 계산 관리 작업 시간에 영향을 미칠 수 있습니다. 장기 실행 작업의 일반적인 경우는 트랜잭션 롤백입니다. 크기 조정 또는 일시 중지 작업이 시작되면 들어오는 모든 세션이 차단되고 쿼리가 해제됩니다. 시스템을 안정된 상태로 유지하기 위해서는 작업을 시작하기 전에 트랜잭션을 롤백해야 합니다. 트랜잭션의 수가 많고 로그 크기가 클수록 시스템을 안정된 상태로 복원하기 위해 작업이 더 오래 중지됩니다.

## <a name="user-support"></a>사용자 지원

17. 기능 요청이 있는 경우 어디에 제출하나요?

A. 기능 요청이 있는 경우 [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지에 제출하세요.

17. 어떻게 하면 될까요?

A. Azure Synapse 개발에 대한 도움말을 보려면 [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-sqldw) 페이지에서 질문을 할 수 있습니다. 

17. 지원 티켓을 제출하려면 어떻게 해야 하나요?

A. [지원 티켓](sql-data-warehouse-get-started-create-support-ticket.md)은 Azure Portal을 통해 정리할 수 있습니다.

## <a name="sql-languagefeature-support"></a>SQL 언어/기능 지원 

17. 어떤 데이터 형식이 지원됩니까?

A. [데이터 형식을](sql-data-warehouse-tables-data-types.md)참조하십시오.

17. 어떤 테이블 기능이 지원되나요?

A. 많은 기능이 지원됩니다. 지원되지 않는 기능은 [지원되지 않는 테이블 기능에서](sql-data-warehouse-tables-data-types.md)찾을 수 있습니다.

## <a name="tooling-and-administration"></a>도구 및 관리

17. SQL 애널리틱스가 REST API를 지원합니까?

A. 예. SQL 데이터베이스와 함께 사용할 수 있는 대부분의 REST 기능은 SQL 분석에서도 사용할 수 있습니다. REST 설명서 페이지 또는 [MSDN](https://msdn.microsoft.com/library/azure/mt163685.aspx)에서 이 API 정보를 찾을 수 있습니다.


## <a name="loading"></a>로드

17. 어떤 클라이언트 드라이버가 지원되나요?

A. SQL 분석에 대한 드라이버 지원은 연결 문자열 페이지에서 찾을 수 [있습니다.](sql-data-warehouse-connection-strings.md)

Q: PolyBase에서 지원하는 파일 형식은 무엇입니까?

A: Orc, RC, Parquet 및 일반 구분 텍스트

Q: PolyBase를 사용하여 어떤 데이터 원본을 연결할 수 있습니까? 

A: [Azure 데이터 레이크 저장소](sql-data-warehouse-load-from-azure-data-lake-store.md) 및 Azure 저장소 [Blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Q: Azure 저장소 Blob 또는 ADLS에 연결할 때 계산 푸시다운이 가능한가요? 

A: 아니요, PolyBase는 저장소 구성 요소와만 상호 작용합니다. 

Q: HDI에 연결할 수 있나요?

A: HDI는 HDFS 계층으로 ADLS 또는 WASB를 사용할 수 있습니다. HDFS 계층이 있는 경우 해당 데이터를 SQL Analytics 데이터 웨어하우스에 로드할 수 있습니다. 그러나 HDI 인스턴스로의 푸시다운 계산을 생성할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
Azure 시냅스 전체에 대한 자세한 내용은 [개요](sql-data-warehouse-overview-faq.md) 페이지를 참조하세요.
