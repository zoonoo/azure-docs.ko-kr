---
title: 'Db2에서 Azure SQL Database로 마이그레이션: 마이그레이션 가이드'
description: 이 가이드에서는 Db2용 SQL Server Migration Assistant(Db2용 SSMA)를 사용하여 IMB Db2 데이터베이스에서 Azure SQL Database로 마이그레이션하는 방법을 설명합니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 05/14/2021
ms.openlocfilehash: 14feb78de79e0a9cb137415968dd9b623a4c603f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528282"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-database"></a>마이그레이션 가이드: IBM Db2에서 Azure SQL Database로 마이그레이션
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 Db2용 [SQL Server Migration](https://azure.microsoft.com/migration/sql-server/) Assistant를 사용하여 IBM Db2 데이터베이스에서 Azure SQL Database로 [마이그레이션하는 방법](https://azure.microsoft.com/migration/migration-journey)을 알아봅니다. 

다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](/data-migration)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소 

Db2 데이터베이스를 SQL Database로 마이그레이션하려면 다음이 필요합니다.

- [원본 환경이 지원](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites)되는지 확인합니다.
- [Db2용 SSMA(SQL Server Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=54254)를 다운로드해야 합니다.
- [Azure SQL Database](../../database/single-database-create-quickstart.md)에 대상 데이터베이스가 필요합니다.
- 원본과 대상 모두에 액세스하려면 연결되어 있어야 하고 충분한 권한이 필요합니다. 

## <a name="pre-migration"></a>사전 마이그레이션

필수 조건을 충족하면 사용자 환경의 토폴로지를 검색하고 [Azure 클라우드 마이그레이션](https://azure.microsoft.com/migration)의 적합성을 평가할 수 있습니다.

### <a name="assess-and-convert"></a>평가 및 변환

DB2용 SSMA를 사용하여 데이터베이스 개체 및 데이터를 검토하고 마이그레이션할 데이터베이스를 평가합니다. 

평가를 만들려면 다음 단계를 따릅니다.

1. [Db2용 SSMA](https://www.microsoft.com/download/details.aspx?id=54254)를 엽니다. 
1. **파일** > **새 프로젝트** 를 선택합니다. 
1. 프로젝트 이름과 위치를 입력하여 프로젝트를 저장합니다. 그런 다음, 드롭다운 목록에서 Azure SQL Database를 마이그레이션 대상으로 선택하고 **확인** 을 선택합니다.

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="지정할 프로젝트 세부 정보를 보여주는 스크린샷.":::


1. **Db2에 연결** 에서 Db2 연결 세부 사항에 대한 값을 입력합니다. 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="Db2 인스턴스에 연결하는 옵션을 보여 주는 스크린샷":::


1. 마이그레이션할 Db2 스키마를 마우스 오른쪽 단추로 클릭한 다음, **보고서 만들기** 를 선택합니다. 그러면 HTML 보고서가 생성됩니다. 또는 스키마를 선택한 후 탐색 모음에서 **보고서 만들기** 를 선택할 수 있습니다.

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="보고서를 만드는 방법을 보여 주는 스크린샷":::

1. HTML 보고서를 검토하여 변환 통계와 오류 또는 경고를 파악합니다. Excel에서 보고서를 열어 Db2 개체의 인벤토리 및 스키마 변환을 수행하는 데 필요한 작업을 가져올 수도 있습니다. 보고서의 기본 위치는 *SSMAProjects* 내의 보고서 폴더에 있습니다.

   예: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="오류 또는 경고를 식별하기 위해 검토하는 보고서의 스크린샷.":::


### <a name="validate-data-types"></a>데이터 형식의 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사하고 필요한 경우 요구 사항에 따라 해당 매핑을 변경합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. 메뉴에서 **도구** 를 선택합니다. 
1. **프로젝트 설정** 을 선택합니다. 
1. **형식 매핑** 탭을 선택합니다.

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="스키마 및 유형 매핑 선택을 보여 주는 스크린샷":::

1. **Db2 메타데이터 탐색기** 에서 테이블을 선택하여 각 테이블의 형식 매핑을 변경할 수 있습니다. 

### <a name="convert-schema"></a>스키마 변환

스키마를 변환하려면 다음 단계를 따릅니다.

1. (선택 사항) 동적 또는 임시 쿼리를 문에 추가합니다. 노드를 마우스 오른쪽 단추로 클릭한 다음, **문 추가** 를 선택합니다. 
1. **Azure SQL Database에 연결** 을 선택합니다. 
    1. Azure SQL Database에서 연결 세부 정보를 입력하여 데이터베이스를 연결합니다.
    1. 드롭다운 목록에서 대상 SQL Database를 선택하거나 새 이름을 제공합니다. 이 경우 대상 서버에 데이터베이스가 생성됩니다. 
    1. 인증 세부 정보를 제공합니다. 
    1. **연결** 을 선택합니다.

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="Azure의 논리 서버에 연결하는 데 필요한 세부 정보를 보여주는 스크린샷":::


1. 스키마를 마우스 오른쪽 단추로 클릭한 다음, **스키마 변환** 을 선택합니다. 또는 스키마를 선택한 후 맨 위 탐색 모음에서 **스키마 변환** 을 선택할 수 있습니다.

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="스키마 선택 및 변환을 보여 주는 스크린샷":::

1. 변환이 완료된 후 스키마 구조를 비교하고 검토하여 잠재적인 문제를 식별합니다. 권장 사항에 따라 문제를 해결합니다.

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="잠재적인 문제를 식별하기 위해 스키마 구조를 비교하고 검토하는 스크린샷":::

1. **출력** 창에서 **결과 검토** 를 선택합니다. **오류 목록** 창에서 오류를 검토합니다. 
1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. **파일** 메뉴에서 **프로젝트 저장** 을 선택합니다. 이렇게 하면 스키마를 SQL Database에 게시하기 전에 원본 및 대상 스키마를 오프라인으로 평가하고 원하는 부분을 수정할 수 있습니다.

## <a name="migrate"></a>마이그레이션

데이터베이스 평가 및 불일치 해결을 완료한 다음에 수행할 단계는 마이그레이션 프로세스를 진행하는 것입니다.

스키마를 게시하고 데이터를 마이그레이션하려면 다음 단계를 따릅니다.

1. 스키마를 게시합니다. **Azure SQL Database 메타데이터 탐색기** 의 **데이터베이스** 노드에서 데이터베이스를 마우스 오른쪽 단추로 클릭합니다. 그런 다음, **데이터베이스와 동기화** 를 선택합니다.

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="데이터베이스와 동기화하는 옵션을 보여 주는 스크린샷":::

1. 데이터를 마이그레이션합니다. **Db2 메타데이터 탐색기** 에서 마이그레이션할 데이터베이스 또는 개체를 마우스 오른쪽 단추로 클릭하고, **데이터 마이그레이션** 을 선택합니다. 또는 탐색 모음에서 **데이터 마이그레이션** 을 선택할 수 있습니다. 전체 데이터베이스의 데이터를 마이그레이션하려면 데이터베이스 이름 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 마이그레이션하려면 데이터베이스를 확장하고 **테이블** 을 확장한 다음, 테이블 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 생략하려면 확인란의 선택을 취소합니다.

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="스키마를 선택하고 데이터를 마이그레이션하도록 선택하는 것을 보여주는 스크린샷.":::

1. Db2 및 Azure SQL Database 모두에 대한 연결 세부 정보를 제공합니다. 
1. 마이그레이션이 완료되면 **데이터 마이그레이션 보고서** 를 확인합니다.  

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="데이터 마이그레이션 보고서를 검토할 위치를 보여 주는 스크린샷":::

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 Azure SQL Database의 데이터베이스에 연결합니다. 데이터 및 스키마를 검토하여 마이그레이션의 유효성을 검사합니다.

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="SQL Server Management Studio의 스키마를 비교하는 방법을 보여주는 스크린샷.":::

## <a name="post-migration"></a>마이그레이션 후 

마이그레이션을 완료한 후 모든 작업이 최대한 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 수행해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정 

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.

### <a name="perform-tests"></a>테스트 수행

테스트는 다음과 같은 작업으로 구성됩니다.

1. **유효성 검사 테스트 개발**: 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.
1. **테스트 환경 설정**: 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.
1. **유효성 검사 테스트 실행**: 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.
1. **성능 테스트 실행**: 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.

## <a name="advanced-features"></a>고급 기능 

SQL Database에서 제공하는 고급 클라우드 기반 기능(예:[기본 제공되는 고가용성](../../database/high-availability-sla.md), [위협 검색](../../database/azure-defender-for-sql.md), [워크로드 모니터링 및 튜닝](../../database/monitor-tune-overview.md))을 활용하는 것이 좋습니다. 

일부 SQL Server 기능은 [데이터베이스 호환성 수준](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)이 최신 호환성 수준으로 변경된 경우에만 사용할 수 있습니다. 

## <a name="migration-assets"></a>마이그레이션 자산 

추가 지원이 필요한 경우 실제 마이그레이션 프로젝트 참여를 지원하여 개발된 다음 리소스를 참조하세요.

|자산  |Description  |
|---------|---------|
|[데이터 워크로드 평가 모델 및 도구](https://www.microsoft.com/download/details.aspx?id=103130)| 이 도구는 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 지정된 워크로드에 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산 평가를 가속화하는 데 도움이 되는 간단한 원클릭 계산 및 보고서 생성 기능을 제공합니다.|
|[Db2 zOS 데이터 자산 검색 및 평가 패키지](https://www.microsoft.com/download/details.aspx?id=103108)|데이터베이스에서 SQL 스크립트를 실행한 후에는 파일 시스템의 파일로 결과를 내보낼 수 있습니다. \*.csv를 비롯한 여러 파일 형식이 지원되므로 스프레드시트와 같은 외부 도구에서 결과를 캡처할 수 있습니다. 이 방법은 워크벤치가 설치되지 않은 팀과 결과를 쉽게 공유하려는 경우에 유용할 수 있습니다.|
|[IBM Db2 LUW 인벤토리 스크립트 및 아티팩트](https://www.microsoft.com/download/details.aspx?id=103109)|이 자산에는 IBM Db2 LUW 버전 11.1 시스템 테이블에 적용되며, 스키마 및 개체 형식별 개체 수, 각 스키마의 대략적인 "원시 데이터" 예측 및 각 스키마의 테이블 크기 조정을 CSV 형식으로 저장된 결과와 함께 제공하는 SQL 쿼리가 포함됩니다.|
|[IBM Db2-SQL DB - 데이터베이스 비교 유틸리티](https://www.microsoft.com/download/details.aspx?id=103016)|데이터베이스 비교 유틸리티는 원본 플랫폼과 대상 플랫폼에서 데이터가 동일한지 확인하는 데 사용할 수 있는 Windows 콘솔 애플리케이션입니다. 이 도구를 사용하여 모든 테이블 또는 선택한 테이블, 행, 열의 행 또는 열 수준까지 데이터를 효율적으로 비교할 수 있습니다.|

데이터 SQL 엔지니어링 팀이 이러한 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.



## <a name="next-steps"></a>다음 단계

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오를 지원하는 Microsoft 및 타사 서비스와 도구는 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- Azure SQL Database에 대해 자세히 알아보려면 다음을 참조하세요.
   - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 

- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션된 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [클라우드 마이그레이션 리소스](https://azure.microsoft.com/migration/resources) 

- 애플리케이션 액세스 계층을 평가하려면 [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)를 참조하세요.
- 데이터 액세스 계층 A/B 테스트를 수행하는 방법에 관한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
