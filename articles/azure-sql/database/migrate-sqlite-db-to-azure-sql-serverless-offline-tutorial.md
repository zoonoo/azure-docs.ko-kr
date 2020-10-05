---
title: '자습서: SQLite 데이터베이스를 Azure SQL Database 서버리스로 마이그레이션하는 방법'
description: Azure Data Factory를 사용하여 SQLite에서 Azure SQL Database 서버리스로 오프라인 마이그레이션을 수행하는 방법을 알아봅니다.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: 6e60403344a0341b4aee74a001287c09ba67e114
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448911"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>SQLite 데이터베이스를 Azure SQL Database 서버리스로 마이그레이션하는 방법
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQLite는 대부분의 사용자에게 첫 번째 데이터베이스 및 SQL 프로그래밍 환경을 제공합니다. 많은 운영 체제에 포함되어 있으며 널리 사용되는 애플리케이션을 통해 전 세계에서 가장 널리 배포되어 사용되고 있는 데이터베이스 엔진입니다. 그리고 대부분의 사용자가 처음으로 사용하는 데이터베이스 엔진일 가능성이 높기 때문에 결국 프로젝트 또는 애플리케이션의 중심 부분이 되는 경우가 많습니다. 프로젝트 또는 애플리케이션이 초기 SQLite 구현보다 커지면 개발자는 데이터를 신뢰할 수 있는 중앙 집중식 데이터 저장소로 마이그레이션해야 할 수 있습니다.

Azure SQL Database 서버리스는 워크로드 수요 및 초당 사용된 컴퓨팅 양에 대한 청구서를 기반으로 컴퓨팅 규모를 자동으로 조정하는 단일 데이터베이스의 컴퓨팅 계층입니다. 또한 서버리스 컴퓨팅 계층은 스토리지 비용만 청구될 때 비활성 기간 동안 데이터베이스를 자동으로 일시 중지하고 활동이 반환되면 데이터베이스를 자동으로 다시 시작합니다.

아래 단계를 수행하면 데이터베이스가 Azure SQL Database 서버리스로 마이그레이션되고, 클라우드의 다른 사용자나 애플리케이션이 데이터베이스를 사용할 수 있고 애플리케이션 코드를 최소한으로 변경하여 사용한 양만큼만 요금을 지불할 수 있습니다.

## <a name="prerequisites"></a>필수 요건

- Azure 구독
- 마이그레이션하려는 SQLite2 또는 SQLite3 데이터베이스
- Windows 환경
  - 로컬 Windows 환경이 없는 경우 Azure에서 Windows VM을 사용하여 마이그레이션을 수행할 수 있습니다. Azure Files 및 Storage Explorer를 사용하여 SQLite 데이터베이스 파일을 이동하고 VM에서 사용할 수 있도록 합니다.

## <a name="steps"></a>단계

1. 서버리스 컴퓨팅 계층에 새 Azure SQL Database를 프로비저닝합니다.

    ![Azure SQL Database 서버리스의 프로비저닝 예제를 보여 주는 Azure Portal의 스크린샷](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Windows 환경에서 SQLite 데이터베이스 파일을 사용할 수 있는지 확인합니다. 아직 설치하지 않은 경우 SQLite ODBC 드라이버를 설치합니다(http://www.ch-werner.de/sqliteodbc/) 처럼 오픈 소스로 제공되는 경우가 많이 있음).

3. 데이터베이스의 시스템 DSN을 만듭니다. 시스템 아키텍처(32비트 또는 64비트)와 일치하는 데이터 원본 관리자 애플리케이션을 사용하고 있는지 확인하십시오. 시스템 설정에서 실행 중인 버전을 확인할 수 있습니다.

    - 사용자 환경에서 ODBC 데이터 원본 관리자를 엽니다.
    - 시스템 DSN 탭을 클릭하고 "추가"를 클릭합니다.
    - 설치한 SQLite ODBC 커넥터를 선택하고 연결에 의미 있는 이름(예: sqlitemigrationsource) 지정
    - .db 파일에 데이터베이스 이름 설정
    - 저장한 후 종료합니다.

4. 자체 호스팅 통합 런타임을 다운로드하고 설치합니다. 이 작업을 수행하는 가장 쉬운 방법은 빠른 설치 옵션입니다(자세한 내용은 설명서 참조). 수동 설치를 선택한 경우 애플리케이션에 인증 키를 제공해야 합니다. 이는 Data Factory 인스턴스에서 다음과 같은 방법으로 찾을 수 있습니다.

    - ADF 설정(Azure Portal의 서비스에서 작성 및 모니터링)
    - 왼쪽의 "작성" 탭(파란색 연필) 클릭
    - 연결(왼쪽 하단), 통합 런타임을 차례로 클릭
    - 새 자체 호스팅 통합 런타임을 추가하고 이름을 지정한 후 *옵션 2*를 선택합니다.

5. Data Factory에서 원본 SQLite 데이터베이스에 연결된 서비스를 새로 만듭니다.

    ![Azure Data Factory에서 빈 연결된 서비스 블레이드를 보여 주는 스크린샷](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. **연결**, **연결된 서비스**에서 **새로 만들기**를 클릭합니다.

7. "ODBC" 커넥터 검색 및 선택

   ![Azure Data Factory에서 연결된 서비스 블레이드의 ODBC 커넥터 로고를 보여 주는 스크린샷](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. 연결된 서비스에 "sqlite_odbc"와 같은 의미 있는 이름을 지정합니다. "통합 런타임을 통해 연결" 드롭다운에서 통합 런타임을 선택합니다. 연결 문자열에 아래를 입력하여 초기 카탈로그 변수를 .db 파일의 파일 경로로 바꾸고 DSN을 시스템 DSN 연결 이름으로 바꿉니다.

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. 인증 유형을 익명으로 설정

10. 연결 테스트

    ![Azure Data Factory의 성공적인 연결을 보여 주는 스크린샷](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. 서버리스 SQL 대상에 대한 다른 연결된 서비스를 만듭니다. 연결된 서비스 마법사를 사용하여 데이터베이스를 선택하고 SQL 인증 자격 증명을 제공합니다.

    ![Azure Data Factory에서 선택된 Azure SQL Database를 보여 주는 스크린샷](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. SQLite 데이터베이스에서 CREATE TABLE 문을 추출합니다. 데이터베이스 파일에서 아래 Python 스크립트를 실행하여 이 작업을 수행할 수 있습니다.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. CreateTables.sql 파일에서 CREATE table 문을 복사하고 Azure Portal의 쿼리 편집기에서 SQL 문을 실행하여 서버리스 SQL 대상 환경에 방문 테이블을 만듭니다.

14. Data Factory의 홈 화면으로 돌아가서 "데이터 복사"를 클릭하여 작업 만들기 마법사를 진행합니다.

    ![Azure Data Factory에서 데이터 복사 마법사 로고를 보여 주는 스크린샷](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. 원본 SQLite 데이터베이스에서 확인란을 사용하여 모든 테이블을 선택하고 Azure SQL의 대상 테이블에 매핑합니다. 작업이 실행되면 SQLite에서 Azure SQL로 데이터를 성공적으로 마이그레이션한 것입니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [빠른 시작: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요.
- 리소스 제한은 [서버리스 컴퓨팅 계층 리소스 제한](../../sql-database/sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)을 참조하세요.
