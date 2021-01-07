---
title: Azure Portal-Azure Database for PostgreSQL 유연한 서버
description: 이 문서에서는 Azure Portal를 통해 Azure Database for PostgreSQL에서 다시 시작 작업을 수행 하는 방법을 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936932"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL-유연한 서버 다시 시작

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

이 문서에서는 유연한 서버를 다시 시작 하는 단계별 절차를 제공 합니다. 이 작업은 데이터베이스 서버를 다시 시작 해야 하는 정적 매개 변수 변경 내용을 적용 하는 데 유용 합니다. 이 절차는 영역 중복 고가용성으로 구성 된 서버에 대해서도 동일 합니다. 

> [!IMPORTANT]
> 고가용성으로 구성 된 경우 주 서버와 대기 서버가 동시에 다시 시작 됩니다.

## <a name="pre-requisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   서버를 유연 하 게 사용할 수 있어야 합니다.

## <a name="restart-your-flexible-server"></a>유연한 서버 다시 시작

유연한 서버를 다시 시작 하려면 다음 단계를 수행 합니다.

1.   [Azure Portal](https://portal.azure.com/)에서 다시 시작 하려는 유연한 서버를 선택 합니다.

2.  왼쪽 패널에서 **개요** 를 클릭 하 고 **다시 시작**을 클릭 합니다.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="선택 영역 다시 시작":::

3.  팝업 확인 메시지가 표시 됩니다.

4.  계속 하려면 **예** 를 클릭 합니다.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="선택 영역 다시 시작":::
 
6.  다시 시작 작업이 시작 되었음을 알리는 알림이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md) 에 대 한 자세한 정보
-    [영역 중복 고가용성](./concepts-high-availability.md) 에 대해 알아보기
