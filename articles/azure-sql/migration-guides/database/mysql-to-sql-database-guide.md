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
ms.openlocfilehash: ff7b2115b396bf42cdeffa9c58bffb1802e980d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721888"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>마이그레이션 가이드: MySQL to Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 mysql 용 SQL Server Migration Assistant (MySQL 용 SSMA)를 사용 하 여 Azure SQL Database로 MySQL 데이터베이스를 마이그레이션하는 방법을 설명 합니다. 

다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](https://datamigration.microsoft.com/)을 참조하세요. 

## <a name="prerequisites"></a>필수 조건

MySQL 데이터베이스를 Azure SQL Database로 마이그레이션하려면 다음이 필요 합니다.

- 원본 환경이 지원되는지 확인해야 합니다. 현재 MySQL 5.6 및 5.7이 지원 됩니다. 
- [MySQL용 SQL Server Migration Assistant](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>사전 마이그레이션 

필수 조건을 충족하면 사용자 환경의 토폴로지를 검색하고 마이그레이션의 적합성을 평가할 준비가 된 것입니다.

### <a name="assess"></a>평가 

[MySQL 용 SQL Server Migration Assistant](https://www.microsoft.com/download/confirmation.aspx?id=54257)를 사용 하 여 데이터베이스 개체 및 데이터를 검토 하 고 마이그레이션할 데이터베이스를 평가할 수 있습니다.

평가를 만들려면 다음 단계를 수행 합니다.

1. MySQL에 대 한 SQL Server Migration Assistant를 엽니다. 
1. 메뉴에서 **파일** 을 선택 하 고 **새 프로젝트** 를 선택 합니다. 프로젝트 이름, 프로젝트를 저장할 위치를 제공 합니다. 마이그레이션 대상으로 **Azure SQL Database** 를 선택 합니다. 

   ![새 프로젝트](./media/mysql-to-sql-database-guide/new-project.png)

1. Mysql **에 연결** 을 선택 하 고 연결 정보를 제공 하 여 mysql 서버에 연결 합니다. 

   ![MySQL에 연결](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. **Mysql 메타 데이터 탐색기** 에서 mysql 스키마를 마우스 오른쪽 단추로 클릭 하 고 **보고서 만들기** 를 선택 합니다. 또는 위쪽의 탐색 모음에서 **보고서 만들기** 를 선택할 수 있습니다. 

   ![보고서 만들기](./media/mysql-to-sql-database-guide/create-report.png)

1. 오류 및 경고 뿐만 아니라 변환 통계에 대 한 HTML 보고서를 검토 합니다. 분석 하 여 변환 문제 및 해결 방법을 파악 합니다. 

   이 보고서는 첫 번째 화면에서 선택한 것 처럼 SSMA 프로젝트 폴더에서 액세스할 수도 있습니다. 위의 예제에서 report.xml 파일을 찾습니다.
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   를 Excel에서 열어 MySQL 개체의 인벤토리 및 스키마 변환을 수행 하는 데 필요한 작업을 가져옵니다.

    ![변환 보고서](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>데이터 형식의 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사하고 필요한 경우 요구 사항에 따라 해당 매핑을 변경합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. 메뉴에서 **도구** 를 선택합니다. 
1. **프로젝트 설정** 을 선택합니다. 
1. **형식 매핑** 탭을 선택합니다. 

   ![형식 매핑](./media/mysql-to-sql-database-guide/type-mappings.png)

1. **MySQL 메타 데이터 탐색기** 에서 테이블을 선택 하 여 각 테이블에 대 한 형식 매핑을 변경할 수 있습니다. 

### <a name="convert-schema"></a>스키마 변환 

스키마를 변환하려면 다음 단계를 따릅니다. 

1. 필드 동적 또는 임시 쿼리를 변환 하려면 노드를 마우스 오른쪽 단추로 클릭 하 고 **문 추가** 를 선택 합니다. 
1. 위쪽 줄 탐색 모음에서 **Azure SQL Database에 연결을** 선택 하 고 연결 정보를 제공 합니다. 기존 데이터베이스에 연결 하거나 새 이름을 제공 하도록 선택할 수 있습니다 .이 경우 대상 서버에 데이터베이스가 생성 됩니다.

   ![SQL에 연결](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. 스키마를 마우스 오른쪽 단추로 클릭 하 고 **스키마 변환** 을 선택 합니다. 

   ![스키마 변환](./media/mysql-to-sql-database-guide/convert-schema.png)

1. 스키마 변환이 완료 된 후 변환 된 코드를 원래 코드와 비교 하 여 잠재적인 문제를 식별 합니다. 

   변환 된 개체를 원본 개체와 비교 합니다. 

   ![ 개체 비교 및 검토 ](./media/mysql-to-sql-database-guide/table-comparison.png)

   변환 된 프로시저를 원래 프로시저와 비교 합니다. 

   ![개체 코드 비교 및 검토](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate"></a>마이그레이션 

데이터베이스 평가 및 불일치 해결을 완료한 다음에 수행할 단계는 마이그레이션 프로세스를 진행하는 것입니다. 마이그레이션에는 스키마 게시 및 데이터 마이그레이션과 같은 두 단계가 포함 됩니다. 

스키마를 게시 하 고 데이터를 마이그레이션하려면 다음 단계를 수행 합니다. 

1. **Azure SQL Database 메타 데이터 탐색기** 에서 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **데이터베이스와 동기화** 를 선택 합니다. 이 작업을 수행 하면 MySQL 스키마가 Azure SQL Database에 게시 됩니다.

   ![데이터베이스와 동기화](./media/mysql-to-sql-database-guide/synchronize-database.png)

   원본 프로젝트와 대상 간의 매핑을 검토 합니다.

   ![데이터베이스 검토와 동기화](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. **Mysql 메타 데이터 탐색기** 에서 mysql 스키마를 마우스 오른쪽 단추로 클릭 하 고 **데이터 마이그레이션** 을 선택 합니다. 또는 최상위 탐색에서 **데이터 마이그레이션** 을 선택할 수도 있습니다. 

   ![데이터 마이그레이션](./media/mysql-to-sql-database-guide/migrate-data.png)

1. 마이그레이션이 완료 되 면 **데이터 마이그레이션** 보고서를 확인 합니다. 

   ![데이터 마이그레이션 보고서](./media/mysql-to-sql-database-guide/data-migration-report.png)

1.  SSMS (SQL Server Management Studio)를 사용 하 여 Azure SQL Database의 데이터와 스키마를 검토 하 여 마이그레이션 유효성을 검사 합니다.

    ![SSMA에서 유효성 검사](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>마이그레이션 후 

**마이그레이션** 단계를 성공적으로 완료 한 후에는 모든 것이 가능한 한 원활 하 고 효율적으로 작동 하는지 확인 하기 위해 일련의 마이그레이션 후 작업을 수행 해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션에 대 한 테스트 접근 방식은 다음 작업을 수행 하는 것으로 구성 됩니다.

1. **유효성 검사 테스트를 개발** 합니다. 데이터베이스 마이그레이션을 테스트 하려면 SQL 쿼리를 사용 해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.

2. **테스트 환경을 설정** 합니다. 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 있어야 합니다. 테스트 환경을 격리해야 합니다.

3. **유효성 검사 테스트를 실행** 합니다. 원본 및 대상에 대해 유효성 검사 테스트를 실행 한 다음 결과를 분석 합니다.

4. **성능 테스트를 실행** 합니다. 원본 및 대상에 대해 성능 테스트를 실행 한 다음 결과를 분석 하 고 비교 합니다.

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정 하 고 완성도를 확인 하는 데 중요 하며 워크 로드와 관련 된 성능 문제를 해결 하는 데 중요 합니다.

이러한 문제와 이러한 문제를 완화 하는 특정 단계에 대 한 자세한 내용은 [마이그레이션 후 유효성 검사 및 최적화 가이드](/sql/relational-databases/post-migration-validation-and-optimization-guide)를 참조 하세요.

## <a name="migration-assets"></a>마이그레이션 자산

이 마이그레이션 시나리오를 완료 하는 방법에 대 한 추가 지원은 실제 마이그레이션 프로젝트 참여를 지원 하기 위해 개발 된 다음 리소스를 참조 하세요.

| 제목/링크     | Description    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [데이터 워크 로드 평가 모델 및 도구](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 이 도구는 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 지정된 워크로드에 제공합니다. 이 도구는 간단한 단일 클릭 계산 및 보고서 생성 기능을 제공 하며,이를 통해 일관 된 대상 플랫폼 의사 결정 프로세스를 제공 하 여 큰 부동산 평가를 가속화 하는 데 매우 유용 합니다. |

위 리소스는 Azure 데이터 그룹 엔지니어링 팀에서 후원하는 Data SQL Ninja 프로그램의 일부로 개발되었습니다. Data SQL Ninja 프로그램의 핵심 선언은 복잡한 현대화의 장애물을 제거하고 속도를 높이며 Microsoft의 Azure 데이터 플랫폼에 대한 데이터 플랫폼 마이그레이션 기회를 놓고 경쟁하는 것입니다. 조직이 Data SQL Ninja 프로그램에 참여하는 데 관심이 있다고 생각되면 계정 팀에 문의하여 추천서를 제출하도록 요청하세요.

## <a name="next-steps"></a>다음 단계 

- Azure로 워크 로드를 마이그레이션하여 실현할 수 있는 비용 절감 액을 예측 하려면 [AZURE TCO (총 소유 비용) 계산기](https://aka.ms/azure-tco) 를 확인 해야 합니다.

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구에 대 한 매트릭스는 [데이터 마이그레이션을 위한 서비스 및 도구](https://docs.microsoft.com/azure/dms/dms-tools-matrix)문서를 참조 하세요.

- 다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](https://datamigration.microsoft.com/)을 참조하세요. 

비디오는 다음을 참조 하세요. 
- [평가 및 마이그레이션을 수행 하는 데 권장 되는 마이그레이션 경험 및 도구/서비스의 개요](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
