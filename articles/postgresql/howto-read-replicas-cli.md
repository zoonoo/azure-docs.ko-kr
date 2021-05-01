---
title: 읽기 복제본 관리 - Azure CLI, REST API - Azure Database for PostgreSQL - 단일 서버
description: Azure CLI 및 REST API의 Azure Database for PostgreSQL - 단일 서버에서 읽기 복제본을 관리하는 방법 알아보기
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d13db238674cae62f528c3d730bf892a72b8f6c2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764696"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Azure CLI 및 REST API에서 읽기 복제본 만들기 및 관리

이 문서에서는 Azure CLI 및 REST API을 이용하여 Azure Database for PostgreSQL의 읽기 복제본을 만들고 관리하는 방법에 대해 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.

## <a name="azure-replication-support"></a>Azure 복제본 지원
[읽기 복제본](concepts-read-replicas.md)과 [논리 디코딩](concepts-logical.md)은 모두 정보에 대한 Postgres WAL(Write Ahead Log)에 따라 달라집니다. 이러한 두 기능에는 Postgres의 다른 로깅 수준이 필요합니다. 논리 디코딩에는 읽기 복제본보다 높은 수준의 로깅이 필요합니다.

올바른 로깅 수준을 구성하려면 Azure 복제본 지원 매개 변수를 사용합니다. Azure 복제본 지원에는 세 가지 설정 옵션이 있습니다.

* **해제** -WAL에 최소 정보를 저장합니다. 이 설정은 대부분의 Azure Database for PostgreSQL 서버에서 사용할 수 없습니다.  
* **복제본** -**해제** 보다 자세한 정보를 표시합니다. 이는 [읽기 복제본](concepts-read-replicas.md)이 작동하는 데 필요한 최소 수준의 로깅입니다. 이 설정은 대부분의 서버에서 기본값입니다.
* **논리** - **복제본** 보다 자세한 정보를 표시합니다. 논리 디코딩을 작동하기 위한 최소 로깅 수준입니다. 읽기 복제본도 이 설정에서 작동합니다.


> [!NOTE]
> 지속적인 쓰기 집약적 주 워크로드에 대한 읽기 복제본을 배포할 때 복제 지연이 계속 증가할 수 있으며, 주 워크로드의 속도를 따라잡지 못할 수도 있습니다. WAL 파일은 복제본에서 수신될 때까지 삭제되지 않기 때문에 주 워크로드에서 스토리지 사용량이 증가할 수도 있습니다.

## <a name="azure-cli"></a>Azure CLI
Azure CLI를 사용하여 읽기 복제본을 생성하고 관리할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

- [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)
- 주 서버가 될 [Azure Database for PostgreSQL 서버](quickstart-create-server-up-azure-cli.md)입니다.


### <a name="prepare-the-primary-server"></a>주 서버 준비

1. 주 서버의 `azure.replication_support` 값을 확인합니다. 읽기 복제본이 작동하려면 적어도 복제본이어야 합니다.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. `azure.replication_support`가 적어도 복제본이 아닌 경우 다음을 참조하세요. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. 서버를 다시 시작하여 변경 내용을 적용합니다.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>읽기 복제본 만들기

