---
title: 'Azure SQL Database로 MySQL 마이그레이션: 마이그레이션 가이드'
description: 이 가이드에서는 MySQL용 SSMA(MySQL용 SQL Server Migration Assistant)를 사용하여 Azure SQL 데이터베이스로 MySQL 데이터베이스를 마이그레이션하는 방법을 알아봅니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 8952e6e1bda8763d403c02dcd5815f1e1c0941e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641878"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>마이그레이션 가이드: Azure SQL Database로 MySQL 마이그레이션
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 MySQL용 SSMA(MySQL용 SQL Server Migration Assistant)를 사용하여 Azure SQL 데이터베이스로 MySQL 데이터베이스를 마이그레이션하는 방법을 알아봅니다. 

다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](https://docs.microsoft.com/data-migration)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

MySQL 데이터베이스를 SQL 데이터베이스로 마이그레이션하기 전에 다음을 수행합니다.

- 원본 환경이 지원되는지 확인합니다. 현재 MySQL 5.6 및 5.7이 지원됩니다. 
- [MySQL용 SQL Server Migration Assistant](https://www.microsoft.com/download/details.aspx?id=54257)를 다운로드하고 설치합니다.
- 연결되어 있으며 원본과 대상 모두에 액세스할 수 있는 충분한 권한이 있는지 확인합니다.

## <a name="pre-migration"></a>사전 마이그레이션 

필수 조건을 충족하면 사용자 환경의 토폴로지를 검색하고 마이그레이션의 적합성을 평가할 준비가 완료됩니다.

### <a name="assess"></a>평가 

MySQL용 SSMA(SQL Server Migration Assistant)를 사용하여 데이터베이스 개체 및 데이터를 검토하고 마이그레이션할 데이터베이스를 평가합니다. 

평가를 만들려면 다음을 수행합니다.

1. [MySQL용 SSMA](https://www.microsoft.com/download/details.aspx?id=54257)를 엽니다. 
1. **파일** 을 선택하고 **새 프로젝트** 를 선택합니다. 
1. **새 프로젝트** 창에서 프로젝트의 이름과 위치를 입력한 다음, **마이그레이션 대상** 드롭다운 목록에서 **Azure SQL Database** 를 선택합니다. 
1. **확인** 을 선택합니다.

   ![마이그레이션 프로젝트 이름, 위치 및 대상을 입력하기 위한 “새 프로젝트” 창의 스크린샷.](./media/mysql-to-sql-database-guide/new-project.png)

1. **MySQL에 연결** 탭을 선택한 다음 MySQL 서버 연결에 관한 세부 정보를 제공합니다. 

   ![원본에 대한 연결을 지정하기 위한 “MySQL에 연결” 창의 스크린샷.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. **MySQL 메타데이터 탐색기** 창에서 MySQL 스키마를 마우스 오른쪽 단추로 클릭한 다음, **보고서 만들기** 를 선택합니다. 또는 오른쪽 상단에서 **보고서 만들기** 탭을 선택할 수 있습니다.

   ![MySQL용 SSMA의 “보고서 만들기” 링크 스크린샷](./media/mysql-to-sql-database-guide/create-report.png)

1. HTML 보고서를 검토하여 변환 통계, 오류, 경고를 파악합니다. 보고서를 분석하여 변환 문제와 해결 방법을 파악합니다. 
   또한 Excel에서 보고서를 열어 MySQL 개체의 인벤토리를 가져오고 스키마 변환을 수행하는 데 필요한 작업을 이해할 수 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더에 있습니다. 예를 들면 다음과 같습니다. 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![SSMA의 예제 변환 보고서 스크린샷.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>데이터 형식의 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사하고 필요한 경우 요구 사항에 따라 해당 매핑을 변경합니다. 이를 수행하려면: 

1. **도구** 를 선택하고, **프로젝트 설정** 을 선택합니다.  
1. **형식 매핑** 탭을 선택합니다. 

   ![MySQL용 SSMA의 “형식 매핑” 창 스크린샷.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. **MySQL 메타데이터 탐색기** 창에서 테이블 이름을 선택하여 각 테이블의 형식 매핑을 변경할 수 있습니다. 

### <a name="convert-the-schema"></a>스키마 변환 

스키마를 변환하려면 다음을 수행합니다. 

1. (선택 사항) 동적 쿼리나 특수화된 쿼리를 변환하려면 노드를 마우스 오른쪽 단추로 클릭하고 **문 추가** 를 선택합니다. 

1. **Azure SQL Database에 연결** 탭을 선택하고 다음을 수행합니다.

   a. SQL 데이터베이스에 연결하는 데 필요한 세부 정보를 입력합니다.  
   b. 드롭다운 목록에서 대상 SQL 데이터베이스를 선택합니다. 또는 새 이름을 제공할 수 있습니다. 그러면 대상 서버에 데이터베이스가 생성됩니다.  
   다. 인증 세부 정보를 제공합니다.  
   d. **연결** 을 선택합니다.

   ![MySQL용 SSMA의 “Azure SQL Database에 연결” 창 스크린샷.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. 작업 중인 스키마를 마우스 오른쪽 단추로 클릭한 다음 **스키마 변환** 을 선택합니다. 또는 오른쪽 상단에서 **스키마 변환** 탭을 선택할 수 있습니다.

   ![“MySQL 메타데이터 탐색기” 창의 “스키마 변환” 명령 스크린샷.](./media/mysql-to-sql-database-guide/convert-schema.png)

1. 변환이 완료된 후 변환된 개체와 원래 개체를 검토하고 비교하여 잠재적인 문제를 파악하고 권장 사항에 따라 해결합니다. 

   ![원래 개체와 변환된 개체의 비교를 보여 주는 스크린샷.](./media/mysql-to-sql-database-guide/table-comparison.png)

   변환된 Transact-SQL 텍스트와 원래 코드를 비교하고 권장 사항을 검토합니다.

   ![변환된 쿼리를 소스 코드와 비교하는 방법을 보여 주는 스크린샷.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. **출력** 창에서 **결과 검토** 를 선택한 다음, **오류 목록** 창에서 오류를 검토합니다. 
1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. 그러려면 **파일** > **프로젝트 저장** 을 선택합니다. 이렇게 하면 스키마를 SQL 데이터베이스에 게시하기 전에 원본과 대상 스키마를 오프라인으로 평가하고 원하는 부분을 수정할 수 있습니다.

   다음과 같이 변환된 프로시저를 원래 프로시저와 비교합니다. 

   ![원래 프로시저와 변환된 프로시저의 비교를 보여 주는 스크린샷.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>데이터베이스 마이그레이션 

데이터베이스를 평가하고 불일치를 해결한 다음, 마이그레이션 프로세스를 실행할 수 있습니다. 마이그레이션에는 스키마 게시 및 데이터 마이그레이션의 두 단계가 포함됩니다. 

스키마를 게시하고 데이터를 마이그레이션하려면 다음을 수행합니다. 

1. 스키마를 게시합니다. **Azure SQL Database 메타데이터 탐색기** 창에서 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **데이터베이스와 동기화** 를 선택합니다. 이 작업을 수행하면 MySQL 스키마가 SQL 데이터베이스에 게시됩니다.

   ![데이터베이스 매핑을 검토하는 ‘데이터베이스와 동기화’ 창 스크린샷.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. 데이터를 마이그레이션합니다. **MySQL 메타데이터 탐색기** 창에서 마이그레이션할 MySQL 스키마를 마우스 오른쪽 단추로 클릭하고 **데이터 마이그레이션** 을 선택합니다. 또는 오른쪽 상단에서 **데이터 마이그레이션** 탭을 선택할 수 있습니다.

   전체 데이터베이스의 데이터를 마이그레이션하려면 데이터베이스 이름 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 마이그레이션하려면 데이터베이스를 확장하고 **테이블** 을 확장한 다음, 테이블 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 생략하려면 확인란의 선택을 취소합니다.

   ![“MySQL 메타데이터 탐색기” 창의 “데이터 마이그레이션” 명령 스크린샷.](./media/mysql-to-sql-database-guide/migrate-data.png)

1. 마이그레이션이 완료되면 **데이터 마이그레이션 보고서** 를 확인합니다.
   
   ![데이터 마이그레이션 보고서 스크린샷.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 SQL 데이터베이스에 연결하고, 데이터 및 스키마를 검토하여 마이그레이션의 유효성을 검사합니다.

   ![SQL Server Management Studio 스크린샷.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>마이그레이션 후 작업 

*마이그레이션* 단계를 성공적으로 완료한 후 모든 것이 최대한 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 완료해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션의 테스트 접근 방식은 다음 작업으로 구성됩니다.

1. **유효성 검사 테스트 개발**: 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.

1. **테스트 환경 설정**: 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.

1. **유효성 검사 테스트 실행**: 원본과 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.

1. **성능 테스트 실행**: 원본과 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정하고 완성도를 확인할 뿐만 아니라 워크로드 관련 성능 문제를 해결하는 데 매우 중요합니다.

해당 문제와 이를 완화하는 구체적인 단계에 관한 자세한 내용은 [마이그레이션 후 유효성 검사 및 최적화 가이드](/sql/relational-databases/post-migration-validation-and-optimization-guide)를 참조하세요.

## <a name="migration-assets"></a>마이그레이션 자산

이 마이그레이션 시나리오를 완료하는 방법에 대한 추가 지원은 다음 리소스를 참조하세요. 실제 마이그레이션 프로젝트 참여를 지원하기 위해 개발되었습니다.

| 제목 | Description |
| --- | --- |
| [데이터 워크로드 평가 모델 및 도구](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 특정 워크로드에 권장되는 ‘최적의’ 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산 평가를 가속화하는 데 도움이 되는 간단한 원클릭 계산 및 보고서 생성 기능을 제공합니다. |

데이터 SQL 엔지니어링 팀이 해당 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.

## <a name="next-steps"></a>다음 단계 

- 워크로드를 Azure로 마이그레이션하여 얻을 수 있는 비용 절감을 추정하려면 [Azure 총 소유 비용 계산기](https://aka.ms/azure-tco)를 참조하세요.

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [데이터 마이그레이션 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- 다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요. 

- 마이그레이션 동영상은 [마이그레이션 과정 개요와 권장되는 마이그레이션 및 평가 도구와 서비스](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)를 참조하세요.
