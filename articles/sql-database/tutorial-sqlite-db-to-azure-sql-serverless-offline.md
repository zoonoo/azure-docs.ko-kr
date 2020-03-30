---
title: '자습서: SQLite 데이터베이스를 Azure SQL 데이터베이스 서버리스로 마이그레이션하는 방법'
description: Azure 데이터 팩터리를 사용하여 SQLite에서 Azure SQL Database Server 서버리스로의 오프라인 마이그레이션을 수행하는 방법을 알아봅니다.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780510"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>SQLite 데이터베이스를 Azure SQL 데이터베이스 서버리스로 마이그레이션하는 방법
많은 사람들에게 SQLite는 데이터베이스 및 SQL 프로그래밍에 대한 첫 번째 경험을 제공합니다. 많은 운영 체제와 인기 있는 응용 프로그램에 포함되어 SQLite는 세계에서 가장 널리 배포되고 사용되는 데이터베이스 엔진입니다. 또한 많은 사람들이 사용하는 첫 번째 데이터베이스 엔진이기 때문에 프로젝트 나 응용 프로그램의 중심 부분으로 끝날 수 있습니다. 프로젝트 또는 응용 프로그램이 초기 SQLite 구현보다 증가하는 경우 개발자는 데이터를 신뢰할 수 있는 중앙 집중식 데이터 저장소로 마이그레이션해야 할 수 있습니다.

Azure SQL Database 서버리스는 워크로드 수요에 따라 계산을 자동으로 확장하고 초당 사용되는 계산 양에 대한 청구를 하는 단일 데이터베이스의 계산 계층입니다. 또한 서버리스 계산 계층은 저장소만 요금이 청구되는 비활성 기간 동안 데이터베이스를 자동으로 일시 중지하고 활동이 반환될 때 데이터베이스를 자동으로 다시 시작합니다.

아래 단계를 수행하면 데이터베이스가 Azure SQL Database Serverless로 마이그레이션되어 클라우드의 다른 사용자 나 응용 프로그램에서 데이터베이스를 사용할 수 있게 하고 최소한의 응용 프로그램 코드 변경만 으로 사용한 것에 대해서만 비용을 지불할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독
- 마이그레이션하려는 SQLite2 또는 SQLite3 데이터베이스
- Windows 환경
  - 로컬 Windows 환경이 없는 경우 마이그레이션에 Azure의 Windows VM을 사용할 수 있습니다. Azure 파일 및 저장소 탐색기를 사용하여 VM에서 SQLite 데이터베이스 파일을 이동하고 사용할 수 있도록 합니다.

## <a name="steps"></a>단계

1. 서버리스 계산 계층에 새 Azure SQL 데이터베이스를 프로비전합니다.

    ![Azure sql 데이터베이스 서버없는 에 대한 프로비저닝 예제를 보여주는 Azure 포털의 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Windows 환경에서 SQLite 데이터베이스 파일을 사용할 수 있는지 확인합니다. SQLite ODBC 드라이버를 아직 설치하지 않은 경우(예: 오픈 소스에서 사용할 http://www.ch-werner.de/sqliteodbc/)수 있는 많은 드라이버가 있습니다.

3. 데이터베이스에 대한 시스템 DSN을 만듭니다. 시스템 아키텍처와 일치하는 데이터 원본 관리자 응용 프로그램을 사용해야 합니다(32비트 대 64비트). 시스템 설정에서 실행 중인 버전을 찾을 수 있습니다.

    - 사용자 환경에서 ODBC 데이터 원본 관리자를 엽니다.
    - 시스템 DSN 탭을 클릭하고 "추가"를 클릭합니다.
    - 설치한 SQLite ODBC 커넥터를 선택하고 연결에 의미 있는 이름(예: sqlitemigrationsource)을 지정합니다.
    - 데이터베이스 이름을 .db 파일로 설정합니다.
    - 저장한 후 종료합니다.

4. 자체 호스팅 통합 런타임을 다운로드하여 설치합니다. 이 작업을 수행하는 가장 쉬운 방법은 설명서에 자세히 설명된 대로 Express 설치 옵션입니다. 수동 설치를 선택하는 경우 다음을 통해 데이터 팩터리 인스턴스에 위치할 수 있는 인증 키를 응용 프로그램에 제공해야 합니다.

    - ADF 시작(Azure 포털의 서비스에서 작성 및 모니터)
    - 왼쪽의 "저자" 탭(파란색 연필)을 클릭합니다.
    - 연결을 클릭한 다음(왼쪽 아래), 통합 런타임
    - 새 자체 호스팅 통합 런타임을 추가하고 이름을 지정하고 *옵션 2를*선택합니다.

5. 데이터 팩터리에서 원본 SQLite 데이터베이스에 대해 연결된 새 서비스를 만듭니다.

    ![Azure 데이터 팩터리에서 빈 링크된 서비스 블레이드를 보여주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. 연결에서 연결된 서비스 에서 새

7. "ODBC" 커넥터 검색 및 선택


    ![Azure 데이터 팩토리의 연결된 서비스 블레이드에서 ODBC 커넥터 로고를 보여주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. 연결된 서비스에 "sqlite_odbc"과 같은 의미 있는 이름을 지정합니다. "통합 런타임을 통한 연결" 드롭다운에서 통합 런타임을 선택합니다. "dsN 파일의 파일 경로로 초기 카탈로그 변수를 대체하고 DSN을 시스템 DSN 연결 의 이름으로 바꾸면서 연결 문자열에 아래를 입력합니다. 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. 인증 유형을 익명으로 설정합니다.

10. 연결 테스트

    ![Azure 데이터 팩터리에서 성공적인 연결을 보여주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Serverless SQL 대상에 대해 연결된 다른 서비스를 만듭니다. 연결된 서비스 마법사를 사용하여 데이터베이스를 선택하고 SQL 인증 자격 증명을 제공합니다.

    ![Azure 데이터 팩터리에서 선택한 Azure SQL 데이터베이스를 보여주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. SQLite 데이터베이스에서 테이블 만들기 문을 추출합니다. 데이터베이스 파일에서 아래 Python 스크립트를 실행하여이 작업을 수행 할 수 있습니다.

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

13. CreateTables.sql 파일에서 CREATE 테이블 문을 복사하고 Azure 포털의 쿼리 편집기에서 SQL 문을 실행하여 Serverless SQL 대상 환경에서 방문 테이블을 만듭니다.

14. 데이터 팩터리의 홈 화면으로 돌아가서 "데이터 복사"를 클릭하여 작업 생성 마법사를 실행합니다.

    ![Azure 데이터 팩터리에서 데이터 복사 마법사 로고를 보여주는 스크린샷](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. 확인란을 사용하여 원본 SQLite 데이터베이스에서 모든 테이블을 선택하고 Azure SQL의 대상 테이블에 매핑합니다. 작업이 실행되면 SQLite에서 Azure SQL로 데이터를 성공적으로 마이그레이션했습니다!

## <a name="next-steps"></a>다음 단계

- 시작하려면 빠른 [시작: Azure 포털을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기를](sql-database-single-database-get-started.md)참조하십시오.
- 리소스 제한은 [서버리스 컴퓨팅 계층 리소스 제한](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)을 참조하세요.
