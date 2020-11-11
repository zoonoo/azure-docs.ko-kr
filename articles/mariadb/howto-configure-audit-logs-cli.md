---
title: 감사 로그에 액세스-Azure CLI-Azure Database for MariaDB
description: 이 문서에서는 Azure CLI에서 Azure Database for MariaDB의 감사 로그를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e532902e1e6da90d4c81320b34a0b2b5dd1133f
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518093"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Azure CLI에서 민 DB 감사 로그에 대해 Azure Database 구성 및 액세스

Azure CLI에서 [Azure Database for MariaDB 감사 로그](concepts-audit-logs.md) 를 구성할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 가이드를 완료 하려면:

- [Azure Database for MariaDB 서버가](quickstart-create-mariadb-server-database-using-azure-portal.md)필요 합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자를 기록 하는 것이 좋습니다.

다음 단계를 사용 하 여 감사 로깅을 설정 하 고 구성 합니다. 

1. **Audit_logs_enabled** 매개 변수를 "설정"으로 설정 하 여 감사 로그를 설정 합니다. 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. **Audit_log_events** 매개 변수를 업데이트 하 여 로깅할 [이벤트 유형을](concepts-audit-logs.md#configure-audit-logging) 선택 합니다.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. **Audit_log_exclude_users** 매개 변수를 업데이트 하 여 로깅에서 제외할 모든 다른 모든 사용자를 추가 합니다. 해당 사용자 이름을 입력 하 여 사용자를 지정 합니다.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. **Audit_log_include_users** 매개 변수를 업데이트 하 여 로깅에 포함할 특정 Aadb 사용자를 추가 합니다. 해당 사용자 이름을 입력 하 여 사용자를 지정 합니다.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>다음 단계

- 에서 [감사 로그](concepts-audit-logs.md) 에 대해 자세히 알아보세요 Azure Database for MariaDB
- [Azure Portal](howto-configure-audit-logs-portal.md) 에서 감사 로그를 구성 하는 방법을 알아봅니다.
