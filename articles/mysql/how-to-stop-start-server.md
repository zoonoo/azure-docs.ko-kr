---
title: 중지/시작 - Azure Portal - Azure Database for MySQL
description: 이 문서는 Azure Database for MySQL에서 작업을 중지/시작하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: ed784f38c5c297e06c62c2bb7f801a4f0aac0799
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642159"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Azure Database for MySQL 중지/시작

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

> [!IMPORTANT]
> 서버를 **중지** 하면 스트레치에서 다음 7일 동안 해당 상태로 유지됩니다. 이 시간 동안 수동으로 **시작** 하지 않는 경우 서버는 7일이 끝날 때 자동으로 시작됩니다. 서버를 사용하지 않는 경우 다시 **중지** 하도록 선택할 수 있습니다.

이 문서에서는 단일 서버의 중지 및 시작을 수행하는 단계별 절차를 제공합니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for MySQL 단일 서버가 있어야 합니다.

> [!NOTE]
> [중지/시작](concepts-servers.md#limitations-of-stopstart-operation) 사용에 대한 제한을 참조하세요.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL을 중지/시작하는 방법

### <a name="stop-a-running-server"></a>실행 중인 서버 중지

1.  [Azure Portal](https://portal.azure.com/)에서 중지하려는 MySQL 서버를 선택합니다.

2.  **개요** 페이지의 도구 모음에서 **중지** 단추를 클릭합니다.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL 중지 서버":::

    > [!NOTE]
    > 서버를 중지한 후에는 단일 서버에 대해 다른 관리 작업을 사용할 수 없습니다.

### <a name="start-a-stopped-server"></a>중지된 서버 시작

1.  [Azure Portal](https://portal.azure.com/)에서, 시작하려는 단일 서버를 선택합니다.

2.  **개요** 페이지의 도구 모음에서 **시작** 단추를 클릭합니다.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL 시작 서버":::

    > [!NOTE]
    > 서버가 시작되면 이제 단일 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>CLI를 사용하여 Azure Database for MySQL을 중지/시작하는 방법

### <a name="stop-a-running-server"></a>실행 중인 서버 중지

1.  [Azure Portal](https://portal.azure.com/)에서 중지하려는 MySQL 서버를 선택합니다.

2.  **개요** 페이지의 도구 모음에서 **중지** 단추를 클릭합니다.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 서버를 중지한 후에는 단일 서버에 대해 다른 관리 작업을 사용할 수 없습니다.

### <a name="start-a-stopped-server"></a>중지된 서버 시작

1.  [Azure Portal](https://portal.azure.com/)에서, 시작하려는 단일 서버를 선택합니다.

2.  **개요** 페이지의 도구 모음에서 **시작** 단추를 클릭합니다.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > 서버가 시작되면 이제 단일 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[메트릭에 대한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.
