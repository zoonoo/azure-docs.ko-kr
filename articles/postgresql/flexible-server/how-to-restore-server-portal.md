---
title: 복원 Azure Portal-Azure Database for PostgreSQL 유연한 서버
description: 이 문서에서는 Azure Portal를 통해 Azure Database for PostgreSQL에서 복원 작업을 수행 하는 방법을 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938864"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>유연한 서버의 특정 시점 복원

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

이 문서에서는 백업을 사용 하 여 유연한 서버에서 지정 시간 복구를 수행 하는 단계별 절차를 제공 합니다. 보존 기간 내에 가장 오래 된 복원 지점이 나 사용자 지정 복원 지점까지 수행할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for PostgreSQL 유연한 서버가 있어야 합니다. 영역 중복성으로 구성 된 유연한 서버에도 동일한 절차를 적용할 수 있습니다.

## <a name="restoring-to-the-earliest-restore-point"></a>가장 이른 복원 지점으로 복원

가장 이른 기존 백업을 사용 하 여 유연한 서버를 복원 하려면 다음 단계를 수행 합니다.

1.   [Azure Portal](https://portal.azure.com/)에서 백업을 복원 하려는 유연한 서버를 선택 합니다.

2.  왼쪽 패널에서 **개요** 를 클릭 하 고 **복원** 을 클릭 합니다.
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="복원 개요":::

3.  복원 페이지는 가장 빠른 복원 지점과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션으로 표시 됩니다.

4.  **가장 빠른 복원 지점을** 선택 하 고 **새 서버에 복원** 필드에 새 서버 이름을 제공 합니다. 복원할 수 있는 가장 이른 타임 스탬프가 표시 됩니다. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="복원 개요":::

5.  **확인**을 클릭합니다.

6.  복원 작업이 시작 되었음을 알리는 알림이 표시 됩니다.

## <a name="restoring-to-a-custom-restore-point"></a>사용자 지정 복원 지점으로 복원

가장 이른 기존 백업을 사용 하 여 유연한 서버를 복원 하려면 다음 단계를 수행 합니다.

1.   [Azure Portal](https://portal.azure.com/)에서 백업을 복원 하려는 유연한 서버를 선택 합니다.

2.  개요 페이지에서 **복원**을 클릭 합니다.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="복원 개요":::
    
3.  복원 페이지는 가장 빠른 복원 지점과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션으로 표시 됩니다.

4.  **사용자 지정 복원 지점**을 선택 합니다.

5.  날짜 및 시간을 선택 하 고 **새 서버에 복원** 필드에 새 서버 이름을 제공 합니다. 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="복원 개요":::
 
6.  **확인**을 클릭합니다.

7.  복원 작업이 시작 되었음을 알리는 알림이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md) 에 대 한 자세한 정보
-    [영역 중복 고가용성](./concepts-high-availability.md) 에 대해 알아보기
-   [백업 및 복구](./concepts-backup-restore.md) 에 대 한 자세한 정보
