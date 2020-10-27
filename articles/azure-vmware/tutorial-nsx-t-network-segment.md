---
title: 자습서 - Azure VMware Solution에서 NSX-T 네트워크 세그먼트 만들기
description: vCenter에서 VM에 사용되는 NSX-T 네트워크 세그먼트를 만드는 방법 알아보기
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f88f186d2af10bcc114d64920a3ac489ef7be54f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367672"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>자습서: Azure VMware Solution에서 NSX-T 네트워크 세그먼트 만들기

vCenter에서 만든 VM(가상 머신)은 NSX-T에서 만든 네트워크 세그먼트에 배치되고 vCenter에 표시됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * NSX-T Manager에서 탐색하여 네트워크 세그먼트 추가
> * 새 네트워크 세그먼트 추가
> * vCenter의 새 네트워크 세그먼트 관찰

## <a name="prerequisites"></a>필수 구성 요소

vCenter 및 NSX-T Manager 관리 인터페이스에 액세스할 수 있는 Azure VMware Solution 프라이빗 클라우드가 있어야 합니다. 자세한 내용은 [네트워킹 구성](tutorial-configure-networking.md) 자습서를 참조하세요.

## <a name="provision-a-network-segment-in-nsx-t"></a>NSX-T에서 네트워크 세그먼트 프로비저닝

1. 프라이빗 클라우드의 vCenter에서 **SDDC-Datacenter > 네트워크** 를 선택하고 아직 네트워크가 없는지 확인합니다.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. 프라이빗 클라우드의 NSX-T Manager에서 **네트워킹** 을 선택합니다.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. **세그먼트** 를 선택합니다.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. NSX-T 세그먼트 개요 페이지에서 **세그먼트 추가** 를 선택합니다. 프라이빗 클라우드 프로비저닝의 일부로 세그먼트 세 개가 생성되며 VM에는 사용할 수 없습니다.  이 목적을 위해서는 새 네트워크 세그먼트를 추가해야 합니다.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. 세그먼트 이름을 지정하고 미리 구성된 Tier1 게이트웨이(TNTxx-T1)를 **연결된 게이트웨이** 로 선택하고, **유형** 은 [유동적]으로 유지하고, 미리 구성된 오버레이 **전송 영역** (TNTxx-OVERLAY-TZ)을 선택한 다음, [서브넷 설정]을 선택합니다. 이 섹션의 다른 모든 설정과 **포트** 및 **세그먼트 프로파일** 은 구성 그대로 기본값으로 유지하면 됩니다.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. 새 세그먼트에 대한 게이트웨이의 IP 주소를 설정한 다음, **추가** 를 선택합니다. 사용하는 IP 주소는 겹치지 않는 RFC1918 주소 블록에 있어야 합니다. 그래야 새 세그먼트의 VM에 연결할 수 있습니다.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. **적용** 을 선택하여 새 네트워크 세그먼트를 적용한 다음, **저장** 을 사용하여 구성을 저장합니다.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. 이제 새 네트워크 세그먼트가 생성되었고 **아니요** 를 선택하여 세그먼트 구성을 계속하는 옵션을 거부합니다.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. **네트워킹 > 세그먼트** 를 선택하고 새 세그먼트가 목록에 있는지 확인하여(이 경우 "ls01") NSX에 새 네트워크 세그먼트가 있는지 확인합니다.

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

1. **네트워킹 > SDDC-Datacenter** 를 선택하고 새 세그먼트가 목록에 있는지 확인하여(이 경우 "ls01") vCenter에 새 네트워크 세그먼트가 있는지 확인합니다.

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="프라이빗 클라우드의 vCenter에서 SDDC-Datacenter > 네트워크를 선택하고 아직 네트워크가 없는지 확인합니다.":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 vCenter의 VM에 사용되는 NSX-T 네트워크 세그먼트를 만들었습니다. 이제 [Azure VMware Solution에서 VM을 배포하는 콘텐츠 라이브러리를 생성](deploy-vm-content-library.md)할 수 있습니다. 이 자습서에서 만든 네트워크에서 VM을 프로비저닝할 수도 있습니다.

그렇지 않은 경우 다음 자습서를 계속 진행하여 Azure VMware Solution에서 프라이빗 클라우드에 대한 ExpressRoute Global Reach 피어링을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [온-프레미스 환경을 프라이빗 클라우드로 피어링](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
