---
title: 가져오기 및 내보내기-Azure Database for MySQL
description: 이 문서에서는 MySQL Workbench와 같은 도구를 사용하여 Azure Database for MySQL에서 데이터베이스를 가져오고 내보내는 일반적인 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 049a0ad45ea82210d8fac28db0fb3d067841bba4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625146"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>가져오기 및 내보내기를 사용하여 MySQL 데이터베이스 마이그레이션

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

이 문서에서는 MySQL Workbench를 사용하여 Azure Database for MySQL 서버로 데이터를 가져오고 내보내는 두 가지 일반적인 방법을 설명합니다.

자세한 마이그레이션 지침 및 포괄적인 마이그레이션 [가이드는 마이그레이션 가이드 리소스](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)를 참조 하세요. 

다른 마이그레이션 시나리오는 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하세요. 

## <a name="prerequisites"></a>전제 조건

MySQL 데이터베이스 마이그레이션을 시작 하기 전에 다음을 수행 해야 합니다.
- [Azure Portal를 사용 하 여 Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md)를 만듭니다.
- 가져오기 및 내보내기를 위한 [Mysql 워크 벤치](https://dev.mysql.com/downloads/workbench/) 또는 다른 타사 mysql 도구를 다운로드 하 여 설치 합니다.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버에서 데이터베이스 만들기
MySQL 워크 벤치, Toad 또는 Navicat를 사용 하 여 Azure Database for MySQL 서버에서 빈 데이터베이스를 만듭니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 같을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

연결 하려면 다음을 수행 합니다.

1. Azure Portal에서 Azure database for MySQL의 **개요** 창에 있는 연결 정보를 찾습니다.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Azure Portal의 Azure Database for MySQL 서버 연결 정보에 대 한 스크린샷":::

1. MySQL Workbench에 연결 정보를 추가합니다.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL 워크 벤치 연결 문자열":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>가져오기 및 내보내기 기술을 사용할 시기 결정

> [!TIP]
> 전체 데이터베이스를 덤프 하 고 복원 하려는 시나리오의 경우 대신 [덤프 및 복원](concepts-migrate-dump-restore.md) 방법을 사용 합니다.

다음 시나리오에서는 MySQL 도구를 사용 하 여 데이터베이스를 MySQL 데이터베이스로 가져오고 내보냅니다. 다른 도구의 경우 MySQL의 "마이그레이션 방법" 섹션 (22 페이지)에서 [Azure 데이터베이스 마이그레이션 가이드](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf)로 이동 합니다. 

- 기존 MySQL 데이터베이스에서 Azure MySQL 데이터베이스로 가져올 몇 가지 테이블을 선택적으로 선택 해야 하는 경우 가져오기 및 내보내기 기술을 사용 하는 것이 가장 좋습니다.  이렇게 하면 시간과 리소스를 절약하도록 마이그레이션에서 불필요한 테이블을 생략할 수 있습니다. 예를 들어 [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)로 `--include-tables` 또는 `--exclude-tables` 스위치를 사용하고 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables) 덤프로 `--tables` 스위치를 사용합니다.
- 테이블이 아닌 다른 데이터베이스 개체를 이동 하는 경우 해당 개체를 명시적으로 만듭니다. 마이그레이션할 제약 조건 (기본 키, 외래 키 및 인덱스), 뷰, 함수, 프로시저, 트리거 및 다른 모든 데이터베이스 개체를 포함 합니다.
- MySQL 데이터베이스 이외의 외부 데이터 원본에서 데이터를 마이그레이션하는 경우 플랫 파일을 만들고 [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)를 사용하여 가져옵니다.

> [!Important]
> 단일 서버와 유연한 서버 모두 *InnoDB 저장소 엔진을* 지원 합니다. Azure database for MySQL에 데이터를 로드할 때 데이터베이스의 모든 테이블에서 InnoDB 저장소 엔진을 사용 하는지 확인 합니다.
>
> 원본 데이터베이스에서 다른 저장소 엔진을 사용 하는 경우 데이터베이스를 마이그레이션하기 전에 먼저 InnoDB 엔진으로 변환 합니다. 예를 들어 MyISAM 엔진을 사용하는 WordPress 또는 웹앱이 있는 경우 먼저 데이터를 InnoDB 테이블로 마이그레이션하여 테이블을 변환합니다. `ENGINE=INNODB` 절을 사용하여 테이블을 만들기 위한 엔진을 설정한 다음 마이그레이션 전에 데이터를 호환되는 테이블로 전송합니다.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>가져오기 및 내보내기를 위한 성능 권장 사항

