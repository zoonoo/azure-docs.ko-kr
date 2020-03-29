---
title: 느린 쿼리 로그 에 액세스 - Azure CLI - MySQL용 Azure 데이터베이스
description: 이 문서에서는 Azure CLI를 사용하여 MySQL용 Azure 데이터베이스의 느린 쿼리 로그에 액세스하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 93840af61a69599447588be01869a20290b2db94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062475"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Azure CLI를 사용하여 느린 쿼리 로그 구성 및 액세스
Azure 명령줄 유틸리티인 Azure CLI를 사용하여 MySQL 느린 쿼리 로그에 대한 Azure 데이터베이스를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [MySQL 서버에 대한 Azure 데이터베이스](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 또는 브라우저의 Azure Cloud Shell

## <a name="configure-logging"></a>로깅 구성
다음 단계를 수행하여 MySQL 느린 쿼리 로그에 액세스하도록 서버를 구성할 수 있습니다.
1. 느린 쿼리 로그 매개 변수를 ON으로 설정하여 **느린\_쿼리\_로깅을** 켭니다.
2. **long\_query\_time** 및 **log\_slow\_admin\_statements**와 같은 다른 매개 변수를 조정합니다.

Azure CLI를 통해 이러한 매개 변수 값을 설정하는 방법을 알아보려면 [서버 매개 변수를 구성하는 방법](howto-configure-server-parameters-using-cli.md)을 참조하세요.

예를 들어 다음 CLI 명령은 느린 쿼리 로그를 켜기로 설정하고, 긴 쿼리 시간을 10초로 설정한 다음, 느린 관리자 명령문의 로깅을 해제합니다. 마지막으로 검토할 구성 옵션을 나열합니다.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버에 대한 로그 나열
서버에 사용할 수 있는 느린 쿼리 로그 파일을 나열하려면 [az mysql 서버 로그 목록 명령을 실행합니다.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

**myresourcegroup** 리소스 그룹에서 **mydemoserver.mysql.database.azure.com** 서버에 대한 로그 파일을 나열할 수 있습니다. 그런 다음, **log\_files\_list.txt**라는 텍스트 파일에 로그 파일 목록을 전송합니다.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>서버에서 로그 다운로드
[az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) 명령을 사용하여 서버에 대한 개별 로그 파일을 다운로드할 수 있습니다. 

다음 예제를 사용하여 **myresourcegroup** 리소스 그룹에 있는 **mydemoserver.mysql.database.azure.com** 서버에 대한 특정 로그 파일을 로컬 환경에 다운로드합니다.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>다음 단계
- [MySQL에 대한 Azure 데이터베이스에서 느린 쿼리 로그에 대해](concepts-server-logs.md)알아봅니다.