[az postgres server replica create](/cli/azure/postgres/server/replica#az_postgres_server_replica_create) 명령에는 다음 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| resource-group | myresourcegroup |  복제본 서버가 만들어지는 리소스 그룹입니다.  |
| name | mydemoserver-replica | 만들어지는 새 복제본 서버의 이름입니다. |
| source-server | mydemoserver | 복제할 기존 주 서버의 이름 또는 리소스 ID입니다. 복제본 및 마스터의 리소스 그룹을 다르게 하려면 리소스 ID를 사용합니다. |

아래 CLI 예제에서는 복제본이 마스터와 동일한 영역에 생성됩니다.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

영역 간 읽기 복제본을 만들려면 `--location` 매개 변수를 사용합니다. 아래 CLI 예제에서는 미국 서부에 복제본을 만듭니다.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요. 

범용 또는 메모리 최적화 주 서버에서 `azure.replication_support` 매개 변수를 **REPLICA** 로 설정하지 않고 서버를 다시 시작하지 않으면 오류가 발생합니다. 복제본을 만들기 전에 이러한 두 단계를 완료합니다.

> [!IMPORTANT]
> [읽기 복제본 개요의 고려 사항 섹션](concepts-read-replicas.md#considerations)을 검토합니다.
>
> 주 서버 설정을 새 값으로 업데이트하기 전에 복제본 설정을 같거나 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 마스터에 대한 변경 내용을 유지할 수 있습니다.

### <a name="list-replicas"></a>복제본 목록
[az postgres server replica list](/cli/azure/postgres/server/replica#az_postgres_server_replica_list) 명령을 사용하여 주 서버의 복제본 목록을 볼 수 있습니다.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지
[az postgres server replica stop](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) 명령을 사용하여 주 서버와 읽기 복제본 간의 복제를 중지할 수 있습니다.

주 서버와 읽기 복제본에 대한 복제를 중지한 경우 실행 취소할 수 없습니다. 읽기 복제본은 읽기 및 쓰기를 둘 다 지원하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>주 서버 또는 복제 서버 삭제
주 서버 또는 복제 서버를 삭제하려면 [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete) 명령을 사용합니다.

주 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/)를 사용하여 읽기 복제본을 생성하고 관리할 수 있습니다.

### <a name="prepare-the-primary-server"></a>주 서버 준비

1. 주 서버의 `azure.replication_support` 값을 확인합니다. 읽기 복제본이 작동하려면 적어도 복제본이어야 합니다.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. `azure.replication_support`가 적어도 복제본이 아닌 경우 다음을 참조하세요.

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

2. [서버를 다시 시작하여](/rest/api/postgresql/servers/restart) 변경 내용을 적용합니다.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>읽기 복제본 만들기
[CREATE API](/rest/api/postgresql/servers/create)를 사용하여 읽기 복제본을 만들 수 있습니다.

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

범용 또는 메모리 최적화 주 서버에서 `azure.replication_support` 매개 변수를 **REPLICA** 로 설정하지 않고 서버를 다시 시작하지 않으면 오류가 발생합니다. 복제본을 만들기 전에 이러한 두 단계를 완료합니다.

복제본은 주 복제본과 동일한 계산 및 스토리지 설정을 사용하여 생성됩니다. 복제본을 만든 후에는 주 서버와는 별도로 컴퓨팅 생성, vCore, 스토리지 및 백업 보존 기간 등의 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.


> [!IMPORTANT]
> 주 서버 설정을 새 값으로 업데이트하기 전에 복제본 설정을 같거나 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 마스터에 대한 변경 내용을 유지할 수 있습니다.

### <a name="list-replicas"></a>복제본 목록
[복제본 목록 API](/rest/api/postgresql/replicas/listbyserver)를 사용하여 주 서버의 복제본 목록을 볼 수 있습니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지
[업데이트 API](/rest/api/postgresql/servers/update)를 사용하여 주 서버와 읽기 복제본 간의 복제를 중지할 수 있습니다.

주 서버와 읽기 복제본에 대한 복제를 중지한 경우 실행 취소할 수 없습니다. 읽기 복제본은 읽기 및 쓰기를 둘 다 지원하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>주 서버 또는 복제 서버 삭제
주 서버 또는 복제 서버를 삭제하려면 [API 삭제](/rest/api/postgresql/servers/delete)를 사용합니다.

주 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>다음 단계
* [Azure Database for PostgreSQL의 읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아봅니다.
* [Azure Portal에서 읽기 복제본을 만들고 관리하는 방법](howto-read-replicas-portal.md)을 알아봅니다.