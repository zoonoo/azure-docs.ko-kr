---
title: "MySQL Workbench에서 MySQL용 Azure Database에 연결 | Microsoft Docs"
description: "이 빠른 시작에서는 MySQL용 Azure Database에서 데이터를 연결하고 쿼리하는 데 MySQL Workbench를 사용하는 단계를 제공합니다."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: seanli1988
ms.service: mysql-database
ms.custom: mvc
ms.topic: article
ms.date: 08/23/2017
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: ffeca6670521fe2b43c4ac47cac26fd2ad30f968
ms.contentlocale: ko-kr
ms.lasthandoff: 08/24/2017

---

# <a name="azure-database-for-mysql-use-mysql-workbench-to-connect-and-query-data"></a>MySQL용 Azure Database: MySQL Workbench을 사용하여 데이터 쿼리
이 빠른 시작에서는 MySQL Workbench 응용 프로그램을 사용하여 MySQL용 Azure Database에 연결하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건
이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.
- [Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI를 사용한 MySQL용 Azure Database 서버 만들기](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>MySQL Workbench 설치
[MySQL 웹 사이트](https://dev.mysql.com/downloads/workbench/)에서 컴퓨터에 MySQL Workbench를 다운로드하여 설치합니다.

## <a name="get-connection-information"></a>연결 정보 가져오기
MySQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭하고 방금 만든 서버를 검색합니다(예: **myserver4demo**).

3. 서버 이름을 클릭합니다.

4. 서버의 **속성** 페이지를 선택합니다. **서버 이름** 및 **서버 관리자 로그인 이름**을 기록해 둡니다.

 ![MySQL용 Azure Database 서버 이름](./media/connect-workbench/1-server-properties-name-login.png)
 
5. 서버 로그인 정보를 잊어버린 경우 **개요** 페이지로 이동하여 서버 관리자 로그인 이름을 확인하고 필요한 경우 암호를 다시 설정합니다.

## <a name="connect-to-the-server-using-mysql-workbench"></a>MySQL Workbench를 사용하여 서버에 연결 
GUI 도구 MySQL Workbench를 사용하여 Azure MySQL 서버에 연결하려면

1.  컴퓨터에서 MySQL Workbench 응용 프로그램을 시작합니다. 

2.  **새 연결 설정** 대화 상자의 **매개 변수** 탭에 다음 정보를 입력합니다.

    ![새 연결 설정](./media/connect-workbench/2-setup-new-connection.png)

    | **설정** | **제안 값** | **필드 설명** |
    |---|---|---|
    |   연결 이름 | 데모 연결 | 이 연결에 대한 레이블을 지정합니다. |
    | 연결 방법 | 표준(TCP/IP) | 표준(TCP/IP)이면 충분합니다. |
    | 호스트 이름 | *서버 이름* | 이전에 Azure Database for MySQL을 만들 때 사용된 서버 이름 값을 지정합니다. 표시된 예제 서버는 myserver4demo.mysql.database.azure.com입니다. 예제에서 표시된 것과 같은 정규화된 도메인 이름(\*.mysql.database.azure.com)을 사용합니다. 서버 이름을 기억하지 못하는 경우 이전 섹션으로 이동하여 연결 정보를 가져옵니다.  |
    | 포트 | 3306 | Azure Database for MySQL에 연결 시 항상 포트 3306을 사용합니다. |
    | 사용자 이름 |  *서버 관리자 로그인 이름* | 이전에 Azure Database for MySQL을 만들 때 제공한 서버 관리자 로그인 사용자 이름을 입력합니다. 이 예제에서는 사용자 이름이 myadmin@myserver4demo입니다. 사용자 이름을 기억하지 못하는 경우 이전 섹션의 단계에 따라 연결 정보를 가져옵니다. 형식은 *username@servername*입니다.
    | 암호 | 사용자 암호 | **Store in Vault(자격 증명 모음에 저장)...** 단추를 클릭하여 암호를 저장합니다. |

3.   **연결 테스트**를 클릭하여 모든 매개 변수가 올바르게 구성되었는지 테스트합니다. 

4.   **확인**을 클릭하여 연결을 저장합니다. 

5.   **MySQL 연결** 목록에서 서버에 해당하는 타일을 클릭하고 연결이 설정될 때까지 대기합니다.

6.   새 SQL 탭이 쿼리를 입력할 수 있는 빈 편집기로 열립니다.

    > [!NOTE]
    > 서버에서 기본적으로 적용되는 SSL은 연결의 성공을 위해 추가적인 구성이 필요합니다. [MySQL용 Azure 데이터베이스에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요.  이 빠른 시작에 대해 SSL을 사용하지 않도록 하려면 Azure Portal을 방문하고 연결 보안 페이지를 클릭하여 SSL 연결 적용 토글 단추를 사용하지 않도록 설정합니다.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>테이블 만들기, 데이터 삽입, 데이터 읽기, 데이터 업데이트, 데이터 삭제
1. 몇 가지 샘플 데이터를 설명하기 위해 샘플 SQL 코드를 빈 SQL 탭에 복사하여 붙여넣습니다.

    이 코드는 quickstartdb라는 빈 데이터베이스를 만든 후 inventory라는 샘플 테이블을 만듭니다. 일부 행을 삽입한 후 행을 읽습니다. 데이터가 update 문으로 변경되고 다시 행을 읽습니다. 마지막으로 행을 삭제하고 다시 행을 읽습니다.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    스크린샷은 SQL Workbench에서의 SQL 코드 예제와 실행 후 출력을 보여 줍니다.
    
    ![샘플 SQL 코드를 실행하기 위한 MySQL Workbench SQL 탭](media/connect-workbench/3-workbench-sql-tab.png)

2. 샘플 SQL 코드를 실행하려면 **SQL 파일** 탭의 도구 모음에서 번갯불 아이콘을 클릭합니다.
3. 페이지 중앙의 **결과 집합** 섹션에서 세 개의 탭 결과를 확인합니다. 
4. 페이지 하단에서 **출력** 목록을 확인합니다. 각 명령의 상태가 표시됩니다. 

이제 MySQL Workbench를 사용하여 MySQL용 Azure Database에 연결했고 SQL 언어를 사용하여 데이터를 쿼리했습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./concepts-migrate-import-export.md)

