---
title: 장애 조치 VM에 대 한 네트워킹 구성 사용자 지정 | Microsoft Docs
description: Azure Site Recovery를 사용 하 여 Azure Vm의 복제에서 장애 조치 VM에 대 한 네트워킹 구성 사용자 지정 개요를 제공 합니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 8038f7c909cfeaf15039afa7335dd6b0460a2622
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293453"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>대상 Azure VM의 네트워킹 구성 사용자 지정

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 한 지역에서 다른 지역으로 azure vm을 복제 및 복구할 때 대상 azure vm (가상 머신)에서 네트워킹 구성을 사용자 지정 하는 방법에 대 한 지침을 제공 합니다.

## <a name="before-you-start"></a>시작하기 전 주의 사항

Site Recovery가 [이 시나리오](azure-to-azure-architecture.md)에 재해 복구를 지원하는 방법을 알아봅니다.

## <a name="supported-networking-resources"></a>지원 되는 네트워킹 리소스

Azure Vm을 복제 하는 동안 장애 조치 VM에 대해 다음과 같은 주요 리소스 구성을 제공할 수 있습니다.

- [내부 부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [공용 IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- 서브넷 및 NIC 모두에 대 한 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

 > [!IMPORTANT]
  > 이러한 설정은 현재 장애 조치 (failover) 작업 에서만 지원 되며 테스트 장애 조치 (failover)에 대해서는 지원 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 복구 쪽 구성을 미리 계획 해야 합니다.
- 네트워킹 리소스를 미리 만듭니다. Azure Site Recovery 서비스에서 이러한 설정을 적용 하 고 장애 조치 (failover) VM이 이러한 설정을 준수 하는지 확인할 수 있도록 입력으로 제공 합니다.

## <a name="customize-failover-networking-configurations"></a>장애 조치 (failover) 네트워킹 구성 사용자 지정

1. 복제 된 **항목**으로 이동 합니다. 
2. 원하는 Azure VM을 선택 합니다.
3. **Compute 및 네트워크** 를 선택 하 고 **편집**을 선택 합니다. NIC 구성 설정에는 원본에 해당 하는 리소스가 포함 되어 있습니다. 

     ![장애 조치 (failover) 네트워킹 구성 사용자 지정](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 구성 하려는 NIC 근처에서 **편집** 을 선택 합니다. 열리는 다음 블레이드에서 대상에서 미리 만든 해당 리소스를 선택 합니다.

    ![NIC 구성 편집](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. **확인**을 선택합니다.

Site Recovery는 이제 이러한 설정을 적용 하 고 장애 조치 (failover) 시 VM이 해당 NIC를 통해 선택한 리소스에 연결 되도록 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="unable-to-view-or-select-a-resource"></a>리소스를 보거나 선택할 수 없습니다.

네트워킹 리소스를 선택 하거나 볼 수 없는 경우에는 다음 검사 및 조건을 확인 하세요.

- 네트워킹 리소스의 대상 필드는 원본 VM에 해당 하는 입력이 있는 경우에만 사용할 수 있습니다. 이는 재해 복구 시나리오에 대 한 원칙을 기반으로 하며, 정확한 또는 축소 된 버전의 원본 중 하나를 사용할 수 있습니다.
- 각 네트워킹 리소스에 대해 일부 필터는 드롭다운 목록에서 적용 되어 장애 조치 (failover) VM이 선택 된 리소스에 자동으로 연결 되 고 장애 조치 (failover) 안정성이 유지 되도록 합니다. 이러한 필터는 원본 VM을 구성할 때 확인 된 것과 동일한 네트워킹 조건을 기반으로 합니다.

내부 부하 분산 장치 유효성 검사:

- 부하 분산 장치 및 대상 VM의 구독과 지역은 동일 해야 합니다.
- 내부 부하 분산 장치와 연결 된 가상 네트워크와 대상 VM의 가상 네트워크는 동일 해야 합니다.
- 대상 VM의 공용 IP SKU와 내부 부하 분산 장치의 SKU는 동일 해야 합니다.
- 대상 VM이 가용성 영역에 배치 되도록 구성 되어 있는 경우 부하 분산 장치가 영역 중복 인지 또는 가용성 영역의 일부 인지 확인 합니다. 기본 SKU 부하 분산 장치는 영역을 지원 하지 않으며이 경우 드롭다운 목록에 표시 되지 않습니다.
- 내부 부하 분산 장치에 미리 만든 백 엔드 풀 및 프런트 엔드 구성이 있는지 확인 합니다.


공용 IP 주소:
    
- 공용 IP와 대상 VM의 구독 및 지역은 동일 해야 합니다.
- 대상 VM의 공용 IP SKU와 내부 부하 분산 장치의 SKU는 동일 해야 합니다.

네트워크 보안 그룹:
- 네트워크 보안 그룹 및 대상 VM의 구독과 지역은 동일 해야 합니다.


> [!WARNING]
> 대상 VM이 가용성 집합에 연결 된 경우 동일한 SKU의 공용 IP 및 내부 부하 분산 장치를 가용성 집합에 있는 다른 VM의 공용 IP 및 내부 부하 분산 장치와 연결 해야 합니다. 그렇지 않으면 장애 조치 (failover)가 실패할 수 있습니다.
