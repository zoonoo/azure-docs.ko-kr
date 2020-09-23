---
title: 복원 Azure Portal-Azure Database for MySQL 유연한 서버
description: 이 문서에서는 Azure Portal를 통해 Azure Database for MySQL에서 복원 작업을 수행 하는 방법을 설명 합니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 1c81ddad8a11cbad361ff84caf6f7200a0c010d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937292"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 유연한 서버 (미리 보기)의 지정 시간 복원


> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 백업을 사용 하 여 유연한 서버에서 지정 시간 복구를 수행 하는 단계별 절차를 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for MySQL 유연한 서버가 있어야 합니다.

## <a name="restore-to-the-latest-restore-point"></a>최신 복원 지점으로 복원

가장 이른 기존 백업을 사용 하 여 유연한 서버를 복원 하려면 다음 단계를 수행 합니다.

1.   [Azure Portal](https://portal.azure.com/)에서 백업을 복원 하려는 유연한 서버를 선택 합니다.

2.  왼쪽 패널에서 **개요** 를 클릭 합니다.

3.  개요 페이지에서 **복원**을 클릭 합니다.

    Placeholder

4.  복원 페이지가 **최신 복원 지점** 및 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션과 함께 표시 됩니다.

5.  **최신 복원 지점**을 선택 합니다.


6.  **새 서버에 복원** 필드에 새 서버 이름을 제공 합니다.

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="가장 빠른 복원 시간":::

8.  **확인**을 클릭합니다.

9.  복원 작업이 시작 되었음을 알리는 알림이 표시 됩니다.

## <a name="restoring-to-a-custom-restore-point"></a>사용자 지정 복원 지점으로 복원

가장 이른 기존 백업을 사용 하 여 유연한 서버를 복원 하려면 다음 단계를 수행 합니다.

1.   [Azure Portal](https://portal.azure.com/)에서 백업을 복원 하려는 유연한 서버를 선택 합니다.

2.  개요 페이지에서 **복원**을 클릭 합니다.

    Placeholder

3.  복원 페이지는 가장 빠른 복원 지점과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션으로 표시 됩니다.

4.  **사용자 지정 복원 지점**을 선택 합니다.

5.  날짜 및 시간을 선택 합니다.

6.  **새 서버에 복원** 필드에 새 서버 이름을 제공 합니다.

6.  **새 서버에 복원** 필드에 새 서버 이름을 제공 합니다. 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="개요 보기":::
 
7.  **확인**을 클릭합니다.

8.  복원 작업이 시작 되었음을 알리는 알림이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

자리표시자
