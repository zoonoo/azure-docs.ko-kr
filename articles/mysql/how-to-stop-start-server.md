---
title: 중지/시작-Azure Portal-Azure Database for MySQL server
description: 이 문서에서는 Azure Database for MySQL 작업을 중지/시작 하는 방법을 설명 합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 047a24133dfdf68e2176b20cf31a871d11f0d4f9
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241160"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Azure Database for MySQL 중지/시작

> [!IMPORTANT]
> Azure Database for MySQL에 대 한 중지/시작 기능은 현재 공개 미리 보기로 제공 됩니다.

이 문서에서는 단일 서버의 중지 및 시작을 수행 하는 단계별 절차를 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

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
