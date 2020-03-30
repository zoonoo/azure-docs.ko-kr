---
title: 클라우드 간이에 의한 Azure VMware 솔루션의 VLAN 및 서브넷
description: 클라우드심플 프라이빗 클라우드에서 VLAN 및 서브넷에 대해 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024894"
---
# <a name="vlans-and-subnets-overview"></a>VLAN 및 서브넷 개요

CloudSimple은 CloudSimple 서비스가 배포되는 지역별 네트워크를 제공합니다.  네트워크는 라우팅을 기본적으로 사용하도록 설정한 단일 TCP 계층 3 주소 공간입니다.  이 리전에서 만든 모든 프라이빗 클라우드 및 서브넷은 추가 구성 없이 서로 통신할 수 있습니다.  VLAN을 사용하여 vCenter에서 분산 포트 그룹을 만들 수 있습니다.

![클라우드심플 네트워크 토폴로지](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

각 프라이빗 클라우드에 대해 VLAN(계층 2 네트워크)이 만들어집니다.  계층 2 트래픽은 프라이빗 클라우드의 경계 내에 유지되므로 프라이빗 클라우드 내에서 로컬 트래픽을 격리할 수 있습니다.  프라이빗 클라우드에서 만든 VLAN은 해당 프라이빗 클라우드에서만 분산 포트 그룹을 만드는 데 사용할 수 있습니다.  프라이빗 클라우드에서 생성된 VLAN은 프라이빗 클라우드의 호스트에 연결된 모든 스위치에서 자동으로 구성됩니다.

## <a name="subnets"></a>서브넷

서브넷의 주소 공간을 정의하여 VLAN을 만들 때 서브넷을 만들 수 있습니다. 주소 공간의 IP 주소가 서브넷 게이트웨이로 할당됩니다. 단일 개인 계층 3 주소 공간은 고객 및 지역에 따라 할당됩니다. 네트워크 리전에서 온-프레미스 네트워크 또는 Azure 가상 네트워크로 RFC 1918 비겹기 주소 공간을 구성할 수 있습니다.

모든 서브넷은 기본적으로 서로 통신할 수 있어 프라이빗 클라우드 간의 라우팅을 위한 구성 오버헤드가 줄어듭니다. 동일한 지역의 PC에서 동서 데이터는 동일한 계층 3 네트워크에 유지되며 리전 내의 로컬 네트워크 인프라를 통해 전송됩니다. 한 리전의 프라이빗 클라우드 간의 통신에는 송신이 필요하지 않습니다. 이 방법을 사용하면 서로 다른 프라이빗 클라우드에 서로 다른 워크로드를 배포할 때 WAN/egress 성능 저하가 제거됩니다.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 서브넷 CIDR 범위

프라이빗 클라우드는 vCenter 서버에서 관리하는 격리된 VMware 스택(ESXi 호스트, vCenter, vSAN 및 NSX) 환경으로 만들어집니다.  관리 구성 요소는 vSphere/vSAN 서브넷 CIDR에 대해 선택된 네트워크에 배포됩니다.  네트워크 CIDR 범위는 배포 하는 동안 다른 서브넷으로 분할 됩니다.

* 최소 vSphere/vSAN 서브넷 CIDR 범위 접두사: **/24**
* 최대 vSphere/vSAN 서브넷 CIDR 범위 접두사: **/21**

> [!CAUTION]
> vSphere/vSAN CIDR 범위의 IP 주소는 프라이빗 클라우드 인프라에서 사용하도록 예약되어 있습니다.  가상 컴퓨터에서 이 범위의 IP 주소를 사용하지 마십시오.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 서브넷 CIDR 범위 제한

vSphere/vSAN 서브넷 CIDR 범위 크기를 선택하면 프라이빗 클라우드의 크기에 영향을 줍니다.  다음 표에서는 vSphere/vSAN 서브넷 CIDR의 크기에 따라 가질 수 있는 최대 노드 수를 보여 주십습니다.

| 지정된 vSphere/vSAN 서브넷 CIDR 접두사 길이 | 최대 노드 수 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>프라이빗 클라우드에서 생성된 관리 서브넷

프라이빗 클라우드를 만들 때 다음 관리 서브넷이 만들어집니다.

* **시스템 관리**. ESXi 호스트의 관리 네트워크, DNS 서버, vCenter 서버에 대한 VLAN 및 서브넷.
* **VMotion**. ESXi 호스트의 vMotion 네트워크에 대한 VLAN 및 서브넷.
* **VSAN**. ESXi 호스트의 vSAN 네트워크에 대한 VLAN 및 서브넷입니다.
* **NsxtEdgeUplink1**. VLAN 및 VLAN용 서브넷은 외부 네트워크로 연결됩니다.
* **NsxtEdgeUplink2**. VLAN 및 VLAN용 서브넷은 외부 네트워크로 연결됩니다.
* **NsxtEdgeTransport**. 전송 영역에 대한 VLAN 및 서브넷은 NSX-T에서 계층 2 네트워크의 도달 범위를 제어합니다.
* **NsxtHostTransport**. 호스트 전송 영역에 대한 VLAN 및 서브넷입니다.

### <a name="management-network-cidr-range-breakdown"></a>관리 네트워크 CIDR 범위 분석

vSphere/vSAN 서브넷 지정된 CIDR 범위는 여러 서브넷으로 나뉩니다.  다음 표에서는 허용된 접두사에 대한 분석의 예를 보여 주었습니다.  이 예제에서는 192.168.0.0을 CIDR 범위로 사용합니다.

예제:

| 지정된 vSphere/vSAN 서브넷 CIDR/접두사 | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 시스템 관리 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| Vmotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 호스트 전송 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T 에지 전송 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T 에지 업링크1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T 엣지 업링크2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>다음 단계

* [VLAN 및 서브넷 만들기 및 관리](create-vlan-subnet.md)
