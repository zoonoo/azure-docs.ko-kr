---
title: NSX-T 네트워크 세그먼트 추가
description: Azure VMware Solution에 대한 NSX-T 네트워크 세그먼트를 추가하는 단계입니다.
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335060"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. NSX-T 관리자에서 **네트워킹** > **세그먼트** 를 선택한 다음, **세그먼트 추가** 를 선택합니다. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="새 세그먼트 추가 방법을 보여 주는 스크린샷":::

1. **세그먼트 추가** 를 선택하고 세그먼트의 이름을 입력합니다.

1. **연결된 게이트웨이** 로 Tier1 Gateway(TNTxx-T1)를 선택하고 **형식** 을 Flexible로 유지합니다.

1. 미리 구성된 오버레이 **전송 영역**(TNTxx-OVERLAY-TZ)을 선택한 다음, **서브넷 설정** 을 선택합니다. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="세그먼트 이름, 연결된 게이트웨이 및 유형, 전송 영역을 설정한 다음, 서브넷 설정을 선택합니다.":::

1. 게이트웨이의 IP 주소를 입력한 다음, **추가** 를 선택합니다. 

   >[!IMPORTANT]
   >IP 주소는 겹치지 않는 RFC1918 주소 블록에 있어야 합니다. 그래야 새 세그먼트의 VM에 대한 연결을 보장합니다.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="새 세그먼트에 대한 게이트웨이의 IP 주소를 설정한 다음, 추가를 선택합니다.":::

1. **적용** 을 선택한 다음, **저장** 을 선택합니다.

1. 세그먼트를 계속 구성하는 옵션을 거부하려면 **아니요** 를 선택합니다. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="[아니요]를 선택하여 새로 만든 네트워크 세그먼트를 추가적으로 구성하는 것을 거부합니다.":::

1. 새 네트워크 세그먼트가 있는지 확인합니다. 이 예제에서 **ls01** 은 새 네트워크 세그먼트입니다.

   1. NSX-T Manager에서 **네트워킹** > **세그먼트** 를 선택합니다. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="NSX에 새 네트워크 세그먼트가 있는지 확인합니다.":::

   1. vCenter **네트워킹 > SDDC-Datacenter** 를 선택합니다.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="vCenter에 새 네트워크 세그먼트가 있는지 확인합니다.":::