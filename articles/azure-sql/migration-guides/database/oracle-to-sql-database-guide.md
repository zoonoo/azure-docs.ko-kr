---
title: 'Oracle to SQL Database: 마이그레이션 가이드'
description: 이 가이드에서는 oracle 용 SQL Server Migration Assistant (oracle 용 SSMA)를 사용 하 여 Oracle 스키마를 Azure SQL Database로 마이그레이션하는 방법을 설명 합니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: f00740de5a327858fd250a0cb561b07c32f3b726
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655574"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>마이그레이션 가이드: Oracle to Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 oracle 용 SQL Server Migration Assistant를 사용 하 Azure SQL Database Oracle 스키마를 마이그레이션하는 방법을 설명 합니다.

다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](https://datamigration.microsoft.com/)을 참조하세요. 

## <a name="prerequisites"></a>필수 조건

Oracle 스키마를 SQL Database로 마이그레이션하려면 다음을 수행 해야 합니다. 

- 원본 환경이 지원 되는지 확인 합니다. 
- [Oracle 용 SSMA (SQL Server Migration Assistant)](https://www.microsoft.com/en-us/download/details.aspx?id=54258)를 다운로드 합니다. 
- 대상 [Azure SQL Database](../../database/single-database-create-quickstart.md)입니다. 
- Oracle 및 [공급자](/sql/ssma/oracle/connect-to-oracle-oracletosql) [용 ssma에 필요한 권한](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) 입니다.
 

## <a name="pre-migration"></a>사전 마이그레이션

필수 조건을 충족하면 사용자 환경의 토폴로지를 검색하고 마이그레이션의 적합성을 평가할 준비가 된 것입니다. 프로세스의이 부분에서는 마이그레이션해야 하는 데이터베이스에 대 한 인벤토리를 수행 하 고, 해당 데이터베이스를 평가 하 여 잠재적인 마이그레이션 문제나 차단기를 확인 한 다음, 표시 되는 항목을 확인 하는 것을 포함 합니다.



### <a name="assess"></a>평가 


Oracle 용 SSMA (SQL Server Migration Assistant)를 사용 하 여 데이터베이스 개체 및 데이터를 검토 하 고, 마이그레이션을 위한 데이터베이스를 평가 하 고, 데이터베이스 개체를 Azure SQL Database으로 마이그레이션하고, 데이터를 데이터베이스에 마이그레이션합니다. 


평가를 만들려면 다음 단계를 따릅니다. 


1. [Oracle에 대 한 SQL Server Migration Assistant를](https://www.microsoft.com/en-us/download/details.aspx?id=54258)엽니다. 
1. **파일** 을 선택한 다음, **새 프로젝트** 를 선택합니다. 
1. 프로젝트 이름, 프로젝트를 저장할 위치를 입력 한 다음 드롭다운에서 마이그레이션 대상으로 Azure SQL Database를 선택 합니다. **확인** 을 선택합니다.

   ![새 프로젝트](./media/oracle-to-sql-database-guide/new-project.png)


1. **Oracle에 연결을** 선택 합니다. Oracle **에 연결** 대화 상자에서 oracle 연결 정보에 대 한 값을 입력 합니다.

   ![Oracle에 연결](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

   마이그레이션할 Oracle 스키마를 선택 합니다. 

   ![Oracle 스키마 선택](./media/oracle-to-sql-database-guide/select-schema.png)

1. **Oracle 메타 데이터 탐색기** 에서 마이그레이션할 oracle 스키마를 마우스 오른쪽 단추로 클릭 한 다음 **보고서 만들기** 를 선택 합니다. 그러면 HTML 보고서가 생성됩니다. 또는 데이터베이스를 선택한 후 탐색 모음에서 **보고서 만들기** 를 선택할 수 있습니다.

   ![보고서 만들기](./media/oracle-to-sql-database-guide/create-report.png)

1. HTML 보고서를 검토하여 변환 통계와 오류 또는 경고를 파악합니다. 또한 Excel에서 보고서를 열어 Oracle 개체의 인벤토리를 가져오고 스키마를 변환 하는 데 필요한 작업을 수행할 수 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더에 있습니다.

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![평가 보고서](./media/oracle-to-sql-database-guide/assessment-report.png) 



### <a name="validate-data-types"></a>데이터 형식의 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사하고 필요한 경우 요구 사항에 따라 해당 매핑을 변경합니다. 이렇게 하려면 다음 단계를 따르십시오.

1. 메뉴에서 **도구** 를 선택합니다. 
1. **프로젝트 설정** 을 선택합니다. 
1. **형식 매핑** 탭을 선택합니다. 

   ![형식 매핑](./media/oracle-to-sql-database-guide/type-mappings.png)

1. **Oracle 메타 데이터 탐색기** 에서 테이블을 선택 하 여 각 테이블에 대 한 형식 매핑을 변경할 수 있습니다.

### <a name="convert-schema"></a>스키마 변환

스키마를 변환하려면 다음 단계를 따릅니다. 

1. (선택 사항) 동적 또는 임시 쿼리를 문에 추가합니다. 노드를 마우스 오른쪽 단추로 클릭한 다음, **문 추가** 를 선택합니다.
1. **Azure SQL Database에 연결을** 선택 합니다. 
    1. 연결 정보를 입력 하 여 Azure SQL Database에서 데이터베이스를 연결 합니다.
    1. 드롭다운에서 대상 SQL Database를 선택 합니다.
    1. **연결** 을 선택합니다.

    ![SQL Database에 연결](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)


1. **Oracle 메타 데이터 탐색기** 에서 oracle 스키마를 마우스 오른쪽 단추로 클릭 한 다음 **스키마 변환** 을 선택 합니다. 또는 스키마를 선택한 후 맨 위 탐색 모음에서 **스키마 변환** 을 선택할 수 있습니다.

   ![스키마 변환](./media/oracle-to-sql-database-guide/convert-schema.png)

1. 변환이 완료 된 후에는 변환 된 개체를 비교 하 여 원본 개체와 비교 하 여 잠재적인 문제를 식별 하 고 권장 사항에 따라 문제를 해결 합니다.

   ![권장 사항 스키마 검토](./media/oracle-to-sql-database-guide/table-mapping.png)

   변환 된 Transact-sql 텍스트를 원래 저장 프로시저와 비교 하 고 권장 사항을 검토 합니다. 

   ![권장 사항 검토](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. **파일** 메뉴에서 **프로젝트 저장** 을 선택합니다.

## <a name="migrate"></a>마이그레이션

데이터베이스 평가 및 불일치 해결을 완료한 다음에 수행할 단계는 마이그레이션 프로세스를 진행하는 것입니다. 마이그레이션에는 스키마 게시 및 데이터 마이그레이션과 같은 두 단계가 포함 됩니다. 

스키마를 게시하고 데이터를 마이그레이션하려면 다음 단계를 따릅니다.

1. 스키마 게시: **Azure SQL Database 메타 데이터 탐색기** **의 데이터베이스 노드에서 데이터베이스** 를 마우스 오른쪽 단추로 클릭 하 고 **데이터베이스와 동기화** 를 선택 합니다.

   ![데이터베이스와 동기화](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

   원본 프로젝트와 대상 간의 매핑을 검토 합니다.

   ![데이터베이스 검토와 동기화](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)


1. 데이터 마이그레이션: **Oracle 메타 데이터 탐색기** 에서 스키마를 마우스 오른쪽 단추로 클릭 하 고 **데이터 마이그레이션** 을 선택 합니다. 또는 스키마를 선택한 후 맨 위 줄 탐색 모음에서 **데이터 마이그레이션** 을 선택할 수 있습니다. 

   ![데이터 마이그레이션](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Oracle 및 Azure SQL Database에 대 한 연결 정보를 제공 합니다.
1. **데이터 마이그레이션 보고서** 를 확인합니다.

   ![데이터 마이그레이션 보고서](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 를 사용 하 여 Azure SQL Database에 연결 하 고 데이터 및 스키마를 검토 하 여 마이그레이션 유효성을 검사 합니다.

   ![SSMA에서 유효성 검사](./media/oracle-to-sql-database-guide/validate-data.png)

또는 SSIS (SQL Server Integration Services)를 사용 하 여 마이그레이션을 수행할 수도 있습니다. 자세한 내용은 다음을 참조하세요. 

- [SQL Server Migration Assistant: 타사 데이터 플랫폼에서 SQL Server로 데이터를 평가 하 고 마이그레이션하는 방법](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)
- [SQL Server Integration Services 시작](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services: Azure 및 하이브리드 데이터 이동을 위한 SSIS](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)


## <a name="post-migration"></a>마이그레이션 후 

**마이그레이션** 단계를 성공적으로 완료 한 후에는 모든 것이 가능한 한 원활 하 고 효율적으로 작동 하는지 확인 하기 위해 일련의 마이그레이션 후 작업을 수행 해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 은 Java 소스 코드를 분석 하 고 데이터 액세스 API 호출 및 쿼리를 검색 하 여 새 데이터베이스 백 엔드를 지원 하기 위해 해결 해야 하는 사항에 대 한 단일 창 보기를 제공 하는 Visual Studio Code에 대 한 확장입니다. 자세히 알아보려면 [Oracle에서 Java 응용 프로그램 마이그레이션](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) 블로그를 참조 하세요. 



### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션에 대 한 테스트 접근 방식은 다음 작업을 수행 하는 것으로 구성 됩니다.

1.  **유효성 검사 테스트를 개발** 합니다. 데이터베이스 마이그레이션을 테스트 하려면 SQL 쿼리를 사용 해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.

2.  **테스트 환경을 설정** 합니다. 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 있어야 합니다. 테스트 환경을 격리해야 합니다.

3.  **유효성 검사 테스트를 실행** 합니다. 원본 및 대상에 대해 유효성 검사 테스트를 실행 한 다음 결과를 분석 합니다.

4.  **성능 테스트를 실행** 합니다. 원본 및 대상에 대해 성능 테스트를 실행 한 다음 결과를 분석 하 고 비교 합니다.


### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정 하 고 완성도를 확인 하는 데 중요 하며 워크 로드와 관련 된 성능 문제를 해결 하는 데 중요 합니다.

> [!NOTE]
> 이러한 문제와 이러한 문제를 완화 하는 특정 단계에 대 한 자세한 내용은 [마이그레이션 후 유효성 검사 및 최적화 가이드](/sql/relational-databases/post-migration-validation-and-optimization-guide)를 참조 하세요.


## <a name="migration-assets"></a>마이그레이션 자산 

이 마이그레이션 시나리오를 완료 하는 방법에 대 한 추가 지원은 실제 마이그레이션 프로젝트 참여를 지원 하기 위해 개발 된 다음 리소스를 참조 하세요.

| **제목/링크**                                                                                                                                          | **설명**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [데이터 워크 로드 평가 모델 및 도구](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 이 도구는 지정 된 작업에 대해 권장 되는 "최적" 대상 플랫폼, 클라우드 준비 및 응용 프로그램/데이터베이스 재구성 수준을 제공 합니다. 이 도구는 간단한 단일 클릭 계산 및 보고서 생성 기능을 제공 하며,이를 통해 일관 된 대상 플랫폼 의사 결정 프로세스를 제공 하 여 큰 부동산 평가를 가속화 하는 데 매우 유용 합니다.                                                          |
| [Oracle 인벤토리 스크립트 아티팩트](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | 이 자산에는 Oracle 시스템 테이블에 도달 하 고 스키마 유형, 개체 유형 및 상태별로 개체 수를 제공 하는 PL/SQL 쿼리가 포함 됩니다. 또한 각 스키마에서 ' 원시 데이터 '의 대략적인 추정치를 제공 하 고 결과를 CSV 형식으로 저장 하 여 각 스키마의 테이블 크기를 조정 합니다.                                                                                                               |
| [SSMA Oracle 평가 수집 & 통합 자동화](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | 이 리소스 집합은 .csv 파일을 항목으로 사용 하 여 (프로젝트 폴더에 sources.csv) 콘솔 모드에서 SSMA 평가를 실행 하는 데 필요한 xml 파일을 생성 합니다. source.csv는 기존 Oracle 인스턴스의 인벤토리에 기반 하 여 고객에 의해 제공 됩니다. 출력 파일은 AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml 및 VariableValueFile.xml입니다.|
| [Oracle의 일반적인 오류 및 해결 방법에 대 한 SSMA](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle을 사용 하면 WHERE 절에 비-스칼라 조건을 할당할 수 있습니다. 그러나 SQL Server이 유형의 조건을 지원 하지 않습니다. 따라서 Oracle의 SSMA (SQL Server Migration Assistant)는 WHERE 절에서 비 스칼라 조건으로 쿼리를 변환 하지 않고 대신 오류 O2SS0001를 생성 합니다. 이 백서에서는 문제 및 해결 방법에 대 한 자세한 정보를 제공 합니다.          |
| [Oracle to SQL Server Migration 안내서](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | 이 문서에서는 Oracle 스키마를 최신 버전의 SQL Server 기반으로 마이그레이션하는 작업과 관련 된 태스크에 대해 집중적으로 설명 합니다. 마이그레이션에 기능/기능을 변경 해야 하는 경우 데이터베이스를 사용 하는 응용 프로그램의 각 변경에 따른 영향을 신중 하 게 고려해 야 합니다.                                                     |

위 리소스는 Azure 데이터 그룹 엔지니어링 팀에서 후원하는 Data SQL Ninja 프로그램의 일부로 개발되었습니다. Data SQL Ninja 프로그램의 핵심 선언은 복잡한 현대화의 장애물을 제거하고 속도를 높이며 Microsoft의 Azure 데이터 플랫폼에 대한 데이터 플랫폼 마이그레이션 기회를 놓고 경쟁하는 것입니다. 조직이 Data SQL Ninja 프로그램에 참여하는 데 관심이 있다고 생각되면 계정 팀에 문의하여 추천서를 제출하도록 요청하세요.

## <a name="next-steps"></a>다음 단계

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구에 대 한 매트릭스는 [데이터 마이그레이션을 위한 서비스 및 도구](https://docs.microsoft.com/azure/dms/dms-tools-matrix)문서를 참조 하세요.

- Azure SQL Database에 대 한 자세한 내용은 다음을 참조 하세요. 
  - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
  - [Azure TCO (총 소유 비용) 계산기](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [작업의 비용 및 크기 조정 작업에 대 한 모범 사례 Azure로 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 비디오 콘텐츠는 다음을 참조하세요. 
    - [평가 및 마이그레이션을 수행 하는 데 권장 되는 마이그레이션 경험 및 도구/서비스의 개요](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)


