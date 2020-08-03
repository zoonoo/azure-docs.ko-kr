---
title: '빠른 시작: 서버 만들기 - Azure CLI - Azure Database for PostgreSQL - 단일 서버'
description: Azure CLI(명령줄 인터페이스)를 통해 Azure Database for PostgreSQL - 단일 서버를 만들기 위한 빠른 시작 가이드입니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: d103ed0ebd565df77032237638c775991324ea44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030184"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Database for PostgreSQL - 단일 서버 만들기

> [!TIP]
> 더 단순한 [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI 명령(현재 미리 보기로 제공)을 사용하는 것이 좋습니다. [빠른 시작](./quickstart-create-server-up-azure-cli.md)을 사용해 보세요.

이 빠른 시작에서는 [Azure Cloud Shell](https://shell.azure.com)에서 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 명령을 사용하여 5분 안에 Azure Database for PostgreSQL 서버를 만드는 방법을 보여 줍니다.  Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 Azure CLI 버전 2.0 이상을 로컬로 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 사용하여 계정에 로그인해야 합니다. Azure 계정에 대한 **구독 ID**를 참조하는 **id** 속성을 기록해 둡니다. 

```azurecli-interactive
az login
```

[az account set](/cli/azure/account) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다. 명령에서 **subscription** 인수에 대한 값으로 사용할 **az login** 출력의 **id** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. 모든 구독을 가져오려면 [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)를 사용합니다.

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기

[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 만든 다음, 이 리소스 그룹 내에 PostgreSQL 서버를 만듭니다. 고유한 이름을 제공해야 합니다. 다음 예제에서는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

[az postgres server create](/cli/azure/postgres/server) 명령을 사용하여 [PostgreSQL용 Azure Database 서버](overview.md)를 만듭니다. 서버는 여러 데이터베이스를 포함할 수 있습니다.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
위의 인수에 대한 세부 정보는 다음과 같습니다. 

**설정** | **샘플 값** | **설명**
---|---|---
name | mydemoserver | Azure Database for PostgreSQL 서버를 식별하는 고유한 이름을 선택합니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자여야 합니다.
resource-group | myresourcegroup | Azure 리소스 그룹의 이름을 입력합니다.
위치 | westus | 서버에 대한 Azure 위치입니다.
admin-user | myadmin | 관리자 로그인에 대한 사용자 이름입니다. **azure_superuser**, **admin**, **administrator**, **root**, **guest** 또는 **public**을 사용할 수 없습니다.
admin-password | *보안 암호* | 관리자 사용자의 암호입니다. 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자 및 영숫자가 아닌 문자
sku-name|GP_Gen5_2|가격 책정 계층 및 컴퓨팅 구성의 이름을 입력합니다. {가격 책정 계층} _{계산 세대}_ {vCores} 규칙을 축약형으로 따릅니다. 자세한 내용은 [Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/)을 참조하세요.

>[!IMPORTANT] 
>- 서버의 기본 PostgreSQL 버전은 9.6입니다. [여기](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)에서 지원되는 모든 버전을 참조하세요.
>- **az postgres server create** 명령에 대한 모든 인수를 보려면 이 [참조 문서](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create)를 참조하세요.
>- SSL은 서버에서 기본적으로 사용하도록 설정됩니다. SSL에 대한 자세한 내용은 [SSL 연결 구성](./concepts-ssl-connection-security.md)을 참조하세요.

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성 
기본적으로 만들어진 서버는 공개적으로 액세스할 수 없으며 방화벽 규칙으로 보호됩니다. [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) 명령을 사용하여 서버의 방화벽 규칙을 구성하여 로컬 환경에 서버에 연결할 수 있는 액세스 권한을 부여할 수 있습니다. 

다음 예제는 특정 IP 주소 192.168.0.1에서 연결하는 것을 허용하는 `AllowMyIP` 방화벽 규칙을 만듭니다. 연결할 위치에 해당하는 IP 주소 또는 IP 주소 범위로 바꿉니다.  IP를 검색하는 방법을 모르는 경우 [https://whatismyipaddress.com/](https://whatismyipaddress.com/)으로 이동하여 IP 주소를 가져옵니다.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  네트워크의 방화벽이 연결 문제를 방지하기 위해 Azure Database for PostgreSQL 서버에서 사용하는 포트 5432를 허용하는지 확인하세요. 

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

결과는 JSON 형식입니다. **administratorLogin** 및 **fullyQualifiedDomainName**을 기록해 둡니다.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>psql을 사용하여 Azure Database for PostgreSQL 서버에 연결
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html)은 PostgreSQL 서버에 연결하는 데 사용되는 인기 클라이언트입니다. [Azure Cloud Shell](../cloud-shell/overview.md)에서 **psql**을 사용하여 서버에 연결할 수 있습니다. 또는 사용 가능한 경우 로컬 환경에서 psql을 사용할 수 있습니다. 빈 데이터베이스 'postgres'는 다음과 같이 psql에 연결하는 데 사용할 수 있는 새 PostgreSQL 서버를 사용하여 이미 만들어졌습니다. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > URL 경로를 사용하여 Postgres에 연결하려는 경우 URL의 사용자 이름에서 @ 기호가 `%40`으로 인코딩됩니다. 예를 들어 psql에 대한 연결 문자열은 다음과 같습니다.
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>리소스 정리
다른 빠른 시작/자습서에서 이러한 리소스가 필요하지 않으면 다음 명령을 실행하여 삭제할 수 있습니다. 

```azurecli-interactive
az group delete --name myresourcegroup
```

새로 만든 서버만 삭제하려면 [az postgres server delete](/cli/azure/postgres/server) 명령을 실행합니다.
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)
> 
> [PostgreSQL을 사용하는 Django 웹앱 배포](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Node.JS 앱을 사용하여 연결](./connect-nodejs.md)

