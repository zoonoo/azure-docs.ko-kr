---
title: 감사 로그에 액세스 - Azure CLI - Azure Database for MariaDB
description: 이 문서에서는 Azure CLI에서 Azure Database for MariaDB의 감사 로그를 구성하고 액세스하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1d446d8ee2863077ad84c361876758336f5a3cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665177"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Azure CLI에서 Maria DB 감사 로그에 대해 Azure Database를 구성하고 액세스

Azure CLI에서 [Azure Database for MariaDB 감사 로그](concepts-audit-logs.md)를 구성할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 가이드를 완료하려면 다음이 필요합니다.

- [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-portal.md)가 필요합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자만 로그하는 것이 좋습니다.

다음 단계를 사용하여 감사 로깅을 설정하고 구성합니다. 

1. **Audit_logs_enabled** 매개 변수를 "설정"으로 설정하여 감사 로그를 설정합니다. 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. **Audit_log_events** 매개 변수를 업데이트하여 로깅할 [이벤트 유형](concepts-audit-logs.md#configure-audit-logging)을 선택합니다.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. **audit_log_exclude_users** 매개 변수를 업데이트하여 로깅에서 제외할 MariaDB 사용자를 추가합니다. MariaDB 사용자 이름을 제공하여 사용자를 지정합니다.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. **audit_log_include_users** 매개 변수를 업데이트하여 로깅을 위해 포함할 특정 MariaDB 사용자를 추가합니다. MariaDB 사용자 이름을 제공하여 사용자를 지정합니다.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>다음 단계

- Azure Database for MariaDB의 [감사 로그](concepts-audit-logs.md) 에 대해 자세히 알아봅니다
- [Azure Portal](howto-configure-audit-logs-portal.md)에서 감사 로그를 구성하는 방법에 대해 알아봅니다
