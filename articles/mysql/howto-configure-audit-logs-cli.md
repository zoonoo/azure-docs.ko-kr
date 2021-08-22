---
title: 감사 로그에 액세스 - Azure CLI - Azure Database for MySQL
description: 이 문서에서는 Azure CLI에서 Azure Database for MySQL의 감사 로그를 구성 및 액세스하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 6/24/2020
ms.openlocfilehash: 249fdc83e42384e4d989293078e842a8bb2a8583
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642154"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Azure CLI에서 감사 로그 구성 및 액세스

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure CLI에서 [Azure Database for MySQL 감사 로그](concepts-audit-logs.md)를 구성할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md)가 필요합니다.

[!INCLUDE[azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

> [!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자만 로그하는 것이 좋습니다.

다음 단계를 사용하여 감사 로깅을 설정하고 구성합니다.

1. **Audit_logs_enabled** 매개 변수를 "설정"으로 설정하여 감사 로그를 설정합니다. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

2. **Audit_log_events** 매개 변수를 업데이트하여 로깅할 [이벤트 유형](concepts-audit-logs.md#configure-audit-logging)을 선택합니다.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

3. **Audit_log_exclude_users** 매개 변수를 업데이트하여 로그에서 제외할 MySQL 사용자를 추가합니다. MySQL 사용자 이름을 제공하여 사용자를 지정합니다.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

4. **Audit_log_include_users** 매개 변수를 업데이트하여 로깅에 포함할 특정 MySQL 사용자를 추가합니다. MySQL 사용자 이름을 제공하여 사용자를 지정합니다.

    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>다음 단계
- Azure Database for MySQL의 [감사 로그](concepts-audit-logs.md)에 대해 자세히 알아봅니다
- [Azure Portal](howto-configure-audit-logs-portal.md)에서 감사 로그를 구성하는 방법에 대해 알아봅니다
