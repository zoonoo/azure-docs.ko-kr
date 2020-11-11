---
title: '마이그레이션 가이드: DB2에서 SQL로 Managed Instance'
description: 이 가이드에 따라 DB2 데이터베이스를 Azure SQL Managed Instance로 마이그레이션합니다.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f691ec2d6ba873ba8052c5fab35162932668f185
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497018"
---
# <a name="migration-guide-db2-to-sql-managed-instance"></a>마이그레이션 가이드: DB2에서 SQL로 Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

이 가이드에서는 DB2 용 SQL Server Migration Assistant를 사용 하 여 DB2 데이터베이스를 Azure SQL Managed Instance로 마이그레이션하는 방법을 설명 합니다. 

다른 시나리오는 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항 

DB2 데이터베이스를 SQL Managed Instance 마이그레이션하려면 다음이 필요 합니다.

- 원본 환경이 지원되는지 확인해야 합니다.
- [DB2용 SSMA(SQL Server Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=54254)를 다운로드해야 합니다.
- 대상 [AZURE SQL Managed Instance](../../database/single-database-create-quickstart.md)입니다.


## <a name="pre-migration"></a>사전 마이그레이션

필수 조건을 충족하면 사용자 환경의 토폴로지를 검색하고 마이그레이션의 적합성을 평가할 준비가 된 것입니다. 

### <a name="assess-and-convert"></a>평가 및 변환

SSMA(SQL Server Migration Assistant)를 사용하여 평가를 만듭니다. 

평가를 만들려면 다음 단계를 따릅니다.

1. DB2용 SSMA(SQL Server Migration Assistant)를 엽니다. 
1. **파일** 을 선택한 다음, **새 프로젝트** 를 선택합니다. 
1. 프로젝트 이름, 프로젝트를 저장할 위치를 입력 한 다음, 드롭다운에서 Azure SQL Managed Instance를 마이그레이션 대상으로 선택 합니다. **확인** 을 선택합니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="프로젝트 세부 정보를 제공하고 확인을 선택하여 저장":::


1. **DB2에 연결** 대화 상자에서 DB2 연결 세부 정보의 값을 입력합니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="DB2 인스턴스에 연결":::


1. 마이그레이션할 DB2 스키마를 마우스 오른쪽 단추로 클릭한 다음, **보고서 만들기** 를 선택합니다. 그러면 HTML 보고서가 생성됩니다. 또는 스키마를 선택한 후 탐색 모음에서 **보고서 만들기** 를 선택할 수 있습니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="스키마를 마우스 오른쪽 단추로 클릭하고 보고서 만들기 선택":::

1. HTML 보고서를 검토하여 변환 통계와 오류 또는 경고를 파악합니다. Excel에서 보고서를 열어 DB2 개체의 인벤토리 및 스키마 변환을 수행하는 데 필요한 작업을 가져올 수도 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더에 있습니다.

   예: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>` 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="보고서를 검토하여 오류 또는 경고 파악":::


### <a name="validate-data-types"></a>데이터 형식의 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사하고 필요한 경우 요구 사항에 따라 해당 매핑을 변경합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. 메뉴에서 **도구** 를 선택합니다. 
1. **프로젝트 설정** 을 선택합니다. 
1. **형식 매핑** 탭을 선택합니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="스키마와 형식 매핑을 차례로 선택":::

1. **DB2 메타데이터 탐색기** 에서 테이블을 선택하여 각 테이블의 형식 매핑을 변경할 수 있습니다. 

### <a name="schema-conversion"></a>스키마 변환 

스키마를 변환하려면 다음 단계를 따릅니다.

1. (선택 사항) 동적 또는 임시 쿼리를 문에 추가합니다. 노드를 마우스 오른쪽 단추로 클릭한 다음, **문 추가** 를 선택합니다. 
1. **Azure SQL Database에 연결을** 선택 합니다. 
    1. 연결 정보를 입력 하 여 Azure SQL Managed Instance에 연결 합니다.  
    1. 드롭다운에서 대상 데이터베이스를 선택 합니다. 
    1. **연결** 을 선택합니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="세부 정보를 입력하여 SQL Server에 연결":::


1. 스키마를 마우스 오른쪽 단추로 클릭한 다음, **스키마 변환** 을 선택합니다. 또는 스키마를 선택한 후 맨 위 탐색 모음에서 **스키마 변환** 을 선택할 수 있습니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="스키마를 마우스 오른쪽 단추로 클릭하고 스키마 변환 선택":::

1. 변환이 완료된 후 스키마 구조를 비교하고 검토하여 잠재적인 문제를 파악하고 권장 사항에 따라 해결합니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="스키마 구조를 비교하고 검토하여 잠재적인 문제를 파악하고 권장 사항에 따라 해결":::

1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. **파일** 메뉴에서 **프로젝트 저장** 을 선택합니다. 


## <a name="migrate"></a>마이그레이션

데이터베이스 평가 및 불일치 해결을 완료한 다음에 수행할 단계는 마이그레이션 프로세스를 진행하는 것입니다.

스키마를 게시하고 데이터를 마이그레이션하려면 다음 단계를 따릅니다.

1. 스키마 게시: **AZURE SQL Managed Instance 메타 데이터 탐색기** **의 데이터베이스 노드에서 데이터베이스** 를 마우스 오른쪽 단추로 클릭 하 고 **데이터베이스와 동기화** 를 선택 합니다.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="데이터베이스를 마우스 오른쪽 단추로 클릭하고 데이터베이스와 동기화 선택":::

1. 데이터 마이그레이션: **DB2 메타데이터 탐색기** 에서 스키마를 마우스 오른쪽 단추로 클릭하고 **데이터 마이그레이션** 을 선택합니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="스키마를 마우스 오른쪽 단추로 클릭하고 데이터 마이그레이션 선택":::

1. DB2와 SQL Managed Instance에 대 한 연결 정보를 제공 합니다. 
1. **데이터 마이그레이션 보고서** 를 확인합니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="데이터 마이그레이션 보고서 검토":::

1. SQL Server Management Studio를 사용 하 여 SQL Managed Instance에 연결 하 고 데이터 및 스키마를 검토 하 여 마이그레이션의 유효성을 검사 합니다. 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="SSMS에서 스키마 비교":::

## <a name="post-migration"></a>마이그레이션 후 작업 

마이그레이션 단계를 성공적으로 완료한 후 모든 것이 최대한 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 수행해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정 

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.


### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션의 테스트 접근 방식은 다음 작업으로 구성됩니다.

1. **유효성 검사 테스트 개발** : 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.
1. **테스트 환경 설정** : 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.
1. **유효성 검사 테스트 실행** : 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.
1. **성능 테스트 실행** : 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.

   > [!NOTE]
   > 마이그레이션 후 유효성 검사 테스트를 개발하고 실행하는 데 도움이 필요한 경우 파트너 [QuerySurge](https://www.querysurge.com/company/partners/microsoft)에서 제공하는 데이터 품질 솔루션을 사용하는 것이 좋습니다. 


## <a name="leverage-advanced-features"></a>고급 기능 활용 

[기본 제공](../../database/high-availability-sla.md)되는 고가용성, [위협 검색](../../database/advanced-data-security.md), [워크 로드 모니터링 및 조정과](../../database/monitor-tune-overview.md)같이 Azure SQL Managed Instance에서 제공 하는 고급 클라우드 기반 기능을 활용 해야 합니다. 


일부 SQL Server 기능은 [데이터베이스 호환성 수준이](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 최신 호환성 수준 (150)으로 변경 된 후에만 사용할 수 있습니다. 

## <a name="migration-assets"></a>마이그레이션 자산 

추가 지원이 필요한 경우 실제 마이그레이션 프로젝트 참여를 지원하여 개발된 다음 리소스를 참조하세요.

|자산  |Description  |
|---------|---------|
|[데이터 워크로드 평가 모델 및 도구](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 이 도구는 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 지정된 워크로드에 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산 평가를 가속화하는 데 도움이 되는 간단한 원클릭 계산 및 보고서 생성 기능을 제공합니다.|
|[DB2 zOS 데이터 자산 검색 및 평가 패키지](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|데이터베이스에서 SQL 스크립트를 실행한 후에는 파일 시스템의 파일로 결과를 내보낼 수 있습니다. *.csv를 비롯한 여러 파일 형식이 지원되므로 스프레드시트와 같은 외부 도구에서 결과를 캡처할 수 있습니다. 이 방법은 워크벤치가 설치되지 않은 팀과 결과를 쉽게 공유하려는 경우에 유용할 수 있습니다.|
|[IBM DB2 LUW 인벤토리 스크립트 및 아티팩트](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|이 자산에는 IBM DB2 LUW 버전 11.1 시스템 테이블에 적용되고 스키마 및 개체 형식별 개체 수, 각 스키마의 대략적인 ‘원시 데이터’ 예측 및 각 스키마의 테이블 크기 조정을 CSV 형식으로 저장된 결과와 함께 제공하는 SQL 쿼리가 포함됩니다.|
|[Azure에서 DB2 LUW 기본 스케일링 - 설정 가이드](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|이 가이드는 DB2 구현 계획의 시작점으로 사용됩니다. 비즈니스 요구 사항은 다르지만 동일한 기본 패턴이 적용됩니다. 이 아키텍처 패턴은 Azure에서 OLAP 애플리케이션에도 사용할 수 있습니다.|

위 리소스는 Azure 데이터 그룹 엔지니어링 팀에서 후원하는 Data SQL Ninja 프로그램의 일부로 개발되었습니다. Data SQL Ninja 프로그램의 핵심 선언은 복잡한 현대화의 장애물을 제거하고 속도를 높이며 Microsoft의 Azure 데이터 플랫폼에 대한 데이터 플랫폼 마이그레이션 기회를 놓고 경쟁하는 것입니다. 조직이 Data SQL Ninja 프로그램에 참여하는 데 관심이 있다고 생각되면 계정 팀에 문의하여 추천서를 제출하도록 요청하세요.


## <a name="next-steps"></a>다음 단계

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구의 행렬은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- Azure SQL Managed Instance에 대 한 자세한 내용은 다음을 참조 하세요.
   - [SQL Managed Instance 개요](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [작업의 비용 및 크기 조정 작업에 대 한 모범 사례 Azure로 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.
