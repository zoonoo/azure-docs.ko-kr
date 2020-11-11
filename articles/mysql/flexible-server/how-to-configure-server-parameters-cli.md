---
title: 서버 매개 변수 구성-Azure CLI-유연한 서버 Azure Database for MySQL
description: 이 문서에서는 Azure CLI 명령줄 유틸리티를 사용 하 Azure Database for MySQL 유연한 서버에서 서비스 매개 변수를 구성 하는 방법을 설명 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58e7c024d6494aee745884997e42b527c51ab237
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489542"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Azure CLI를 사용 하 Azure Database for MySQL 유연한 서버에서 서버 매개 변수 구성

> [!IMPORTANT] 
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure 명령줄 유틸리티인 Azure CLI를 사용 하 여 Azure Database for MySQL 유연한 서버에 대 한 매개 변수를 나열 하 고, 표시 하 고, 업데이트할 수 있습니다. 서버 매개 변수는 서버를 만들 때 기본 및 권장 값으로 구성 됩니다.  

이 문서에서는 Azure CLI를 사용 하 여 서버 매개 변수를 나열, 표시 및 업데이트 하는 방법을 설명 합니다.

>[!Note]
> 서버 매개 변수는 서버 수준에서 전역적으로 업데이트 될 수 있으며 [Azure CLI](./how-to-configure-server-parameters-cli.md) 또는 [Azure Portal](./how-to-configure-server-parameters-portal.md) 를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for MySQL 유연한 서버](quickstart-create-server-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 명령줄 유틸리티 또는 브라우저의 Azure Cloud Shell

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버에 대 한 서버 매개 변수 나열
서버에 있는 모든 매개 변수와 해당 값을 나열 하려면 [az mysql 신축-server parameter list](/cli/azure/mysql/flexible-server/parameter) 명령을 실행 합니다.

리소스 그룹 **myresourcegroup** 에서 **mydemoserver.mysql.database.azure.com** 서버에 대 한 서버 매개 변수를 나열할 수 있습니다.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
나열된 각 매개 변수의 정의를 보려면 [서버 시스템 변수](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)에서 MySQL 참조 섹션을 참조하세요.

## <a name="show-server-parameter-details"></a>서버 매개 변수 세부 정보 표시
서버에 대 한 특정 매개 변수에 대 한 세부 정보를 표시 하려면 [az mysql 신축-server parameter show](/cli/azure/mysql/flexible-server/parameter) 명령을 실행 합니다.

이 예에서는 리소스 그룹 **myresourcegroup** 에서 서버 **mydemoserver.mysql.database.azure.com** 에 대 한 **저속 \_ 쿼리 \_ 로그** 서버 매개 변수의 세부 정보를 보여 줍니다.
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>서버 매개 변수 값 수정
MySQL 서버 엔진에 대 한 기본 구성 값을 업데이트 하는 특정 서버 매개 변수의 값을 수정할 수도 있습니다. 서버 매개 변수를 업데이트 하려면 [az mysql 신축-server parameter set](/cli/azure/mysql/flexible-server/parameter) 명령을 사용 합니다. 

리소스 그룹 **myresourcegroup** 에서 서버 **mydemoserver.mysql.database.azure.com** 의 **저속 \_ 쿼리 \_ 로그** 서버 매개 변수를 업데이트 합니다.
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
매개 변수 값을 다시 설정 하려면 선택적 매개 변수를 생략 하 `--value` 고 서비스에서 기본값을 적용 합니다. 위의 예제에서는 다음과 같이 표시됩니다.
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
이 코드는 **저속 \_ 쿼리 \_ 로그** 를 기본값으로 다시 설정 합니다 **.** 

## <a name="setting-non-modifiable-server-parameters"></a>수정할 수정 불가능 서버 매개 변수 설정

업데이트 하려는 서버 매개 변수를 수정할 수 없는 경우 필요에 따라를 사용 하 여 연결 수준에서 매개 변수를 설정할 수 있습니다 `init_connect` . 서버에 연결 하는 각 클라이언트에 대 한 서버 매개 변수를 설정 합니다. 

리소스 그룹 **myresourcegroup** 에서 서버 **mydemoserver.mysql.database.azure.com** 의 **init \_ connect** 서버 매개 변수를 업데이트 하 여 문자 집합과 같은 값을 설정 합니다.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect`를 사용하여 세션 수준에서 SUPER 권한이 필요하지 않은 매개 변수를 변경할 수 있습니다. `init_connect`를 사용하여 매개 변수를 설정할 수 있는지 확인하려면 `set session parameter_name=YOUR_DESIRED_VALUE;` 명령을 실행하고 **액세스 거부됨; SUPER 권한 필요** 오류가 발생한 경우 'init_connect'를 사용하여 매개 변수를 설정할 수 없습니다.

## <a name="working-with-the-time-zone-parameter"></a>표준 시간대 매개 변수 작업

### <a name="populating-the-time-zone-tables"></a>표준 시간대 테이블 채우기

MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하면 서버의 표준 시간대 테이블을 채울 수 있습니다.

> [!NOTE]
> MySQL Workbench에서 `mysql.az_load_timezone` 명령을 실행하는 경우, 먼저 `SET SQL_SAFE_UPDATES=0;`을 사용하여 안전한 업데이트 모드를 꺼야 할 수 있습니다.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 표준 시간대 테이블이 제대로 채워지도록 하려면 서버를 다시 시작해야 합니다.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

사용 가능한 표준 시간대 값을 보려면 다음 명령을 실행합니다.

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>전역 수준 표준 시간대 설정

[Az mysql 신축 서버 매개 변수 set](/cli/azure/mysql/flexible-server/parameter) 명령을 사용 하 여 전역 수준 표준 시간대를 설정할 수 있습니다.

다음 명령은 리소스 그룹 **myresourcegroup** 에서 서버 **mydemoserver.mysql.database.azure.com** 의 **표준 \_ 시간대** 서버 매개 변수를 **US/태평양** 로 업데이트 합니다.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>세션 수준 표준 시간대 설정

세션 수준 표준 시간대는 MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `SET time_zone` 명령을 실행하여 설정할 수 있습니다. 아래 예제에서는 표준 시간대를 **US/Pacific** 표준 시간대로 설정합니다.  

```sql
SET time_zone = 'US/Pacific';
```

[날짜 및 시간 함수](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)에 대한 MySQL 문서를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 서버 매개 변수](./how-to-configure-server-parameters-portal.md)를 구성하는 방법
