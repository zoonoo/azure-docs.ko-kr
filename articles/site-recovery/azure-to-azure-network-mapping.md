---
title: Azure Site Recovery에서 두 Azure 지역 간 가상 네트워크 매핑 | Microsoft Docs
description: Azure Site Recovery는 가상 머신 및 실제 서버의 복제, 장애 조치 및 복구를 조정합니다. Azure로 또는 보조 데이터 센터로 장애 조치에 대해 알아봅니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 1e8bad9a7a194c96c39be0ab4f1c2f40d79031ea
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209604"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>다른 Azure 지역에 있는 가상 네트워크 매핑


이 문서에는 다른 Azure 지역에 있는 Azure 가상 네트워크의 두 인스턴스를 서로 매핑하는 방법이 설명되어 있습니다. 네트워크를 매핑하면 복제된 가상 머신이 대상 Azure 지역에 만들어지는 경우 원본 가상 머신의 가상 네트워크에 매핑되는 가상 네트워크에도 가상 머신이 만들어지도록 합니다.  

## <a name="prerequisites"></a>필수 조건
네트워크를 매핑하기 전에 원본 지역 및 대상 Azure 지역 모두에 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)를 만들도록 합니다.

## <a name="map-virtual-networks"></a>가상 네트워크 매핑

한 Azure 지역에 있는 Azure 가상 네트워크(원본 네트워크)를 다른 지역에 있는 가상 네트워크(대상 네트워크)로 매핑하려면 Azure 가상 머신에서 **Site Recovery 인프라** > **네트워크 매핑**으로 이동합니다. 네트워크 매핑을 만듭니다.

