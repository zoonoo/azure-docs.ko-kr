---
title: Azure VMware Solution 배포 및 구성
description: 계획 단계에서 수집된 정보를 사용하여 Azure VMware Solution 프라이빗 클라우드를 배포하고 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.custom: contperf-fy21q4, devx-track-azurecli
ms.date: 04/23/2021
ms.openlocfilehash: fe03713a8bb8cd2304e75c828b27d479eb83c0e0
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109751441"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Azure VMware Solution 배포 및 구성

이 문서에서는 [계획 섹션](production-ready-deployment-steps.md)의 정보를 사용하여 Azure VMware Solution을 배포하고 구성합니다. 

>[!IMPORTANT]
>계속하기 전에 [계획 섹션](production-ready-deployment-steps.md)을 살펴본 정보입니다.

## <a name="step-1-register-the-microsoftavs-resource-provider"></a>1단계. **Microsoft.AVS** 리소스 공급자 등록

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]

## <a name="step-2-create-an-azure-vmware-solution-private-cloud"></a>2단계. Azure VMware Solution 프라이빗 클라우드 만들기

[!INCLUDE [create-private-cloud-azure-portal-steps](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>이 단계의 엔드투엔드 개요는 [Azure VMware Solution: 배포](https://www.youtube.com/embed/gng7JjxgayI) 비디오를 참조하세요.


## <a name="step-3-connect-to-azure-virtual-network-with-expressroute"></a>3단계: ExpressRoute를 사용하여 Azure Virtual Network에 연결

계획 단계에서 *기존* 또는 *새* ExpressRoute 가상 네트워크 게이트웨이를 사용할지 여부를 정의했습니다.  

### <a name="use-a-new-expressroute-virtual-network-gateway"></a>새 ExpressRoute 가상 네트워크 게이트웨이 사용

>[!IMPORTANT]
>가상 네트워크 게이트웨이가 아직 **없는** GatewaySubnet이 있는 가상 네트워크가 있어야 합니다.

| 조건 | 결과  |
| --- | --- |
| 가상 네트워크가 아직 없습니다.     |  다음을 만듭니다.<ul><li><a href="tutorial-configure-networking.md#create-a-virtual-network">가상 네트워크</a></li><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">가상 네트워크 게이트웨이</a></li></ul>        |
| GatewaySubnet이 **없는** 가상 네트워크가 이미 있습니다.   | 다음을 만듭니다. <ul><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">가상 네트워크 게이트웨이</a></li></ul>          |
| GatewaySubnet이 **있는** 가상 네트워크가 이미 있습니다. | [가상 네트워크 게이트웨이 만들기](tutorial-configure-networking.md#create-a-virtual-network-gateway)   |


### <a name="use-an-existing-virtual-network-gateway"></a>기존 가상 네트워크 게이트웨이 사용

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="step-4-validate-the-connection"></a>4단계. 연결 유효성 검사

ExpressRoute를 종료하는 Azure Virtual Network와 Azure VMware Solution 프라이빗 클라우드가 연결되어 있어야 합니다. 

1. Azure VMware Solution ExpressRoute를 종료하는 Azure Virtual Network 내에 있는 [가상 머신](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)을 사용합니다([3단계. ExpressRoute를 사용하여 Azure Virtual Network에 연결](#step-3-connect-to-azure-virtual-network-with-expressroute) 참조).  

   1. Azure [Portal](https://portal.azure.com)에 로그인합니다.
   2. 실행 상태인 VM으로 이동하고 **설정** 에서 **네트워킹** 을 선택한 다음, 네트워크 인터페이스 리소스를 선택합니다.
      ![네트워크 인터페이스 보기](../virtual-network/media/diagnose-network-routing-problem/view-nics.png)
   4. 왼쪽에서 **유효한 경로** 를 선택합니다. 배포 단계에서 입력한 `/22` CIDR 블록 내에 포함된 주소 접두사 목록이 표시됩니다.

1. vCenter 및 NSX-T Manager 모두에 로그인하려면 웹 브라우저를 열고 네트워크 경로 유효성 검사에 사용되는 동일한 가상 머신에 로그인합니다.  

   Azure Portal에서 vCenter 및 NSX-T Manager 콘솔의 IP 주소와 자격 증명을 식별할 수 있습니다.  프라이빗 클라우드를 선택한 다음, **관리** > **ID** 를 선택합니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="프라이빗 클라우드 vCenter와 NSX Manager의 URL 및 자격 증명에 대한 스크린샷." border="true":::


## <a name="next-steps"></a>다음 단계

다음 섹션에서는 ExpressRoute를 통해 Azure VMware Solution을 온-프레미스 네트워크에 연결합니다.

> [!div class="nextstepaction"]
> [온-프레미스 환경에 연결](tutorial-expressroute-global-reach-private-cloud.md)