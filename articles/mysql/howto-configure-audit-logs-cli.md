---
title: 액세스 감사 로그 - Azure CLI - MySQL용 Azure 데이터베이스
description: 이 문서에서는 Azure CLI에서 MySQL용 Azure 데이터베이스의 감사 로그를 구성하고 액세스하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384169"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Azure CLI에서 감사 로그 구성 및 액세스

Azure CLI에서 [MySQL 감사 로그에 대한 Azure 데이터베이스를](concepts-audit-logs.md) 구성할 수 있습니다.

> [!IMPORTANT]
> 감사 로그 기능은 현재 미리 보기 상태입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [MySQL 서버에 대한 Azure 데이터베이스](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 이 방법 가이드에서는 Azure CLI 버전 2.0 이상을 사용해야 합니다. 버전을 확인하려면 Azure CLI 명령 프롬프트에서 `az --version`을 입력합니다. 설치하거나 업그레이드하려면 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="configure-audit-logging"></a>감사 로깅 구성

다음 단계를 사용하여 감사 로깅을 활성화하고 구성합니다.

1. **audit_logs_enabled** 매개 변수를 "ON"으로 설정하여 감사 로그를 켭니다. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. **audit_log_events** 매개 변수를 업데이트하여 기록할 [이벤트 유형을](concepts-audit-logs.md#configure-audit-logging) 선택합니다.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. **audit_log_exclude_users** 매개 변수를 업데이트하여 로깅에서 제외할 MySQL 사용자를 추가합니다. MySQL 사용자 이름을 제공하여 사용자를 지정합니다.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. **audit_log_include_users** 매개 변수를 업데이트하여 로깅에 포함할 특정 MySQL 사용자를 추가합니다. MySQL 사용자 이름을 제공하여 사용자를 지정합니다.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>다음 단계
- MySQL용 Azure 데이터베이스에서 [감사 로그에](concepts-audit-logs.md) 대해 자세히 알아보기
- [Azure 포털에서](howto-configure-audit-logs-portal.md) 감사 로그를 구성하는 방법에 대해 알아봅니다.