최적의 데이터 가져오기 및 내보내기 성능을 위해서는 다음을 수행 하는 것이 좋습니다.
-   데이터를 로드 하기 전에 클러스터형 인덱스 및 기본 키를 만듭니다. 기본 키 순서로 데이터를 로드 합니다.
-   데이터가 로드 될 때까지 보조 인덱스 생성을 지연 시킵니다.
-   데이터를 로드 하기 전에 foreign key 제약 조건을 사용 하지 않도록 설정 합니다. 외래 키 검사 비활성화는 상당한 성능 향상을 제공합니다. 제약 조건을 활성화하고 참조 무결성을 확인하도록 로드 후 데이터를 확인합니다.
-   병렬로 데이터를 로드합니다. 리소스 제한에 도달하도록 하는 너무 많은 병렬 처리를 피하고 Azure Portal에서 사용할 수 있는 메트릭을 사용하여 리소스를 모니터링합니다.
-   적절한 경우 분할된 테이블을 사용합니다.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>MySQL 워크 벤치를 사용 하 여 데이터 가져오기 및 내보내기
MySQL 워크 벤치에서 데이터를 내보내고 가져오는 방법에는 개체 브라우저 상황에 맞는 메뉴 또는 탐색기 창 등 두 가지가 있습니다. 각 방법은 서로 다른 용도로 사용 됩니다.

> [!NOTE]
> Mysql 워크 벤치에서 MySQL 단일 서버 또는 유연한 서버 (미리 보기)에 대 한 연결을 추가 하는 경우 다음을 수행 합니다.
> - MySQL 단일 서버의 경우 사용자 이름이 형식 인지 확인 *\<username@servername>* 합니다.
> - MySQL 유연한 서버에는 *\<username>* 만 사용 합니다. 를 사용 하 여 *\<username@servername>* 연결 하면 연결이 실패 합니다.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>개체 브라우저 상황에 맞는 메뉴에서 테이블 데이터 내보내기 및 가져오기 마법사를 실행 합니다.

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="개체 브라우저의 상황에 맞는 메뉴에 있는 MySQL 워크 벤치 내보내기 및 가져오기 마법사 명령의 스크린샷":::

테이블 데이터 마법사는 CSV 및 JSON 파일을 사용 하 여 가져오기 및 내보내기 작업을 지원 합니다. 이 마법사에는 구분 기호, 열 선택, 인코딩 선택 등의 몇 가지 구성 옵션이 포함 되어 있습니다. 로컬 또는 원격으로 연결 된 MySQL 서버에 대해 각 마법사를 실행할 수 있습니다. 가져오기 작업은 테이블, 열 및 형식 매핑을 포함합니다.

개체 브라우저 상황에 맞는 메뉴에서 이러한 마법사에 액세스 하려면 테이블을 마우스 오른쪽 단추로 클릭 한 다음 **테이블 데이터 내보내기 마법사** 또는 **테이블 데이터 가져오기 마법사** 를 선택 합니다.

#### <a name="the-table-data-export-wizard"></a>테이블 데이터 내보내기 마법사

테이블을 CSV 파일로 내보내려면:

1. 내보낼 데이터베이스의 테이블을 마우스 오른쪽 단추로 클릭합니다.
1. **테이블 데이터 내보내기 마법사** 를 선택합니다. 내보낼 열, 행 오프셋(있는 경우) 및 개수(있는 경우)를 선택합니다.
1. **내보내기에 대 한 데이터 선택** 창에서 **다음** 을 선택 합니다. 파일 경로, CSV 또는 JSON 파일 형식을 선택합니다. 또한 선 구분 기호, 문자열을 묶는 방법 및 필드 구분 기호를 선택합니다.
1. **출력 파일 위치 선택** 창에서 **다음** 을 선택 합니다.
1. **데이터 내보내기** 창에서 **다음** 을 선택 합니다.

