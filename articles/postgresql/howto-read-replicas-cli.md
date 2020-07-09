---
title: 읽기 복제본 관리-Azure CLI, REST API Azure Database for PostgreSQL 단일 서버
description: Azure CLI에서 Azure Database for PostgreSQL 단일 서버에서 읽기 복제본을 관리 하는 방법에 대해 알아봅니다 REST API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: b8da326ea48133d2029f385fc55450c00aecf656
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106614"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Azure CLI에서 읽기 복제본을 만들고 관리 REST API

이 문서에서는 Azure CLI 및 REST API를 사용 하 여 Azure Database for PostgreSQL에서 읽기 복제본을 만들고 관리 하는 방법에 대해 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.

## <a name="azure-replication-support"></a>Azure 복제 지원
[읽기 복제본](concepts-read-replicas.md) 과 [논리적 디코딩](concepts-logical.md) 은 모두 정보에 대 한 postgres WRITE 미리 로그 (WAL)에 따라 달라 집니다. 이러한 두 기능에는 Postgres의 다른 로깅 수준이 필요 합니다. 논리적 디코딩에는 읽기 복제본 보다 높은 수준의 로깅이 필요 합니다.

올바른 로깅 수준을 구성 하려면 Azure replication support 매개 변수를 사용 합니다. Azure 복제 지원에는 세 가지 설정 옵션이 있습니다.

* **Off** -WAL에 최소 정보를 저장 합니다. 이 설정은 대부분의 Azure Database for PostgreSQL 서버에서 사용할 수 없습니다.  
* **복제본** -보다 자세한 정보를 **해제**합니다. 이는 [읽기 복제본](concepts-read-replicas.md) 이 작동 하는 데 필요한 최소 수준의 로깅입니다. 이 설정은 대부분의 서버에서 기본값입니다.
* **논리적** - **복제본**보다 자세한 정보를 표시 합니다. 논리적 디코딩을 작동 하기 위한 최소 로깅 수준입니다. 읽기 복제본도이 설정에서 작동 합니다.

이 매개 변수를 변경한 후에는 서버를 다시 시작 해야 합니다. 내부적으로이 매개 변수는 Postgres 매개 변수, 및를 설정 합니다 `wal_level` `max_replication_slots` `max_wal_senders` .

## <a name="azure-cli"></a>Azure CLI
Azure CLI를 사용 하 여 읽기 복제본을 만들고 관리할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

- [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 마스터 서버가 될 [Azure Database for PostgreSQL 서버](quickstart-create-server-up-azure-cli.md)


### <a name="prepare-the-master-server"></a>마스터 서버 준비

1. 마스터 서버의 값을 확인 `azure.replication_support` 합니다. 읽기 복제본이 작동 하려면 적어도 복제본 이어야 합니다.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. `azure.replication_support`가 적어도 복제본이 아닌 경우 설정 합니다. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. 서버를 다시 시작 하 여 변경 내용을 적용 합니다.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>읽기 복제본 만들기

[Az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) 명령에는 다음 매개 변수가 필요 합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| resource-group | myresourcegroup |  복제본 서버가 생성 될 리소스 그룹입니다.  |
| name | mydemoserver-복제본 | 만들어지는 새 복제본 서버의 이름입니다. |
| source-server | mydemoserver | 복제할 기존 마스터 서버의 이름 또는 리소스 ID입니다. 복제본 및 마스터의 리소스 그룹을 다르게 하려면 리소스 ID를 사용 합니다. |

아래 CLI 예제에서 복제본은 마스터와 동일한 지역에 생성 됩니다.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

지역 간 읽기 복제본을 만들려면 매개 변수를 사용 `--location` 합니다. 아래 CLI 예제에서는 미국 서 부에 복제본을 만듭니다.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요. 

`azure.replication_support`범용 또는 메모리 액세스에 최적화 된 마스터 서버에서 매개 변수를 **복제본** 으로 설정 하지 않은 경우 서버를 다시 시작 하면 오류가 발생 합니다. 복제본을 만들기 전에이 두 단계를 완료 합니다.

복제본은 마스터와 동일한 계산 및 저장소 설정을 사용 하 여 생성 됩니다. 복제본을 만든 후에는 마스터 서버와는 별도로 컴퓨팅 생성, vCores, 스토리지 및 백업 보존 기간 등의 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.

> [!IMPORTANT]
> 마스터 서버 설정을 새 값으로 업데이트 하기 전에 복제본 설정을 같거나 큰 값으로 업데이트 합니다. 이 작업을 수행 하면 복제본이 마스터의 모든 변경 내용을 유지 하는 데 도움이 됩니다.

### <a name="list-replicas"></a>복제본 나열
[Az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) 명령을 사용 하 여 마스터 서버의 복제본 목록을 볼 수 있습니다.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지
[Az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) 명령을 사용 하 여 마스터 서버와 읽기 복제본 간의 복제를 중지할 수 있습니다.

마스터 서버와 읽기 복제본에 대한 복제를 중지한 경우 실행 취소할 수 없습니다. 읽기 복제본은 읽기 및 쓰기를 둘 다 지원하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>마스터 서버 또는 복제 서버 삭제
마스터 서버 또는 복제 서버를 삭제 하려면 [az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) 명령을 사용 합니다.

마스터 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/)를 사용 하 여 읽기 복제본을 만들고 관리할 수 있습니다.

