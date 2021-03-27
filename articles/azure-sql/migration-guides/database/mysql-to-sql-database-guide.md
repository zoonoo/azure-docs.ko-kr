---
title: 'MySQL to Azure SQL Database: 마이그레이션 가이드'
description: 이 가이드에서는 mysql 용 SQL Server Migration Assistant (MySQL 용 SSMA)를 사용 하 여 MySQL 데이터베이스를 Azure SQL database로 마이그레이션하는 방법에 대해 알아봅니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 1f1692aaa74f56c404a8fae7aa91e94baecbb7e1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626489"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>마이그레이션 가이드: MySQL to Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 mysql에 대 한 SQL Server Migration Assistant (MySQL 용 SSMA)를 사용 하 여 MySQL 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법에 대해 알아봅니다. 

다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](https://docs.microsoft.com/data-migration)를 참조 하세요. 

## <a name="prerequisites"></a>전제 조건

MySQL 데이터베이스를 SQL database로 마이그레이션하기 전에 다음을 수행 합니다.

- 원본 환경이 지원 되는지 확인 합니다. 현재 MySQL 5.6 및 5.7이 지원 됩니다. 
- [MySQL 용 SQL Server Migration Assistant](https://www.microsoft.com/download/details.aspx?id=54257)를 다운로드 하 고 설치 합니다.
- 원본과 대상 모두에 액세스할 수 있는 충분 한 권한이 있는지 확인 하십시오.

## <a name="pre-migration"></a>마이그레이션 전 

필수 구성 요소를 충족 하 고 나면 환경의 토폴로지를 검색 하 고 마이그레이션의 실현 가능성을 평가할 준비가 된 것입니다.

### <a name="assess"></a>평가 

MySQL 용 SSMA (SQL Server Migration Assistant)를 사용 하 여 데이터베이스 개체 및 데이터를 검토 하 고 마이그레이션을 위한 데이터베이스를 평가 합니다. 

평가를 만들려면 다음을 수행 합니다.

1. [MySQL 용 Ssma를](https://www.microsoft.com/download/details.aspx?id=54257)엽니다. 
1. **파일** 을 선택한 다음 **새 프로젝트** 를 선택 합니다. 
1. **새 프로젝트** 창에서 프로젝트의 이름과 위치를 입력 하 고 다음 **으로 마이그레이션** 드롭다운 목록에서 **Azure SQL Database** 을 선택 합니다. 
1. **확인** 을 선택합니다.

   ![마이그레이션 프로젝트 이름, 위치 및 대상을 입력 하기 위한 "새 프로젝트" 창의 스크린샷](./media/mysql-to-sql-database-guide/new-project.png)

1. **Mysql에 연결** 탭을 선택한 다음 mysql 서버 연결에 대 한 세부 정보를 제공 합니다. 

   ![원본에 대 한 연결을 지정 하기 위한 "MySQL에 연결" 창의 스크린샷](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. **Mysql 메타 데이터 탐색기** 창에서 mysql 스키마를 마우스 오른쪽 단추로 클릭 한 다음 **보고서 만들기** 를 선택 합니다. 또는 오른쪽 위에 있는 **보고서 만들기** 탭을 선택할 수 있습니다.

   ![MySQL 용 SSMA의 "보고서 만들기" 링크 스크린샷](./media/mysql-to-sql-database-guide/create-report.png)

1. HTML 보고서를 검토 하 여 변환 통계, 오류 및 경고를 파악 합니다. 분석 하 여 변환 문제 및 해결 방법을 파악 합니다. 
   또한 Excel에서 보고서를 열어 MySQL 개체의 인벤토리를 가져오고 스키마 변환을 수행 하는 데 필요한 작업을 이해할 수 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더에 있습니다. 예를 들어: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![SSMA의 예제 변환 보고서 스크린샷](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>데이터 형식 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사 하 고 필요한 경우 요구 사항에 따라 변경 합니다. 이를 수행하려면: 

1. **도구** 를 선택한 다음 **프로젝트 설정** 을 선택 합니다.  
1. **유형 매핑** 탭을 선택 합니다. 

   ![MySQL 용 SSMA의 "형식 매핑" 창 스크린샷](./media/mysql-to-sql-database-guide/type-mappings.png)

1. **MySQL 메타 데이터 탐색기** 창에서 테이블 이름을 선택 하 여 각 테이블에 대 한 형식 매핑을 변경할 수 있습니다. 

### <a name="convert-the-schema"></a>스키마 변환 

스키마를 변환 하려면 다음을 수행 합니다. 

1. 필드 동적 또는 특수 쿼리를 변환 하려면 노드를 마우스 오른쪽 단추로 클릭 한 다음 **문 추가** 를 선택 합니다. 

1. **Azure SQL Database에 연결** 탭을 선택 하 고 다음을 수행 합니다.

   a. SQL database에 연결 하는 데 필요한 세부 정보를 입력 합니다.  
   b. 드롭다운 목록에서 대상 SQL 데이터베이스를 선택 합니다. 또는 새 이름을 제공할 수 있습니다 .이 경우 대상 서버에 데이터베이스가 생성 됩니다.  
   다. 인증 세부 정보를 제공 합니다.  
   d. **연결** 을 선택합니다.

   ![MySQL 용 SSMA에서 "Azure SQL Database에 연결" 창의 스크린샷](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. 작업 중인 스키마를 마우스 오른쪽 단추로 클릭 한 다음 **스키마 변환** 을 선택 합니다. 또는 오른쪽 위에서 **스키마 변환** 탭을 선택할 수 있습니다.

   !["MySQL 메타 데이터 탐색기" 창에 있는 "스키마 변환" 명령의 스크린샷](./media/mysql-to-sql-database-guide/convert-schema.png)

1. 변환이 완료 된 후에는 변환 된 개체를 검토 하 고 원래 개체와 비교 하 여 잠재적인 문제를 식별 하 고 권장 사항에 따라 문제를 해결 합니다. 

   ![변환 된 개체와 원본 개체의 비교를 보여 주는 스크린샷](./media/mysql-to-sql-database-guide/table-comparison.png)

   변환 된 Transact-sql 텍스트를 원래 코드와 비교 하 고 권장 사항을 검토 합니다.

   ![변환 된 쿼리를 소스 코드와 비교 하는 방법을 보여 주는 스크린샷](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. **출력** 창에서 **결과 검토** 를 선택한 다음 **오류 목록** 창에서 오류를 검토 합니다. 
1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. 이렇게 하려면 **파일**  >  **프로젝트 저장** 을 선택 합니다. 이를 통해 원본 및 대상 스키마를 오프 라인으로 평가 하 고 스키마를 SQL 데이터베이스에 게시 하기 전에 수정 사항을 수행할 수 있습니다.

   다음과 같이 변환 된 프로시저를 원래 프로시저와 비교 합니다. 

   ![변환 된 프로시저와 원본 프로시저의 비교를 보여 주는 스크린샷](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>데이터베이스 마이그레이션 

데이터베이스를 평가 하 고 불일치를 해결 한 후 마이그레이션 프로세스를 실행할 수 있습니다. 마이그레이션은 스키마 게시 및 데이터 마이그레이션의 두 단계로 이루어집니다. 

스키마를 게시 하 고 데이터를 마이그레이션하려면 다음을 수행 합니다. 

1. 스키마를 게시 합니다. **Azure SQL Database 메타 데이터 탐색기** 창에서 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **데이터베이스와 동기화** 를 선택 합니다. 이 작업을 수행 하면 MySQL 스키마가 SQL 데이터베이스에 게시 됩니다.

   ![데이터베이스 매핑을 검토 하기 위한 "데이터베이스와 동기화" 창의 스크린샷](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. 데이터를 마이그레이션합니다. **Mysql 메타 데이터 탐색기** 창에서 마이그레이션할 MySQL 스키마를 마우스 오른쪽 단추로 클릭 한 다음 **데이터 마이그레이션** 을 선택 합니다. 또는 오른쪽 위에 있는 **데이터 마이그레이션** 탭을 선택할 수 있습니다.

   전체 데이터베이스의 데이터를 마이그레이션하려면 데이터베이스 이름 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 마이그레이션하려면 데이터베이스를 확장 하 고 **테이블** 을 확장 한 다음 테이블 옆의 확인란을 선택 합니다. 개별 테이블에서 데이터를 생략하려면 확인란의 선택을 취소합니다.

   !["MySQL 메타 데이터 탐색기" 창에 있는 "데이터 마이그레이션" 명령의 스크린샷](./media/mysql-to-sql-database-guide/migrate-data.png)

1. 마이그레이션이 완료 되 면 **데이터 마이그레이션 보고서** 를 확인 합니다.
   
   ![데이터 마이그레이션 보고서의 스크린샷](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 를 사용 하 여 SQL database에 연결 하 고 데이터 및 스키마를 검토 하 여 마이그레이션 유효성을 검사 합니다.

   ![SQL Server Management Studio의 스크린샷](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>마이그레이션 후 

*마이그레이션* 단계를 성공적으로 완료 한 후에는 모든 것이 가능한 한 원활 하 고 효율적으로 작동 하는지 확인 하기 위해 일련의 마이그레이션 후 작업을 완료 해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션에 대 한 테스트 접근 방식은 다음과 같은 작업으로 구성 됩니다.

1. **유효성 검사 테스트 개발**: 데이터베이스 마이그레이션을 테스트 하려면 SQL 쿼리를 사용 해야 합니다. 원본 및 대상 데이터베이스에 대해 실행 되는 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리가 정의한 범위를 포함 해야 합니다.

1. **테스트 환경 설정**: 테스트 환경에 원본 데이터베이스와 대상 데이터베이스의 복사본을 포함 해야 합니다. 테스트 환경을 격리해야 합니다.

1. **유효성 검사 테스트 실행**: 소스와 대상에 대해 유효성 검사 테스트를 실행 한 다음 결과를 분석 합니다.

1. **성능 테스트 실행**: 소스와 대상에 대해 성능 테스트를 실행 한 다음 결과를 분석 하 고 비교 합니다.

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정 하 고, 완전성을 확인 하 고, 워크 로드와 관련 된 성능 문제를 해결 하는 데 중요 합니다.

이러한 문제와 이러한 문제를 완화 하는 단계에 대 한 자세한 내용은 [마이그레이션 후 유효성 검사 및 최적화 가이드](/sql/relational-databases/post-migration-validation-and-optimization-guide)를 참조 하세요.

## <a name="migration-assets"></a>마이그레이션 자산

이 마이그레이션 시나리오를 완료 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요. 실제 마이그레이션 프로젝트 참여를 지원 하기 위해 개발 되었습니다.

| 제목 | Description |
| --- | --- |
| [데이터 워크로드 평가 모델 및 도구](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 는 지정 된 작업에 대해 제안 된 "최적" 대상 플랫폼, 클라우드 준비 상태, 응용 프로그램/데이터베이스 재구성 수준을 제공 합니다. 이는 자동화 된 단일 대상 플랫폼 의사 결정 프로세스를 제공 하 여 방대한 부동산 평가를 가속화 하는 간단한 단일 클릭 계산 및 보고서 생성을 제공 합니다. |

데이터 SQL 엔지니어링 팀이이 리소스를 개발 했습니다. 팀의 핵심 기본은 Microsoft의 Azure 데이터 플랫폼으로 데이터 플랫폼 마이그레이션 프로젝트에 대 한 복잡 한 현대화의 차단을 해제 하 고 가속화 하는 것입니다.

## <a name="next-steps"></a>다음 단계 

- 워크 로드를 Azure로 마이그레이션하여 비용 절감 액을 예측할 수 있도록 [azure 총 소유 비용 계산기](https://aka.ms/azure-tco)를 참조 하세요.

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구에 대 한 자세한 내용은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- 다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하세요. 

- 마이그레이션 비디오 [는 마이그레이션 경험 개요 및 권장 되는 마이그레이션 및 평가 도구 및 서비스](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)를 참조 하세요.
