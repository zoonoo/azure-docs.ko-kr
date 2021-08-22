---
title: MySQL Workbench를 사용하여 Amazon RDS for MySQL를 Azure Database for MySQL로 마이그레이션
description: 이 문서에서는 MySQL Workbench 마이그레이션 마법사를 사용하여 Amazon RDS for MySQL을 Azure Database for MySQL로 마이그레이션하는 방법을 설명합니다.
author: HJToland3
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 05/21/2021
ms.openlocfilehash: c744517dfab088fa4edb104b2c5aebda2818ab8f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642160"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-mysql-workbench"></a>MySQL Workbench를 사용하여 Amazon RDS for MySQL를 Azure Database for MySQL로 마이그레이션

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

MySQL Workbench 내보내기/가져오기, DMS(Azure Database Migration Service), MySQL 덤프 및 복원과 같은 다양한 유틸리티를 사용하여 Amazon RDS for MySQL을 Azure Database for MySQL 마이그레이션할 수 있습니다. 그러나 MySQL Workbench 마이그레이션 마법사를 사용하면 Amazon RDS for MySQL 데이터베이스를 Azure Database for MySQL 쉽고 편리하게 이동할 수 있습니다.

마이그레이션 마법사를 사용하면 마이그레이션할 스키마와 개체를 편리하게 선택할 수 있습니다. 또한 서버 로그를 보고 오류 및 병목 상태를 실시간으로 식별할 수 있습니다. 따라서 오류가 검색되면 마이그레이션 프로세스 중 테이블 또는 데이터베이스 구조와 개체를 편집하고 수정한 다음 처음부터 다시 시작하지 않고도 마이그레이션을 계속할 수 있습니다.

> [!NOTE]
> 마이그레이션 마법사를 사용하여 이 문서의 범위를 벗어나는 기타 원본(예: Microsoft SQL Server, Oracle, PostgreSQL, MariaDB 등)을 마이그레이션할 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

마이그레이션 프로세스를 시작하기 전 아래에 설명된 대로 여러 매개 변수 및 기능을 올바르게 구성하고 설정하는 것이 좋습니다.

- 원본 및 대상 데이터베이스의 문자 집합이 동일한지 확인합니다.
- 가져오거나 마이그레이션하려는 데이터 또는 워크로드 양에 따라 대기 시간 초과를 적절한 시간으로 설정합니다.
- 가져오거나 마이그레이션하려는 데이터베이스의 크기에 따라 `max_allowed_packet parameter`를 적절한 크기로 설정합니다.
- Azure Database for MySQL Server가 InnoDB 스토리지 엔진만 지원하기 때문에 모든 테이블이 InnoDB를 사용하는지 확인합니다.
- 루트 사용자 또는 수퍼 사용자 정의자를 포함하는 모든 트리거, 저장된 프로시저 및 기타 함수를 제거, 바꾸기 또는 수정합니다(Azure Database for MySQL은 슈퍼 사용자 권한을 지원하지 않습니다). 가져오기 프로세스를 실행하는 관리 사용자의 이름으로 바꾸려면 다음의 명령을 실행하십시오.

  ```
  DELIMITER; ;/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
  DELIMITER;
  /* Modified to */
  DELIMITER;
  /*!50003 CREATE*//*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
  DELIMITER;

  ```

- UDF(사용자 정의 함수)가 데이터베이스 서버에서 실행되고 있는 경우 MySQL 데이터베이스에 대한 권한을 삭제해야 합니다. 서버에서 UDF가 실행되고 있는지 확인하려면 다음 쿼리를 사용합니다.

  ```
  SELECT * FROM mysql.func;
  ```

  UDF가 실행되고 있음을 발견한 경우 다음 쿼리를 팔로우하여 UDF를 드롭할 수 있습니다.

  ```
  DROP FUNCTION your_UDFunction;
  ```

- 특히 초대형 데이터베이스를 내보내는 경우에는 도구가 실행되는 서버와 궁극적으로 내보내기 위치에 충분한 디스크 공간 및 계산 능력(vCores, CPU 및 메모리)이 있는지 확인해야 합니다.
- 워크로드가 방화벽 또는 기타 네트워크 보안 계층 뒤에 있는 경우 온-프레미스 또는 AWS 인스턴스와 Azure Database for MySQL 사이에 경로를 만듭니다.

## <a name="begin-the-migration-process"></a>마이그레이션 프로세스 시작

