---
title: 만들기 및 관리 Azure 데이터베이스의 읽기 복제본 MariaDB
description: 이 문서에서는 설정 하 고 포털을 사용 하 여 MariaDB에 대 한 Azure 데이터베이스의 읽기 복제본을 관리 하는 방법 설명
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: eb228138118512c5c64574212910c5f16885ee94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079029"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>만들기 및 관리 하는 방법을 읽을 Azure 데이터베이스의 복제본 MariaDB에 대 한 Azure portal을 사용 하 여

이 문서를 만들고 Azure portal을 사용 하 여 MariaDB service 용 Azure 데이터베이스의 읽기 복제본을 관리 하는 방법을 배웁니다.

> [!IMPORTANT]
> 동일한 지역 복제본이 현재 공개 미리 보기를 읽습니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Database for MariaDB 서버](quickstart-create-mariadb-server-database-using-azure-portal.md) 마스터 서버로 사용할 합니다.

> [!IMPORTANT]
> 읽기 복제본 기능은 범용 또는 메모리 최적화 가격 책정 계층에서 MariaDB 서버용 Azure Database에 사용할 수 있습니다. 마스터 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인하세요.

## <a name="create-a-read-replica"></a>읽기 복제본 만들기

다음 단계에 따라 읽기 복제본 서버를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. MariaDB 서버를 마스터로 사용 하려는 대 한 기존 Azure 데이터베이스를 선택 합니다. 이 작업은 **개요** 페이지를 엽니다.

3. 메뉴의 **설정** 아래에서 **복제**를 선택합니다.

4. **복제본 추가**를 선택합니다.

   ![Azure Database for MariaDB-복제](./media/howto-read-replica-portal/add-replica.png)

5. 복제본 서버의 이름을 입력 합니다.

    ![Azure Database for MariaDB-복제본 이름](./media/howto-read-replica-portal/replica-name.png)

6. 선택 **확인** 복제본 만들기를 확인 합니다.

> [!NOTE]
> 읽기 복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 복제본이 마스터와 동일한 성능을 유지할 수 있도록 복제본 서버의 구성은 마스터의 구성 값 이상으로 유지하는 것이 좋습니다.

생성된 복제본 서버는 **복제** 블레이드에서 확인할 수 있습니다.

   ![Azure Database for MariaDB-복제본 목록](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지

> [!IMPORTANT]
> 서버로의 복제는 중지하고 나면 취소할 수 없습니다. 즉, 마스터와 복제본 간의 복제를 중지한 후에 실행 취소할 수는 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

Azure Portal에서 마스터와 복제본 서버 간의 복제를 중지하려면 다음 단계를 수행합니다.

1. Azure portal에서 마스터 Azure Database for MariaDB 서버를 선택 합니다. 

2. 메뉴의 **설정** 아래에서 **복제**를 선택합니다.

3. 복제를 중지할 복제본 서버를 선택합니다.

   ![Azure Database for MariaDB-중지 복제 서버 선택](./media/howto-read-replica-portal/stop-replication-select.png)

4. **복제 중지**를 선택합니다.

   ![Azure Database for MariaDB-복제 중지](./media/howto-read-replica-portal/stop-replication.png)

5. **확인**을 클릭하여 복제 중지를 확인합니다.

   ![Azure Database for MariaDB-복제 중지 확인](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>복제본 서버 삭제

Azure Portal에서 읽기 복제본 서버를 삭제하려면 다음 단계를 수행합니다.

1. Azure portal에서 마스터 Azure Database for MariaDB 서버를 선택 합니다.

2. 메뉴의 **설정** 아래에서 **복제**를 선택합니다.

3. 삭제할 복제본 서버를 선택합니다.

   ![Azure Database for MariaDB-복제본 선택 서버 삭제](./media/howto-read-replica-portal/delete-replica-select.png)

4. **복제본 삭제**를 선택합니다.

   ![Azure Database for MariaDB-복제본 삭제](./media/howto-read-replica-portal/delete-replica.png)

5. 복제본의 이름을 입력하고 **삭제**를 클릭하여 복제본 삭제를 확인합니다.  

   ![Azure Database for MariaDB-복제본 삭제 확인](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>마스터 서버 삭제

> [!IMPORTANT]
> 마스터 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 마스터 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

Azure Portal에서 마스터 서버를 삭제하려면 다음 단계를 수행합니다.

1. Azure portal에서 마스터 Azure Database for MariaDB 서버를 선택 합니다.

2. **개요**에서 **삭제**를 선택합니다.

   ![Azure Database for MariaDB-삭제 마스터](./media/howto-read-replica-portal/delete-master-overview.png)

3. 마스터 서버의 이름을 입력하고 **삭제**를 클릭하여 마스터 서버 삭제를 확인합니다.  

   ![Azure Database for MariaDB-삭제 마스터](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>복제 모니터링

1. 에 [Azure portal](https://portal.azure.com/)를 모니터링 하려는 MariaDB 서버용 Azure 데이터베이스 복제본을 선택 합니다.

2. 사이드바의 **모니터링** 섹션에서 **메트릭**을 선택합니다.

3. 사용 가능한 메트릭의 드롭다운 목록에서 **복제 지연 시간(초)** 를 선택합니다.

   ![복제 지연 시간 선택](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. 확인할 시간 범위를 선택합니다. 아래 그림에서는 시간 범위로 30분이 선택되어 있습니다.

   ![시간 범위 선택](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. 선택한 시간 범위의 복제 지연 시간을 확인합니다. 아래 이미지는 큰 워크 로드에 대 한 지난 30 분을 표시합니다.

   ![시간 범위 선택](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>다음 단계

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기