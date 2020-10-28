---
title: 읽기 복제본 관리-Azure Portal-Azure Database for MySQL 유연한 서버
description: Azure Portal를 사용 하 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 설정 하 고 관리 하는 방법을 알아봅니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: cd2d3363b9c035987280eb27632c470c012b8bbb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795300"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Azure Portal를 사용 하 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 만들고 관리 하는 방법

> [!IMPORTANT]
> Azure Database for MySQL-유연한 서버에서 복제본 읽기는 미리 보기 상태입니다.

이 문서에서는 Azure Portal를 사용 하 여 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 만들고 관리 하는 방법에 대해 설명 합니다.

> [!Note]
> 고가용성 지원 서버에서는 복제본이 지원 되지 않습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 원본 서버로 사용 되는 [Azure Database for MySQL 서버 유연한 서버](quickstart-create-server-portal.md) 입니다.

## <a name="create-a-read-replica"></a>읽기 복제본 만들기

> [!IMPORTANT]
> 기존 복제본이 없는 원본에 대 한 복제본을 만드는 경우 원본 복제본이 먼저 다시 시작 되어 복제를 위한 준비가 됩니다. 이를 고려하고 사용량이 적은 기간 동안 이러한 작업을 수행합니다.

다음 단계에 따라 읽기 복제본 서버를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 원본으로 사용 하려는 기존 Azure Database for MySQL 유연한 서버를 선택 합니다. 이 작업은 **개요** 페이지를 엽니다.

3. 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.

4. **복제본 추가** 를 선택합니다.

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL - 복제":::

5. 복제본 서버의 이름을 입력합니다.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL - 복제":::

6. **확인** 을 선택하여 복제본 만들기를 확인합니다.

> [!NOTE]
> 읽기 복제본은 원본과 동일한 서버 구성으로 만들어집니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 복제 서버는 항상 원본 서버와 동일한 리소스 그룹 및 동일한 위치에 생성 됩니다. 다른 리소스 그룹 또는 다른 구독에 복제본 서버를 만들려면 복제본 서버를 만든 후에 [이동](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)할 수 있습니다. 복제본이 원본을 사용할 수 있도록 복제본 서버 구성을 원본과 같거나 큰 값으로 유지 하는 것이 좋습니다.

생성된 복제본 서버는 **복제** 블레이드에서 확인할 수 있습니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지

> [!IMPORTANT]
> 서버로의 복제는 중지하고 나면 취소할 수 없습니다. 원본 및 복제본 간에 복제가 중지 된 후에는 실행 취소할 수 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

Azure Portal에서 원본과 복제 서버 간의 복제를 중지 하려면 다음 단계를 사용 합니다.

1. Azure Portal에서 원본 Azure Database for MySQL 유연한 서버를 선택 합니다. 

2. 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.

3. 복제를 중지할 복제본 서버를 선택합니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. **복제 중지** 를 선택합니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. **확인** 을 클릭하여 복제 중지를 확인합니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>복제본 서버 삭제

Azure Portal에서 읽기 복제본 서버를 삭제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 원본 Azure Database for MySQL 유연한 서버를 선택 합니다.

2. 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.

3. 삭제할 복제본 서버를 선택합니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. **복제본 삭제** 를 선택합니다.

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL - 복제":::

5. 복제본의 이름을 입력하고 **삭제** 를 클릭하여 복제본 삭제를 확인합니다.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL - 복제":::

## <a name="delete-a-source-server"></a>원본 서버 삭제

> [!IMPORTANT]
> 원본 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 원본 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

Azure Portal에서 원본 서버를 삭제 하려면 다음 단계를 사용 합니다.

1. Azure Portal에서 원본 Azure Database for MySQL 유연한 서버를 선택 합니다.

2. **개요** 에서 **삭제** 를 선택합니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. 원본 서버의 이름을 입력 하 고 **삭제** 를 클릭 하 여 원본 서버 삭제를 확인 합니다.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL - 복제":::

## <a name="monitor-replication"></a>복제 모니터링

1. [Azure Portal](https://portal.azure.com/)에서 모니터링할 유연한 서버 Azure Database for MySQL 복제본을 선택 합니다.

2. 사이드바의 **모니터링** 섹션에서 **메트릭** 을 선택합니다.

3. 사용 가능한 메트릭의 드롭다운 목록에서 **복제 지연 시간(초)** 를 선택합니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. 확인할 시간 범위를 선택합니다. 아래 그림에서는 시간 범위로 30분이 선택되어 있습니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. 선택한 시간 범위의 복제 지연 시간을 확인합니다. 아래 그림에는 지난 30분 동안의 복제 지연 시간이 표시되어 있습니다.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Azure Database for MySQL - 복제":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기
