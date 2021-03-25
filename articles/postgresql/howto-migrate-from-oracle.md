---
title: 'Oracle to Azure Database for PostgreSQL: 마이그레이션 가이드'
titleSuffix: Azure Database for PostgreSQL
description: 이 가이드에서는 Oracle 스키마를 Azure Database for PostgreSQL로 마이그레이션하는 방법을 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: b41f894a7e4742b75ea06684a960221d4a5b7641
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024764"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle을 Azure Database for PostgreSQL로 마이그레이션

이 가이드에서는 Oracle 스키마를 Azure Database for PostgreSQL로 마이그레이션하는 방법을 설명 합니다. 

자세한 마이그레이션 지침 및 포괄적인 마이그레이션 [가이드는 마이그레이션 가이드 리소스](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)를 참조 하세요. 

## <a name="prerequisites"></a>사전 요구 사항

Oracle 스키마를 Azure Database for PostgreSQL 마이그레이션하려면 다음을 수행 해야 합니다. 

- 원본 환경이 지원 되는지 확인 합니다. 
- 최신 버전의 [ora2pg](https://ora2pg.darold.net/)를 다운로드 합니다. 
- 최신 버전의 [Dbd 모듈](https://www.cpan.org/modules/by-module/DBD/)입니다. 


## <a name="overview"></a>개요

PostgreSQL은 세계 최고 수준의 고급 오픈 소스 데이터베이스 중 하나입니다. 이 문서에서는 무료 ora2pg 유틸리티를 사용 하 여 Oracle 데이터베이스를 PostgreSQL로 마이그레이션하는 방법에 대해 설명 합니다. 무료 도구인 ora2pg를 사용 하 여 Oracle 또는 MySQL 데이터베이스를 PostgreSQL 호환 스키마로 마이그레이션할 수 있습니다. 이 유틸리티는 Oracle 데이터베이스를 연결 하 고, 자동으로 검색 하 고, 구조 또는 데이터를 추출 합니다. 그런 다음 ora2pg는 PostgreSQL 데이터베이스에 로드할 수 있는 SQL 스크립트를 생성 합니다. ora2pg은 Oracle 데이터베이스를 리버스 엔지니어링 하거나, 대규모 엔터프라이즈 데이터베이스 마이그레이션을 수행 하거나, 일부 Oracle 데이터를 PostgreSQL 데이터베이스로 복제 하는 작업에 사용할 수 있습니다. 사용 하기 쉬우며 Oracle 데이터베이스에 연결 하는 데 필요한 매개 변수를 제공 하는 기능 외에 Oracle 데이터베이스 지식이 필요 하지 않습니다.

> [!NOTE]
> 최신 버전의 ora2pg를 사용 하는 방법에 대 한 자세한 내용은 [ora2pg 설명서](https://ora2pg.darold.net/documentation.html)를 참조 하세요.

### <a name="typical-ora2pg-migration-architecture"></a>일반적인 ora2pg 마이그레이션 아키텍처

![Ora2pg 마이그레이션 아키텍처의 스크린샷](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

VM을 프로 비전 하 고 Azure Database for PostgreSQL 후 다음과 같이 **Azure 서비스를 허용** 하 고 **SSL 연결을 적용** 하도록 두 가지 구성이 필요 합니다.

- **연결 보안** 블레이드-> **Azure 서비스에 대 한 액세스를 허용 합니다** .->

- **연결 보안** 블레이드- **ssl 설정**>  ->  **ssl 연결 적용** -> 사용 안 함

### <a name="recommendations"></a>권장 사항

- Oracle 서버에서 평가 또는 내보내기 작업의 성능을 향상 시키려면 다음과 같이 통계를 수집 합니다.

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- INSERT 대신 복사 명령을 사용 하 여 데이터를 내보냅니다.

- FKs, 제약 조건 및 인덱스를 사용 하 여 테이블을 내보내지 않습니다. 이렇게 하면 데이터를 PostgreSQL로 가져오는 속도가 느려집니다.

- **No data 절** 을 사용 하 여 구체화 된 뷰를 만들고 나중에 새로 고칩니다.

- 가능 하면 구체화 된 뷰에서 고유 인덱스를 구현 합니다. 그러면 구문을 사용 하 여 새로 고침이 더 빠르게 수행 됩니다 `REFRESH MATERIALIZED VIEW CONCURRENTLY` .



## <a name="pre-migration"></a>사전 마이그레이션 

원본 환경이 지원 되는지 확인 하 고 모든 필수 구성 요소를 해결 한 후에는 마이그레이션 전 단계를 시작할 준비가 된 것입니다. 프로세스의 이 부분에서는 마이그레이션해야 하는 데이터베이스에 대한 인벤토리를 수행하고, 해당 데이터베이스에 잠재적인 마이그레이션 문제나 방해 요소가 있는지 평가한 다음, 발견했을 수 있는 모든 항목을 확인합니다. Azure Database for PostgreSQL Oracle과 같은 다른 유형의 마이그레이션의 경우이 단계에서는 원본 데이터베이스의 스키마를 대상 환경과 호환 되도록 변환 하는 작업도 수행 합니다.

### <a name="discover"></a>검색

검색 단계의 목표는 기존 데이터 원본 및 마이그레이션에 대 한 이해를 개선 하는 데 사용 되는 기능에 대 한 세부 정보를 확인 하는 것입니다. 이 프로세스에서는 네트워크를 검색하여 사용 중인 버전 및 기능과 함께 조직의 모든 Oracle 인스턴스를 파악합니다.

Oracle 데이터베이스에 대해 Microsoft Oracle 사전 평가 스크립트가 실행 됩니다. 사전 평가 스크립트는 Oracle 메타 데이터에 도달 하는 쿼리 집합으로, 다음을 제공 합니다.

- 스키마, 유형 및 상태별 개체 개수를 포함 하는 데이터베이스 인벤토리

- 통계를 기반으로 하는 각 스키마의 원시 데이터에 대 한 대략적인 예상 값입니다.

- 각 스키마의 테이블 크기 조정

- 패키지, 함수, 프로시저 등의 코드 줄 수입니다.

[Ora2pg 웹 사이트](http://ora2pg.darold.net/)에서 관련 스크립트를 다운로드 합니다.

### <a name="assess"></a>평가

Oracle 데이터베이스의 인벤토리를 완료 하 여 데이터베이스 크기와 당면 과제를 파악 한 후 다음 단계는 평가를 실행 하는 것입니다.

Oracle에서 PostgreSQL로 마이그레이션 프로세스의 비용을 예측 하는 것은 쉽지 않습니다. 이 마이그레이션 비용에 대 한 적절 한 평가를 얻기 위해 ora2pg는 모든 데이터베이스 개체, 모든 함수 및 저장 프로시저를 검사 하 여 ora2pg에서 자동으로 변환할 수 없는 일부 개체 및 PL/SQL 코드가 있는지 확인 합니다.

ora2pg에는 oracle 데이터베이스를 검사 하 여 Oracle 데이터베이스에 포함 된 내용 및 내보낼 수 없는 항목에 대 한 텍스트 보고서를 생성 하는 내용 분석 모드가 있습니다.

**분석 및 보고서** 모드를 활성화 하려면 `SHOW_REPORT` 다음 명령에 표시 된 것 처럼 내보낸 유형을 사용 합니다.

```
ora2pg -t SHOW_REPORT
```

데이터베이스를 분석 한 후 SQL 및 PL/SQL 코드를 Oracle 구문에서 PostgreSQL로 변환 하는 기능을 사용 하 여 코드 문제를 예측 하 고 전체 데이터베이스 마이그레이션을 수행 하는 데 필요한 시간을 ora2pg 수 있습니다.

Ora2pg을 통해 마이그레이션 비용을 예측 하기 위해 명령줄에서 사용할 수 있는 ESTIMATE_COST 이라는 구성 지시어를 사용할 수 있습니다.

```
ora2pg -t SHOW_REPORT --estimate_cost
```

기본 마이그레이션 단위는 PostgreSQL 전문가의 약 5 분을 나타냅니다. 첫 번째 마이그레이션 인 경우 구성 지시문 COST_UNIT_VALUE 또는--cost_unit_value 명령줄 옵션을 사용 하 여 더 높은 수준으로 가져올 수 있습니다.

보고서의 마지막 줄에는 각 개체에 대해 예상 되는 마이그레이션 단위 수에 따라 일의 총 예상 마이그레이션 코드가 표시 됩니다.

이 마이그레이션 단위는 PostgreSQL 전문가의 약 5 분을 나타냅니다. 처음으로 마이그레이션하는 경우 구성 지시문 COST_UNIT_VALUE 또는--cost_unit_value 명령줄 옵션을 사용 하 여 기본값을 늘릴 수 있습니다. 평가 a의 일부 변형 테이블 평가를 찾습니다. b) 열 평가 c) 스키마 평가 기본 cost_unit (5 분) d) 스키마 평가를 사용 하 여 10 분을 비용 단위로 사용 합니다.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

스키마 평가의 출력은 다음과 같습니다.

**마이그레이션 수준: B-5**

마이그레이션 수준:

A-자동으로 실행 될 수 있는 마이그레이션

B-코드 재작성을 사용 하 여 마이그레이션 및 사용자 일 비용 (최대 5 일)

C-코드 재작성을 사용한 마이그레이션 및 5 일 이상 사용자 일 비용

기술 수준:

1 = trivial: 저장 된 함수 및 트리거 없음

2 = easy: 저장 된 함수가 없지만 트리거, 수동 재작성 안 함

3 = 단순: 저장 된 함수 및/또는 트리거, 수동 재작성 안 함

4 = manual: 저장 된 함수는 없지만 코드 재작성이 포함 된 트리거 또는 뷰가 있습니다.

5 = 어려워집니다: 코드 재작성이 있는 저장 된 함수 및/또는 트리거

평가는 문자 (A 또는 B)로 구성 되어 마이그레이션을 수동으로 다시 작성 해야 하는지 여부를 지정 하 고, 1에서 5 사이의 숫자를 지정 하 여 기술 난이도 수준을 나타냅니다. 추가 human_days_limit 옵션을 사용 하 여 마이그레이션 수준을 C로 설정 해야 하는 사용자 일 제한 수를 지정할 수 있습니다 .이 경우에는 상당한 양의 작업이 필요 하 고 마이그레이션 지원에 대 한 전체 프로젝트 관리가 필요 하다는 것을 나타낼 수 있습니다. 기본값은 10 분입니다. 구성 지시문 HUMAN_DAYS_LIMIT를 사용 하 여이 기본값을 영구적으로 변경할 수 있습니다.

이 기능은 먼저 마이그레이션할 데이터베이스를 결정 하 고 mobilized 해야 하는 팀을 결정 하는 데 도움이 되도록 개발 되었습니다.

### <a name="convert"></a>변환

가동 중지 시간을 최소화 하 고 마이그레이션 하는 원본은 일회성 마이그레이션 후 데이터 및 스키마를 기준으로 대상에서 유동 계속 변경 됩니다. **데이터 동기화** 단계 중에 원본에 있는 모든 변경 내용이 캡처되고 거의 실시간으로 대상에 적용 되는지 확인 해야 합니다. 원본에 있는 모든 변경 내용이 대상에 적용 되었는지 확인 한 후 원본에서 대상 환경으로 건너뛸 수 있습니다.

마이그레이션의이 단계에서는 Oracle 코드 + DDLS에서 PostgreSQL로의 변환이 나 변환이 발생 합니다. Ora2pg 도구는 PostgreSQL 형식의 Oracle 개체를 자동으로 내보냅니다. 생성 되는 개체의 경우 일부는 수동으로 변경 하지 않고 PostgreSQL 데이터베이스에서 컴파일되지 않습니다.  
수동 개입이 필요한 요소를 이해 하는 과정은 PostgreSQL 데이터베이스에 대해 ora2pg에서 생성 된 파일을 컴파일하고, 로그를 확인 하 고, 모든 스키마 구조가 PostgreSQL 구문과 호환 될 때까지 필요한 변경을 수행 하는 것으로 구성 됩니다.


#### <a name="create-migration-template"></a>마이그레이션 템플릿 만들기 

먼저 ora2pg를 사용 하 여 기본 제공 되는 마이그레이션 템플릿을 만드는 것이 좋습니다. Project_base 및--init_project를 사용 하는 경우이 옵션을 사용 하면 작업 트리, 구성 파일 및 Oracle 데이터베이스의 모든 개체를 내보내는 스크립트를 사용 하 여 프로젝트 템플릿을 만들어야 한다는 ora2pg이 표시 됩니다. 자세한 내용은 [ora2pg 설명서](https://ora2pg.darold.net/documentation.html)를 참조 하세요.

   다음 명령을 사용합니다. 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

예제 출력: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

원본/디렉터리에는 Oracle 코드가 포함 되 고, schema/directory에는 PostgreSQL로 이식 되는 코드가 포함 됩니다. 보고서/디렉터리에는 마이그레이션 비용 평가를 포함 하는 html 보고서가 포함 되어 있습니다.


프로젝트 구조를 만든 후에는 일반 구성 파일이 만들어집니다. Oracle 데이터베이스 연결 및 구성의 관련 구성 매개 변수를 정의 합니다.  구성 파일에서 구성할 수 있는 항목 및 방법에 대해 알아보려면 ora2pg 설명서를 참조 하세요.


#### <a name="export-oracle-objects"></a>Oracle 개체 내보내기

그런 다음 export_schema. sh 파일을 실행 하 여 Oracle 개체를 PostgreSQL 개체로 내보냅니다.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
   SET namespace="/app/migration/mig_project"
   
   ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
   %namespace%/config/ora2pg.conf
   ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
   %namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
   ora2pg -p -t PACKAGE -o packages.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
   ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
   ora2pg -p -t PROCEDURE -o procs.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
   ora2pg -t SEQUENCE -o sequences.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
   ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
   %namespace%/config/ora2pg.conf
   ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
   %namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
   ```

   데이터를 추출 하려면 다음 명령을 사용 합니다.

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>파일 컴파일

마지막으로 Azure Database for PostgreSQL 서버에 대 한 모든 파일을 컴파일합니다. 이제 수동으로 생성 된 DDL 파일을 로드 하거나 두 번째 스크립트 import_all를 사용 하 여 해당 파일을 대화형으로 가져올 수 있습니다.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   데이터 가져오기 명령:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

파일을 컴파일하는 동안 로그를 확인 하 고 ora2pg가 기본으로 변환 하지 못한 필요한 구문을 수정 합니다.

문제 해결에 대 한 지원에 대 한 자세한 내용은 [Azure Database for PostgreSQL Oracle](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) 백서를 참조 하십시오.

## <a name="migrate"></a>마이그레이션 

필요한 필수 조건을 준비하고 **마이그레이션 전** 단계와 관련된 작업을 완료했으면 스키마 및 데이터 마이그레이션을 수행할 준비가 된 것입니다.

### <a name="migrate-schema-and-data"></a>스키마 및 데이터 마이그레이션

수정이 준비 되 면 데이터베이스의 안정적인 빌드를 배포할 준비가 된 것입니다.

이 시점에서는 PostgreSQL 데이터베이스에 대해 데이터베이스 개체를 컴파일하고 데이터를 가져오기 위해 수정 된 코드를 포함 하는 파일을 가리키는 *psql* 가져오기 명령만 실행 하면 됩니다.

이 단계에서는 데이터를 가져올 때의 병렬 처리 수준을 구현할 수 있습니다.

### <a name="data-sync-and-cutover"></a>데이터 동기화 및 가공선

온라인 (최소 가동 중지 시간) 마이그레이션을 사용 하 여 마이그레이션 중인 원본은 일회성 마이그레이션이 발생 한 후에도 데이터 및 스키마를 기준으로 대상에서 유동 계속 변경 됩니다. **데이터 동기화** 단계 중에 원본에 있는 모든 변경 내용이 캡처되고 거의 실시간으로 대상에 적용 되는지 확인 해야 합니다. 원본에 있는 모든 변경 내용이 대상에 적용 되었는지 확인 한 후 원본에서 대상 환경으로 건너뛸 수 있습니다.

3 월 2019을 기준으로 온라인 마이그레이션을 수행 하려는 경우 Microsoft migration 또는 Striim에 대해 Attunity 복제를 사용 하는 것이 좋습니다.

Ora2pg를 사용 하는 *델타/증분* 마이그레이션의 경우이 기술은 각 테이블에 대해 필터 (잘라내기)를 날짜 또는 시간으로 적용 하는 쿼리를 적용 하는 것으로 구성 되며 나머지 데이터를 마이그레이션하는 두 번째 쿼리를 적용 하 여 마이그레이션을 완료 한 후에는 나머지 데이터를 마이그레이션합니다 (남겨진).

원본 데이터 테이블에서 모든 기록 데이터를 먼저 마이그레이션합니다. 예를 들면 다음과 같습니다.

```
select * from table1 where filter_data < 01/01/2019
```

다음과 유사한 명령을 실행 하 여 초기 마이그레이션 이후 변경 된 내용을 쿼리할 수 있습니다.

```
select * from table1 where filter_data >= 01/01/2019
```

이 경우, 원본 및 대상 모두에서 데이터 패리티를 검사 하 여 유효성 검사를 개선 하는 것이 좋습니다.

## <a name="post-migration"></a>마이그레이션 후 

**마이그레이션** 단계를 성공적으로 완료한 후 모든 것이 최대한 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 수행해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이를 위해 애플리케이션을 변경해야 하는 경우도 있습니다.

### <a name="perform-tests"></a>테스트 수행

데이터를 대상으로 마이그레이션한 후에는 데이터베이스에 대 한 테스트를 수행 하 여 마이그레이션 후 응용 프로그램이 대상에 대해 잘 수행 되는지 확인 합니다.

원본 및 대상이 제대로 마이그레이션 되도록 하려면 Oracle 원본 및 PostgreSQL 대상 데이터베이스에 대해 수동 데이터 유효성 검사 스크립트를 실행 합니다.

이상적으로 원본 및 대상 데이터베이스에 네트워킹 경로가 있는 경우 데이터 유효성 검사에 ora2pg를 사용 해야 합니다. 테스트 작업을 사용 하면 Oracle 데이터베이스의 모든 개체가 PostgreSQL 아래에 생성 되었는지 확인할 수 있습니다. 다음과 같이 명령을 실행 합니다.

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정하고 완성도를 확인할 뿐만 아니라 워크로드 관련 성능 문제를 해결하는 데 매우 중요합니다.

## <a name="migration-assets"></a>마이그레이션 자산 

이 마이그레이션 시나리오를 완료하는 데 추가 지원이 필요한 경우 실제 마이그레이션 프로젝트 참여를 지원하여 개발된 다음 리소스를 참조하세요.

| **제목 링크** | **설명**    |
| -------------- | ------------------ |
| [Oracle에서 Azure PostgreSQL로 마이그레이션 쿡북](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | 이 문서에서는 ora2pg 도구를 사용 하 여 Oracle에서 Azure Database for PostgreSQL로 신속 하 게 작업을 마이그레이션하는 방법에 대 한 지침을 제공 하는 설계자, 컨설턴트, Dba 및 관련 역할을 제공 합니다. |
| [Oracle에서 Azure PostgreSQL로 마이그레이션 해결 방법](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | 이 문서에서는 작업을 Oracle에서 Azure Database for PostgreSQL로 마이그레이션하는 동안 문제를 신속 하 게 해결 하 고 해결 하기 위한 가이드를 설계자, 컨설턴트, Dba 및 관련 역할에 제공 합니다. |
| [Windows 또는 Linux에서 ora2pg를 설치 하는 단계](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | 이 문서는 Windows 또는 Linux에서 ora2pg 도구를 사용 하 여 Oracle에서 Azure Database for PostgreSQL 스키마 & 데이터를 마이그레이션할 수 있도록 하는 빠른 설치 가이드로 사용 됩니다. 이 도구에 대 한 전체 세부 정보는에서 찾을 수 있습니다 http://ora2pg.darold.net/documentation.html . |

위 리소스는 Azure 데이터 그룹 엔지니어링 팀에서 후원하는 Data SQL Ninja 프로그램의 일부로 개발되었습니다. Data SQL Ninja 프로그램의 핵심 선언은 복잡한 현대화의 장애물을 제거하고 속도를 높이며 Microsoft의 Azure 데이터 플랫폼에 대한 데이터 플랫폼 마이그레이션 기회를 놓고 경쟁하는 것입니다. 조직이 Data SQL Ninja 프로그램에 참여하는 데 관심이 있다고 생각되면 계정 팀에 문의하여 추천서를 제출하도록 요청하세요.


### <a name="contact-support"></a>기술 지원 서비스에 문의하십시오.

Ora2pg 도구 외의 마이그레이션에 대 한 지원이 필요한 경우 [ @Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) 별칭에 다른 마이그레이션 옵션에 대 한 자세한 내용을 문의 하세요.

## <a name="next-steps"></a>다음 단계

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스/도구에 대 한 매트릭스는 [데이터 마이그레이션을 위한 서비스 및 도구](https://docs.microsoft.com/azure/dms/dms-tools-matrix)문서를 참조 하세요.

자세한 내용은 다음을 참조하세요. 
- [Azure Database for PostgreSQL 설명서](https://docs.microsoft.com/azure/postgresql/)
- [ora2pg 설명서](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL 웹 사이트](https://www.postgresql.org/)
- [PostgreSQL의 자치 트랜잭션 지원](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

비디오 콘텐츠: 
- [Overview of the migration journey and the tools/services recommended for performing assessment and migration](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)(평가 및 마이그레이션을 수행하는 데 권장되는 도구/서비스 및 마이그레이션 과정에 대한 개요)