---
title: 구독 관리
description: 구독은 관리되는 커밋 디바이스로 구성되며 필요에 따라 온보딩 또는 오프보딩할 수 있습니다.
ms.date: 3/30/2021
ms.topic: how-to
ms.openlocfilehash: 10098ec66d15b4c894106cc1d37d7ac339d508cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387231"
---
# <a name="manage-a-subscription"></a>구독 관리

구독은 월 단위로 관리됩니다. 구독을 등록하면 해당 구독에 대한 요금이 월말까지 청구됩니다. 마찬가지로 구독을 오프보딩할 때 해당 구독을 오프보딩한 달의 나머지 기간에 대한 요금이 청구됩니다.

## <a name="onboard-a-subscription"></a>구독 온보딩

Azure Defender for IoT를 시작하려면 Microsoft Azure 구독이 있어야 합니다. 구독이 없는 경우 체험 계정으로 등록할 수 있습니다. Azure 구독에 대한 액세스 권한이 이미 있지만 목록에 없는 경우 계정 정보를 확인하고 구독 소유자에게 권한을 확인합니다.

구독을 온보딩하려면 다음을 수행합니다.

1. Azure Portal의 가격 책정 페이지로 이동합니다. 

   :::image type="content" source="media/how-to-manage-subscriptions/no-subscription.png" alt-text="Azure Portal의 가격 책정 페이지로 이동합니다.":::

1. **구독 등록** 을 선택합니다.

1. **구독 등록** 창에서 구독을 선택하고 드롭다운 메뉴에서 커밋된 디바이스 수를 선택합니다. 

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="구독 및 커밋된 디바이스 수를 선택합니다.":::

1. **등록** 을 선택합니다.

## <a name="offboard-a-subscription"></a>구독 오프보딩

구독은 월 단위로 관리됩니다. 구독을 오프보딩하면 해당 구독에 대한 요금이 월말까지 청구됩니다.

구독을 오프보딩하기 전에 구독과 연결된 모든 센서를 제거합니다. 센서를 삭제하는 방법에 대한 자세한 내용은 [센서 삭제](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor)를 참조하세요. 

구독을 오프보딩하려면 다음을 수행합니다.

1. **가격 책정** 페이지로 이동합니다.
1. 구독을 선택한 다음, **삭제** 아이콘 :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false":::를 선택합니다.
1. 확인 팝업에서 확인란을 선택하여 구독과 관련된 모든 센서를 삭제했는지 확인합니다.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="확인란을 선택하고 오프보딩을 선택하여 센서를 오프보딩합니다.":::

1. **오프보딩** 단추를 선택합니다. 

## <a name="next-steps"></a>다음 단계

[온-프레미스 관리 콘솔 활성화 및 설정](how-to-activate-and-set-up-your-on-premises-management-console.md)
