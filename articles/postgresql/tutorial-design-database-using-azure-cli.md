---
title: "Azure CLI를 사용하여 첫 번째 Azure Database for PostgreSQL 디자인 | Microsoft Docs"
description: "이 자습서에서는 Azure CLI를 사용하여 첫 번째 Azure Database for PostgreSQL을 디자인하는 방법을 보여 줍니다."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fbac141243f0f36aab1a2ef9f28b4e107fd2d390
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Azure CLI를 사용하여 Azure Database for PostgreSQL을 디자인합니다 
이 자습서에서는 Azure CLI(명령줄 인터페이스) 및 기타 유틸리티를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Database for PostgreSQL 만들기
> * 서버 방화벽 구성
> * [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) 유틸리티를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트
> * 데이터 복원

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>Azure에 로그인

[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.
```azurecli
az login
```

구독이 여러 개인 경우 리소스가 있거나 요금이 청구되는 적절한 구독을 선택합니다. [az account set](/cli/azure/account#set) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다.
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버 만들기

**az postgres server create** 명령을 사용하여 [Azure Database for PostgreSQL 서버](overview.md)를 만듭니다. 서버는 그룹으로 관리되는 데이터베이스 그룹을 포함합니다. 

다음 예제에서는 `mylogin` 서버 관리자 로그인을 사용하여 `myresourcegroup` 리소스 그룹에 `mypgserver-20170401`이라는 서버를 만듭니다. 서버 이름은 DNS 이름에 매핑되므로 Azure에서 전역적으로 고유해야 합니다. `<server_admin_password>`를 자신의 고유한 값으로 직접 바꿉니다.
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> 여기에 지정한 서버 관리자 로그인 및 암호는 이 빠른 시작의 뒷부분에 나오는 서버 및 데이터베이스에 로그인해야 합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.

기본적으로 **postgres** 데이터베이스가 서버 아래에 만들어집니다. [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) 데이터베이스는 사용자, 유틸리티 및 타사 응용 프로그램에서 사용하는 기본 데이터베이스입니다. 


## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

**az postgres server firewall-rule create** 명령을 사용하여 Azure PostgreSQL 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) 또는 [PgAdmin](https://www.pgadmin.org/)과 같은 외부 응용 프로그램에서 Azure PostgreSQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다. 

IP 범위를 적용하는 방화벽 규칙을 설정하여 네트워크에서 연결할 수 있습니다. 다음 예제에서는 **az postgres server firewall-rule create**를 사용하여 IP 주소 범위에 대한 `AllowAllIps` 방화벽 규칙을 만듭니다. 모든 IP 주소를 열려면 시작 IP 주소로 0.0.0.0을, 끝나는 IP 주소로 255.255.255.255를 사용합니다.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL 서버는 5432 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 5432 포트를 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.
>

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.
```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

결과는 JSON 형식입니다. **administratorLogin** 및 **fullyQualifiedDomainName**을 기록해 둡니다.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>psql을 사용하여 Azure Database for PostgreSQL 데이터베이스에 연결

클라이언트 컴퓨터에 PostgreSQL이 설치되어 있는 경우 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 로컬 인스턴스를 사용하여 Azure PostgreSQL 서버에 연결할 수 있습니다. 이제 psql 명령줄 유틸리티를 사용하여 Azure PostgreSQL 서버에 연결해 보겠습니다.

1. 다음 psql 명령을 실행하여 Azure Database for PostgreSQL 서버에 연결합니다.
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  예를 들어 다음 명령은 액세스 자격 증명을 사용하여 **mypgserver-20170401.postgres.database.azure.com** PostgreSQL 서버의 **postgres**라는 기본 데이터베이스에 연결합니다. 암호를 묻는 메시지가 표시되면 선택한 `<server_admin_password>`를 입력합니다.
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  서버에 연결되면 프롬프트에서 빈 데이터베이스를 만듭니다.
```bash
CREATE DATABASE mypgsqldb;
```

3.  프롬프트에서 다음 명령을 실행하여 새로 만든 **mypgsqldb** 데이터베이스에 대한 연결로 전환합니다.
```bash
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기
이제 Azure Database for PostgreSQL에 연결하는 방법을 알았으므로 몇 가지 기본 작업을 수행하는 방법을 살펴볼 수 있습니다.

먼저 테이블을 만들고 일부 데이터와 함께 로드할 수 있습니다. 인벤토리 정보를 추적하는 테이블을 만들어 보겠습니다.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

다음과 같이 입력하면 테이블 목록에서 새로 만든 테이블을 볼 수 있습니다.
```bash
\dt
```

## <a name="load-data-into-the-tables"></a>테이블에 데이터 로드
이제 테이블을 만들었으므로 이 테이블에 일부 데이터를 삽입할 수 있습니다. 열린 명령 프롬프트 창에서 다음 쿼리를 실행하여 데이터 행을 일부 삽입합니다.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

이제 앞에서 만든 테이블에 두 개의 샘플 데이터 행이 있습니다.

## <a name="query-and-update-the-data-in-the-tables"></a>테이블의 데이터 쿼리 및 업데이트
다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다. 
```sql
SELECT * FROM inventory;
```

또한 테이블의 데이터를 업데이트할 수도 있습니다
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

이에 따라 데이터를 검색할 때 해당 행이 업데이트됩니다.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>이전 시점으로 데이터베이스 복원
실수로 테이블을 삭제한 경우를 가정해 보겠습니다. 이런 경우는 쉽게 복구할 수 없는 경우입니다. Azure Database for PostgreSQL를 사용하면 특정 시점(마지막 시점에서 최대 7일 전(기본) 및 35일 전(표준))으로 되돌아가 이 시점의 새 서버로 복원할 수 있습니다. 이 새 서버를 사용하여 삭제된 데이터를 복구할 수 있습니다. 다음 단계에서는 테이블이 추가되기 이전 시점으로 샘플 서버를 복원합니다.

복원에는 다음 정보가 필요합니다.
- **복원 지점:** 데이터베이스를 변경하기 전의 특정 시점을 선택합니다. 원본 데이터베이스의 가장 오래된 백업 값보다 크거나 같아야 합니다.
- **대상 서버:** 복원해 두려는 새 서버의 이름을 제공합니다.
- **원본 서버:** 복원해 오려는 서버의 이름을 제공합니다.
- **위치:** 지역은 선택할 수 없으며, 기본적으로 원본 서버와 동일합니다.
- **가격 책정 계층:** 서버를 복원할 때 이 값을 변경할 수 없습니다. 원본 서버와 동일합니다. 

```azurecli
az postgres server restore --resource-group myResourceGroup --name mypgserver-20170401-restored --restore-point-in-time "2017-04-13 03:10" --source-server-name mypgserver-20170401
```

서버를 테이블 삭제 이전의 [특정 시점으로 복원](./howto-restore-server-portal.md)합니다. 다른 특정 시점으로 서버를 복원하는 경우 [서비스 계층](./concepts-service-tiers.md)에 대한 보존 기간 내에 있다면 중복된 새 서버를 지정한 시점의 원본 서버로 만들어집니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure CLI(명령줄 인터페이스) 및 기타 유틸리티를 사용하여 다음을 수행하는 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> * Azure Database for PostgreSQL 만들기
> * 서버 방화벽 구성
> * [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) 유틸리티를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트
> * 데이터 복원

다음에는 Azure Portal을 사용하여 유사한 작업을 수행하는 방법에 대해 알아보고, [Azure Portal을 사용하여 첫 번째 Azure Database for PostgreSQL 디자인](tutorial-design-database-using-azure-portal.md) 자습서를 검토합니다.

