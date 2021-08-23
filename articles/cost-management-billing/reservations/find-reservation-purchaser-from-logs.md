---
title: Azure Monitor 로그에서 예약 구매자 찾기
description: 이 문서는 Azure Monitor 로그의 정보를 사용하여 예약 구매자를 찾을 수 있도록 도와줍니다.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: baf7fe1968f2ba2a72e27f1caf85d02c65a3e93f
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122568335"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Azure 로그에서 예약 구매자 찾기

이 문서는 디렉터리 로그의 정보를 사용하여 예약 구매자를 찾을 수 있도록 도와줍니다. Azure Monitor의 디렉터리 로그에는 예약 구매한 사용자의 이메일 ID가 표시됩니다.

## <a name="find-the-purchaser"></a>구매자 찾기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Monitor** > **활동 로그** > **활동** 으로 이동합니다.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="활동 로그-활동으로 이동을 보여주는 스크린샷." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. **디렉터리 활동** 을 선택합니다. *디렉터리 수준 로그를 볼 수 있는 권한이 필요* 하다는 메시지가 표시되면 [링크](../../role-based-access-control/elevate-access-global-admin.md)를 선택하여 권한을 가져오는 방법을 알아보세요.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="로그를 볼 수 있는 권한이 없는 디렉터리 활동을 보여주는 스크린샷." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. 권한이 있으면 **Microsoft Capacity** 로 **테넌트 리소스 공급자** 를 필터링합니다. 선택한 시간 범위의 모든 예약 관련 이벤트가 표시됩니다. 필요한 경우 시간 범위를 변경합니다.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="예약을 구매한 사용자를 보여주는 스크린샷." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    필요한 경우 **이벤트를 시작한 사람** 을 선택하기 위해 **열을 편집** 해야 할 수도 있습니다.
   예약 구매한 사용자는 **이벤트를 시작한 사람** 아래에 표시됩니다.

## <a name="next-steps"></a>다음 단계

- 필요한 경우 청구 관리자가 [예약 소유권을 가져올](view-reservations.md#view-and-manage-reservations) 수 있습니다.
