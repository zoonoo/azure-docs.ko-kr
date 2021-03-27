---
title: 'Azure SQL Database에 대 한 액세스: 마이그레이션 가이드'
description: 이 가이드에서는 access에 대 한 SQL Server Migration Assistant (Access 용 SSMA)를 사용 하 여 Microsoft Access 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법에 대해 알아봅니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 48fe734b382d661f96a86ede181a1258e38120a1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626540"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>마이그레이션 가이드: Azure SQL Database에 대 한 액세스

이 가이드에서는 access에 대 한 SQL Server Migration Assistant (Access 용 SSMA)를 사용 하 여 Microsoft Access 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법에 대해 알아봅니다.

다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](https://docs.microsoft.com/data-migration)를 참조 하세요. 

## <a name="prerequisites"></a>전제 조건

SQL 데이터베이스로 Access 데이터베이스 마이그레이션을 시작 하기 전에 다음을 수행 합니다.

- 원본 환경이 지원 되는지 확인 합니다. 
- [액세스를 위해 SQL Server Migration Assistant](https://www.microsoft.com/download/details.aspx?id=54255)를 다운로드 하 고 설치 합니다.
- 원본 및 대상 모두에 액세스할 수 있는 연결 및 권한이 있는지 확인 하십시오.

## <a name="pre-migration"></a>마이그레이션 전

필수 구성 요소를 충족 하 고 나면 환경의 토폴로지를 검색 하 고 마이그레이션의 실현 가능성을 평가할 준비가 된 것입니다.


### <a name="assess"></a>평가 

Access 용 SSMA를 사용 하 여 데이터베이스 개체 및 데이터를 검토 하 고 마이그레이션할 데이터베이스를 평가 합니다. 

평가를 만들려면 다음을 수행 합니다. 

1. [Access 용 Ssma를](https://www.microsoft.com/download/details.aspx?id=54255)엽니다. 
1. **파일** 을 선택한 다음 **새 프로젝트** 를 선택 합니다. 
1. 프로젝트 이름과 프로젝트 위치를 입력 한 다음 드롭다운 목록에서 **Azure SQL Database** 를 마이그레이션 대상으로 선택 합니다. 
1. **확인** 을 선택합니다. 

   ![마이그레이션 프로젝트 이름 및 위치를 입력 하기 위한 "새 프로젝트" 창의 스크린샷](./media/access-to-sql-database-guide/new-project.png)

1. **데이터베이스 추가** 를 선택한 다음, 새 프로젝트에 추가할 데이터베이스를 선택 합니다. 

   ![Access 용 SSMA의 "데이터베이스 추가" 탭 스크린샷](./media/access-to-sql-database-guide/add-databases.png)

1. **액세스 메타 데이터 탐색기** 창에서 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **보고서 만들기** 를 선택 합니다. 또는 오른쪽 위에 있는 **보고서 만들기** 탭을 선택할 수 있습니다.

   ![액세스 메타 데이터 탐색기에 있는 "보고서 만들기" 명령의 스크린샷](./media/access-to-sql-database-guide/create-report.png)

1. HTML 보고서를 검토 하 여 변환 통계 및 오류 또는 경고를 파악 합니다. Excel에서 보고서를 열어 Access 개체의 인벤토리를 가져오고 스키마 변환을 수행 하는 데 필요한 작업을 이해할 수도 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더에 있습니다. 예를 들어:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![SSMA의 예제 데이터베이스 보고서 평가 스크린샷](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>데이터 형식 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사 하 고 필요한 경우 요구 사항에 따라 변경 합니다. 이를 수행하려면:

1. Access 용 SSMA에서 **도구** 를 선택한 다음 **프로젝트 설정** 을 선택 합니다. 
1. **유형 매핑** 탭을 선택 합니다. 

   ![Access 용 SSMA의 "형식 매핑" 창 스크린샷](./media/access-to-sql-database-guide/type-mappings.png)

1. **액세스 메타 데이터 탐색기** 창에서 테이블 이름을 선택 하 여 각 테이블에 대 한 형식 매핑을 변경할 수 있습니다.


### <a name="convert-the-schema"></a>스키마 변환

데이터베이스 개체를 변환 하려면 다음을 수행 합니다. 

1. **Azure SQL Database에 연결** 탭을 선택 하 고 다음을 수행 합니다.

   a. SQL database에 연결 하는 데 필요한 세부 정보를 입력 합니다.  
   b. 드롭다운 목록에서 대상 SQL 데이터베이스를 선택 합니다. 또는 새 이름을 입력할 수 있습니다 .이 경우 대상 서버에 데이터베이스가 생성 됩니다.  
   다. 인증 세부 정보를 제공 합니다.   
   d. **연결** 을 선택합니다.

   ![연결 정보를 입력 하기 위한 "Azure SQL Database에 연결" 창의 스크린샷](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. **액세스 메타 데이터 탐색기** 창에서 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **스키마 변환** 을 선택 합니다. 또는 데이터베이스를 선택 하 고 **스키마 변환** 탭을 선택할 수 있습니다.

   !["액세스 메타 데이터 탐색기" 창에 있는 "스키마 변환" 명령의 스크린샷](./media/access-to-sql-database-guide/convert-schema.png)

1. 변환이 완료 된 후 변환 된 개체를 원래 개체와 비교 하 여 잠재적인 문제를 식별 하 고 권장 사항에 따라 문제를 해결 합니다.

   ![변환 된 개체와 원본 개체의 비교를 보여 주는 스크린샷](./media/access-to-sql-database-guide/table-comparison.png)

    변환 된 Transact-sql 텍스트를 원래 코드와 비교 하 고 권장 사항을 검토 합니다.

   ![변환 된 쿼리를 소스 코드와 비교 하는 방법을 보여 주는 스크린샷](./media/access-to-sql-database-guide/query-comparison.png) 

1. 필드 개별 개체를 변환 하려면 개체를 마우스 오른쪽 단추로 클릭 한 다음 **스키마 변환** 을 선택 합니다. 변환 된 개체는 **Access 메타 데이터 탐색기** 에서 굵은 텍스트로 표시 됩니다. 

   ![Access Metadata Explorer의 개체가 변환 되었음을 보여 주는 스크린샷](./media/access-to-sql-database-guide/converted-items.png)
 
1. **출력** 창에서 **결과 검토** 아이콘을 선택 하 고 **오류 목록** 창에서 오류를 검토 합니다. 
1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. 이렇게 하려면 **파일**  >  **프로젝트 저장** 을 선택 합니다. 이를 통해 원본 및 대상 스키마를 오프 라인으로 평가 하 고 SQL database에 게시 하기 전에 수정을 수행할 수 있습니다.

## <a name="migrate-the-databases"></a>데이터베이스 마이그레이션

데이터베이스를 평가 하 고 불일치를 해결 한 후 마이그레이션 프로세스를 실행할 수 있습니다. 데이터 마이그레이션은 트랜잭션에서 Azure SQL database로 데이터 행을 이동 하는 대량 로드 작업입니다. 각 트랜잭션에서 SQL database로 로드 되는 행 수는 프로젝트 설정에서 구성 됩니다.

Access 용 SSMA를 사용 하 여 스키마를 게시 하 고 데이터를 마이그레이션하려면 다음을 수행 합니다. 

1. 아직 수행 하지 않은 경우 **Azure SQL Database 연결** 을 선택 하 고 연결 정보를 제공 합니다. 

1. 스키마를 게시 합니다. **Azure SQL Database 메타 데이터 탐색기** 창에서 작업 중인 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **데이터베이스와 동기화** 를 선택 합니다. 이 작업을 수행 하면 MySQL 스키마가 SQL 데이터베이스에 게시 됩니다.

1. **데이터베이스와 동기화** 창에서 원본 프로젝트와 대상 간의 매핑을 검토 합니다.

   ![데이터베이스와의 동기화를 검토 하기 위한 "데이터베이스와 동기화" 창의 스크린샷](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. **액세스 메타 데이터 탐색기** 창에서 마이그레이션하려는 항목 옆에 있는 확인란을 선택 합니다. 전체 데이터베이스를 마이그레이션하려면 데이터베이스 옆의 확인란을 선택 합니다. 

1. 데이터를 마이그레이션합니다. 마이그레이션하려는 데이터베이스 또는 개체를 마우스 오른쪽 단추로 클릭 한 다음 **데이터 마이그레이션** 을 선택 합니다. 또는 오른쪽 위에 있는 **데이터 마이그레이션** 탭을 선택할 수 있습니다.  

   전체 데이터베이스의 데이터를 마이그레이션하려면 데이터베이스 이름 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 마이그레이션하려면 데이터베이스를 확장 하 고 **테이블** 을 확장 한 다음 테이블 옆의 확인란을 선택 합니다. 개별 테이블에서 데이터를 생략하려면 확인란의 선택을 취소합니다.

    !["액세스 메타 데이터 탐색기" 창에 있는 "데이터 마이그레이션" 명령의 스크린샷](./media/access-to-sql-database-guide/migrate-data.png)

1. 마이그레이션이 완료 되 면 **데이터 마이그레이션 보고서** 를 확인 합니다.  

    ![검토할 예제 보고서를 보여 주는 "데이터 보고서 마이그레이션" 창의 스크린샷](./media/access-to-sql-database-guide/migrate-data-review.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용 하 여 Azure SQL database에 연결 하 고 데이터 및 스키마를 검토 하 여 마이그레이션의 유효성을 검사 합니다.

   ![SSMA에서 마이그레이션의 유효성을 검사 하기 위한 SQL Server Management Studio 개체 탐색기 스크린샷](./media/access-to-sql-database-guide/validate-data.png)

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

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구에 대 한 자세한 내용은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- Azure SQL Database에 대한 자세한 정보는 다음을 참조하세요.
   - [SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로의 마이그레이션을 위한 작업의 비용을 계산 하 고 크기를 조정 하는 최선의 방법](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미 개요](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.
