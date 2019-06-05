---
title: Azure CLI로부터 읽기 복제본 만들기 및 관리
description: Azure CLI에서 Azure Database for PostgreSQL - 단일 서버의 읽기 복제본을 관리하는 방법에 대해 알아봅니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 9a6a1a744a8441d2f082d4d14a3aba8aa1cfc09e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306029"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Azure CLI에서 읽기 복제본 만들기 및 관리

이 문서에서는 Azure CLI에서 Azure Database for PostgreSQL의 읽기 복제본을 만들고 관리하는 방법을 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.


> [!NOTE]
> Azure CLI 마스터 서버에서 다른 지역에 복제본을 만드는 것은 아직 지원하지 않습니다. 교차 지역 복제본을 만들려면 [Azure portal](howto-read-replicas-portal.md)을 사용합니다.


## <a name="prerequisites"></a>필수 조건
- 마스터 서버가 될 [Azure Database for PostgreSQL 서버](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 


## <a name="prepare-the-master-server"></a>마스터 서버 준비
이러한 단계는 범용 또는 메모리 최적화 계층에서 마스터 서버를 준비하는 데 사용합니다.

마스터 서버에서 `azure.replication_support` 매개 변수를 **REPLICA**로 설정해야 합니다. 이 정적 매개 변수가 변경되면 변경 내용을 적용하기 위해 서버 재시작이 필요합니다.

1. `azure.replication_support`를 REPLICA로 설정합니다.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. 서버에 변경 내용을 적용 하려면 다시 시작 합니다.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>읽기 복제본 만들기

[az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) 명령에는 다음 매개 변수가 필요합니다.

| 설정 | 예제 값 | 설명  |
| --- | --- | --- |
| resource-group | myresourcegroup |  복제 서버가 만들어지는 리소스 그룹.  |
| name | mydemoserver-replica | 만들어지는 새 복제본 서버의 이름입니다. |
| source-server | mydemoserver | 복제에 기존 마스터 서버 이름 또는 리소스 ID입니다. |

다음 CLI 예제에서는 복제본 마스터와 동일한 지역에 만들어집니다.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```


크로스 만들려는 지역 읽기 복제본을 사용 하 여는 `--location` 매개 변수입니다. 다음 CLI 예제에서는 미국 서 부에 복제본을 만듭니다.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```


복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본을 만든 후에는 마스터 서버와는 별도로 컴퓨팅 세대, vCore, 스토리지 및 백업 보존 기간 등의 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우 이외의 다른 방식으로 독립적으로 변경할 수 있습니다.

> [!IMPORTANT]
> 마스터 서버 구성을 새 값으로 업데이트하기 전에 복제본의 구성을 같거나 더 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 마스터에 대한 변경 내용을 유지할 수 있습니다.

## <a name="list-replicas"></a>복제본 목록
[az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) 명령을 사용하여 마스터 서버의 복제본 목록을 볼 수 있습니다.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지
[az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) 명령을 사용하여 마스터 서버와 읽기 복제본 간의 복제를 중지할 수 있습니다.

범용 또는 메모리 액세스에 최적화된 마스터 서버에서 `azure.replication_support` 매개 변수를 **REPLICA**로 설정하지 않고 서버를 다시 시작하게 되면 오류가 발생합니다. 복제본을 만들기 전에 이 두 단계를 완료합니다.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>마스터 또는 복제본 서버 삭제
[az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) 명령을 사용하여 마스터 또는 복제본 서버를 삭제합니다.

마스터 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>다음 단계
[Azure Database for PostgreSQL의 읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아봅니다.
