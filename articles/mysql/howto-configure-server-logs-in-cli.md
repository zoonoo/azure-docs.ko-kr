---
title: Azure CLI를 사용하여 Azure Database for MySQL의 서버 로그에 액세스
description: 이 아티클에서는 Azure CLI 명령줄 유틸리티를 사용하여 Azure Database for MySQL에서 서버 로그에 액세스하는 방법을 설명합니다.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e3940f07ab97eb2ad03c347c39bf2412a8497f5e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978457"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Azure CLI를 사용하여 서버 로그 구성 및 액세스
Azure 명령줄 유틸리티인 Azure CLI를 사용하여 Azure Database for MySQL 서버 로그를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 또는 브라우저의 Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대한 로깅 구성
다음 단계를 수행하여 MySQL 느린 쿼리 로그에 액세스하도록 서버를 구성할 수 있습니다.
1. **slow\_query\_log** 매개 변수를 켜기로 설정하여 로깅을 사용합니다.
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
서버에 대한 사용 가능한 로그 파일을 나열하려면 [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) 명령을 실행합니다.

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
- [Azure Database for MySQL의 서버 로그](concepts-server-logs.md)에 대해 자세히 알아봅니다.
