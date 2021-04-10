---
title: 자습서 - Azure VMware Solution에서 NSX-T 네트워크 세그먼트 추가
description: vCenter에서 VM(가상 머신)에 사용할 NSX-T 네트워크 세그먼트를 만드는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9125e552f9641a2d26b9584b66a4447f9c152161
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462129"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>자습서: Azure VMware Solution에서 네트워크 세그먼트 추가 

vCenter에서 만든 VM(가상 머신)은 NSX-T에서 만든 네트워크 세그먼트에 배치되고 vCenter에 표시됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * NSX-T Manager에서 탐색하여 네트워크 세그먼트 추가
> * 새 네트워크 세그먼트 추가
> * vCenter의 새 네트워크 세그먼트 관찰

## <a name="prerequisites"></a>필수 구성 요소

vCenter 및 NSX-T Manager 인터페이스에 대한 액세스 권한이 있는 Azure VMware Solution 프라이빗 클라우드. 자세한 내용은 [네트워킹 구성](tutorial-configure-networking.md) 자습서를 참조하세요.

## <a name="add-a-network-segment"></a>네트워크 세그먼트 추가

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 vCenter의 VM에 사용할 NSX-T 네트워크 세그먼트를 만들었습니다. 

이제 다음과 같은 작업을 수행할 수 있습니다. 

- [Azure VMware Solution용 DHCP 만들기 및 관리](manage-dhcp.md)
- [Azure VMware Solution에서 VM을 배포하는 콘텐츠 라이브러리 만들기](deploy-vm-content-library.md) 
- [온-프레미스 환경을 프라이빗 클라우드로 피어링](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
