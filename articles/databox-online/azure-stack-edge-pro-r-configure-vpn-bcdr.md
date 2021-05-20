---
title: Azure Stack Edge VPN(가상 사설망)에서 BCDR(비즈니스 연속성 및 재해 복구) 구성
description: Azure Stack Edge VPN에서 BCDR을 구성하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367693"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Azure Stack Edge VPN에 대한 비즈니스 연속성 및 재해 복구 구성

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge 디바이스에 구성된 VPN(가상 사설망)에서 BCDR(비즈니스 연속성 및 재해 복구)을 구성하는 방법을 설명합니다.

이 문서는 Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 디바이스에 모두 적용됩니다.

## <a name="configure-failover-to-a-paired-region"></a>쌍을 이루는 지역으로 장애 조치(failover) 구성

Azure Stack Edge 디바이스는 Azure Storage와 같은 다른 Azure 서비스를 사용합니다. Azure Stack Edge 디바이스에서 사용하는 특정 Azure 서비스에 대한 BCDR을 구성할 수 있습니다. Azure Stack Edge에서 사용하는 Azure 서비스가 쌍을 이루는 지역으로 장애 조치(failover)되면 이제 Azure Stack Edge 디바이스가 새 IP 주소에 연결되고 통신이 이중 암호화되지 않습니다. 

Azure Stack Edge 디바이스는 분할 터널링을 사용하고 홈 지역(Azure Stack Edge 디바이스와 연결된 지역)에 구성된 모든 데이터와 서비스는 VPN 터널을 통해 이동합니다. Azure 서비스가 홈 지역 외부의 쌍을 이루는 지역으로 장애 조치(failover)되면 데이터는 더 이상 VPN을 통해 이동하지 않고 이중 암호화되지 않습니다. 

이 시나리오에서는 일반적으로 소수의 Azure 서비스만 영향을 받습니다. 이 문제를 해결하려면 Azure Stack Edge VPN 구성에서 다음을 변경해야 합니다.

1. Azure Stack Edge에서 VPN에 대한 포괄 경로에 장애 조치(failover) Azure 서비스 IP 범위를 추가합니다. 이후 서비스는 VPN을 통해 라우팅되기 시작합니다.

    포괄 경로를 추가하려면 서비스별 경로를 보유하는 json 파일을 다운로드해야 합니다. 새 경로를 사용하여 이 파일을 업데이트해야 합니다.
2. Azure Route 테이블에 해당하는 Azure 서비스 IP 범위를 추가합니다.
3. 방화벽에 경로를 추가합니다.

> [!NOTE]
>
> 1. Azure VPN 게이트웨이 및 Azure Virtual Network(VNET)의 장애 조치(failover)는 [재해로 인해 실패한 Azure 지역에서 복구](#recover-from-a-failed-azure-region) 섹션에서 다룹니다.
> 2. Azure 경로 테이블에 추가되는 IP 범위가 400개 제한을 초과할 수 있습니다. 이 문제가 발생하는 경우 [한 Azure 지역에서 다른 Azure 지역으로 이동](#move-from-an-azure-region-to-another) 섹션의 지침을 따라야 합니다.

## <a name="recover-from-a-failed-azure-region"></a>실패한 Azure 지역에서 복구

지진과 같은 중대한 이벤트로 인해 전체 Azure 지역에 장애 조치(failover)가 이루어지는 경우 Azure Stack Edge 서비스를 포함하여 해당 지역의 모든 Azure 서비스가 장애 조치(failover)됩니다. 여러 서비스가 있으므로 포괄 경로는 쉽게 몇백 개에 이를 수 있습니다. Azure는 경로를 400개로 제한합니다. 

지역에서 장애 조치(failover)가 이루어지면 가상 네트워크(Vnet)는 물론 가상 네트워크 게이트웨이(VPN 게이트웨이)도 장애 조치(failover)됩니다. 이 변경 사항을 해결하려면 Azure Stack Edge VPN 구성에서 다음을 변경합니다.

1. Vnet을 대상 지역으로 이동합니다. 자세한 내용은 [Azure Portal을 통해 Azure 가상 네트워크를 다른 지역으로 이동](../virtual-network/move-across-regions-vnet-portal.md)을 참조하세요.
2. Vnet을 이동한 대상 지역에 새 Azure VPN 게이트웨이를 배포합니다. 자세한 내용은 [가상 네트워크 게이트웨이 만들기](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)를 참조하세요.
3. VPN 연결에서 위의 VPN 게이트웨이를 사용하도록 Azure Stack Edge VPN 구성을 업데이트한 다음 대상 지역을 선택하여 VPN 게이트웨이를 사용하는 경로를 추가합니다.
4. 클라이언트 주소 풀도 변경되는 경우 수신 Azure 경로 테이블을 업데이트합니다. 

## <a name="move-from-an-azure-region-to-another"></a>Azure 지역에서 다른 Azure 지역으로 이동

Azure Stack Edge 디바이스를 한 위치에서 다른 위치로 이동할 수 있습니다. 디바이스가 배포되는 위치와 가장 가까운 지역을 사용하려면 새 홈 지역에 대해 디바이스를 구성해야 합니다. 다음과 같이 변경합니다.

1. 새 지역의 VPN 게이트웨이를 사용하도록 Azure Stack Edge VPN 구성을 업데이트하고 새 지역을 선택하여 VPN 게이트웨이를 사용하는 경로를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 디바이스 백업](azure-stack-edge-gpu-prepare-device-failure.md).