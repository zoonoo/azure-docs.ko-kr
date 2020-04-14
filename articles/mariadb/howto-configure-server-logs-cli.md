---
title: 느린 쿼리 로그 에 액세스 - Azure CLI - MariaDB용 Azure 데이터베이스
description: 이 문서에서는 Azure CLI 명령줄 유틸리티를 사용하여 MariaDB용 Azure 데이터베이스의 느린 로그에 액세스하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 75efdd8ed855fe78651fce5828aacb2384052ae5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270539"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Azure CLI를 사용하여 느린 쿼리 로그 구성 및 액세스
Azure 명령줄 유틸리티인 Azure CLI를 사용하여 MariaDB 느린 쿼리 로그에 대한 Azure 데이터베이스를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 또는 브라우저의 Azure Cloud Shell

## <a name="configure-logging"></a>로깅 구성
다음 단계를 수행하여 MySQL 느린 쿼리 로그에 액세스하도록 서버를 구성할 수 있습니다.
1. 느린 쿼리 로그 매개 변수를 ON으로 설정하여 **느린\_쿼리\_로깅을** 켭니다.
2. **\_로그 출력을**사용하여 로그를 출력할 위치를 선택합니다. 로컬 저장소와 Azure 모니터 진단 로그에 로그를 보내려면 **파일**을 선택합니다. Azure 모니터 로그에만 로그를 보내려면 **없음을** 선택합니다.
3. **long\_query\_time** 및 **log\_slow\_admin\_statements**와 같은 다른 매개 변수를 조정합니다.

Azure CLI를 통해 이러한 매개 변수 값을 설정하는 방법을 알아보려면 [서버 매개 변수를 구성하는 방법](howto-configure-server-parameters-cli.md)을 참조하세요.

예를 들어 다음 CLI 명령은 느린 쿼리 로그를 켜기로 설정하고, 긴 쿼리 시간을 10초로 설정한 다음, 느린 관리자 명령문의 로깅을 해제합니다. 마지막으로 검토할 구성 옵션을 나열합니다.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Azure Database for MariaDB 서버에 대한 로그 나열
**log_output** "파일"로 구성된 경우 서버의 로컬 저장소에서 직접 로그에 액세스할 수 있습니다. 서버에 사용할 수 있는 느린 쿼리 로그 파일을 나열하려면 [az mariadb 서버 로그 목록 명령을 실행합니다.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

**myresourcegroup** 리소스 그룹에 있는 **mydemoserver.mariadb.database.azure.com** 서버에 대한 로그 파일을 나열할 수 있습니다. 그런 다음, **log\_files\_list.txt**라는 텍스트 파일에 로그 파일 목록을 전송합니다.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>서버에서 로그 다운로드
**log_output** "파일"로 구성된 경우 [az mariadb 서버 로그 다운로드](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) 명령을 사용하여 서버에서 개별 로그 파일을 다운로드 할 수 있습니다.

다음 예제를 사용하여 **myresourcegroup** 리소스 그룹에 있는 **mydemoserver.mariadb.database.azure.com** 서버에 대한 특정 로그 파일을 로컬 환경으로 다운로드합니다.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>다음 단계
- [MariaDB 에 대한 Azure 데이터베이스에서 느린 쿼리 로그에](concepts-server-logs.md)대해 알아봅니다.
