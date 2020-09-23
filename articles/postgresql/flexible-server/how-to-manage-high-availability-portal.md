---
title: 영역 중복 고가용성-Azure Portal-Azure Database for PostgreSQL 유연한 서버 관리
description: 이 문서에서는 Azure Portal를 통해 Azure Database for PostgreSQL 유연한 서버에서 영역 중복 고가용성을 사용 하거나 사용 하지 않도록 설정 하는 방법을 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937004"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>유연한 서버에서 영역 중복 고가용성 관리

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

이 문서에서는 유연한 서버에서 영역 중복 고가용성 구성을 사용 하거나 사용 하지 않도록 설정 하는 방법을 설명 합니다.

고가용성 기능은 서로 다른 영역에서 물리적으로 분리 되는 주 복제본과 대기 복제본을 프로 비전 합니다. 자세한 내용은 [고가용성 개념 설명서](./concepts-high-availability.md)를 참조 하세요. 서버를 유연 하 게 만들 때 또는 만든 후에 고가용성을 사용 하도록 선택할 수 있습니다. 이 페이지에서는 고가용성을 사용 하거나 사용 하지 않도록 설정 하는 방법에 대 한 지침을 제공 합니다. 이 작업을 수행 해도 VNET 구성, 방화벽 설정 및 백업 보존을 비롯 한 다른 설정은 변경 되지 않습니다. 마찬가지로, 고가용성을 사용 하거나 사용 하지 않도록 설정 하는 것은 온라인 작업 이며 응용 프로그램 연결 및 작업에 영향을 주지 않습니다.

## <a name="pre-requisites"></a>필수 구성 요소

영역 중복 고가용성은 여러 영역이 지원 되는 지역 에서만 사용할 수 있습니다. 

## <a name="enable-high-availability-during-server-creation"></a>서버를 만드는 동안 고가용성 사용

이 섹션에서는 HA 관련 필드에 대 한 세부 정보를 제공 합니다. 유연한 서버를 만드는 동안 고가용성을 배포 하려면 다음 단계를 수행 하면 됩니다.

1.   [Azure Portal](https://portal.azure.com/)에서 유연한 서버를 선택 하 고 만들기를 클릭 합니다.  **구독**, **리소스 그룹**, **서버 이름**, **지역**및 기타 필드와 같은 세부 정보를 입력 하는 방법에 대 한 자세한 내용은 서버 만들기에 대 한 방법 문서를 참조 하세요.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="구독 및 지역 보기":::

2.  **가용성 영역**을 선택 합니다. 이는 대기 시간을 줄이기 위해 데이터베이스와 동일한 가용성 영역에 응용 프로그램을 배치 하는 경우에 유용 합니다. 유연한 서버를 모든 가용성 영역에 배포 하려면 **기본 설정 없음** 을 선택 합니다.
    ![AZ selection ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="Availability zone selection":::  

3.  가용성 옵션에서 **영역 중복 고가용성** 확인란을 클릭 합니다.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="고가용성 확인란":::

4.  기본 계산 및 저장소를 변경 하려면  **서버 구성**을 클릭 합니다.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="서버 구성-계산 + 저장소":::  

5.  고가용성 옵션을 선택한 경우에는 삼 계층을 선택할 수 없습니다. 범용 또는 **메모리** 액세스에 최적화 된 계산 **계층 중 하나** 를 선택할 수 있습니다. 그런 다음 드롭다운에서 원하는 **계산 크기** 를 선택할 수 있습니다.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="계산 계층 선택":::  


6.  슬라이딩 막대를 사용 하 여 GiB에서 **저장소 크기** 를 선택 하 고 7 일에서 35 일 사이의 **백업 보존 기간** 을 선택 합니다.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="저장소 백업"::: 

7. **저장**을 클릭합니다. 

## <a name="enable-high-availability-post-server-creation"></a>고가용성 게시 서버 만들기 사용

기존 유연한 서버에 대해 고가용성을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1.   [Azure Portal](https://portal.azure.com/)에서 기존 PostgreSQL 유연한 서버를 선택 합니다.

2.  유연한 서버 페이지의 왼쪽 패널에서 **고가용성을 클릭 하** 여 고가용성 페이지를 엽니다.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="왼쪽 패널 선택"::: 

3.  **영역 중복 고가용성** 확인란을 클릭 하 여 옵션을 **사용 하도록 설정** 하 고 **저장**을 클릭 하 여   변경 내용을 저장 합니다.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="고가용성 사용"::: 

4.  고가용성을 사용 하도록 설정 하면 추가 서버 및 저장소 배포로 인해 비용이 증가 한다는 확인 대화 상자가 표시 됩니다.

5.  **HA 사용** 단추를 클릭 하 여 고가용성을 사용 하도록 설정 합니다.

6.  고가용성 배포가 진행 되는 것을 나타내는 알림이 표시 됩니다.

## <a name="disable-high-availability"></a>고가용성 사용 안 함

영역 중복성으로 이미 구성 된 유연한 서버에 대해 고가용성을 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

1.   [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for PostgreSQL 유연한 서버를 선택 합니다.

2.  유연한 서버 페이지의 전면 패널에서 **고가용성을 클릭 하** 여 고가용성 페이지를 엽니다.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="왼쪽 패널 선택"::: 

3.  **영역 중복 고가용성** 확인란을 클릭 하 여 옵션을 **사용 하지 않도록 설정** 합니다. 그런 다음 **저장**   을 클릭 하 여 변경 내용을 저장 합니다.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="고가용성 사용 안 함"::: 

4.  고가용성을 사용 하지 않도록 설정할 수 있는 확인 대화 상자가 표시 됩니다.

5.  **HA 사용 안** 함 단추를 클릭 하 여 고가용성을 사용 하지 않도록 설정 합니다.

6.  고가용성 배포의 서비스 해제가 진행 중으로 표시 되는 알림이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md) 에 대 한 자세한 정보
-    [영역 중복 고가용성](./concepts-high-availability.md) 에 대해 알아보기
