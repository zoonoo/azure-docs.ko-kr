---
title: 'SAP ASE to Azure SQL Database: 마이그레이션 가이드'
description: 이 가이드에서는 SAP 어댑터 서버 엔터프라이즈에 대 한 SQL Server Migration Assistant를 사용 하 여 SAP ASE 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법에 대해 알아봅니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 138a23b610ab96194424bb0f88cf94f516c2d223
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626455"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>마이그레이션 가이드: SAP ASE to Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 SAP Adapter Server Enterprise에 대 한 SQL Server Migration Assistant를 사용 하 여 Azure ASE (SAP Adapter Server Enterprise) 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법에 대해 알아봅니다.

다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](https://docs.microsoft.com/data-migration)를 참조 하세요. 

## <a name="prerequisites"></a>전제 조건 

SAP SE 데이터베이스를 SQL database로 마이그레이션하기 전에 다음을 수행 합니다.

- 원본 환경이 지원 되는지 확인 합니다. 
- [Sap 적응 서버 Enterprise (이전의 Sap SYBASE ASE) 용 SQL Server Migration Assistant를](https://www.microsoft.com/en-us/download/details.aspx?id=54256)다운로드 하 여 설치 합니다.
- 원본 및 대상 모두에 액세스할 수 있는 연결 및 권한이 있는지 확인 하십시오.

## <a name="pre-migration"></a>마이그레이션 전

필수 구성 요소를 충족 하 고 나면 환경의 토폴로지를 검색 하 고 마이그레이션의 실현 가능성을 평가할 준비가 된 것입니다.

### <a name="assess"></a>평가

[Sap 적응 서버 Enterprise (공식 Sap SYBASE ASE)에 대해 SSMA (SQL Server Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=54256))를 사용 하 여 데이터베이스 개체 및 데이터를 검토 하 고, 마이그레이션을 위해 데이터베이스를 평가 하 고, Sybase 데이터베이스 개체를 sql 데이터베이스로 마이그레이션하고, 데이터를 sql 데이터베이스로 마이그레이션할 수 있습니다. 자세히 알아보려면 [Sybase용 SQL Server Migration Assistant(SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)를 참조하세요.

평가를 만들려면 다음을 수행 합니다. 

1. Sybase용 SSMA를 엽니다. 
1. **파일** 을 선택한 다음 **새 프로젝트** 를 선택 합니다. 
1. **새 프로젝트** 창에서 프로젝트의 이름과 위치를 입력 하 고 다음 **으로 마이그레이션** 드롭다운 목록에서 **Azure SQL Database** 을 선택 합니다. 
1. **확인** 을 선택합니다.
1. **Sybase에 연결** 창에서 SAP 연결 정보를 입력 합니다. 
1. 마이그레이션하려는 SAP 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **보고서 만들기** 를 선택 합니다. 이렇게 하면 HTML 보고서가 생성됩니다. 또는 오른쪽 위에 있는 **보고서 만들기** 탭을 선택할 수 있습니다.
1. HTML 보고서를 검토 하 여 변환 통계 및 오류 또는 경고를 파악 합니다. Excel에서 보고서를 열어 SAP ASE 개체의 인벤토리 및 스키마 변환을 수행 하는 데 필요한 작업을 가져올 수도 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더에 있습니다. 예를 들어:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>형식 매핑의 유효성을 검사 합니다.

스키마 변환을 수행 하기 전에 기본 데이터 형식 매핑의 유효성을 검사 하거나 요구 사항에 따라 변경 합니다. **도구**  >  **프로젝트 설정** 을 선택 하 여이 작업을 수행 하거나, **SAP ASE 메타 데이터 탐색기** 에서 테이블을 선택 하 여 각 테이블에 대 한 형식 매핑을 변경할 수 있습니다.

### <a name="convert-the-schema"></a>스키마 변환

스키마를 변환 하려면 다음을 수행 합니다.

1. 필드 동적 또는 특수 쿼리를 변환 하려면 노드를 마우스 오른쪽 단추로 클릭 한 다음 **문 추가** 를 선택 합니다. 
1. **Azure SQL Database에 연결** 탭을 선택 하 고 SQL Database에 대 한 세부 정보를 입력 합니다. 기존 데이터베이스에 연결하거나 새 이름을 제공하도록 선택할 수 있습니다. 이 경우 대상 서버에 데이터베이스가 생성됩니다.
1. **Sybase 메타 데이터 탐색기** 창에서 작업 중인 SAP ASE 스키마를 마우스 오른쪽 단추로 클릭 한 다음 **스키마 변환** 을 선택 합니다. 
1. 스키마가 변환 된 후 변환 된 구조를 원래 구조와 비교 하 고 검토 하 여 잠재적인 문제를 식별 합니다. 

   스키마 변환 후 오프 라인 스키마 재구성 연습을 위해이 프로젝트를 로컬로 저장할 수 있습니다. 이렇게 하려면 **파일**  >  **프로젝트 저장** 을 선택 합니다. 이를 통해 원본 및 대상 스키마를 오프 라인으로 평가 하 고 스키마를 SQL 데이터베이스에 게시 하기 전에 수정 사항을 수행할 수 있습니다.

1. **출력** 창에서 **결과 검토** 를 선택 하 고 **오류 목록** 창에서 오류를 검토 합니다. 
1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. 이렇게 하려면 **파일**  >  **프로젝트 저장** 을 선택 합니다. 이를 통해 원본 및 대상 스키마를 오프 라인으로 평가 하 고 스키마를 SQL 데이터베이스에 게시 하기 전에 수정 사항을 수행할 수 있습니다.

## <a name="migrate-the-databases"></a>데이터베이스 마이그레이션 

필요한 필수 구성 요소를 준비 하 고 *마이그레이션 전* 단계와 관련 된 작업을 완료 한 후에는 스키마 및 데이터 마이그레이션을 실행할 준비가 된 것입니다.

스키마를 게시 하 고 데이터를 마이그레이션하려면 다음을 수행 합니다. 

1. 스키마를 게시 합니다. **Azure SQL Database 메타 데이터 탐색기** 창에서 데이터베이스를 마우스 오른쪽 단추로 클릭 한 다음 **데이터베이스와 동기화** 를 선택 합니다. 이 작업은 SAP ASE 스키마를 SQL 데이터베이스에 게시 합니다.

1. 데이터를 마이그레이션합니다. **SAP Ase 메타 데이터 탐색기** 창에서 마이그레이션할 sap ASE 데이터베이스 또는 개체를 마우스 오른쪽 단추로 클릭 한 다음 **데이터 마이그레이션** 을 선택 합니다. 또는 오른쪽 위에 있는 **데이터 마이그레이션** 탭을 선택할 수 있습니다. 

   전체 데이터베이스의 데이터를 마이그레이션하려면 데이터베이스 이름 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 마이그레이션하려면 데이터베이스를 확장 하 고 **테이블** 을 확장 한 다음 테이블 옆의 확인란을 선택 합니다. 개별 테이블에서 데이터를 생략하려면 확인란의 선택을 취소합니다. 
1. 마이그레이션이 완료 되 면 **데이터 마이그레이션 보고서** 를 확인 합니다. 
1. 데이터 및 스키마를 검토 하 여 마이그레이션 유효성을 검사 합니다. 이렇게 하려면 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용 하 여 SQL database에 연결 합니다.

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


## <a name="next-steps"></a>다음 단계

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구에 대 한 자세한 내용은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- Azure SQL Database에 대 한 자세한 내용은 다음을 참조 하세요.
   - [SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/)  

- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로의 마이그레이션을 위한 작업의 비용을 계산 하 고 크기를 조정 하는 최선의 방법](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.
