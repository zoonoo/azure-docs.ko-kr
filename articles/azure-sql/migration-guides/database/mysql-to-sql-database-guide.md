---
title: 'MySQL to Azure SQL Database: 마이그레이션 가이드'
description: 이 가이드에서는 mysql 용 SQL Server Migration Assistant (MySQL 용 SSMA)를 사용 하 여 Azure SQL Database로 MySQL 데이터베이스를 마이그레이션하는 방법을 설명 합니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 14b2c1f98ae977548edb635b8a8a7a956b3f2dd7
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023778"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>마이그레이션 가이드: MySQL to Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 mysql 용 SQL Server Migration Assistant (MySQL 용 SSMA)를 사용 하 여 Azure SQL Database로 MySQL 데이터베이스를 마이그레이션하는 방법을 설명 합니다. 

다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](https://docs.microsoft.com/data-migration)을 참조하세요. 

## <a name="prerequisites"></a>필수 조건

MySQL 데이터베이스를 Azure SQL Database로 마이그레이션하려면 다음이 필요 합니다.

- 원본 환경이 지원되는지 확인 현재 MySQL 5.6 및 5.7이 지원 됩니다. 
- [MySQL에 대 한 SQL Server Migration Assistant](https://www.microsoft.com/download/details.aspx?id=54257)
- 원본 및 대상 모두에 액세스할 수 있는 연결 및 권한이 필요 합니다. 


## <a name="pre-migration"></a>사전 마이그레이션 

필수 조건을 충족하면 사용자 환경의 토폴로지를 검색하고 마이그레이션의 적합성을 평가할 준비가 된 것입니다.

### <a name="assess"></a>평가 

MySQL 용 SSMA (SQL Server Migration Assistant)를 사용 하 여 데이터베이스 개체 및 데이터를 검토 하 고 마이그레이션을 위한 데이터베이스를 평가 합니다. 

평가를 만들려면 다음 단계를 수행 합니다. 

1. [MySQL에 대 한 SQL Server Migration Assistant를](https://www.microsoft.com/download/details.aspx?id=54257)엽니다. 
1. 메뉴에서 **파일** 을 선택 하 고 **새 프로젝트** 를 선택 합니다. 
1. 프로젝트 이름, 프로젝트를 저장할 위치를 제공 합니다. 마이그레이션 대상으로 **Azure SQL Database** 를 선택 합니다. **확인을** 선택 합니다.

   ![새 프로젝트](./media/mysql-to-sql-database-guide/new-project.png)

1. Mysql **에 연결** 을 선택 하 고 연결 정보를 제공 하 여 mysql 서버에 연결 합니다.

   ![MySQL에 연결](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. **Mysql 메타 데이터 탐색기** 에서 mysql 스키마를 마우스 오른쪽 단추로 클릭 하 고 **보고서 만들기** 를 선택 합니다. 또는 위쪽의 탐색 모음에서 **보고서 만들기** 를 선택할 수 있습니다.

   ![보고서 만들기](./media/mysql-to-sql-database-guide/create-report.png)

1. HTML 보고서를 검토하여 변환 통계와 오류 또는 경고를 파악합니다. 또한 Excel에서 보고서를 열어 MySQL 개체의 인벤토리를 가져오고 스키마 변환을 수행 하는 데 필요한 작업을 수행할 수 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더에 있습니다.
 
   예: `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![변환 보고서](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>데이터 형식의 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사하고 필요한 경우 요구 사항에 따라 해당 매핑을 변경합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. 메뉴에서 **도구** 를 선택합니다. 
1. **프로젝트 설정** 을 선택합니다. 
1. **유형 매핑** 탭을 선택 합니다.

   ![형식 매핑](./media/mysql-to-sql-database-guide/type-mappings.png)

1. **MySQL 메타 데이터 탐색기** 에서 테이블을 선택 하 여 각 테이블에 대 한 형식 매핑을 변경할 수 있습니다. 

### <a name="convert-schema"></a>스키마 변환 

스키마를 변환하려면 다음 단계를 따릅니다. 

1. (선택 사항) 동적 쿼리나 임시 쿼리를 변환하려면 노드를 마우스 오른쪽 단추로 클릭하고 **문 추가** 를 선택합니다. 
1. **Azure SQL Database에 연결을** 선택 합니다. 
    1. 연결 정보를 입력 하 여 Azure SQL Database에서 데이터베이스를 연결 합니다.
    1. 드롭다운에서 대상 SQL Database를 선택 하거나 새 이름을 제공 합니다 .이 경우 대상 서버에 데이터베이스가 생성 됩니다. 
    1. 인증 세부 정보를 제공 합니다. 
    1. **연결** 선택:

   ![SQL에 연결](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. 스키마를 마우스 오른쪽 단추로 클릭 하 고 **스키마 변환** 을 선택 합니다. 또는 데이터베이스를 선택한 후 맨 위 줄 탐색 모음에서 **스키마 변환** 을 선택할 수 있습니다.

   ![스키마 변환](./media/mysql-to-sql-database-guide/convert-schema.png)

1. 변환이 완료 된 후에는 변환 된 개체를 비교 하 여 원본 개체와 비교 하 여 잠재적인 문제를 식별 하 고 권장 사항에 따라 문제를 해결 합니다.

   ![변환 된 개체를 원본과 비교할 수 있습니다.](./media/mysql-to-sql-database-guide/table-comparison.png)

   변환 된 Transact-sql 텍스트를 원래 코드와 비교 하 고 권장 사항을 검토 합니다.

   ![변환 된 쿼리를 소스 코드와 비교할 수 있습니다.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. 출력 창에서 **결과 검토** 를 선택하고 **오류 목록** 창에서 오류를 검토합니다. 
1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. **파일** 메뉴에서 **프로젝트 저장** 을 선택합니다. 이를 통해 원본 및 대상 스키마를 오프 라인으로 평가 하 고, 스키마를 SQL Database에 게시 하기 전에 수정을 수행할 수 있습니다.



## <a name="migrate"></a>마이그레이션 

데이터베이스 평가 및 불일치 해결을 완료한 다음에 수행할 단계는 마이그레이션 프로세스를 진행하는 것입니다. 마이그레이션에는 스키마 게시 및 데이터 마이그레이션의 두 단계가 포함됩니다. 

스키마를 게시 하 고 데이터를 마이그레이션하려면 다음 단계를 수행 합니다. 

1. 스키마 게시: **Azure SQL Database 메타 데이터 탐색기** 에서 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **데이터베이스와 동기화** 를 선택 합니다. 이 작업을 수행 하면 MySQL 스키마가 Azure SQL Database에 게시 됩니다.

   ![데이터베이스와 동기화](./media/mysql-to-sql-database-guide/synchronize-database.png)

   원본 프로젝트와 대상 간의 매핑을 검토 합니다.

   ![데이터베이스 검토와 동기화](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. 데이터 마이그레이션: **MySQL 메타 데이터 탐색기** 에서 마이그레이션할 데이터베이스 또는 개체를 마우스 오른쪽 단추로 클릭 하 고 **데이터 마이그레이션** 을 선택 합니다. 또는 위쪽의 탐색 모음에서 **데이터 마이그레이션** 을 선택할 수 있습니다. 전체 데이터베이스의 데이터를 마이그레이션하려면 데이터베이스 이름 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 마이그레이션하려면 데이터베이스를 확장하고 테이블을 확장한 다음, 테이블 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 생략 하려면 확인란의 선택을 취소 합니다.

   ![데이터 마이그레이션](./media/mysql-to-sql-database-guide/migrate-data.png)

1. 마이그레이션이 완료 되 면 **데이터 마이그레이션** 보고서를 확인 합니다. 

   ![데이터 마이그레이션 보고서](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 를 사용 하 여 Azure SQL Database에 연결 하 고 데이터 및 스키마를 검토 하 여 마이그레이션 유효성을 검사 합니다.

    ![SSMA에서 유효성 검사](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>마이그레이션 후 

**마이그레이션** 단계를 성공적으로 완료한 후 모든 것이 최대한 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 수행해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션의 테스트 접근 방식은 다음 작업을 수행하는 것으로 구성됩니다.

1. **유효성 검사 테스트 개발**. 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.

2. **테스트 환경 설정**. 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.

3. **유효성 검사 테스트 실행**. 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.

4. **성능 테스트 실행**. 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정하고 완성도를 확인할 뿐만 아니라 워크로드 관련 성능 문제를 해결하는 데 매우 중요합니다.

이러한 문제와 이를 완화하는 구체적인 단계에 대한 자세한 내용은 [마이그레이션 후 유효성 검사 및 최적화 가이드](/sql/relational-databases/post-migration-validation-and-optimization-guide)를 참조하세요.

## <a name="migration-assets"></a>마이그레이션 자산

이 마이그레이션 시나리오를 완료하는 데 추가 지원이 필요한 경우 실제 마이그레이션 프로젝트 참여를 지원하여 개발된 다음 리소스를 참조하세요.

| 제목/링크     | Description    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Data Workload Assessment Model and Tool](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)(데이터 워크로드 평가 모델 및 도구) | 이 도구는 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 지정된 워크로드에 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산을 더 빠르게 평가하는 데 큰 도움이 되는 간편한 원클릭 계산 및 보고서 생성 기능을 제공합니다. |

위 리소스는 Azure 데이터 그룹 엔지니어링 팀에서 후원하는 Data SQL Ninja 프로그램의 일부로 개발되었습니다. Data SQL Ninja 프로그램의 핵심 선언은 복잡한 현대화의 장애물을 제거하고 속도를 높이며 Microsoft의 Azure 데이터 플랫폼에 대한 데이터 플랫폼 마이그레이션 기회를 놓고 경쟁하는 것입니다. 조직이 Data SQL Ninja 프로그램에 참여하는 데 관심이 있다고 생각되면 계정 팀에 문의하여 추천서를 제출하도록 요청하세요.

## <a name="next-steps"></a>다음 단계 

- Azure로 워크 로드를 마이그레이션하여 실현할 수 있는 비용 절감 액을 예측 하려면 [AZURE TCO (총 소유 비용) 계산기](https://aka.ms/azure-tco) 를 확인 해야 합니다.

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [Service and tools for data migration](https://docs.microsoft.com/azure/dms/dms-tools-matrix)(데이터 마이그레이션을 위한 서비스 및 도구) 문서를 참조하세요.

- 다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](https://datamigration.microsoft.com/)을 참조하세요. 

비디오의 경우 다음을 참조하세요. 
- [평가 및 마이그레이션을 수행 하는 데 권장 되는 마이그레이션 경험 및 도구/서비스의 개요](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
