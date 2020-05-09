---
title: '자습서: 서버 리스 데이터베이스로 SQLite 데이터베이스를 Azure SQL Database 마이그레이션하는 방법'
description: Azure Data Factory를 사용 하 여 SQLite에서 Azure SQL Database 서버 리스로 오프 라인 마이그레이션을 수행 하는 방법을 알아봅니다.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 2aed3a66f70864b3445418a51b0e79280e6884bd
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629204"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>서버를 사용 하지 않는 Azure SQL Database SQLite 데이터베이스를 마이그레이션하는 방법
SQLite는 대부분의 사용자에 게 데이터베이스 및 SQL 프로그래밍의 첫 번째 환경을 제공 합니다. 이는 많은 운영 체제에 포함 되어 있으며 널리 사용 되는 응용 프로그램을 통해 전 세계에서 가장 널리 배포 되 고 사용 되는 데이터베이스 엔진 중 하나를 사용 합니다. 그리고 대부분의 사용자가 사용 하는 첫 번째 데이터베이스 엔진 일 가능성이 많기 때문에 종종 프로젝트 또는 응용 프로그램의 중심 부분으로 종료 될 수 있습니다. 프로젝트 또는 응용 프로그램이 초기 SQLite 구현을 보다 커지면 하는 경우 개발자는 데이터를 신뢰할 수 있는 중앙 집중식 데이터 저장소로 마이그레이션해야 할 수 있습니다.

서버를 사용 하지 않는 Azure SQL Database 작업 부하에 따라 자동으로 계산을 확장 하는 단일 데이터베이스에 대 한 계산 계층 이며, 초당 사용 된 계산의 양에 대 한 요금을 청구 합니다. 서버를 사용 하지 않는 계산 계층은 저장소가 청구 될 때 비활성 기간 동안 데이터베이스를 자동으로 일시 중지 하 고 작업이 반환 될 때 데이터베이스를 자동으로 다시 시작 합니다.

아래 단계를 수행 하면 데이터베이스가 서버를 사용 하지 않는 Azure SQL Database로 마이그레이션되면 클라우드의 다른 사용자나 응용 프로그램에서 데이터베이스를 사용할 수 있도록 하 고 응용 프로그램 코드를 최소한으로 변경 하 여 사용한 양만큼만 요금을 지불 하면 됩니다.

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독
- 마이그레이션하려는 SQLite2 또는 SQLite3 데이터베이스
- Windows 환경
  - 로컬 Windows 환경이 없는 경우 Azure에서 Windows VM을 사용 하 여 마이그레이션을 수행할 수 있습니다. Azure Files 및 Storage 탐색기를 사용 하 여 SQLite 데이터베이스 파일을 VM에서 사용할 수 있도록 설정 합니다.

## <a name="steps"></a>단계

1. 서버를 사용 하지 않는 계산 계층에 새 Azure SQL Database를 프로 비전 합니다.

    ![서버를 사용 하지 않는 Azure sql database의 프로 비전 예제를 보여 주는 Azure Portal 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Windows 환경에서 SQLite 데이터베이스 파일을 사용할 수 있는지 확인 합니다. SQLite ODBC 드라이버를 아직 설치 하지 않은 경우 설치 http://www.ch-werner.de/sqliteodbc/)합니다 (예: 오픈 소스에서 사용 가능한 여러 항목 포함).

3. 데이터베이스에 대 한 시스템 DSN을 만듭니다. 시스템 아키텍처 (32 비트 vs 64 비트)와 일치 하는 데이터 원본 관리자 응용 프로그램을 사용 하 고 있는지 확인 하십시오. 시스템 설정에서 실행 중인 버전을 찾을 수 있습니다.

    - 사용자 환경에서 ODBC 데이터 원본 관리자를 엽니다.
    - 시스템 DSN 탭을 클릭 하 고 "추가"를 클릭 합니다.
    - 설치한 SQLite ODBC 커넥터를 선택 하 고 연결에 의미 있는 이름 (예: sqlitemigrationsource)을 지정 합니다.
    - 데이터베이스 이름을 db-library 파일로 설정 합니다.
    - 저장한 후 종료합니다.

4. 자체 호스팅 통합 런타임을 다운로드 하 여 설치 합니다. 이 작업을 수행 하는 가장 쉬운 방법은 설명서에 설명 된 대로 빠른 설치 옵션입니다. 수동 설치를 사용 하도록 선택 하는 경우 응용 프로그램에 인증 키를 제공 해야 합니다 .이 키는 다음을 통해 Data Factory 인스턴스에 있을 수 있습니다.

    - ADF 시작 (Azure Portal 서비스에서 작성자 및 모니터)
    - 왼쪽의 "Author" 탭 (파란색 연필)을 클릭 합니다.
    - 연결 (왼쪽 아래), 통합 런타임을 차례로 클릭 합니다.
    - 새 자체 호스팅 Integration Runtime 추가 하 고 이름을 지정 하 고 *옵션 2*를 선택 합니다.

5. Data Factory에서 원본 SQLite 데이터베이스에 대해 연결 된 서비스를 새로 만듭니다.

    ![Azure Data Factory에서 빈 연결 된 서비스 블레이드를 보여 주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. 연결의 연결 된 서비스에서 새로 만들기를 클릭 합니다.

7. "ODBC" 커넥터를 검색 하 고 선택 합니다.


    ![Azure Data Factory의 연결 된 서비스 블레이드에서 ODBC 커넥터 로고를 보여 주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. "Sqlite_odbc"와 같이 연결 된 서비스에 의미 있는 이름을 지정 합니다. "Integration runtime을 통해 연결" 드롭다운에서 integration runtime을 선택 합니다. 연결 문자열에 아래를 입력 하 여 초기 카탈로그 변수를 db-library 파일의 filepath로 바꾸고 DSN에 시스템 DSN 연결의 이름을 입력 합니다. 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. 인증 유형을 익명으로 설정 합니다.

10. 연결 테스트

    ![Azure Data Factory의 성공적인 연결을 보여 주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. 서버를 사용 하지 않는 SQL 대상에 대 한 다른 연결 된 서비스를 만듭니다. 연결 된 서비스 마법사를 사용 하 여 데이터베이스를 선택 하 고 SQL 인증 자격 증명을 제공 합니다.

    ![Azure Data Factory에서 선택 Azure SQL Database을 보여 주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. SQLite 데이터베이스에서 CREATE TABLE 문을 추출 합니다. 데이터베이스 파일에서 아래 Python 스크립트를 실행 하 여이 작업을 수행할 수 있습니다.

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

13. Createtables.sql 파일에서 CREATE table 문을 복사 하 고 Azure Portal의 쿼리 편집기에서 SQL 문을 실행 하 여 서버를 사용 하지 않는 SQL 대상 환경에 랜딩 테이블을 만듭니다.

14. Data Factory의 홈 화면으로 돌아가서 "데이터 복사"을 클릭 하 여 작업 만들기 마법사를 실행 합니다.

    ![Azure Data Factory에서 데이터 복사 마법사 로고를 보여 주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. 원본 SQLite 데이터베이스에서 확인란을 사용 하 여 모든 테이블을 선택 하 고 Azure SQL의 대상 테이블에 매핑합니다. 작업이 실행 되 면 SQLite에서 Azure SQL로 데이터를 성공적으로 마이그레이션 했습니다.

## <a name="next-steps"></a>다음 단계

- 시작 하려면 [퀵 스타트: Azure Portal를 사용 하 여 Azure SQL Database에서 단일 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조 하세요.
- 리소스 제한은 [서버리스 컴퓨팅 계층 리소스 제한](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)을 참조하세요.