### <a name="prepare-the-master-server"></a>마스터 서버 준비

1. 마스터 서버의 값을 확인 `azure.replication_support` 합니다. 읽기 복제본이 작동 하려면 적어도 복제본 이어야 합니다.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. `azure.replication_support`가 적어도 복제본이 아닌 경우 설정 합니다.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [서버를 다시 시작](/rest/api/postgresql/servers/restart) 하 여 변경 내용을 적용 합니다.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>읽기 복제본 만들기
[CREATE API](/rest/api/postgresql/servers/create)를 사용 하 여 읽기 복제본을 만들 수 있습니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요. 

`azure.replication_support`범용 또는 메모리 액세스에 최적화 된 마스터 서버에서 매개 변수를 **복제본** 으로 설정 하지 않은 경우 서버를 다시 시작 하면 오류가 발생 합니다. 복제본을 만들기 전에이 두 단계를 완료 합니다.

복제본은 마스터와 동일한 계산 및 저장소 설정을 사용 하 여 생성 됩니다. 복제본을 만든 후에는 마스터 서버와는 별도로 컴퓨팅 생성, vCores, 스토리지 및 백업 보존 기간 등의 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.


> [!IMPORTANT]
> 마스터 서버 설정을 새 값으로 업데이트 하기 전에 복제본 설정을 같거나 큰 값으로 업데이트 합니다. 이 작업을 수행 하면 복제본이 마스터의 모든 변경 내용을 유지 하는 데 도움이 됩니다.

### <a name="list-replicas"></a>복제본 나열
[복제본 목록 API](/rest/api/postgresql/replicas/listbyserver)를 사용 하 여 마스터 서버의 복제본 목록을 볼 수 있습니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지
[업데이트 API](/rest/api/postgresql/servers/update)를 사용 하 여 마스터 서버와 읽기 복제본 간의 복제를 중지할 수 있습니다.

마스터 서버와 읽기 복제본에 대한 복제를 중지한 경우 실행 취소할 수 없습니다. 읽기 복제본은 읽기 및 쓰기를 둘 다 지원하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>마스터 서버 또는 복제 서버 삭제
마스터 서버 또는 복제 서버를 삭제 하려면 [DELETE API](/rest/api/postgresql/servers/delete)를 사용 합니다.

마스터 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>다음 단계
* [Azure Database for PostgreSQL의 읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아봅니다.
* [Azure Portal에서 읽기 복제본을 만들고 관리하는 방법](howto-read-replicas-portal.md)을 알아봅니다.