#### <a name="the-table-data-import-wizard"></a>테이블 데이터 가져오기 마법사

CSV 파일에서 테이블을 가져오려면 다음을 수행 합니다.

1. 가져올 데이터베이스의 테이블을 마우스 오른쪽 단추로 클릭합니다.
1. 가져올 CSV 파일을 찾아서 선택 하 고 **다음** 을 선택 합니다.
1. 대상 테이블 (신규 또는 기존)을 선택 하 고 **가져오기 전에 테이블 자르기** 확인란을 선택 하거나 선택을 취소 한 후 **다음** 을 선택 합니다.
1. 가져올 인코딩 및 열을 선택 하 고 **다음** 을 선택 합니다.
1. **데이터 가져오기** 창에서 **다음** 을 선택 합니다. 마법사에서 데이터를 가져옵니다.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>탐색기 창에서 SQL 데이터 내보내기 및 가져오기 마법사를 실행 합니다.
마법사를 사용 하 여 MySQL 워크 벤치 또는 mysqldump 명령에서 생성 된 SQL 데이터를 내보내거나 가져올 수 있습니다. **탐색기** 창에서 마법사에 액세스 하거나 주 메뉴에서 **서버** 를 선택할 수 있습니다.

#### <a name="export-data"></a>데이터 내보내기

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="탐색기 창을 사용 하 여 MySQL 워크 벤치에서 데이터 내보내기 창을 표시 하는 스크린샷":::

**데이터 내보내기** 창을 사용 하 여 MySQL 데이터를 내보낼 수 있습니다.

1. MySQL 워크 벤치의 **탐색기** 창에서 **데이터 내보내기** 를 선택 합니다.

1. **데이터 내보내기** 창에서 내보내려는 각 스키마를 선택 합니다.
 
   각 스키마에 대해 내보낼 특정 스키마 개체 또는 테이블을 선택할 수 있습니다. 구성 옵션에는 프로젝트 폴더 또는 자체 포함 된 SQL 파일로 내보내기, 저장 된 루틴 및 이벤트 덤프 또는 테이블 데이터 건너뛰기가 포함 됩니다.

   또는 **결과 집합 내보내기** 를 사용 하 여 SQL 편집기에서 CSV, JSON, HTML 및 XML 등의 다른 형식으로 특정 결과 집합을 내보냅니다.

1. 내보낼 데이터베이스 개체를 선택하고 관련 옵션을 구성합니다.
1. **새로 고침** 을 선택 하 여 현재 개체를 로드 합니다.
1. 필요에 따라 내보내기 작업을 구체화 하기 위해 오른쪽 위에 있는 **고급 옵션** 을 선택 합니다. 예를 들어 테이블 잠금을 추가하고, insert 문 대신 replace를 사용하고, 억음 부호로 식별자를 묶는 작업을 수행할 수 있습니다.
1. 내보내기 **시작** 을 선택 하 여 내보내기 프로세스를 시작 합니다.


#### <a name="import-data"></a>데이터 가져오기

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="탐색기 창을 사용 하 여 MySQL 워크 벤치에서 데이터 가져오기 창을 표시 하는 스크린샷":::

**데이터 가져오기** 창을 사용 하 여 데이터 내보내기 작업 또는 mysqldump 명령에서 내보낸 데이터를 가져오거나 복원할 수 있습니다.

1. MySQL 워크 벤치의 **탐색기** 창에서 **데이터 내보내기/복원** 을 선택 합니다.
1. 프로젝트 폴더 또는 자체 포함 된 SQL 파일을 선택 하거나, 가져올 스키마를 선택 하거나, **새로 만들기** 단추를 선택 하 여 새 스키마를 정의 합니다.
1. 가져오기 **시작** 을 선택 하 여 가져오기 프로세스를 시작 합니다.

## <a name="next-steps"></a>다음 단계
- 다른 마이그레이션 방법의 경우 [덤프 및 복원을 사용 하 여 mysql database를 mysql 용 Azure database로 마이그레이션](concepts-migrate-dump-restore.md)을 참조 하세요.
- Azure database for MySQL로 데이터베이스를 마이그레이션하는 방법에 대 한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)를 참조 하세요.
