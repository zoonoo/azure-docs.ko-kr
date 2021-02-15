---
title: 중지/시작-Azure Portal-Azure Database for MySQL server
description: 이 문서에서는 Azure Database for MySQL 작업을 중지/시작 하는 방법을 설명 합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516875"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Azure Database for MySQL 중지/시작

> [!IMPORTANT]
>  서버를 **중지** 하면 스트레치에서 다음 7 일 동안 해당 상태가 유지 됩니다. 이 시간 동안 수동으로 **시작** 하지 않는 경우 서버는 7 일이 끝날 때 자동으로 시작 됩니다. 서버를 사용 하지 않는 경우 다시 **중지** 하도록 선택할 수 있습니다.

이 문서에서는 단일 서버의 중지 및 시작을 수행 하는 단계별 절차를 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for MySQL 단일 서버가 있어야 합니다.

> [!NOTE]
> [Stop/start](concepts-servers.md#limitations-of-stopstart-operation) 사용에 대 한 제한을 참조 하세요.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MySQL를 중지/시작 하는 방법

### <a name="stop-a-running-server"></a>실행 중인 서버 중지

1.  [Azure Portal](https://portal.azure.com/)에서 중지 하려는 MySQL 서버를 선택 합니다.

2.  **개요** 페이지의 도구 모음에서 **중지** 단추를 클릭 합니다.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL 서버 중지":::

    > [!NOTE]
    > 서버를 중지 한 후에는 단일 서버에 대해 다른 관리 작업을 사용할 수 없습니다.

### <a name="start-a-stopped-server"></a>중지 된 서버 시작

1.  [Azure Portal](https://portal.azure.com/)에서 시작 하려는 단일 서버를 선택 합니다.

2.  **개요** 페이지의 도구 모음에서 **시작** 단추를 클릭 합니다.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="서버 시작 Azure Database for MySQL":::

    > [!NOTE]
    > 서버가 시작 되 면 이제 단일 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>CLI를 사용 하 여 Azure Database for MySQL를 중지/시작 하는 방법

### <a name="stop-a-running-server"></a>실행 중인 서버 중지

1.  [Azure Portal](https://portal.azure.com/)에서 중지 하려는 MySQL 서버를 선택 합니다.

2.  **개요** 페이지의 도구 모음에서 **중지** 단추를 클릭 합니다.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 서버를 중지 한 후에는 단일 서버에 대해 다른 관리 작업을 사용할 수 없습니다.

### <a name="start-a-stopped-server"></a>중지 된 서버 시작

1.  [Azure Portal](https://portal.azure.com/)에서 시작 하려는 단일 서버를 선택 합니다.

2.  **개요** 페이지의 도구 모음에서 **시작** 단추를 클릭 합니다.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 서버가 시작 되 면 이제 단일 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[메트릭에 대 한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.
