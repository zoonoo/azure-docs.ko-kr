---
title: Azure Stack Edge VPN (가상 사설망)에서 BCDR (비즈니스 연속성 및 재해 복구) 구성
description: Azure Stack Edge VPN에서 BCDR을 구성 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: 4d735c623a6dffe24108d06d00caa7fba987c7df
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467275"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Azure Stack Edge VPN에 대 한 비즈니스 연속성 및 재해 복구 구성

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

이 문서에서는 Azure Stack Edge 장치에 구성 된 VPN (가상 사설망)에서 비즈니스 연속성 및 재해 복구 (BCDR)를 구성 하는 방법을 설명 합니다.

이 문서는 Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 장치에 모두 적용 됩니다.

## <a name="configure-failover-to-a-paired-region"></a>쌍을 이루는 지역으로 장애 조치 (failover) 구성

Azure Stack Edge 장치는 Azure Storage와 같은 다른 Azure 서비스를 사용 합니다. Azure Stack Edge 장치에서 사용 하는 특정 Azure 서비스에 대 한 BCDR을 구성할 수 있습니다. Azure Stack Edge에서 사용 하는 Azure 서비스가 쌍을 이루는 지역으로 장애 조치 (failover) 되 면 이제 Azure Stack에 지 장치가 새 IP 주소에 연결 되 고 통신이 이중 암호화 되지 않습니다. 

Azure Stack Edge 장치는 분할 터널링을 사용 하 고 홈 지역 (Azure Stack에 지 장치와 연결 된 지역)에 구성 된 모든 데이터와 서비스를 VPN 터널을 통해 이동 합니다. Azure 서비스가 홈 지역 외부에 있는 쌍을 이루는 지역으로 장애 조치 (failover) 되 면 데이터가 더 이상 VPN을 통해 전달 되지 않으므로 이중 암호화 되지 않습니다. 

이 시나리오에서는 일반적으로 소수의 Azure 서비스만 영향을 받습니다. 이 문제를 해결 하려면 Azure Stack Edge VPN 구성에서 다음과 같이 변경 해야 합니다.

1. Azure Stack Edge에서 VPN에 대 한 포괄 경로에 장애 조치 (failover) Azure 서비스 IP 범위를 추가 합니다. 그러면 서비스가 VPN을 통해 라우팅되도록 시작 됩니다.

    포함 경로를 추가 하려면 서비스별 경로를 포함 하는 json 파일을 다운로드 해야 합니다. 새 경로를 사용 하 여이 파일을 업데이트 해야 합니다.
2. Azure 경로 테이블에 해당 하는 Azure 서비스 IP 범위를 추가 합니다.
3. 방화벽에 경로를 추가 합니다.

> [!NOTE]
>
> 1. Azure VPN gateway 및 Azure Virtual Network (VNET)의 장애 조치 (failover)는 [재해로 인해 실패 한 azure 지역에서 복구](#recover-from-a-failed-azure-region)섹션에서 다룹니다.
> 2. Azure 경로 테이블에 추가 되는 IP 범위는 400 제한을 넘을 수 있습니다. 이 문제가 발생 하는 경우 섹션의 지침에 따라 [azure 지역에서 다른 azure 지역으로 이동](#move-from-an-azure-region-to-another)해야 합니다.

## <a name="recover-from-a-failed-azure-region"></a>실패 한 Azure 지역에서 복구

지진과 같은 치명적인 이벤트로 인해 전체 Azure 지역이 장애 조치 (failover) 되는 경우 Azure Stack Edge 서비스를 포함 하 여 해당 지역의 모든 Azure 서비스가 장애 조치 (failover) 됩니다. 여러 서비스가 있으므로 포함 경로를 몇 개의 매우 쉽게 사용할 수 있습니다. Azure는 400 경로를 제한 합니다. 

지역이 장애 조치 (failover) 되 면 Vnet (가상 네트워크)도 새 지역으로 장애 조치 (failover) 되므로 가상 네트워크 게이트웨이 (VPN gateway)가 수행 됩니다. 이러한 변경을 해결 하려면 Azure Stack Edge VPN 구성에서 다음과 같이 변경 합니다.

1. Vnet을 대상 지역으로 이동 합니다. 자세한 내용은 [Azure Portal를 통해 Azure 가상 네트워크를 다른 지역으로 이동](../virtual-network/move-across-regions-vnet-portal.md)을 참조 하세요.
2. Vnet을 이동한 대상 지역에 새 Azure VPN gateway를 배포 합니다. 자세한 내용은 [가상 네트워크 게이트웨이 만들기](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)를 참조 하세요.
3. VPN 연결에서 위의 VPN gateway를 사용 하도록에 지 VPN 구성을 Azure Stack 업데이트 하 고 대상 지역을 선택 하 여 VPN gateway를 사용 하는 경로를 추가 합니다.
4. 클라이언트 주소 풀도 변경 하는 경우 들어오는 Azure 경로 테이블을 업데이트 합니다. 

## <a name="move-from-an-azure-region-to-another"></a>Azure 지역에서 다른 지역으로 이동

Azure Stack Edge 장치를 한 위치에서 다른 위치로 이동할 수 있습니다. 장치를 배포 하는 위치와 가장 가까운 지역을 사용 하려면 새 홈 지역에 대해 장치를 구성 해야 합니다. 다음과 같이 변경합니다.

1. 새 지역의 VPN gateway를 사용 하도록 Azure Stack Edge VPN 구성을 업데이트 하 고 새 지역을 선택 하 여 VPN gateway를 사용 하는 경로를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 장치를 백업](azure-stack-edge-gpu-prepare-device-failure.md)합니다.