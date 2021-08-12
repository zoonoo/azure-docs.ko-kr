---
title: Azure VMware Solution by CloudSimple의 VLAN 및 서브넷
description: Cloudsimple 프라이빗 클라우드의 VLAN 및 서브넷과 CloudSimple 서비스가 배포된 위치에서 CloudSimple이 제공하는 네트워크에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3235b394f739e8af846f30dfe51705d0926c843
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140669"
---
# <a name="vlans-and-subnets-overview"></a>VLAN 및 서브넷 개요

CloudSimple은 CloudSimple 서비스가 배포되는 지역당 하나의 네트워크를 제공합니다.  네트워크는 기본적으로 라우팅을 사용할 수 있는 단일 TCP 계층 3 주소 공간입니다.  이 지역에서 만든 모든 프라이빗 클라우드 및 서브넷은 추가 구성 없이 서로 통신할 수 있습니다.  VLAN을 사용하여 vCenter에서 분산 포트 그룹을 만들 수 있습니다.

![CloudSimple 네트워크 토폴로지](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

각 프라이빗 클라우드에 대한 VLAN(계층 2 네트워크)이 생성됩니다.  계층 2 트래픽은 프라이빗 클라우드 경계 내에 유지되므로 프라이빗 클라우드 내의 로컬 트래픽을 격리할 수 있습니다.  프라이빗 클라우드에 생성된 VLAN을 사용하면 해당 프라이빗 클라우드에서만 분산 포트 그룹을 만들 수 있습니다.  프라이빗 클라우드에 생성된 VLAN은 프라이빗 클라우드의 호스트에 연결된 모든 스위치에서 자동으로 구성됩니다.

## <a name="subnets"></a>서브넷

VLAN을 만들 때 서브넷의 주소 공간을 정의하여 서브넷을 만들 수 있습니다. 주소 공간의 IP 주소 중 하나가 서브넷 게이트웨이로 할당됩니다. 고객과 지역당 하나의 프라이빗 계층 3 주소 공간이 할당됩니다. 네트워크 지역에서 온-프레미스 네트워크 또는 Azure 가상 네트워크를 사용하여 겹치지 않는 RFC 1918 주소 공간을 구성할 수 있습니다.

모든 서브넷은 기본적으로 서로 통신할 수 있으므로 프라이빗 클라우드 간 라우팅을 위한 구성 오버헤드를 줄일 수 있습니다. 동일한 지역의 PC 간 동-서 데이터는 동일한 계층 3 네트워크에 유지되며 지역 내 로컬 네트워크 인프라를 통해 전송됩니다. 한 지역의 프라이빗 클라우드 간 통신에는 송신이 필요하지 않습니다. 이 방법을 사용하면 각 프라이빗 클라우드에 서로 다른 워크로드를 배포할 때 발생하는 WAN/송신 성능 저하가 제거됩니다.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 서브넷 CIDR 범위

프라이빗 클라우드는 vCenter Server에서 관리하는 격리된 VMware 스택(ESXi 호스트, vCenter, vSAN, NSX) 환경으로 생성됩니다.  관리 구성 요소는 vSphere/vSAN 서브넷 CIDR에 대해 선택한 네트워크에 배포됩니다.  네트워크 CIDR 범위는 배포 중에 여러 서브넷으로 나뉩니다.

* 최소 vSphere/vSAN 서브넷 CIDR 범위 접두사: **/24**
* 최대 vSphere/vSAN 서브넷 CIDR 범위 접두사: **/21**

> [!CAUTION]
> vSphere/vSAN CIDR 범위의 IP 주소는 프라이빗 클라우드 인프라에서 사용하도록 예약되어 있습니다.  가상 머신에서 이 범위의 IP 주소를 사용하면 안 됩니다.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 서브넷 CIDR 범위 제한

vSphere/vSAN 서브넷 CIDR 범위 크기를 선택하면 프라이빗 클라우드의 크기에 영향을 줍니다.  다음 표에는 vSphere/vSAN 서브넷 CIDR의 크기에 따라 사용할 수 있는 최대 노드 수가 나와 있습니다.

| 지정한 vSphere/vSAN 서브넷 CIDR 접두사 길이 | 최대 노드 수 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>프라이빗 클라우드에 생성되는 관리 서브넷

프라이빗 클라우드를 만들 때 생성되는 관리 서브넷은 다음과 같습니다.

* **시스템 관리**. ESXi 호스트 관리 네트워크, DNS 서버, vCenter Server의 VLAN 및 서브넷
* **VMotion**. ESXi 호스트 vMotion 네트워크의 VLAN 및 서브넷
* **VSAN**. ESXi 호스트 vSAN 네트워크의 VLAN 및 서브넷
* **NsxtEdgeUplink1**. 외부 네트워크에 대한 VLAN 업링크의 VLAN 및 서브넷
* **NsxtEdgeUplink2**. 외부 네트워크에 대한 VLAN 업링크의 VLAN 및 서브넷
* **NsxtEdgeTransport**. NSX-T에서 계층 2 네트워크의 도달률을 제어하는 전송 영역의 VLAN 및 서브넷
* **NsxtHostTransport**. 호스트 전송 영역의 VLAN 및 서브넷

### <a name="management-network-cidr-range-breakdown"></a>관리 네트워크 CIDR 범위 분석

지정한 vSphere/vSAN 서브넷 CIDR 범위는 여러 서브넷으로 나뉩니다.  다음 표에는 허용되는 접두사의 분석 예제를 보여 줍니다.  이 예제에서는 CIDR 범위로 192.168.0.0을 사용합니다.

예:

| 지정한 vSphere/vSAN 서브넷 CIDR/접두사 | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 시스템 관리 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 호스트 전송 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T 에지 전송 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T 에지 업링크1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T 에지 업링크2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>다음 단계

* [VLAN 및 서브넷 만들기 및 관리](create-vlan-subnet.md)
