---
title: 서버 관리-Azure CLI-Azure Database for PostgreSQL
description: Azure CLI에서 Azure Database for PostgreSQL 서버를 관리 하는 방법을 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 6b76b5bc6158786c7f60e762590d41b7cb243c40
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936908"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for PostgreSQL 단일 서버 관리

이 문서에서는 Azure에 배포 된 단일 서버를 관리 하는 방법을 보여 줍니다. 관리 작업에는 계산 및 저장소 크기 조정, 관리자 암호 재설정 및 서버 세부 정보 보기가 포함 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. 이 문서에서는 Azure CLI 버전 2.0 이상을 로컬로 실행해야 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) 명령을 사용하여 계정에 로그인해야 합니다. Azure 계정에 대한 **구독 ID**를 참조하는 **id** 속성을 기록해 둡니다.

```azurecli-interactive
az login
```

[az account set](/cli/azure/account) 명령을 사용하여 계정에 속한 특정 구독을 선택합니다. 명령에서 **subscription** 인수에 대한 값으로 사용할 **az login** 출력의 **id** 값을 적어 둡니다. 구독이 여러 개인 경우 리소스가 과금되어야 할 적절한 구독을 선택합니다. 모든 구독을 가져오려면 [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list)를 사용합니다.

```azurecli
az account set --subscription <subscription id>
```

서버를 아직 만들지 않은 경우이 [빠른](quickstart-create-server-database-azure-cli.md) 시작을 참조 하 여 새로 만듭니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>계산 및 저장소 크기 조정

다음 명령을 사용 하 여 가격 책정 계층, 계산 및 저장소를 쉽게 확장할 수 있습니다. 수행할 수 있는 모든 서버 작업을 볼 수 있습니다 [az postgres server 개요](/cli/azure/mysql/server)

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

위의 인수에 대한 세부 정보는 다음과 같습니다.

**설정** | **샘플 값** | **설명**
---|---|---
name | mydemoserver | Azure Database for PostgreSQL 서버에 대 한 고유한 이름을 입력 합니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자여야 합니다.
resource-group | myresourcegroup | Azure 리소스 그룹의 이름을 입력합니다.
sku-name|GP_Gen5_2|가격 책정 계층 및 컴퓨팅 구성의 이름을 입력합니다. {가격 책정 계층} _{계산 세대}_ {vCores} 규칙을 축약형으로 따릅니다. 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.
storage-size | 6144 | 서버의 스토리지 용량입니다(단위는 메가바이트). 최소 5120 및 1024 증분 단위로 증가 합니다.

> [!Important]
> - 저장소를 확장할 수 있습니다 (그러나 저장소 크기를 줄일 수 없음).
> - 기본에서 범용 또는 메모리 액세스에 최적화 된 가격 책정 계층으로 확장 하는 것은 지원 되지 않습니다. [Bash 스크립트를 사용](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) 하거나 [PostgreSQL 워크 벤치](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134) 를 사용 하 여를 수동으로 확장할 수 있습니다.


## <a name="manage-postgresql-databases-on-a-server"></a>서버에서 PostgreSQL 데이터베이스를 관리 합니다.
이러한 명령 중 하나를 사용 하 여 서버에 있는 데이터베이스의 데이터베이스 속성을 만들고, 삭제 하 고, 나열 하 고, 볼 수 있습니다.

| cmdlet | 사용| Description |
| --- | ---| --- |
|[az postgres db create](/cli/azure/sql/db#az-mysql-db-create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |데이터베이스 만들기|
|[az postgres db delete](/cli/azure/sql/db#az-mysql-db-delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|서버에서 데이터베이스를 삭제 합니다. 이 명령은 서버를 삭제 하지 않습니다. |
|[az postgres db list](/cli/azure/sql/db#az-mysql-db-list)|```az postgres db list -g myresourcegroup -s mydemoserver```|서버에 있는 모든 데이터베이스를 나열 합니다.|
|[az postgres db show](/cli/azure/sql/db#az-mysql-db-show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|데이터베이스에 대 한 자세한 정보를 표시 합니다.|

## <a name="update-admin-password"></a>관리자 암호 업데이트
이 명령을 사용 하 여 관리자 역할의 암호를 변경할 수 있습니다.
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  암호는 최소 8 자, 최대 128 자 인지 확인 합니다.
> 암호는 영어 대문자, 영어 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지에 해당 하는 문자를 포함 해야 합니다.

## <a name="delete-a-server"></a>서버 삭제
PostgreSQL 단일 서버를 삭제 하려는 경우 [az postgres server delete](/cli/azure/mysql/server#az-mysql-server-delete) 명령을 실행할 수 있습니다.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계
- [서버 다시 시작](howto-restart-server-cli.md)
- [잘못 된 상태로 서버 복원](howto-restore-server-cli.md)
- [서비스 모니터링 및 튜닝](concepts-monitoring.md)
