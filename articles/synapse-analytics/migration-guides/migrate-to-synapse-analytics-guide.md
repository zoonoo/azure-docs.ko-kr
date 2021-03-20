---
title: 'Azure Synapse Analytics: 마이그레이션 가이드'
description: 이 가이드에 따라 Azure Synapse Analytics 전용 SQL 풀로 데이터베이스를 마이그레이션합니다.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: aa32083d48c868435ce77114f3a036b8bec47201
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609558"
---
# <a name="migrating-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 데이터 웨어하우스를 전용 SQL 풀로 마이그레이션 
다음 섹션에서는 기존 데이터 웨어하우스 솔루션을 Azure Synapse Analytics 전용 SQL 풀로 마이그레이션하는 것과 관련 된 작업에 대 한 개요를 제공 합니다.

## <a name="overview"></a>개요
마이그레이션하기 전에 Azure Synapse Analytics가 작업에 가장 적합 한 솔루션 인지 확인 해야 합니다. Azure Synapse Analytics는 대량 데이터에 대 한 분석을 수행 하도록 설계 된 분산 시스템입니다. Azure Synapse Analytics로 마이그레이션하려면 이해 하기 어려운 몇 가지 디자인 변경이 필요 하지만이를 구현 하는 데 다소 시간이 걸릴 수 있습니다. 비즈니스에 엔터프라이즈 수준의 데이터 웨어하우스가 필요한 경우 충분한 혜택을 얻을 수 있습니다. 그러나 Azure Synapse Analytics의 기능이 필요 하지 않은 경우 [SQL Server](https://docs.microsoft.com/sql/sql-server/) 또는 [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/)를 사용 하는 것이 더 비용 효율적입니다.

다음을 수행 하는 경우 Azure Synapse Analytics를 사용 하십시오.
- 하나 이상의 Tb의 데이터를 포함 합니다.
- 상당한 양의 데이터에 대해 분석을 실행 하도록 계획 합니다.
- 계산 및 저장소의 크기를 조정 하는 기능이 필요 합니다.
- 필요 하지 않은 경우 계산 리소스를 일시 중지 하 여 비용을 절약 하려고 합니다.

Azure Synapse Analytics 대신 다음과 같은 작업 (OLTP) 워크 로드에 대 한 다른 옵션을 고려 합니다.
- 높은 빈도의 읽기 및 쓰기입니다.
- 단일 항목을 여러 개 선택 합니다.
- 대량의 단일 행 삽입.
- 행 단위 처리에 필요 합니다.
- 호환 되지 않는 형식 (JSON, XML)입니다.

## <a name="azure-synapse-pathway"></a>Azure Synapse 경로
중요 한 차단 고객 중 하나는 한 시스템에서 다른 시스템으로 마이그레이션할 때 해당 데이터베이스 개체를 변환 하는 것입니다. [Azure Synapse 경로](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) 를 사용 하면 기존 데이터 웨어하우스의 개체 변환을 자동화 하 여 최신 데이터 웨어하우스 플랫폼으로 업그레이드할 수 있습니다. 무료로 제공되며 직관적이고 사용이 간단한 도구로, 코드 변환을 자동화하여 Azure Synapse Analytics로 신속하게 마이그레이션할 수 있도록 합니다.

## <a name="prerequisites"></a>필수 구성 요소
# <a name="migrate-from-sql-server"></a>[SQL Server에서 마이그레이션](#tab/migratefromSQLServer)
SQL Server 데이터 웨어하우스를 Azure Synapse Analytics로 마이그레이션하려면 다음과 같은 필수 구성 요소가 있는지 확인 합니다. 

- 데이터 웨어하우스 또는 분석 워크 로드 
- 최신 [Azure Synapse 경로](https://www.microsoft.com/en-us/download/details.aspx?id=102787) 도구를 다운로드 하 여 SQL Server 개체를 azure Synapse 개체로 마이그레이션합니다.
- Azure Synapse 작업 영역의 [전용 SQL 풀](../get-started-create-workspace.md) . 

# <a name="migrate-from-netezza"></a>[Netezza에서 마이그레이션](#tab/migratefromNetezza)
Netezza 데이터 웨어하우스를 Azure Synapse Analytics로 마이그레이션하려면 다음 필수 구성 요소가 있는지 확인 합니다. 

- 최신 [Azure Synapse 경로](https://www.microsoft.com/en-us/download/details.aspx?id=102787) 도구를 다운로드 하 여 SQL Server 개체를 azure Synapse 개체로 마이그레이션합니다.
- Azure Synapse 작업 영역의 [전용 SQL 풀](../get-started-create-workspace.md) .

자세한 내용은 [Azure Synapse Analytics 솔루션 및 Netezza에 대 한 마이그레이션](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)을 참조 하세요.

# <a name="migrate-from-snowflake"></a>[눈송이에서 마이그레이션](#tab/migratefromSnowflake)
눈송이 데이터 웨어하우스를 Azure Synapse Analytics로 마이그레이션하려면 다음 필수 구성 요소가 있는지 확인 합니다. 

- 최신 [Azure Synapse 경로](https://www.microsoft.com/en-us/download/details.aspx?id=102787) 도구를 다운로드 하 여 눈송이 개체를 azure Synapse 개체로 마이그레이션합니다.
- Azure Synapse 작업 영역의 [전용 SQL 풀](../get-started-create-workspace.md) . 

# <a name="migrate-from-oracle"></a>[Oracle에서 마이그레이션](#tab/migratefromOracle)
Oracle 데이터 웨어하우스를 Azure Synapse Analytics로 마이그레이션하려면 다음과 같은 필수 구성 요소가 있는지 확인 합니다. 

- 데이터 웨어하우스 또는 분석 워크 로드 
- Oracle 용 SSMA를 다운로드 하 여 Oracle 개체를 SQL Server으로 변환 합니다. 자세한 내용은 [Oracle 데이터베이스를 SQL Server로 마이그레이션 (OracleToSQL)을](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) 참조 하세요. 
- 최신 버전의 [Azure Synapse 보도](https://www.microsoft.com/download/details.aspx?id=102787) 도구를 다운로드 하 여 SQL Server 개체를 azure Synapse 개체로 마이그레이션합니다.
- Azure Synapse 작업 영역의 [전용 SQL 풀](../get-started-create-workspace.md) . 

자세한 내용은 [Oracle data warehouse에 대 한 Azure Synapse Analytics 솔루션 및 마이그레이션](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)을 참조 하세요.

---

## <a name="pre-migration"></a>마이그레이션 전
기존 솔루션을 Azure Synapse Analytics로 마이그레이션하도록 결정 한 후에는 시작 하기 전에 마이그레이션을 계획 하는 것이 중요 합니다. 계획의 주요 목표는 데이터, 테이블 스키마 및 코드가 Azure Synapse Analytics와 호환 되는지 확인 하는 것입니다. 사용자가 해결 해야 하는 Azure Synapse Analytics와 현재 시스템 간에는 몇 가지 호환성 차이가 있습니다. 또한 많은 양의 데이터를 Azure로 마이그레이션하는 데 시간이 걸립니다. 신중한 계획은 데이터를 Azure로 가져오는 프로세스를 가속화 합니다. 계획의 또 다른 주요 목표는 솔루션이 Azure Synapse Analytics가 제공 하도록 설계 된 높은 쿼리 성능을 최대한 활용할 수 있도록 설계를 조정 하는 것입니다. 크기에 대 한 데이터 웨어하우스 디자인은 고유한 디자인 패턴을 도입 하므로 기존 방법이 항상 가장 적합 한 것은 아닙니다. 마이그레이션 후에는 일부 디자인을 조정할 수 있지만 이전에 프로세스를 변경 하면 나중에 시간을 절약할 수 있습니다.

## <a name="migrate"></a>마이그레이션
마이그레이션을 성공적으로 수행 하려면 테이블 스키마, 코드 및 데이터를 마이그레이션해야 합니다. 이러한 항목에 대 한 자세한 지침은 다음을 참조 하세요.
- 이 문서에서는 [테이블 디자인을 고려](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)합니다.
- 이 문서에서는 [코드 변경 사항](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)에 대해 설명 합니다.
- [데이터 마이그레이션](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)문서를 참조 하세요.
- 이 문서에서는 [워크 로드 관리를 고려](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)합니다.

## <a name="additional-resources"></a>추가 리소스 
- CAT (고객 자문 팀)에는 블로그 게시물로 게시 된 몇 가지 훌륭한 Azure Synapse Analytics (이전의 SQL DW) 지침이 있습니다. 마이그레이션을 수행 하는 방법에 대 한 자세한 내용은이 문서에서 [Azure SQL Data Warehouse 하 여 데이터를 마이그레이션하](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)는 방법을 참조 하세요.

## <a name="migration-assets-from-real-world-engagements"></a>실제 계약의 마이그레이션 자산
이 마이그레이션 시나리오를 완료 하는 방법에 대 한 추가 지원은 실제 마이그레이션 프로젝트 참여를 지원 하기 위해 개발 된 다음 리소스를 참조 하세요.

| 제목/링크                              | 설명                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [데이터 워크 로드 평가 모델 및 도구](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 이 도구는 지정 된 작업에 대해 권장 되는 "최적" 대상 플랫폼, 클라우드 준비 및 응용 프로그램/데이터베이스 재구성 수준을 제공 합니다. 이 도구는 간단한 단일 클릭 계산 및 보고서 생성 기능을 제공 하며,이를 통해 일관 된 대상 플랫폼 의사 결정 프로세스를 제공 하 여 큰 부동산 평가를 가속화 하는 데 매우 유용 합니다. |
| [Azure Synapse Analytics로 데이터를 로드 하는 동안 데이터 인코딩 문제 처리](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | 이 블로그는 PolyBase를 사용 하 여 SQL Data Warehouse 데이터를 로드 하는 동안 발생할 수 있는 데이터 인코딩 문제 중 일부에 대 한 정보를 제공 하기 위한 것입니다. 또한이 문서에서는 이러한 문제를 극복 하 고 데이터를 성공적으로 로드 하는 데 사용할 수 있는 몇 가지 옵션을 제공 합니다. |
| [Azure Synapse Analytics 전용 SQL 풀에서 테이블 크기 가져오기](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | 설계자가 실행을 수행 해야 하는 주요 작업 중 하나는 마이그레이션 후 새 환경에 대 한 메트릭을 얻는 것입니다. 즉, 온-프레미스에서 클라우드로 로드 시간을 수집 하 고, PolyBase 로드 시간을 수집 하는 등의 작업을 수행 합니다. 이러한 작업 중 가장 중요 한 것은 고객의 현재 플랫폼과 비교 하 여 SQL Data Warehouse에서 저장소 크기를 확인 하는 것입니다. |
| [온-프레미스 SQL Server 로그인을 Azure Synapse Analytics로 이동 하는 유틸리티](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | 로그인을 다시 만들고 "온-프레미스" SQL Server에서 Azure SQL PaaS 서비스로 데이터베이스 사용자를 선택 하는 T-sql 명령 스크립트를 만드는 PowerShell 스크립트입니다. 이 도구를 사용 하면 Azure AD 계정에 대 한 Windows AD 계정 자동 매핑을 허용 하거나 온-프레미스 Windows Active Directory에 대 한 각 로그인에 대해 UPN 조회를 수행할 수 있습니다. 이 도구는 선택적으로 SQL Server 기본 로그인도 이동 합니다. 사용자 지정 서버 및 데이터베이스 역할은 스크립팅된 역할 멤버 자격, 데이터베이스 역할 및 사용자 권한입니다. 포함 된 데이터베이스는 아직 지원 되지 않으며 가능한 SQL Server 권한의 하위 집합만 스크립팅 됩니다. 즉 grant 권한 부여는 지원 되지 않습니다 (복잡 한 권한 트리). 자세한 내용은 지원 문서에서 확인할 수 있으며 스크립트는 이해 하기 쉽도록 설명 합니다. |

> [!NOTE]
> 위의 리소스는 Azure 데이터 그룹 엔지니어링 팀이 후원 하는 데이터 마이그레이션 Jumpstart 프로그램 (DM Jumpstart)의 일부로 개발 되었습니다. DM 시작의 핵심은 복잡 한 현대화를 차단 및 가속화 하 고 Microsoft의 Azure 데이터 플랫폼으로 데이터 플랫폼 마이그레이션 기회를 경합 하는 것입니다. 조직이 DM Jumpstart 프로그램 참여에 관심이 있다고 생각 되는 경우 계정 팀에 문의 하 고 추천를 제출 하도록 요청 하세요.

## <a name="videos"></a>동영상
- Walgreens에서 Netezza (이전 SQL DW)에서 Azure Synapse Analytics (이전의 SQL DW)로 약 100TB의 데이터를 사용 하 여 [소매 재고 시스템을 마이그레이션하](https://www.youtube.com/watch?v=86dhd8N1lH4) 는 방법을 시청 하세요. 