![네트워크 매핑 창 - 네트워크 매핑 만들기](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


다음 예제에서는 가상 머신이 동아시아 지역에서 실행 중입니다. 이 가상 머신은 동남 아시아 지역으로 복제됩니다.

동아시아 지역에서 동남 아시아 지역으로의 네트워크 매핑을 만들려면 원본 네트워크의 위치와 대상 네트워크의 위치를 선택합니다. 그런 다음 **확인**을 선택합니다.

![네트워크 매핑 추가 창 - 원본 네트워크의 원본 및 대상 위치 선택](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


이전 프로세스를 반복하여 동남 아시아 지역에서 동아시아 지역으로의 네트워크 매핑을 만듭니다.

![네트워크 매핑 추가 창 - 대상 네트워크의 원본 및 대상 위치 선택](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>복제를 사용하도록 설정할 때 네트워크 매핑

한 Azure 지역에서 다른 지역으로 가상 머신을 처음으로 복제할 때 네트워크 매핑이 존재하지 않을 경우 복제를 설정할 때 대상 네트워크를 설정할 수 있습니다. Azure Site Recovery는 이 설정에 따라 원본 지역에서 대상 지역으로 그리고 대상 지역에서 원본 지역으로 네트워크 매핑을 만듭니다.   

![설정 구성 창 - 대상 위치 선택](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

기본적으로 Site Recovery는 원본 네트워크와 동일한 대상 지역에 네트워크를 만듭니다. Site Recovery는 원본 네트워크의 이름에 **-asr**을 접두사로 추가하여 네트워크를 만듭니다. 이미 생성된 네트워크를 추가하려면 **사용자 지정**을 선택합니다.

![대상 설정 사용자 지정 창 - 대상 리소스 그룹 이름 및 대상 가상 네트워크 이름 설정](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

네트워크 매핑이 이미 수행된 경우 복제를 사용하도록 설정할 때 대상 가상 네트워크를 변경할 수 없습니다. 이 경우 대상 가상 네트워크를 변경하려면 기존 네트워크 매핑을 수정합니다.  

![대상 설정 사용자 지정 창 - 대상 리소스 그룹 이름 설정](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![네트워크 매핑 수정 창 - 기존 대상 가상 네트워크 이름 수정](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> 지역 A에서 지역 B로 네트워크 매핑을 수정하는 경우 지역 B에서 지역 A로도 네트워크 매핑을 수정해야 합니다.
>
>


## <a name="subnet-selection"></a>서브넷 선택
원본 가상 머신의 서브넷의 이름에 따라 대상 가상 머신의 서브넷이 선택됩니다. 원본 가상 머신과 동일한 이름의 서브넷을 대상 네트워크에서 사용할 수 있는 경우 해당 서브넷은 대상 가상 머신에서 설정됩니다. 대상 네트워크에 동일한 이름의 서브넷이 없는 경우 사전순으로 첫 번째 서브넷이 대상 서브넷으로 설정됩니다.

서브넷을 수정하려면 가상 머신에 대한 **계산 및 네트워크** 설정으로 이동합니다.

![계산 및 네트워크 계산 속성 창](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP 주소

대상 가상 머신의 각 네트워크 인터페이스에 대한 IP 주소는 다음 섹션에 설명된 것처럼 설정됩니다.

### <a name="dhcp"></a>DHCP
원본 가상 머신의 네트워크 인터페이스에서 DHCP를 사용하는 경우 대상 가상 머신의 네트워크 인터페이스도 DHCP를 사용하도록 설정됩니다.

### <a name="static-ip-address"></a>고정 IP 주소
원본 가상 머신의 네트워크 인터페이스에서 고정 IP 주소를 사용하는 경우 대상 가상 머신의 네트워크 인터페이스도 고정 IP 주소를 사용하도록 설정됩니다. 다음 섹션에서는 고정 IP 주소를 설정하는 방법에 대해 설명합니다.

### <a name="ip-assignment-behavior-during-failover"></a>장애 조치(Failover) 중 IP 할당 동작
#### <a name="1-same-address-space"></a>1. 동일한 주소 공간

원본 서브넷과 대상 서브넷에 동일한 주소 공간이 있는 경우 원본 가상 머신의 네트워크 인터페이스 IP 주소를 대상 IP로 설정합니다. 동일한 IP 주소를 사용할 수 없는 경우 다음으로 사용 가능한 IP 주소를 대상 IP 주소로 설정합니다.

#### <a name="2-different-address-spaces"></a>2. 다른 주소 공간

원본 서브넷과 대상 서브넷에 주소 공간이 다른 경우 대상 서브넷의 다음으로 사용 가능한 IP 주소를 대상 IP 주소로 설정합니다.


### <a name="ip-assignment-behavior-during-test-failover"></a>테스트 장애 조치(Failover) 중 IP 할당 동작
#### <a name="1-if-the-target-network-chosen-is-the-production-vnet"></a>1. 선택한 대상 네트워크가 프로덕션 vNet인 경우
- 복구 IP(대상 IP)는 고정 IP가 되지만 장애 조치(Failover)용으로 예약된 **IP 주소와 동일하지 않습니다**.
- 할당된 IP 주소는 서브넷 주소 범위 끝에서부터 사용 가능한 다음 IP가 됩니다.
- 예를 들어, 원본 VM 고정 IP가 10.0.0.19로 구성되고 구성된 프로덕션 네트워크 ***dr-PROD-nw***, 서브넷 범위 10.0.0.0/24를 사용하여 테스트 장애 조치(Failover)가 시도되면 </br>
장애 조치(Failover)된 VM에는 서브넷 주소 범위의 끝에서부터 사용 가능한 다음 IP인 10.0.0.254가 할당됩니다. </br>

**참고:** 용어 **프로덕션 vNet**은 재해 복구 구성 동안 매핑되는 '대상 네트워크'를 나타냅니다.
#### <a name="2-if-the-target-network-chosen-is-not-the-production-vnet-but-has-the-same-subnet-range-as-production-network"></a>2. 선택한 대상 네트워크가 프로덕션 vNet이 아니지만 프로덕션 네트워크와 동일한 서브넷 범위에 있는 경우

- 복구 IP(대상 IP)는 장애 조치(Failover)용으로 예약된 **IP 주소(예: 구성된 고정 IP 주소)와 동일한** 고정 IP가 됩니다. 단, 동일한 IP 주소가 사용 가능해야 합니다.
- 구성된 고정 IP가 이미 일부 다른 VM/장치에 할당되면 복구 IP는 서브넷 주소 범위의 끝에서부터 사용 가능한 다음 IP가 됩니다.
- 예를 들어, 원본 VM 고정 IP가 10.0.0.19로 구성되고 테스트 네트워크 ***dr-NON-PROD-nw***, 프로덕션 네트워크와 동일한 서브넷 범위 10.0.0.0/24를 사용하여 테스트 장애 조치(Failover)가 시도되면 </br>
  장애 조치(Failover)된 VM에는 다음 고정 IP가 할당됩니다. </br>
    - 구성된 고정 IP: 10.0.0.19(IP가 사용 가능한 경우)
    - 사용 가능한 다음 IP: 10.0.0.254(IP 주소 10.0.0.19가 이미 사용 중인 경우)


각 네트워크 인터페이스의 대상 IP를 수정하려면 가상 머신의 **계산 및 네트워크** 설정으로 이동합니다.</br>
모범 사례로, 항상 테스트 장애 조치(Failover)를 수행할 테스트 네트워크를 선택하는 것이 좋습니다.
## <a name="next-steps"></a>다음 단계

* [Azure Virtual Machines 복제에 대한 네트워킹 지침](site-recovery-azure-to-azure-networking-guidance.md)을 살펴봅니다.
