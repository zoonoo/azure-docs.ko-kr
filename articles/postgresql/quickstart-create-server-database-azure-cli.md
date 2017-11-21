---
title: "Azure 명령줄 인터페이스를 사용하여 Azure Database for PostgreSQL 만들기 | Microsoft Docs"
description: "Azure 명령줄 인터페이스(명령줄 인터페이스)를 통해 Azure Database for PostgreSQL 서버를 만들고 관리하기 위한 빠른 시작 가이드입니다."
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 11/03/2017
ms.openlocfilehash: a47e0c98593f92af6988795779700dc641f3011c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Azure 명령줄 인터페이스를 사용하여 Azure Database for PostgreSQL 만들기
Azure Database for PostgreSQL은 클라우드에서 항상 사용 가능한 PostgreSQL 데이터베이스를 실행, 관리 및 크기 조정할 수 있게 하는 관리 서비스입니다. 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure 명령줄 인터페이스가 사용됩니다. 이 빠른 시작에서는 Azure 명령줄 인터페이스를 사용하여 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)에서 PostgreSQL용 Azure Database 서버를 만드는 방법을 살펴봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

명령줄 인터페이스를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure 명령줄 인터페이스 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure 명령줄 인터페이스 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

CLI를 로컬로 실행 중인 경우 [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) 명령을 사용하여 사용자 계정에 로그인해야 합니다.
```azurecli-interactive
az login
```

구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. [az account set](/cli/azure/account#set) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기

[az postgres server create](/cli/azure/postgres/server#create) 명령을 사용하여 [PostgreSQL용 Azure Database 서버](overview.md)를 만듭니다. 서버는 그룹으로 관리되는 데이터베이스 그룹을 포함합니다. 

다음 예제에서는 `mylogin` 서버 관리자 로그인을 사용하여 `myresourcegroup` 리소스 그룹에 이름이 `mypgserver-20170401`인 서버를 만듭니다. 서버 이름은 DNS 이름에 매핑되므로 Azure에서 전역적으로 고유해야 합니다. `<server_admin_password>`를 자신의 고유한 값으로 직접 바꿉니다.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> 여기에 지정한 서버 관리자 로그인 및 암호는 이 빠른 시작의 뒷부분에 나오는 서버 및 데이터베이스에 로그인해야 합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.

기본적으로 **postgres** 데이터베이스가 서버 아래에 만들어집니다. [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) 데이터베이스는 사용자, 유틸리티 및 타사 응용 프로그램에서 사용하는 기본 데이터베이스입니다. 


## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) 명령을 사용하여 Azure PostgreSQL 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) 또는 [PgAdmin](https://www.pgadmin.org/)과 같은 외부 응용 프로그램에서 Azure PostgreSQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다. 

IP 범위를 적용하는 방화벽 규칙을 설정하여 네트워크에서 연결할 수 있습니다. 다음 예제에서는 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create)를 사용하여 IP 주소 범위에 대한 `AllowAllIps` 방화벽 규칙을 만듭니다. 모든 IP 주소를 열려면 시작 IP 주소로 0.0.0.0을, 끝나는 IP 주소로 255.255.255.255를 사용합니다.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL 서버는 5432 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. IT 부서에서 포트 5432를 열어 Azure SQL Database 서버에 연결할 수 있도록 합니다.

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.
```azurecli-interactive
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

## <a name="connect-to-postgresql-database-using-psql"></a>psql을 사용하여 PostgreSQL 데이터베이스에 연결

클라이언트 컴퓨터에 PostgreSQL이 설치되어 있는 경우 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 로컬 인스턴스를 사용하여 Azure PostgreSQL 서버에 연결할 수 있습니다. 이제 psql 명령줄 유틸리티를 사용하여 Azure PostgreSQL 서버에 연결해 보겠습니다.

1. 다음 psql 명령을 실행하여 Azure Database for PostgreSQL 서버에 연결합니다.
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  예를 들어 다음 명령은 액세스 자격 증명을 사용하여 **mypgserver-20170401.postgres.database.azure.com** PostgreSQL 서버의 **postgres**라는 기본 데이터베이스에 연결합니다. 암호를 묻는 메시지가 표시되면 선택한 `<server_admin_password>`를 입력합니다.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  서버에 연결되면 프롬프트에서 빈 데이터베이스를 만듭니다.
```sql
CREATE DATABASE mypgsqldb;
```

3.  프롬프트에서 다음 명령을 실행하여 새로 만든 **mypgsqldb** 데이터베이스에 대한 연결로 전환합니다.
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>pgAdmin을 사용하여 PostgreSQL 데이터베이스에 연결

GUI 도구 _pgAdmin_을 사용하여 Azure PostgreSQL 서버에 연결하려면
1.  클라이언트 컴퓨터에서 _pgAdmin_ 응용 프로그램을 실행합니다. _pgAdmin_은 http://www.pgadmin.org/에서 설치할 수 있습니다.
2.  **빠른 링크** 메뉴에서 **새 서버 추가**를 선택합니다.
3.  **만들기-서버** 대화 상자의 **일반** 탭에서서버에 대해 고유하면서 익숙한 이름을 입력합니다. **Azure PostgreSQL 서버**라고 하겠습니다.
 ![pgAdmin 도구 - 만들기 - 서버](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  **만들기 - 서버** 대화 상자의 **연결** 탭에서 
    - **호스트 이름/주소** 상자에 정규화된 서버 이름(예: **mypgserver 20170401.postgres.database.azure.com**)을 입력합니다. 
    - **포트** 상자에 포트 5432를 입력합니다. 
    - 이 빠른 시작의 앞 부분에서 구한 **서버 관리자 로그인(user@mypgserver)**과, 서버를 만들 때 입력한 암호를 각각 **사용자 이름**과 **암호**에 입력합니다.
    - **SSL 모드**를 **필수**로 선택합니다. 기본적으로 모든 Azure PostgreSQL 서버는 SSL을 실행한 상태에서 만들어집니다.  SSL 실행을 해제하려면 [SSL 적용](./concepts-ssl-connection-security.md)에서 세부 사항을 참조하세요.

    ![pgAdmin - 만들기 - 서버](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  **Save**를 클릭합니다.
6.  브라우저 왼쪽 창에서 **서버 그룹**을 확장합니다. **Azure PostgreSQL 서버**를 선택합니다.
7.  연결된 **서버**를 선택한 다음 그 아래의 **데이터베이스**를 선택합니다. 
8.  **데이터베이스**를 마우스 오른쪽 단추로 클릭하여 데이터베이스를 만듭니다.
9.  데이터베이스 이름 **mypgsqldb**를 선택하고 그에 대한 소유자를 서버 관리자 로그인 **mylogin**으로 선택합니다.
10. **저장**을 클릭하여 빈 데이터베이스를 만듭니다.
11. **브라우저**에서 **서버** 그룹을 확장합니다. 만든 서버를 확장하고 그 아래에서 **mypgsqldb** 데이터베이스를 확인합니다.
 ![pgAdmin - 만들기 - 데이터베이스](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>리소스 정리

[Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 삭제하여 이 빠른 시작에서 만든 모든 리소스를 정리합니다.

> [!TIP]
> 이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다. 다음 빠른 시작을 계속 진행하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 단계에 따라 이 빠른 시작에서 만든 모든 리소스를 Azure 명령줄 인터페이스에서 삭제합니다.

```azurecli-interactive
az group delete --name myresourcegroup
```

새로 만든 서버만 삭제하려면 [az postgres server delete](/cli/azure/postgres/server#delete) 명령을 실행합니다.
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)
