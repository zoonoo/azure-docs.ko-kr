---
title: 읽기 복제본 관리 - Azure 포털 - MariaDB용 Azure 데이터베이스
description: 이 문서에서는 포털을 사용하여 MariaDB용 Azure 데이터베이스에서 읽기 복제본을 설정하고 관리하는 방법에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 20d8e46d6fa6b031c809d629a6af41e8e682bcef
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025087"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Azure 포털을 사용하여 MariaDB용 Azure 데이터베이스에서 읽기 복제본을 만들고 관리하는 방법

이 문서에서는 Azure 포털을 사용하여 MariaDB 서비스에 대한 Azure 데이터베이스에서 읽기 복제본을 만들고 관리하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 마스터 서버로 사용되는 [MariaDB 서버에 대한 Azure 데이터베이스입니다.](quickstart-create-mariadb-server-database-using-azure-portal.md)

> [!IMPORTANT]
> 읽기 복제본 기능은 범용 또는 메모리 최적화가격 책정 계층의 MariaDB 서버에 대한 Azure 데이터베이스에서만 사용할 수 있습니다. 마스터 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인하세요.

## <a name="create-a-read-replica"></a>읽기 복제본 만들기

다음 단계에 따라 읽기 복제본 서버를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 마스터로 사용할 MariaDB 서버에 대한 기존 Azure 데이터베이스를 선택합니다. 이 작업은 **개요** 페이지를 엽니다.

3. 메뉴의 **설정** 아래에서 **복제**를 선택합니다.

4. **복제본 추가**를 선택합니다.

   ![MariaDB용 Azure 데이터베이스 - 복제](./media/howto-read-replica-portal/add-replica.png)

5. 복제서버의 이름을 입력합니다.

    ![MariaDB에 대 한 Azure 데이터베이스 - 복제본 이름](./media/howto-read-replica-portal/replica-name.png)

6. 복제서버의 위치를 선택합니다. 기본 위치는 마스터 서버의 위치와 동일합니다.

    ![MariaDB에 대 한 Azure 데이터베이스 - 복제본 위치](./media/howto-read-replica-portal/replica-location.png)

7. 복제본 만들기를 확인하려면 **확인을** 선택합니다.

> [!NOTE]
> 읽기 복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 복제본이 마스터와 동일한 성능을 유지할 수 있도록 복제본 서버의 구성은 마스터의 구성 값 이상으로 유지하는 것이 좋습니다.

생성된 복제본 서버는 **복제** 블레이드에서 확인할 수 있습니다.

   ![MariaDB에 대 한 Azure 데이터베이스 - 목록 복제본](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지

> [!IMPORTANT]
> 서버로의 복제는 중지하고 나면 취소할 수 없습니다. 즉, 마스터와 복제본 간의 복제를 중지한 후에 실행 취소할 수는 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

Azure Portal에서 마스터와 복제본 서버 간의 복제를 중지하려면 다음 단계를 수행합니다.

1. Azure 포털에서 MariaDB 서버에 대한 마스터 Azure 데이터베이스를 선택합니다. 

2. 메뉴의 **설정** 아래에서 **복제**를 선택합니다.

3. 복제를 중지할 복제본 서버를 선택합니다.

   ![MariaDB에 대 한 Azure 데이터베이스 - 복제 선택 서버 중지](./media/howto-read-replica-portal/stop-replication-select.png)

4. **복제 중지를**선택합니다.

   ![MariaDB용 Azure 데이터베이스 - 복제 중지](./media/howto-read-replica-portal/stop-replication.png)

5. **확인**을 클릭하여 복제 중지를 확인합니다.

   ![MariaDB에 대 한 Azure 데이터베이스 - 복제 중지 확인](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>복제본 서버 삭제

Azure Portal에서 읽기 복제본 서버를 삭제하려면 다음 단계를 수행합니다.

1. Azure 포털에서 MariaDB 서버에 대한 마스터 Azure 데이터베이스를 선택합니다.

2. 메뉴의 **설정** 아래에서 **복제**를 선택합니다.

3. 삭제할 복제본 서버를 선택합니다.

   ![MariaDB에 대 한 Azure 데이터베이스 - 복제본 선택 서버 삭제](./media/howto-read-replica-portal/delete-replica-select.png)

4. **복제본 삭제** 선택

   ![MariaDB에 대 한 Azure 데이터베이스 - 복제본 삭제](./media/howto-read-replica-portal/delete-replica.png)

5. 복제본의 이름을 입력하고 **삭제**를 클릭하여 복제본 삭제를 확인합니다.  

   ![MariaDB에 대 한 Azure 데이터베이스 - 복제본 삭제 확인](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>마스터 서버 삭제

> [!IMPORTANT]
> 마스터 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 마스터 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

Azure Portal에서 마스터 서버를 삭제하려면 다음 단계를 수행합니다.

1. Azure 포털에서 MariaDB 서버에 대한 마스터 Azure 데이터베이스를 선택합니다.

2. **개요**에서 **삭제**를 선택합니다.

   ![MariaDB에 대 한 Azure 데이터베이스 - 마스터 삭제](./media/howto-read-replica-portal/delete-master-overview.png)

3. 마스터 서버의 이름을 입력하고 **삭제**를 클릭하여 마스터 서버 삭제를 확인합니다.  

   ![MariaDB에 대 한 Azure 데이터베이스 - 마스터 삭제](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>복제 모니터링

1. Azure [포털에서](https://portal.azure.com/)모니터링할 MariaDB 서버에 대한 복제본 Azure 데이터베이스를 선택합니다.

2. 사이드바의 **모니터링** 섹션에서 **메트릭**을 선택합니다.

3. 사용 가능한 메트릭의 드롭다운 목록에서 **복제 지연 시간(초)** 를 선택합니다.

   ![복제 지연 시간 선택](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. 확인할 시간 범위를 선택합니다. 아래 그림에서는 시간 범위로 30분이 선택되어 있습니다.

   ![시간 범위 선택](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. 선택한 시간 범위의 복제 지연 시간을 확인합니다. 아래 이미지는 대규모 워크로드에 대해 마지막 30분을 표시합니다.

   ![시간 범위 선택](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>다음 단계

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기