1. 마이그레이션 프로세스를 시작하려면 MySQL Workbench에 로그인한 후 홈 아이콘을 선택합니다.
2. 왼쪽 탐색 막대에서 아래 스크린샷에 표시된 것처럼 마이그레이션 마법사 아이콘을 선택합니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/begin-the-migration.png" alt-text="MySQL Workbench 시작 화면":::

   아래와 같이 마이그레이션 마법사의 **개요** 페이지가 표시됩니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-wizard-welcome.png" alt-text="MySQL Workbench 마이그레이션 마법사 홈페이지":::

3. **ODBC 관리자 열기** 를 선택하여 MySQL용 ODBC 드라이버를 설치했는지 확인합니다.

   이 경우 **드라이버** 탭에서 이미 두 개의 MySQL Server ODBC 드라이버를 설치했음을 알 수 있습니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/obdc-administrator-page.png" alt-text="ODBC 데이터 원본 관리자 페이지":::

   MySQL ODBC 드라이버가 설치되지 않은 경우 드라이버를 설치하기 위해 MySQL Workbench를 설치하는 데 사용한 MySQL 설치 관리자를 사용합니다. MySQL ODBC 드라이버 설치에 대한 자세한 정보는 다음 리소스를 참조하세요.

   - [MySQL:: MySQL Connector/ODBC 개발자 가이드 :: Windows용 4.1 커넥터/ODBC 설치](https://dev.mysql.com/doc/connector-odbc/en/connector-odbc-installation-binary-windows.html)
   - [MySQL용 ODBC 드라이버: 연결 설치 및 설정 방법(단계별) – {coding}Sight (codingsight.com)](https://codingsight.com/install-and-configure-odbc-drivers-for-mysql/)

4. **ODBC 데이터 원본 관리자** 대화 상자를 닫은 후 마이그레이션 프로세스를 계속합니다.

## <a name="configure-source-database-server-connection-parameters"></a>원본 데이터베이스 서버 연결 매개 변수 구성

1. 작업의 **개요** 페이지에서 **마이그레이션 시작** 을 선택합니다.

   **원본 선택** 페이지가 나타납니다. 이 페이지를 사용하여 마이그레이션하는 RDBMS 및 연결에 대한 매개 변수에 대한 정보를 제공할 수 있습니다.

2. 매개 변수의 **데이터베이스 시스템** 필드에서 **MySQL** 을 선택합니다.
3. 매개 변수의 **저장된 연결** 필드에서 해당 RDBMS에 대해 저장된 연결 설정 중 하나를 선택합니다.

   페이지 아래쪽의 확인란을 체크하고 기본 설정의 이름을 입력하여 연결을 저장할 수 있습니다.

4. 매개 변수의 **연결 메서드** 필드에서 **표준 TCP/IP** 를 선택합니다.
5. 매개 변수의 **호스트 이름** 필드에서 원본 데이터베이스 서버의 이름을 지정합니다.
6. 매개 변수의 **포트** 필드에서 **3306** 을 지정한 다음 서버에 연결하기 위한 사용자 이름 및 암호를 입력합니다.
7. 매개 변수의 **데이터베이스** 필드에 마이그레이션할 데이터베이스의 이름을 알고 있는 경우 입력합니다. 모르는 경우에는 필드를 공백으로 비워 둡니다.
8. 메뉴에서 **연결 테스트를** 선택하여 MySQL 서버 인스턴스의 연결을 확인합니다.

   올바른 매개 변수를 입력한 경우 연결 시도가 성공했음을 알리는 메시지가 나타납니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-connection-parameters.png" alt-text="원본 데이터베이스 연결 매개 변수 페이지":::

9. **다음** 을 선택합니다.

## <a name="configure-target-database-server-connection-parameters"></a>대상 데이터베이스 서버 연결 매개 변수 구성

1. **대상 선택** 페이지에서 원본 서버에 대한 연결을 설정하는 것과 유사한 프로세스를 사용하여 대상 MySQL 서버 인스턴스에 연결할 매개 변수를 설정합니다.
2. 성공적인 연결을 확인하려면 **연결 테스트** 를 선택합니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/target-connection-parameters.png" alt-text="대상 데이터베이스 연결 매개 변수 페이지":::

3. **다음** 을 선택합니다.

## <a name="select-the-schemas-to-migrate"></a>마이그레이션할 스키마를 선택합니다

마이그레이션 마법사는 MySQL 서버 인스턴스와 통신하고 원본 서버에서 스키마 목록을 페치합니다.

1. **로그 표시** 를 선택하여 이 작업을 확인합니다.

   아래 스크린샷은 원본 데이터베이스 서버에서 스키마를 검색하는 방법을 보여줍니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/retrieve-schemas.png" alt-text="스키마 목록 페이지 페치":::

2. **다음** 을 선택하여 모든 스키마가 성공적으로 페치되었는지 확인합니다.

   아래 스크린샷에서는 페치된 스키마의 목록을 보여줍니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/schemas-selection.png" alt-text="스키마 선택 페이지":::

   이 목록에 표시되는 스키마만 마이그레이션 할 수 있습니다.

3. 마이그레이션하려는 스키마를 선택한 후 **다음** 을 선택합니다.

## <a name="object-migration"></a>개체 마이그레이션

다음에는 마이그레이션할 개체를 지정합니다.

1. **선택 영역 표시** 를 선택한 다음 **사용 가능한 개체** 에서 마이그레이션하려는 개체를 선택하여 추가합니다.

   개체를 추가하면 아래 스크린샷에 표시된 것처럼 **마이그레이션할 개체** 에 표시됩니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-objects.png" alt-text="원본 개체 선택 페이지":::

   이 시나리오에서는 모든 테이블 개체를 선택 했습니다.

2. **다음** 을 선택합니다.

## <a name="edit-data"></a>데이터 편집

이 섹션에서는 마이그레이션하려는 개체를 편집하는 옵션을 사용할 수 있습니다.

1. **수동 편집** 페이지의 우측 상단 모퉁이에 있는 **보기** 드롭다운 메뉴를 확인합니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/manual-editing.png" alt-text="수동 편집 선택 페이지":::

   이 **보기** 드롭다운 상자에는 다음 세 가지 항목이 포함됩니다.

   - **모든 개체** – 모든 개체를 표시합니다. 이 옵션을 사용하면 생성된 SQL을 대상 데이터베이스 서버에 적용하기 전에 수동으로 편집할 수 있습니다. 이렇게 하려면 개체를 선택하고 코드 및 메시지 표시를 선택합니다. 선택한 개체에 해당하는 생성된 MySQL 코드를 보고 편집할 수 있습니다.
   - **마이그레이션 문제** – 마이그레이션 중 발생한 모든 문제를 표시하며,이 문제를 검토하고 확인할 수 있습니다.
   - **열 매핑** – 열 매핑 정보를 표시합니다. 이 보기를 사용하여 대상 개체의 이름 및 변경 열을 편집할 수 있습니다.

2. **다음** 을 선택합니다.

## <a name="create-the-target-database"></a>대상 데이터베이스 만들기

1. 확인란 **대상 RDBMS에서 스키마 만들기** 를 선택합니다.

   기존 스키마를 수정하거나 업데이트하지 않게 기존 스키마를 유지하도록 선택할 수도 있습니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-database.png" alt-text="대상 만들기 옵션 페이지":::

   이 문서에서는 대상 RDBMS에서 스키마 만들기로 선택했지만 **SQL 스크립트 파일 만들기** 확인란을 선택하여 로컬 컴퓨터 또는 기타 용도로 파일을 저장할 수도 있습니다.

2. **다음** 을 선택합니다.

## <a name="run-the-mysql-script-to-create-the-database-objects"></a>MySQL 스크립트를 실행하여 데이터베이스 개체 만들기

대상 RDBMS에서 스키마를 만들도록 선택했으므로 마이그레이션된 SQL 스크립트가 대상 MySQL 서버에서 실행됩니다. 아래 스크린샷과 같이 진행률을 볼 수 있습니다.

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-schemas.png" alt-text="스키마 만들기 페이지":::

1. 스키마 및 해당 개체 만들기가 완료되면 **다음** 을 선택합니다.

   다음 스크린샷과 같이 **대상 결과 만들기** 페이지에는 생성된 개체 목록과 개체를 만드는 동안 발생한 오류에 대한 알림이 표시됩니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-results.png" alt-text="대상 결과 만들기 페이지":::

2. 이 페이지의 세부 정보를 검토하여 모든 것이 의도대로 완료되었는지 확인합니다.

   이 문서에는 오류가 없습니다. 오류 메시지를 해결할 필요가 없는 경우 마이그레이션 스크립트를 편집할 수 있습니다.

3. **개체** 상자에서 편집할 개체를 선택합니다.
4. **선택한 개체에 대한 SQL CREATE 스크립트** 에서 SQL 스크립트를 수정한 다음 **적용** 을 선택하여 변경 내용을 저장합니다.
5. **개체 다시 만들기** 를 선택하여 변경 내용을 포함한 스크립트를 실행합니다.

   스크립트가 실패하면 생성된 스크립트를 편집해야 할 수 있습니다. 그런 다음 SQL 스크립트를 수동으로 수정하고 모든 것을 다시 실행할 수 있습니다. 이 문서에서는 아무것도 변경하지 않으므로 스크립트를 그대로 둡니다.

6. **다음** 을 선택합니다.

## <a name="transfer-data"></a>데이터 전송

프로세스의 이 파트에서는 원본 MySQL Server 데이터베이스 인스턴스의 데이터를 새로 만든 대상 MySQL 데이터베이스 인스턴스로 이동합니다. **데이터 전송 설정** 페이지를 사용하여 이 프로세스를 구성할 수 있습니다.

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/data-transfer-setup.png" alt-text="데이터 전송 설정 페이지":::

이 페이지에서는 데이터 전송을 설정하는 옵션을 제공합니다. 이 문서의 목표를 감안해 기본값을 그대로 수락합니다.

1. 데이터 전송의 실제 프로세스를 시작하려면 **다음** 을 선택합니다.

   다음 스크린샷에 표시된 것처럼 데이터 전송 프로세스의 진행률이 표시됩니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/bulk-data-transfer.png" alt-text="대량 데이터 전송 페이지":::

   > [!NOTE]
   > 데이터 전송 프로세스의 기간은 마이그레이션하는 데이터베이스의 크기와 직접적으로 관련이 있습니다. 원본 데이터베이스가 클수록 프로세스가 더 길어질 수 있으며, 더 큰 데이터베이스의 경우 최대 몇 시간이 걸릴 수 있습니다.

2. 전송이 완료되면 **다음** 을 선택합니다.

   아래 스크린샷에 표시된 것처럼 전체 프로세스가 요약된 보고서를 제공하는 **마이그레이션 보고서** 페이지가 나타납니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-report.png" alt-text="마이그레이션 진행률 보고서 페이지":::

3. **마침** 을 선택하여 마이그레이션 마법사를 닫습니다.

   이제 마이그레이션이 성공적으로 완료 되었습니다.

## <a name="verify-consistency-of-the-migrated-schemas-and-tables"></a>마이그레이션된 스키마 및 테이블의 일관성 확인

1. 다음으로 MySQL 대상 데이터베이스 인스턴스에 로그인하여 마이그레이션된 스키마 및 테이블이 MySQL 원본 데이터베이스와 일치하는지 확인합니다.

   이 경우 Amazon RDS for MySQL: **MyjolieDB** 데이터베이스의 모든 스키마(sakila, moda, items, customer, clothes, world, and world_x)가 Azure Database for MySQL: **azmysql** 인스턴스로 성공적으로 마이그레이션된 것을 알 수 있습니다.

2. 테이블 및 행 개수를 확인하려면 두 인스턴스에서 다음 쿼리를 실행합니다.

      `SELECT COUNT (*) FROM sakila.actor;`

   아래 스크린샷에서 Amazon RDS MySQL의 행 개수가 Azure Database for MySQL 인스턴스와 일치하는 200 임을 확인할 수 있습니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-source.png" alt-text="테이블 및 행 크기 원본 데이터베이스":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-target.png" alt-text="테이블 및 행 크기 대상 데이터베이스":::

   모든 단일 스키마 및 테이블에서 위의 쿼리를 실행할 수 있지만 수백, 수천 또는 수백만 개의 테이블을 처리하는 경우에는 약간 많은 작업량을 수행하게 됩니다. 대신 아래 쿼리를 사용하여 스키마(데이터베이스) 및 테이블 크기를 확인할 수 있습니다.

3. 데이터베이스 크기를 확인하려면 다음 쿼리를 실행합니다.

    ```
   SELECT table_schema AS "Database", 
   ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)" 
   FROM information_schema.TABLES 
   GROUP BY table_schema;
   ```

4. 테이블 크기를 확인하려면 다음 쿼리를 실행합니다.

   ```
   SELECT table_name AS "Table",
   ROUND(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)"
   FROM information_schema.TABLES
   WHERE table_schema = "database_name"
   ORDER BY (data_length + index_length) DESC;
   ```

   아래 스크린샷에서 원본 Amazon RDS MySQL 인스턴스의 스키마(데이터베이스) 크기가 대상 Azure Database for MySQL 인스턴스의 크기와 동일함을 확인할 수 있습니다.

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-source.png" alt-text="데이터베이스 크기 원본 데이터베이스":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-target.png" alt-text="데이터베이스 크기 대상 데이터베이스":::

   스키마(데이터베이스) 크기는 두 인스턴스에서 동일하기 때문에 개별 테이블 크기를 확인할 필요가 없습니다. 어떤 경우든 항상 위의 쿼리를 사용하여 필요에 따라 테이블 크기를 확인할 수 있습니다.

   이제 마이그레이션이 성공적으로 완료되었는지 확인했습니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)을 참조합니다.
- Azure Database for MySQL에 MySQL 앱을 마이그레이션하는 방법을 보여주는 데모가 포함된 [MySQL/PostgreSQL 앱을 Azure 관리 서비스에 간편하게 마이그레이션하기](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) 비디오를 시청합니다.
