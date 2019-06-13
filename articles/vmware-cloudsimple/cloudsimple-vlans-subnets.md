---
title: Vlan 및 서브넷 CloudSimple-Azure에서 VMware 솔루션
description: Vlan 및 서브넷 CloudSimple 사설 클라우드에서 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d29445054848d798476fed8184b89f9b6c1210f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497568"
---
# <a name="vlans-and-subnets-overview"></a>Vlan 및 서브넷 개요

CloudSimple는 CloudSimple 서비스를 배포할 지역 마다 네트워크를 제공 합니다.  네트워크가 라우팅을 기본적으로 사용할 수 있는 단일 TCP 계층 3 주소 공간입니다.  모든 사설 클라우드 및이 지역에서 생성 하는 서브넷은 추가 구성 없이 서로 통신할 수 있습니다.  Vlan을 사용 하 여 vCenter에서 분산된 포트 그룹을 만들 수 있습니다.

![CloudSimple 네트워크 토폴로지](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

Vlan (계층 2 네트워크)는 사설 클라우드 마다 생성 됩니다.  계층 2 트래픽이 사설 클라우드, 사설 클라우드 내에서 로컬 트래픽을 격리할 수 있도록 경계 내에서 유지 됩니다.  사설 클라우드에서 만든 VLAN 해당 사설 클라우드에서 분산된 포트 그룹을 만드는 데 사용할 수 있습니다.  사설 클라우드에서 만든 VLAN은 사설 클라우드의 호스트에 연결 된 모든 스위치에 자동으로 구성 됩니다.

## <a name="subnets"></a>서브넷

서브넷의 주소 공간을 정의 하 여 VLAN을 만들 때 서브넷을 만들 수 있습니다. 게이트웨이 서브넷 주소 공간에서 IP 주소 할당 됩니다. 단일 개인 3 계층 주소 공간 고객 및 지역별로 당 할당 됩니다. 네트워크 지역에서 온-프레미스 네트워크 또는 Azure virtual network를 사용 하 여 모든 RFC 1918 겹치지 않는 주소 공간을 구성할 수 있습니다.

모든 서브넷 구성을 사설 클라우드 간의 라우팅에 대 한 오버 헤드를 줄이고 기본적으로 서로 통신할 수 있습니다. 동일한 지역에는 Pc에서 동-서 데이터가 동일한 3 계층 네트워크에 유지 되 고 지역 내에서 로컬 네트워크 인프라를 통해 전송 합니다. 없는 송신 지역에서 사설 클라우드 간의 통신에 필요합니다. 이렇게 하면 모든 WAN/송신 성능이 다른 사설 클라우드에서 다양 한 워크 로드를 배포 합니다.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 서브넷 CIDR 범위

사설 클라우드는 격리 된 VMware 스택을 (ESXi 호스트, vCenter, vSAN을 및 NSX)으로 만들어집니다. vCenter 서버에서 관리 하는 환경입니다.  관리 구성 요소에 대해 선택한 네트워크에 배포 됩니다 **vSphere/vSAN 서브넷 CIDR**합니다.  네트워크 CIDR 범위는 배포 하는 동안 서로 다른 서브넷으로 구분 됩니다.

최소 vSphere/vSAN 서브넷 CIDR 범위 접두사: **/24** 최대 vSphere/vSAN 서브넷 CIDR 범위 접두사: **21 /**

> [!CAUTION]
> VSphere/vSAN CIDR 범위에서 IP 주소는 사설 클라우드 인프라에서 사용 하기 위해 예약 되어 있습니다. 모든 가상 머신에서이 범위의 IP 주소를 사용 하지 마세요.


### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 서브넷 CIDR 범위 제한

사설 클라우드의 크기 vSphere/vSAN 서브넷 CIDR 범위 크기 선택에 영향을 줍니다.  아래 표에서 vSphere/vSAN 서브넷 CIDR의 크기에 기준 하 사용할 수 있는 노드의 최대 수를 보여 줍니다.

| 지정 된 vSphere/vSAN 서브넷 CIDR 접두사 길이 | 최대 노드 수 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>관리 서브넷에서 사설 클라우드 생성

관리 서브넷에는 사설 클라우드를 만들 때 생성 됩니다. 

* **시스템 관리** -VLAN 및 서브넷 ESXi 호스트의 관리에 대 한 네트워크 DNS 서버에서 vCenter 서버.
* **VMotion** -VLAN 및 ESXi 호스트의 vMotion 네트워크의 서브넷입니다.
* **VSAN** -VLAN 및 ESXi 호스트의 vSAN 네트워크의 서브넷입니다.
* **NsxtEdgeUplink1** -VLAN 및 서브넷 VLAN 업링크 외부 네트워크에 대 한 합니다.
* **NsxtEdgeUplink2** -VLAN 및 서브넷 VLAN 업링크 외부 네트워크에 대 한 합니다.
* **NsxtEdgeTransport** -전송 영역에 대 한 서브넷 및 VLAN NSX T.에서 계층 2 네트워크의 범위 제어
* **NsxtHostTransport** -VLAN 및 서브넷 호스트에 대 한 영역을 전송 합니다.

### <a name="management-network-cidr-range-breakdown"></a>관리 네트워크 CIDR 범위 분석

지정 된 vSphere/vSAN 서브넷 CIDR 범위를 여러 서브넷으로 구분 됩니다.  아래 테이블에는 허용 된 접두사에 대 한 분석의 예가 나와 있습니다.  이 예제에서는 사용 **192.168.0.0** CIDR 범위로 합니다.

예제:

| VSphere/vSAN 서브넷 CIDR/접두사를 지정합니다. | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 시스템 관리 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 호스트 전송 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T Edge 전송 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>다음 단계

* [Vlan 및 서브넷 만들기 및 관리](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
