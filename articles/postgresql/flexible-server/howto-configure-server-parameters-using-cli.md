---
title: 매개 변수 구성-유연한 서버 Azure Database for PostgreSQL
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure Database for PostgreSQL 유연한 서버에서 Postgres 매개 변수를 구성 하는 방법을 설명 합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b14858cc99cac41e277b03171fd4cac4d6eafa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940733"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure Database for PostgreSQL 유연한 서버에 대 한 서버 매개 변수 사용자 지정

Azure CLI(명령줄 인터페이스)를 사용하여 Azure PostgreSQL 서버의 구성 매개 변수를 나열하고, 표시하며, 업데이트할 수 있습니다. 엔진 매개 변수의 하위 집합은 서버 수준에서 노출 되 고 수정할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for PostgreSQL 만들기](quickstart-create-server-cli.md)에 따라 Azure Database for PostgreSQL 서버 및 데이터베이스 만들기
- [Azure CLI](/cli/azure/install-azure-cli) 명령줄 인터페이스를 머신에 설치하거나 브라우저를 사용하여 Azure Portal의 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.

## <a name="list-server-parameters-for-a-flexible-server"></a>유연한 서버에 대 한 서버 매개 변수 나열

서버에 있는 수정 가능한 모든 매개 변수와 해당 값을 나열 하려면 [az postgres 신축 서버 매개 변수 목록](/cli/azure/postgres/flexible-server/parameter) 명령을 실행 합니다.

리소스 그룹 **myresourcegroup**에서 **mydemoserver.postgres.database.azure.com** 서버에 대 한 서버 매개 변수를 나열할 수 있습니다.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>서버 매개 변수 세부 정보 표시

서버에 대 한 특정 매개 변수에 대 한 세부 정보를 표시 하려면 [az postgres 신축 서버 매개 변수 show](/cli/azure/postgres/flexible-server/parameter)  명령을 실행 합니다.

이 예에서는 리소스 그룹 **myresourcegroup** 에서 서버 **mydemoserver.postgres.database.azure.com** 에 대 한 **log \_ min \_ messages** 서버 매개 변수의 세부 정보를 보여 줍니다.

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>서버 매개 변수 값 수정

PostgreSQL 서버 엔진에 대 한 기본 구성 값을 업데이트 하는 특정 서버 매개 변수의 값을 수정할 수도 있습니다. 매개 변수를 업데이트 하려면 [az postgres 유연한 서버 매개 변수 set](/cli/azure/postgres/flexible-server/parameter) 명령을 사용 합니다. 

리소스 그룹 **myresourcegroup** 에서 서버 **mydemoserver.postgres.database.azure.com** 의 **log \_ min \_ messages** 서버 매개 변수를 업데이트 합니다.

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

매개 변수 값을 다시 설정 하려는 경우 선택적 매개 변수를 유지 하도록 선택 하기만 하면 `--value` 서비스에서 기본값을 적용 합니다. 위의 예제에서는 다음과 같이 표시됩니다.

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

이 명령은 **log \_ min \_ messages** 매개 변수를 기본값 **WARNING**으로 다시 설정 합니다. 서버 매개 변수 및 허용 되는 값에 대 한 자세한 내용은 [매개 변수 설정](https://www.postgresql.org/docs/12/config-setting.html)에 대 한 PostgreSQL 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 서버 로그를 구성하고 액세스하려면 [PostgreSQL용 Azure Database의 서버 로그](concepts-logging.md)를 참조하세요.
