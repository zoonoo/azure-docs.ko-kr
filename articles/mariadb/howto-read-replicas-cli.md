---
title: Azure Database for MariaDB에서 읽기 복제본 만들기 및 관리
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure Database for MariaDB에서 읽기 복제본을 설정 하 고 관리 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 7944f985f2317690f3a13add783192c49acbe22f
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907655"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for MariaDB에서 읽기 복제본을 만들고 관리 하는 방법

이 문서에서는 Azure CLI를 사용 하 여 Azure Database for MariaDB 서비스에서 마스터와 동일한 Azure 지역 내에서 읽기 복제본을 만들고 관리 하는 방법에 대해 설명 합니다.

> [!IMPORTANT]
> 마스터 서버와 동일한 지역 또는 선택한 다른 Azure 지역에서 읽기 복제본을 만들 수 있습니다. 읽기 복제본 (동일한 지역 및 지역 간)은 현재 공개 미리 보기로 제공 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 마스터 서버로 사용 되는 [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-portal.md) 입니다. 

> [!IMPORTANT]
> 복제본 읽기 기능은 범용 또는 메모리 액세스에 최적화 된 가격 책정 계층의 Azure Database for MariaDB 서버에 대해서만 사용할 수 있습니다. 마스터 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인하세요.

## <a name="create-a-read-replica"></a>읽기 복제본 만들기

다음 명령을 사용하여 읽기 복제본 서버를 만들 수 있습니다.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica create` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  복제본 서버가 만들어지는 리소스 그룹입니다.  |
| name | mydemoreplicaserver | 만들어지는 새 복제본 서버의 이름입니다. |
| source-server | mydemoserver | 복제할 기존 마스터 서버의 이름 또는 ID입니다. |

지역 간 읽기 복제본을 만들려면 `--location` 매개 변수를 사용 합니다. 아래 CLI 예제에서는 미국 서 부에 복제본을 만듭니다.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 에서 복제본을 만들 수 있는 지역에 대 한 자세한 내용은 [복제본 개념 읽기 문서](concepts-read-replicas.md)를 참조 하세요. 

> [!NOTE]
> 읽기 복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 복제본이 마스터와 동일한 성능을 유지할 수 있도록 복제본 서버의 구성은 마스터의 구성 값 이상으로 유지하는 것이 좋습니다.

## <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지

> [!IMPORTANT]
> 서버로의 복제는 중지하고 나면 취소할 수 없습니다. 즉, 마스터와 복제본 간의 복제를 중지한 후에 실행 취소할 수는 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

다음 명령을 사용하여 읽기 복제본 서버에 대한 복제를 중지할 수 있습니다.

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mariadb server replica stop` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  복제본 서버가 있는 리소스 그룹입니다.  |
| name | mydemoreplicaserver | 복제를 중지할 복제본 서버의 이름입니다. |

## <a name="delete-a-replica-server"></a>복제본 서버 삭제

Read replica 서버 삭제는 **[az aadb server delete](/cli/azure/mariadb/server)** 명령을 실행 하 여 수행할 수 있습니다.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>마스터 서버 삭제

> [!IMPORTANT]
> 마스터 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 마스터 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

마스터 서버를 삭제 하려면 **[az mariadb server delete](/cli/azure/mariadb/server)** 명령을 실행 합니다.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>마스터 서버에 대한 복제본 나열

지정된 마스터 서버에 대한 모든 복제본을 보려면 다음 명령을 실행합니다. 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica list` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | 설명  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  복제본 서버가 만들어지는 리소스 그룹입니다.  |
| 서버 이름 | mydemoserver | 마스터 서버의 이름 또는 ID입니다. |

## <a name="next-steps"></a>다음 단계

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기
