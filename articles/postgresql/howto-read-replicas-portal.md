---
title: Azure Database for PostgreSQL의 Azure Portal에서 읽기 복제본 관리
description: 이 문서는 Azure Portal에서 Azure Database for PostgreSQL 읽기 복제본을 관리하는 방법을 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 6c1a0a4a13a70daec157ede98f850f87150f8d93
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383866"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Azure Portal에서 읽기 복제본을 만들고 관리하는 방법
이 문서에서는 Azure Portal을 사용하여 Azure Database for PostgreSQL 서비스의 읽기 복제본을 만들고 관리하는 방법에 대해 알아봅니다. 읽기 복제본에 대해 자세히 알아보려면 [개념 설명서를 읽어보세요](concepts-read-replicas.md).

## <a name="prerequisites"></a>필수 조건
- 마스터 서버가 될 [Azure Database for PostgreSQL 서버](quickstart-create-server-database-portal.md)

## <a name="prepare-the-master-server"></a>마스터 서버 준비
이 마스터 준비 단계는 범용 및 메모리 최적화 서버에만 적용됩니다.

마스터 서버에서 **azure.replication_support** 매개 변수를 REPLICA로 설정해야 합니다. 이 매개 변수 변경 내용을 적용하려면 서버를 다시 시작해야 합니다.

1. Azure Portal에서 마스터로 사용할 기존 Azure Database for PostgreSQL 서버를 선택합니다.

2. 왼쪽의 메뉴에서 **서버 매개 변수**를 선택합니다.

