---
title: 읽기 복제본 관리 - Azure CLI, REST API - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: Azure CLI 및 REST API에서 PostgreSQL - 단일 서버에 대한 Azure 데이터베이스에서 읽기 복제본을 관리하는 방법을 알아봅니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774799"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Azure CLI, REST API에서 읽기 복제본 생성 및 관리

이 문서에서는 Azure CLI 및 REST API를 사용하여 PostgreSQL용 Azure 데이터베이스에서 읽기 복제본을 만들고 관리하는 방법을 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.

## <a name="azure-cli"></a>Azure CLI
Azure CLI를 사용하여 읽기 복제본을 만들고 관리할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 마스터 서버가 될 [Azure Database for PostgreSQL 서버](quickstart-create-server-up-azure-cli.md)


### <a name="prepare-the-master-server"></a>마스터 서버 준비
이러한 단계는 범용 또는 메모리 최적화 계층에서 마스터 서버를 준비하는 데 사용합니다.

마스터 서버에서 `azure.replication_support` 매개 변수를 **REPLICA**로 설정해야 합니다. 이 정적 매개 변수가 변경되면 변경이 적용되려면 서버를 다시 시작해야 합니다.

1. 복제본으로 설정합니다. `azure.replication_support`

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Azure CLI에서 azure.replication_support 설정하는 동안 "잘못된 값"이라는 오류가 발생하면 서버에 기본적으로 REPLICA가 이미 설정되어 있을 수 있습니다. 버그로 인해 REPLICA가 내부 기본값인 최신 서버에 이 설정이 올바르게 반영되지 않습니다. <br><br>
> 마스터 준비 단계를 건너뛰고 복제본을 만들 수 있습니다. <br><br>
> 서버가 이 범주에 있는지 확인하려면 Azure Portal에서 서버의 복제 페이지를 방문하십시오. "복제 사용 안 함"이 회색으로 표시되고 도구 모음에서 "복제본 추가"가 활성화됩니다.

2. 서버를 다시 시작하여 변경 을 적용합니다.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>읽기 복제본 만들기

[az postgres 서버 복제본 만들기](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) 명령에는 다음 매개 변수가 필요합니다.

| 설정 | 예제 값 | 설명  |
| --- | --- | --- |
| resource-group | myresourcegroup |  복제본 서버가 생성되는 리소스 그룹입니다.  |
| name | my데모서버 복제본 | 만들어지는 새 복제본 서버의 이름입니다. |
| source-server | mydemoserver | 복제할 기존 마스터 서버의 이름 또는 리소스 ID입니다. |

아래 CLI 예제에서 복제본은 마스터와 동일한 영역에서 만들어집니다.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

교차 영역 읽기 복제본을 `--location` 만들려면 매개 변수를 사용합니다. 아래 CLI 예제는 미국 서부에서 복제본을 만듭니다.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서를 방문하십시오.](concepts-read-replicas.md) 

범용 또는 메모리 `azure.replication_support` 최적화 마스터 서버에서 매개 변수를 **REPLICA로** 설정하지 않았고 서버를 다시 시작한 경우 오류가 발생합니다. 복제본을 만들기 전에 이 두 단계를 완료합니다.

복제본은 마스터와 동일한 계산 및 저장소 설정을 사용하여 만들어집니다. 복제본을 만든 후에는 마스터 서버와는 별도로 컴퓨팅 생성, vCores, 스토리지 및 백업 보존 기간 등의 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.

> [!IMPORTANT]
> 마스터 서버 설정을 새 값으로 업데이트하기 전에 복제본 설정을 같거나 더 큰 값으로 업데이트합니다. 이 작업은 복제본이 마스터에 대한 변경 내용을 유지하는 데 도움이 됩니다.

### <a name="list-replicas"></a>복제본 목록
[az postgres 서버](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) 복제본 목록 명령을 사용하여 마스터 서버의 복제본 목록을 볼 수 있습니다.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지
az postgres 서버 복제본 중지 명령을 사용하여 마스터 서버와 읽기 복제본 간의 [복제를 중지할](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) 수 있습니다.

마스터 서버와 읽기 복제본에 대한 복제를 중지한 경우 실행 취소할 수 없습니다. 읽기 복제본은 읽기 및 쓰기를 둘 다 지원하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>마스터 또는 복제서버 삭제
마스터 또는 복제본 서버를 삭제하려면 [az postgres 서버 삭제](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) 명령을 사용합니다.

마스터 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
[Azure REST API를](/rest/api/azure/)사용하여 읽기 복제본을 만들고 관리할 수 있습니다.

### <a name="prepare-the-master-server"></a>마스터 서버 준비
이러한 단계는 범용 또는 메모리 최적화 계층에서 마스터 서버를 준비하는 데 사용합니다.

마스터 서버에서 `azure.replication_support` 매개 변수를 **REPLICA**로 설정해야 합니다. 이 정적 매개 변수가 변경되면 변경이 적용되려면 서버를 다시 시작해야 합니다.

1. 복제본으로 설정합니다. `azure.replication_support`

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

2. [서버를 다시 시작하여](/rest/api/postgresql/servers/restart) 변경 을 적용합니다.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>읽기 복제본 만들기
[API 만들기를](/rest/api/postgresql/servers/create)사용하여 읽기 복제본을 만들 수 있습니다.

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
> 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서를 방문하십시오.](concepts-read-replicas.md) 

범용 또는 메모리 `azure.replication_support` 최적화 마스터 서버에서 매개 변수를 **REPLICA로** 설정하지 않았고 서버를 다시 시작한 경우 오류가 발생합니다. 복제본을 만들기 전에 이 두 단계를 완료합니다.

복제본은 마스터와 동일한 계산 및 저장소 설정을 사용하여 만들어집니다. 복제본을 만든 후에는 마스터 서버와는 별도로 컴퓨팅 생성, vCores, 스토리지 및 백업 보존 기간 등의 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.


> [!IMPORTANT]
> 마스터 서버 설정을 새 값으로 업데이트하기 전에 복제본 설정을 같거나 더 큰 값으로 업데이트합니다. 이 작업은 복제본이 마스터에 대한 변경 내용을 유지하는 데 도움이 됩니다.

### <a name="list-replicas"></a>복제본 목록
복제 본문 목록 [API를](/rest/api/postgresql/replicas/listbyserver)사용하여 마스터 서버의 복제본 목록을 볼 수 있습니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지
[업데이트 API를](/rest/api/postgresql/servers/update)사용하여 마스터 서버와 읽기 복제본 간의 복제를 중지할 수 있습니다.

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

### <a name="delete-a-master-or-replica-server"></a>마스터 또는 복제서버 삭제
마스터 또는 복제본 서버를 삭제하려면 [API 삭제를](/rest/api/postgresql/servers/delete)사용합니다.

마스터 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>다음 단계
* [Azure Database for PostgreSQL의 읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아봅니다.
* [Azure Portal에서 읽기 복제본을 만들고 관리하는 방법](howto-read-replicas-portal.md)을 알아봅니다.
