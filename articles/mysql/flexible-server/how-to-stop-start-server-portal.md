---
title: 중지/시작-Azure Portal-유연한 서버 Azure Database for MySQL
description: 이 문서에서는 Azure Portal를 통해 Azure Database for MySQL에서 작업을 중지/시작 하는 방법을 설명 합니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e3e08ae9bbf00e1c9a44e6ba913cac1d842928b7
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567501"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 유연한 서버 중지/시작 (미리 보기)

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 유연한 서버를 중지 하 고 시작 하는 단계별 절차를 제공 합니다.

## <a name="prerequisites"></a>사전 준비 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for MySQL 유연한 서버가 있어야 합니다.

## <a name="stop-a-running-server"></a>실행 중인 서버 중지

1.  [Azure Portal](https://portal.azure.com/)에서 중지 하려는 유연한 서버를 선택 합니다.

2.  **개요** 페이지의 도구 모음에서 **중지** 단추를 클릭 합니다.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="유연한 서버를 중지 합니다."::: 

3.  **예** 를 클릭 하 여 서버 중지를 확인 합니다.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="유연한 서버를 중지 합니다."::: 

> [!NOTE]
> 서버를 중지 한 후에는 다른 관리 작업을 유연한 서버에서 사용할 수 없습니다.

## <a name="start-a-stopped-server"></a>중지 된 서버 시작

1.  [Azure Portal](https://portal.azure.com/)에서 시작 하려는 유연한 서버를 선택 합니다.

2.  **개요** 페이지의 도구 모음에서 **시작** 단추를 클릭 합니다.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="유연한 서버를 중지 합니다.":::  

> [!NOTE]
> 서버를 시작한 후에는 이제 유연한 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [유연한 서버 Azure Database for MySQL의 네트워킹](./concepts-networking.md) 에 대 한 자세한 정보
- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-portal.md).

