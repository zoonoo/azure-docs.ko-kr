---
title: 감사 로그에 액세스-Azure CLI-Azure Database for MySQL
description: 이 문서에서는 Azure CLI에서 Azure Database for MySQL의 감사 로그를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7ffabae785a589bacf349356079f6046039f9c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542000"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Azure CLI에서 감사 로그 구성 및 액세스

Azure CLI에서 [Azure Database for MySQL 감사 로그](concepts-audit-logs.md) 를 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 단계별로 실행 하려면 다음을 수행 합니다.

- [Azure Database for MySQL 서버가](quickstart-create-mysql-server-database-using-azure-portal.md)필요 합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

>[!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자를 기록 하는 것이 좋습니다.

다음 단계를 사용 하 여 감사 로깅을 설정 하 고 구성 합니다.

1. **Audit_logs_enabled** 매개 변수를 "설정"으로 설정 하 여 감사 로그를 설정 합니다. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. **Audit_log_events** 매개 변수를 업데이트 하 여 로깅할 [이벤트 유형을](concepts-audit-logs.md#configure-audit-logging) 선택 합니다.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. **Audit_log_exclude_users** 매개 변수를 업데이트 하 여 로그에서 제외할 MySQL 사용자를 추가 합니다. MySQL 사용자 이름을 제공 하 여 사용자를 지정 합니다.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. **Audit_log_include_users** 매개 변수를 업데이트 하 여 로깅에 포함할 특정 MySQL 사용자를 추가 합니다. MySQL 사용자 이름을 제공 하 여 사용자를 지정 합니다.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>다음 단계
- 에서 [감사 로그](concepts-audit-logs.md) 에 대해 자세히 알아보세요 Azure Database for MySQL
- [Azure Portal](howto-configure-audit-logs-portal.md) 에서 감사 로그를 구성 하는 방법을 알아봅니다.
