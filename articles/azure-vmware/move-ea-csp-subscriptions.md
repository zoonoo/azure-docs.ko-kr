---
title: EA 및 CSP Azure VMware 솔루션 구독 이동
description: 한 구독에서 다른 구독으로 사설 클라우드를 이동 하는 방법에 대해 알아봅니다. 요금 청구와 같은 다양 한 이유로 이동 하는 것이 가능 합니다.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555617"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>EA 및 CSP Azure VMware 솔루션 구독 이동

이 문서에서는 하나의 구독에서 다른 구독으로 사설 클라우드를 이동 하는 방법에 대해 알아봅니다. 요금 청구와 같은 다양 한 이유로 이동 하는 것이 가능 합니다. 

>[!IMPORTANT]
>원본 및 대상 구독에 대 한 참가자 이상의 권한이 있어야 합니다. VNet 및 VNet 게이트웨이를 하나의 구독에서 다른 구독으로 이동할 수 없습니다. 또한 구독을 이동 해도 vCenter, NSX 및 워크 로드 가상 머신과 같은 관리 및 워크 로드에는 영향을 주지 않습니다.

1. Azure Portal에 로그인 하 여 이동 하려는 사설 클라우드를 선택 합니다.

1. **구독 (변경)** 링크를 선택 합니다.

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="사설 클라우드 세부 정보를 보여 주는 스크린샷":::

1. **대상** 에 대 한 구독 정보를 제공 하 고 **다음** 을 선택 합니다.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="대상 리소스의 스크린샷" lightbox="media/move-resources-subscription-target.png":::

1. 이동 하도록 선택한 리소스의 유효성 검사를 확인 하 고 **다음** 을 선택 합니다. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="이동 중인 리소스를 보여 주는 스크린샷" lightbox="media/confirm-move-resources-subscription-target.png":::

1. 새 리소스 Id를 사용 하도록 업데이트 될 때까지 연결 된 도구와 스크립트가 작동 하지 않는다는 것을 나타내는 확인란을 선택 합니다. 그런 다음, **이동** 을 선택 합니다.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="이동 중인 선택한 리소스의 요약을 보여 주는 스크린샷 " lightbox="media/review-move-resources-subscription-target.png":::

   리소스 이동이 완료 되 면 알림이 표시 됩니다. 새 구독은 사설 클라우드 개요에 표시 됩니다.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="새 구독을 보여 주는 스크린샷" lightbox="media/moved-subscription-target.png":::

