---
title: 크기 조정 작업-Azure Portal-Azure Database for PostgreSQL 유연한 서버
description: 이 문서에서는 Azure Portal를 통해 Azure Database for PostgreSQL에서 크기 조정 작업을 수행 하는 방법을 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939244"
---
# <a name="scale-operations-in-flexible-server"></a>유연한 서버에서 작업 확장

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

이 문서에서는 계산 및 저장소에 대 한 크기 조정 작업을 수행 하는 단계를 제공 합니다. 응용 프로그램을 실행 하는 데 적합 한 vCores 수를 선택 하는 것을 비롯 하 여 안정적인 계층, 범용 및 메모리 액세스에 최적화 된 Sku 간에 계산 계층을 변경할 수 있습니다. 저장소를 확장할 수도 있습니다. 예상 IOPS는 계산 계층, vCores 및 저장소 용량을 기준으로 표시 됩니다. 비용 예측도 선택에 따라 표시 됩니다.

> [!IMPORTANT]
> 저장소를 축소할 수는 없습니다.

## <a name="pre-requisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for PostgreSQL 유연한 서버가 있어야 합니다. 영역 중복성으로 구성 된 유연한 서버에도 동일한 절차를 적용할 수 있습니다.
> [!IMPORTANT]
> 고가용성으로 구성 된 경우에는 서로 증가 하는 안정적인 SKU를 선택할 수 없습니다. 크기 조정 작업을 수행 하는 동안 대기는 먼저 원하는 크기로 확장 되 고 주 서버는 장애 조치 (failover) 되며 주 서버는 크기가 조정 됩니다. 

## <a name="scaling-the-compute-tier-and-size"></a>계산 계층 및 크기 크기 조정

다음 단계를 수행 하 여 계산 계층을 선택 합니다.
 
1.   [Azure Portal](https://portal.azure.com/)에서 백업을 복원 하려는 유연한 서버를 선택 합니다.

2.  **Compute + storage**를 클릭 합니다.

3.  현재 설정이 있는 페이지가 표시 됩니다.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="compute + 저장소 뷰":::

4.  계산 클래스를 선택 하 여 안정적인, 범용 및 메모리 최적화 계층 사이에서 선택할 수 있습니다.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="계산 계층 나열":::


5.  기본 vCores 및 메모리 크기에 적합 한 경우 다음 단계를 건너뛸 수 있습니다.

6.  VCores 수를 변경 하려는 경우 **계산 크기** 의 드롭다운을 클릭 하 고 목록에서 원하는 Vcores/메모리 수를 클릭 하면 됩니다.
    
    - 삼 계층 계산 계층: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="안정적인 계산":::

    - 범용 계산 계층: 범용 :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="계산":::

    - 메모리 액세스에 최적화 된 계산 계층: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="메모리 최적화 계산":::

7.  **저장**을 클릭합니다. 
8.  확인 메시지가 표시됩니다. 계속 하려면 **확인** 을 클릭 합니다. 
9.  진행 중인 크기 조정 작업에 대 한 알림입니다.


## <a name="scaling-storage-size"></a>저장소 크기 조정

저장소 크기를 늘리려면 다음 단계를 수행 합니다.

1.   [Azure Portal](https://portal.azure.com/)에서 저장소 크기를 늘릴 유연한 서버를 선택 합니다.
2.  **Compute + storage**를 클릭 합니다.

3.  현재 설정이 있는 페이지가 표시 됩니다.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="compute + storage를 클릭 합니다.":::
4.  슬라이드 막대가 있는 **GiB의 필드 저장소 크기** 는 현재 크기로 표시 됩니다.

5.  막대를 원하는 크기로 밉니다. 해당 IOPS 수가 표시 됩니다. IOPS는 계산 계층 및 크기에 따라 달라 집니다. 비용 정보도 표시 됩니다. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="저장소 확장":::

6.  저장소 크기에 적합 한 경우 **저장**을 클릭 합니다. 
7.  확인 메시지가 표시됩니다. 계속 하려면 **확인** 을 클릭 합니다. 
8.  진행 중인 크기 조정 작업에 대 한 알림입니다.

## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md) 에 대 한 자세한 정보
-   [고가용성](./concepts-high-availability.md) 에 대 한 자세한 정보
-   [백업 및 복구](./concepts-backup-restore.md) 에 대 한 자세한 정보
