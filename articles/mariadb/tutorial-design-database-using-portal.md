---
title: '자습서: Azure Portal을 사용하여 Azure Database for MariaDB 디자인'
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Database for MariaDB 서버 및 데이터베이스를 만들고 관리하는 방법에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: tutorial
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: 1938a84d3e9c0ba8c84cbdbd2eee5b7ca448554d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515648"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 Azure Database for MariaDB 데이터베이스 디자인

Azure Database for MariaDB는 클라우드에서 고가용성 MySQL 데이터베이스를 실행, 관리 및 확장하는 데 사용하는 관리 서비스입니다. Azure Portal을 사용하면 쉽게 서버를 관리하고 데이터베이스를 디자인할 수 있습니다.

이 자습서에서는 Azure Portal을 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Database for MariaDB 만들기
> * 서버 방화벽 구성
> * mysql 명령줄 도구를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트
> * 데이터 복원

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버 만들기

Azure Database for MariaDB 서버는 정의된 [계산 및 스토리지 리소스](concepts-pricing-tiers.md) 세트를 사용하여 만듭니다. 서버는 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 내에 만들어집니다.

1. 포털의 왼쪽 상단 모서리에서 **리소스 만들기** 단추(+)를 선택합니다.

2. **데이터베이스** > **Azure Database for MariaDB**를 차례로 선택합니다. 검색 상자에서 **MariaDB**를 입력하여 해당 서비스를 찾을 수도 있습니다.
   
   ![MySQL로 이동](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. **Azure Database for MariaDB** 타일을 선택한 다음, **만들기**를 선택합니다. 필요한 정보를 입력하거나 선택합니다.
   
   ![양식 만들기](./media/tutorial-design-database-using-portal/2-create-form.png)

    설정 | 제안 값 | 필드 설명 
    ---|---|---
    서버 이름 | *고유 서버 이름* | Azure Database for MariaDB 서버를 식별하는 고유한 이름을 선택합니다. 예를 들어 **mydemoserver**가 있습니다. 입력한 서버 이름에 도메인 이름 *.mariadb.database.azure.com*이 추가됩니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3~63자여야 합니다.
    구독 | *구독* | 서버에 사용할 Azure 구독을 선택합니다. 구독이 여러 개인 경우 해당 리소스에 대해 요금이 청구되는 적절한 구독을 선택합니다.
    리소스 그룹 | **myresourcegroup** | 리소스 그룹 이름을 입력하거나 기존 리소스 그룹을 선택합니다.
    원본 선택 | **비어 있음** | 새 서버를 만들려면 **비어 있음**을 선택합니다. 기존 Azure Database for MariaDB 서버의 지역 백업에서 서버를 만드는 경우 **백업**을 선택합니다.
    서버 관리자 로그인 | **myadmin** | 서버 연결에 사용할 로그인 계정입니다. 관리자 로그인 이름은 **azure_superuser**, **admin**, **administrator**, **root**, **guest** 또는 **public**일 수 없습니다.
    암호 | *사용자 선택* | 서버 관리자 계정의 새 암호를 입력합니다. 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).
    암호 확인 | *사용자 선택*| 관리자 계정 암호를 확인합니다.
    위치 | *사용자와 가장 가까운 지역*| 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다.
    버전 | *최신 버전*| 최신 버전입니다(다른 버전을 사용하는 특정 요구 사항이 없는 경우).
    가격 책정 계층 | 설명을 참조하세요. | 새 서버에 대한 계산, 스토리지 및 백업 구성입니다. **가격 책정 계층** > **범용**을 선택합니다. 다음 설정에 대해 기본값을 그대로 유지합니다.<br><ul><li>**컴퓨팅 세대**(Gen 5)</li><li>**vCore**(vCore 4개)</li><li>**스토리지**(100GB)</li><li>**백업 보존 기간**(7일)</li></ul><br>지역 중복 저장소에서 서버 백업을 사용하도록 설정하려면 **백업 중복 옵션**에서 **지역 중복**을 선택합니다. <br><br>이 가격 책정 계층 선택을 저장하려면 **확인**을 선택합니다. 다음 스크린샷은 이러한 선택을 캡처한 것입니다.
    
   ![가격 책정 계층](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

   > [!TIP]
   > **자동 증가**를 사용하면 워크로드에 영향을 주지 않고 할당된 제한에 도달할 때 서버가 스토리지를 늘립니다.

4. **만들기**를 선택합니다. 1, 2분 안에 새 Azure Database for MariaDB 서버가 클라우드에서 실행됩니다. 배포 프로세스를 모니터링하려면 도구 모음에서 **알림**을 클릭합니다.

## <a name="configure-the-firewall"></a>방화벽 구성

Azure Databases for MariaDB는 방화벽으로 보호됩니다. 기본적으로 서버 및 서버 내의 데이터베이스에 대한 모든 연결은 거부됩니다. 처음으로 Azure Database for MariaDB에 연결하기 전에 먼저 방화벽을 구성하여 클라이언트 컴퓨터의 공용 네트워크 IP 주소(또는 IP 주소 범위)를 추가합니다.

1. 새로 만든 서버를 선택한 다음, **연결 보안**을 선택합니다.
   
   ![연결 보안](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. **내 IP 추가**를 선택하거나 여기서 방화벽 규칙을 구성할 수 있습니다. 규칙을 만든 후에는 **저장**해야 합니다.

이제 mysql 명령줄 도구 또는 MySQL Workbench를 사용하여 서버에 연결할 수 있습니다.

> [!TIP]
> Azure Databases for MariaDB 서버는 3306 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 3306 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 Azure Database for MariaDB 서버에 연결하려면 IT 부서에서 포트 3306을 열어야 합니다.

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure Portal에서 Azure Database for MariaDB 서버의 정규화된 **서버 이름** 및 **서버 관리자 로그인 이름**을 가져옵니다. 정규화된 서버 이름을 사용하여 mysql 명령줄 도구를 통해 서버에 연결합니다. 

1. [Azure Portal](https://portal.azure.com/)의 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 서버 이름을 입력하고 Azure Database for MariaDB 서버를 검색합니다. 서버 이름을 선택하여 서버 세부 정보를 표시합니다.

2. **개요 페이지**에서 **서버 이름** 및 **서버 관리자 로그인 이름**의 값을 기록해 둡니다. 각 필드 옆에 있는 **복사** 단추를 클릭하여 해당 값을 클립보드에 복사할 수도 있습니다.

   ![서버 속성](./media/tutorial-design-database-using-portal/2-server-properties.png)

이 예에서 서버 이름은 **mydemoserver.mariadb.database.azure.com**이고, 서버 관리자 로그인 이름은 **myadmin\@mydemoserver**입니다.

## <a name="connect-to-the-server-by-using-mysql"></a>mysql을 사용하여 서버에 연결

[mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)를 사용하여 Azure Database for MariaDB 서버에 연결합니다. 브라우저의 Azure Cloud Shell에서나 로컬로 설치된 mysql 도구를 사용하여 사용자 컴퓨터에서 mysql 명령줄 도구를 실행할 수 있습니다. Azure Cloud Shell을 시작하려면 이 문서의 코드 블록에서 **Try It** 단추를 클릭하거나 Azure Portal의 오른쪽 위 도구 모음에서 **>_** 아이콘을 클릭합니다. 

연결할 명령을 입력합니다.

```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>빈 데이터베이스 만들기

서버에 연결되면 사용할 빈 데이터베이스를 만듭니다.

```sql
CREATE DATABASE mysampledb;
```

프롬프트에서 다음 명령을 실행하여 새로 만든 이 데이터베이스에 대한 연결로 전환합니다.

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기

이제 Azure Database for MariaDB 데이터베이스에 연결하는 방법을 알았으므로 몇 가지 기본 작업을 수행할 수 있습니다.

먼저 테이블을 만들고 일부 데이터와 함께 로드합니다. 인벤토리 정보를 저장하는 테이블을 만들어 보겠습니다.

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>테이블에 데이터 로드

이제 테이블을 만들었으므로 이 테이블에 일부 데이터를 삽입합니다. 열린 명령 프롬프트 창에서 다음 쿼리를 실행하여 데이터 행을 삽입합니다.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>테이블의 데이터 쿼리 및 업데이트

다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다.

```sql
SELECT * FROM inventory;
```

또한 테이블의 데이터를 업데이트할 수도 있습니다

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

행은 데이터를 검색할 때 업데이트됩니다.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>이전 시점으로 데이터베이스 복원

실수로 중요한 데이터베이스 테이블을 삭제했는데 데이터를 쉽게 복구할 수 없다고 상상해 봅니다. Azure Database for MariaDB를 사용하면 서버를 지정 시간으로 복원하여 데이터베이스의 복사본을 새 서버에 만들 수 있습니다. 이 새 서버를 사용하여 삭제된 데이터를 복구할 수 있습니다. 다음 단계에서는 테이블이 추가되기 이전 시점으로 샘플 서버를 복원합니다.

1. Azure Portal에서 Azure Database for MariaDB를 찾습니다. **개요** 페이지에서 **복원**을 선택합니다.

   ![데이터베이스 복원](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. **복원** 페이지에서 다음 정보를 입력하거나 선택합니다.
   
   ![복원 양식](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **복원 지점**: 나열된 기간 내에서 복원하려는 지정 시간을 선택합니다. 현지 표준 시간대를 UTC로 변환해야 합니다.
   - **새 서버로 복원**: 복원하려는 새 서버 이름을 입력합니다.
   - **위치**: 지역은 원본 서버와 같으며 변경할 수 없습니다.
   - **가격 책정 계층**: 가격 책정 계층은 원본 서버와 같으며 변경할 수 없습니다.
   
3. **확인**을 선택하여 서버를 테이블 삭제 이전의 [지정 시간](./howto-restore-server-portal.md)으로 복원합니다. 서버를 복원하면 사용자가 선택한 시간을 기준으로 서버의 새 복사본이 만들어집니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal을 사용하여 다음을 수행하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Database for MariaDB 만들기
> * 서버 방화벽 구성
> * mysql 명령줄 도구를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트
> * 데이터 복원

> [!div class="nextstepaction"]
> [Azure Database for MariaDB에 응용 프로그램을 연결하는 방법](./howto-connection-string.md)
