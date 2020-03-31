---
title: 장애 조치 VM에 대한 네트워킹 구성 사용자 지정 | 마이크로 소프트 문서
description: Azure 사이트 복구를 사용하는 Azure VM 복제에서 장애 조치 VM에 대한 네트워킹 구성을 사용자 지정하는 개요를 제공합니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292861"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>대상 Azure VM의 네트워킹 구성 사용자 지정

이 문서에서는 [Azure 사이트 복구를](site-recovery-overview.md)사용하여 한 지역에서 다른 리전으로 Azure VM을 복제하고 복구할 때 대상 VM(대상 Azure 가상 시스템)에서 네트워킹 구성을 사용자 지정하는 방법에 대한 지침을 제공합니다.

## <a name="before-you-start"></a>시작하기 전에

Site Recovery가 [이 시나리오](azure-to-azure-architecture.md)에 재해 복구를 지원하는 방법을 알아봅니다.

## <a name="supported-networking-resources"></a>지원되는 네트워킹 리소스

Azure VM을 복제하는 동안 장애 조치 VM에 대해 다음과 같은 주요 리소스 구성을 제공할 수 있습니다.

- [내부 부하 분산기](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [공용 IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- 서브넷과 NIC에 대한 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

## <a name="prerequisites"></a>사전 요구 사항

- 복구 측 구성을 미리 계획해야 합니다.
- 네트워킹 리소스를 미리 만듭니다. Azure 사이트 복구 서비스가 이러한 설정을 준수하고 장애 조치 VM이 이러한 설정을 준수하도록 입력으로 제공합니다.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>장애 조치 및 테스트 장애 조치 네트워킹 구성 사용자 지정

1. **복제된 항목으로**이동합니다. 
2. 원하는 Azure VM을 선택합니다.
3. **계산 및 네트워크 계산을** 선택하고 **편집을**선택합니다. NIC 구성 설정에는 원본에 해당하는 리소스가 포함됩니다. 

     ![장애 조치 네트워킹 구성 사용자 지정](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 테스트 장애 조치 가상 네트워크를 선택합니다. 비워 두고 테스트 장애 조치(failover)시에 하나를 선택할 수 있습니다.
5. 장애 조치 네트워크는 구성하려는 NIC 근처에서 **편집을** 선택합니다. 열리는 다음 블레이드에서 테스트 장애 조치 및 장애 조치 위치에서 미리 생성된 해당 리소스를 선택합니다.

    ![NIC 구성 편집](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. **확인**을 선택합니다.

이제 사이트 복구는 이러한 설정을 준수하고 장애 조치의 VM이 해당 NIC를 통해 선택한 리소스에 연결되어 있는지 확인합니다.

복구 계획을 통해 테스트 장애 조치(failover)를 트리거하면 항상 Azure 가상 네트워크에 요청합니다. 이 가상 네트워크는 미리 구성된 테스트 장애 조치 설정이 없는 컴퓨터에 대한 테스트 장애 조치(failover)에 사용됩니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="unable-to-view-or-select-a-resource"></a>리소스를 보거나 선택할 수 없음

네트워킹 리소스를 선택하거나 볼 수 없는 경우 다음 검사 및 조건을 진행합니다.

- 네트워킹 리소스의 대상 필드는 원본 VM에 해당 입력이 있는 경우에만 활성화됩니다. 이는 재해 복구 시나리오의 경우 소스의 정확한 버전또는 축소된 버전을 원한다는 원칙에 따라 선택됩니다.
- 각 네트워킹 리소스에 대해 장애 조치 VM이 선택한 리소스에 자신을 연결하고 장애 조치 안정성이 유지되도록 일부 필터가 드롭다운 목록에 적용됩니다. 이러한 필터는 원본 VM을 구성할 때 확인된 것과 동일한 네트워킹 조건을 기반으로 합니다.

내부 부하 분산 검사:

- 로드 밸러블러와 대상 VM의 구독 및 지역은 동일해야 합니다.
- 내부 로드 밸러블러 및 대상 VM과 연결된 가상 네트워크는 동일해야 합니다.
- 대상 VM의 공용 IP SKU와 내부 로드 밸런서의 SKU는 동일해야 합니다.
- 대상 VM이 가용성 영역에 배치하도록 구성된 경우 로드 밸런서가 영역 중복인지 아니면 가용성 영역의 일부인지 확인합니다. 기본 SKU 로드 밸러울러는 영역을 지원하지 않으며 이 경우 드롭다운 목록에 표시되지 않습니다.
- 내부 로드 밸로이터에 미리 생성된 백 엔드 풀 및 프런트 엔드 구성이 있는지 확인합니다.

공용 IP 주소:

- 공용 IP와 대상 VM의 구독 및 지역은 동일해야 합니다.
- 대상 VM의 공용 IP SKU와 내부 로드 밸런서의 SKU는 동일해야 합니다.

네트워크 보안 그룹:
- 네트워크 보안 그룹과 대상 VM의 구독 및 지역은 동일해야 합니다.


> [!WARNING]
> 대상 VM이 가용성 집합과 연결된 경우 동일한 SKU의 공용 IP 및 내부 로드 밸런서를 가용성 집합의 다른 VM의 공용 IP 및 내부 로드 밸런서와 연결해야 합니다. 그렇지 않으면 장애 조치(failover)가 성공하지 못할 수 있습니다.