3. **azure.replication_support**를 검색합니다.

   ![Azure Database for PostgreSQL - azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. **azure.replication_support**를 REPLICA로 설정합니다. 변경 내용을 **저장**합니다.

   ![Azure Database for PostgreSQL - REPLICA 및 저장](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. 저장이 완료되면 알림을 받게 됩니다.

   ![Azure Database for PostgreSQL - 저장 알림](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. 저장된 후 변경 내용을 적용하려면 서버를 다시 시작합니다. 서버를 다시 시작하는 방법을 알아보려면 [다시 시작 설명서](howto-restart-server-portal.md)를 참조하세요.

## <a name="create-a-read-replica"></a>읽기 복제본 만들기
다음 단계에 따라 읽기 복제본을 만들 수 있습니다.
1.  마스터로 사용할 기존 Azure Database for PostgreSQL 서버를 선택합니다. 

2.  메뉴의 설정 아래에서 복제를 선택합니다.

   범용 또는 메모리 최적화 마스터에서 **azure.replication_support**를 설정한 후 서버를 다시 시작하지 않으면 다시 시작하라는 메시지가 표시됩니다. 만들기를 진행하기 전에 이 작업을 수행합니다.

3.  복제본 추가를 선택합니다.

   ![Azure Database for PostgreSQL - 복제본 추가](./media/howto-read-replicas-portal/add-replica.png)

4.  복제본 서버의 이름을 입력하고 확인을 선택하여 복제본 만들기를 확인합니다.

   ![Azure Database for PostgreSQL - 복제본 이름 지정](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> 읽기 복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본을 만든 후에는 마스터 서버와는 별도로 가격 책정 계층(기본 가격 책정 계층에서 이 계층으로 또는 그 반대로 변경하는 경우는 제외), 계산 생성, vCores, 저장소 및 백업 보존 기간을 변경할 수 있습니다.

> [!IMPORTANT]
> 마스터 서버 구성을 새 값으로 업데이트하기 전에 복제본의 구성을 같거나 더 큰 값으로 업데이트해야 합니다. 그렇지 않은 경우 오류가 발생합니다. 위와 같이 업데이트하면 마스터 변경 내용을 복제본에도 유지할 수 있습니다. 


복제본 서버가 만들어지면 복제 창에서 볼 수 있습니다.

![Azure Database for PostgreSQL - 새 복제본](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>복제 중지

> [!IMPORTANT]
> 서버로의 복제는 중지하고 나면 취소할 수 없습니다. 즉, 마스터와 복제본 간의 복제를 중지한 후에 실행 취소할 수는 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

Azure Portal에서 마스터와 복제본 간의 복제를 중지하려면 다음 단계를 수행합니다.
1.  Azure Portal에서 마스터 Azure Database for PostgreSQL 서버를 선택합니다.

2.  메뉴의 설정 아래에서 복제를 선택합니다.

3.  복제를 중지할 복제본 서버를 선택합니다.

   ![Azure Database for PostgreSQL - 복제본 선택](./media/howto-read-replicas-portal/select-replica.png)
 
4.  복제 중지를 선택합니다.

   ![Azure Database for PostgreSQL - 복제 중지 선택](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  확인을 클릭하여 복제 중지를 확인합니다.

   ![Azure Database for PostgreSQL - 복제 중지 확인](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>마스터 삭제

> [!IMPORTANT]
> 마스터 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.
마스터를 삭제할 때는 독립 실행형 Azure Database for PostgreSQL 서버를 삭제할 때와 동일한 단계를 따릅니다. Azure Portal에서 서버를 삭제하려면 다음을 수행합니다.

1.  Azure Portal에서 마스터 Azure Database for PostgreSQL 서버를 선택합니다.

2.  개요에서 삭제를 선택합니다.

   ![Azure Database for PostgreSQL - 서버 삭제](./media/howto-read-replicas-portal/delete-server.png)
 
3.  마스터 서버의 이름을 입력하고 삭제를 선택하여 마스터 서버 삭제를 확인합니다.

   ![Azure Database for PostgreSQL - 삭제 확인](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>복제본 삭제
읽기 복제본을 삭제하려면 위에서 마스터 서버를 삭제할 때와 동일한 단계를 따를 수 있습니다. 먼저 복제본의 개요 페이지를 연 다음, 삭제를 선택합니다.

   ![Azure Database for PostgreSQL - 복제본 삭제](./media/howto-read-replicas-portal/delete-replica.png)
 
또는 복제 창에서 삭제할 수 있습니다.
1.  Azure Portal에서 마스터 Azure Database for PostgreSQL 서버를 선택합니다.

2.  메뉴의 설정 아래에서 복제를 선택합니다.

3.  삭제할 복제본 서버를 선택합니다. 

   ![Azure Database for PostgreSQL - 복제본 선택](./media/howto-read-replicas-portal/select-replica.png)
 
4.  복제본 삭제를 선택합니다.

   ![Azure Database for PostgreSQL - 복제본 삭제 선택](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  복제본의 이름을 입력하고 삭제를 선택하여 복제본 삭제를 확인합니다.

   ![Azure Database for PostgreSQL - 복제본 삭제 확인](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>복제본 모니터링
### <a name="max-lag-across-replicas"></a>복제본 간 최대 지연 시간
**복제본 간 최대 지연 시간** 메트릭은 마스터와 가장 느리게 지연 복제본 간의 지연 시간을 보여 줍니다. 

1.  Azure Portal에서 **마스터** Azure Database for PostgreSQL 서버를 선택합니다.

2.  메트릭을 선택합니다. 메트릭 창에서 **복제본 간 지연 최대 시간**을 선택합니다.

    ![Azure Database for PostgreSQL - 복제본 간 지연 최대 시간](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  집계로 **최대**를 선택합니다. 

### <a name="replica-lag"></a>복제본 지연 시간
**복제본 지연 시간** 메트릭은 이 복제본에서 마지막으로 재생된 트랜잭션 이후의 시간을 보여 줍니다. 마스터에서 트랜잭션이 발생하지 않으면 이 메트릭은 이 지연 시간을 반영합니다.

1.  Azure Portal에서 **복제본** Azure Database for PostgreSQL 서버를 선택합니다.

2.  메트릭을 선택합니다. 메트릭 창에서 **복제본 지연 시간**을 선택합니다.

   ![Azure Database for PostgreSQL - 복제본 지연 시간 모니터링](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  집계로 **최대**를 선택합니다. 
 
## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL의 읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아봅니다.