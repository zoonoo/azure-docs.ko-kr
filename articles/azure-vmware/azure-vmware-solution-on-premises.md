---
title: 온-프레미스 환경에 Azure VMware Solution 연결
description: 온-프레미스 환경에 Azure VMware Solution을 연결하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578911"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>온-프레미스 환경에 Azure VMware Solution 연결

이 문서에서는 Azure VMware Solution을 온-프레미스 환경에 연결하기 위해 [계획 중에 수집된 정보](production-ready-deployment-steps.md)를 계속 사용합니다.

시작하기 전에 Azure VMware Solution을 온-프레미스 환경에 연결하기 위한 두 가지 필수 구성 요소가 있습니다.

- 온-프레미스 환경에서 Azure로의 ExpressRoute 회로
- [계획 단계](production-ready-deployment-steps.md)의 일부로 정의한 ExpressRoute Global Reach 피어링에 대한 /29 겹치지 않는 네트워크 주소 블록

>[!NOTE]
> VPN을 통해 연결할 수 있지만 이 빠른 시작 문서의 범위를 벗어나는 것입니다.

## <a name="establish-an-expressroute-global-reach-connection"></a>ExpressRoute Global Reach 연결 설정

ExpressRoute Global Reach를 사용하여 Azure VMware Solution 프라이빗 클라우드에 온-프레미스 연결을 설정하려면 [온-프레미스 환경을 프라이빗 클라우드로 피어링](tutorial-expressroute-global-reach-private-cloud.md) 자습서를 따르세요.



## <a name="verify-on-premises-network-connectivity"></a>온-프레미스 네트워크 연결 확인

이제 ExpressRoute에서 NSX 네트워크 세그먼트와 Azure VMware Solution 관리 세그먼트를 연결하는 **온-프레미스 에지 라우터**에 표시됩니다.

>[!NOTE]
>모든 사용자는 다른 환경을 가지며, 일부는 이러한 경로를 온-프레미스 네트워크에 다시 전파하도록 허용해야 합니다.  

일부 환경에서는 ExpressRoute 회로를 보호하는 방화벽이 있습니다.  방화벽 및 경로 잘라내기가 발생하지 않는 경우 온-프레미스 환경에서 NSX-T 세그먼트에서 Azure VMware Solution vCenter 서버 또는 [VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment)을 ping할 수 있습니다.

또한 NSX-T 세그먼트의 VM에서 온-프레미스 환경의 리소스에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 섹션으로 계속 진행하여 VMware HCX를 배포 및 구성합니다.

> [!div class="nextstepaction"]
> [VMware HCX 배포 및 구성](tutorial-deploy-vmware-hcx.md)