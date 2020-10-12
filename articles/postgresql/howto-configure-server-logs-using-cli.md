---
title: 로그 관리-Azure CLI-Azure Database for PostgreSQL-단일 서버
description: 이 문서에서는 Azure CLI를 사용 하 여 Azure Database for PostgreSQL 단일 서버에서 서버 로그 (.log 파일)를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71dac0b6ea2202e712280607a73e860ae68bdb73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704874"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Azure CLI를 사용하여 서버 로그 구성 및 액세스
Azure CLI(명령줄 인터페이스)를 사용하여 PostgreSQL 서버 오류 로그를 다운로드할 수 있습니다. 그러나 트랜잭션 로그에 대한 액세스는 지원되지 않습니다. 

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for PostgreSQL 서버](quickstart-create-server-database-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 명령줄 유틸리티 또는 브라우저의 Azure Cloud Shell

## <a name="configure-logging"></a>로깅 구성
쿼리 로그 및 오류 로그에 액세스하도록 서버를 구성할 수 있습니다. 오류 로그에는 자동 진공, 연결 및 검사점 정보가 포함될 수 있습니다.
1. 로깅을 설정합니다.
2. 쿼리 로깅을 사용하려면 **log\_statement** 및 **log\_min\_duration\_statement**를 업데이트합니다.
3. 보존 기간을 업데이트합니다.

자세한 내용은 [서버 구성 매개 변수 사용자 지정](howto-configure-server-parameters-using-cli.md)을 참조하세요.

## <a name="list-logs"></a>로그 나열
서버에 대한 사용 가능한 로그 파일을 나열하려면 [az postgres server-logs list](/cli/azure/postgres/server-logs) 명령을 실행합니다.

**myresourcegroup** 리소스 그룹에서 **mydemoserver.postgres.database.azure.com** 서버에 대한 로그 파일을 나열할 수 있습니다. 그런 다음, **log\_files\_list.txt**라는 텍스트 파일에 로그 파일 목록을 전송합니다.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>로그를 서버에서 로컬로 다운로드
[az postgres server-logs download](/cli/azure/postgres/server-logs) 명령을 사용하여 서버에 대한 개별 로그 파일을 다운로드할 수 있습니다. 

다음 예제를 사용하여 **myresourcegroup** 리소스 그룹에 있는 **mydemoserver.postgres.database.azure.com** 서버에 대한 특정 로그 파일을 로컬 환경에 다운로드합니다.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>다음 단계
- 서버 로그에 대해 자세히 알아보려면 [Azure Database for PostgreSQL의 서버 로그](concepts-server-logs.md)를 참조 하세요.
- 서버 매개 변수에 대한 자세한 내용은 [Azure CLI를 사용하여 서버 구성 매개 변수 사용자 지정](howto-configure-server-parameters-using-cli.md)을 참조하세요.
