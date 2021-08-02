---
title: Azure VMware Solution 구독을 다른 구독으로 이동
description: 이 문서에서는 Azure VMware Solution 구독을 다른 구독으로 이동하는 방법을 설명합니다. 요금 청구와 같은 다양한 이유로 리소스를 이동할 수 있습니다.
ms.custom: subject-moving-resources
ms.topic: how-to
ms.date: 04/26/2021
ms.openlocfilehash: 02f35efe90d4670c4ab38155f129ec0d61c1b03d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966706"
---
# <a name="move-azure-vmware-solution-subscription-to-another-subscription"></a>Azure VMware Solution 구독을 다른 구독으로 이동

이 문서에서는 Azure VMware Solution 구독을 다른 구독으로 이동하는 방법을 설명합니다. 요금 청구와 같은 다양한 이유로 구독을 이동할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
**이전** 구독 및 **대상** 구독 둘 다에서 기여자 이상의 권한이 있어야 합니다. 

>[!IMPORTANT]
>VNet 및 VNet 게이트웨이는 한 구독에서 다른 구독으로 이동할 수 없습니다. 또한 구독을 이동해도 vCenter, NSX, 워크로드 가상 머신과 같은 관리 및 워크로드에 영향을 주지 않습니다.

## <a name="prepare-and-move"></a>준비 및 이동 

1. Azure Portal에서 이동할 프라이빗 클라우드를 선택합니다.

   :::image type="content" source="media/move-subscriptions/source-subscription-id.png" alt-text="선택한 프라이빗 클라우드에 대한 개요 세부 정보를 보여 주는 스크린샷입니다.":::

1. 명령 프롬프트에서 구성 요소와 워크로드를 ping하여 동일한 구독에서 ping하는지 확인합니다.  

   :::image type="content" source="media/move-subscriptions/verify-components-workloads.png" alt-text="ping 명령 및 ping 결과를 보여 주는 스크린샷입니다.":::

1. **구독(변경)** 링크를 선택합니다.

   :::image type="content" source="media/move-subscriptions/private-cloud-overview-subscription-id.png" alt-text="프라이빗 클라우드 세부 정보를 보여 주는 스크린샷입니다."::: 

1. **대상** 에 대한 구독 세부 정보를 제공하고 **다음** 을 선택합니다.

   :::image type="content" source="media/move-subscriptions/move-resources-subscription-target.png" alt-text="대상 리소스 스크린샷입니다.":::

1. 이동하도록 선택한 리소스의 유효성 검사를 확인합니다.  이동하도록 선택한 모든 리소스의 유효성을 검사합니다. 선택한 리소스의 유효성을 검사하는 동안에는 유효성 검사 상태가 **유효성 검사 보류 중** 으로 표시됩니다. 

   :::image type="content" source="media/move-subscriptions/pending-move-resources-subscription-target.png" alt-text="이동되는 리소스를 보여 주는 스크린샷입니다.":::

1. 유효성 검사가 성공적으로 완료되면 **다음** 을 선택하여 프라이빗 클라우드 마이그레이션을 시작합니다.

   :::image type="content" source="media/move-subscriptions/move-resources-succeeded.png" alt-text=" 성공 유효성 검사 상태를 보여 주는 스크린샷입니다.":::

1. 새 리소스 ID를 사용하도록 업데이트할 때까지 연결된 도구와 스크립트가 작동하지 않는 것을 알고 있음을 나타내는 확인란을 선택합니다. 그런 다음, **이동** 을 선택합니다.

   :::image type="content" source="media/move-subscriptions/review-move-resources-subscription-target.png" alt-text="이동 중인 선택된 리소스에 대한 요약을 보여 주는 스크린샷입니다.":::

## <a name="verify-the-move"></a>이동 확인

리소스 이동이 완료되면 알림이 표시됩니다. 

:::image type="content" source="media/move-subscriptions/notification-move-resources-subscription-target.png" alt-text="리소스 이동이 완료된 후 표시되는 알림 스크린샷입니다.":::

새 구독은 프라이빗 클라우드 개요에 표시됩니다.

:::image type="content" source="media/move-subscriptions/moved-subscription-target.png" alt-text="새 구독을 보여 주는 스크린샷입니다.":::

## <a name="next-steps"></a>다음 단계
다음에 대해 자세히 알아봅니다.

- [지역간 Azure VMware Solution 이동](move-azure-vmware-solution-across-regions.md)
- [네트워킹 리소스 이동 지침](../azure-resource-manager/management/move-limitations/networking-move-limitations.md)
- [가상 머신 이동 지침](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)
- [App Service 리소스 이동 지침](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)