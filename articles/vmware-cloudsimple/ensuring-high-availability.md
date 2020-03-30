---
title: Azure에서 VMware에서 실행할 때 응용 프로그램 고가용성 보장
description: CloudSimple 프라이빗 클라우드에서 실행되는 애플리케이션에 대한 일반적인 응용 프로그램 실패 시나리오를 해결하기 위한 CloudSimple 고가용성 기능에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025353"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Azure에서 VMware에서 실행할 때 응용 프로그램 고가용성 보장

CloudSimple 솔루션은 Azure 환경에서 VMware에서 실행되는 응용 프로그램에 대해 고가용성을 제공합니다. 다음 표에는 실패 시나리오와 관련된 고가용성 기능이 나열되어 있습니다.

| 실패 시나리오 | 응용 프로그램 보호? | 플랫폼 HA 기능 | VMware HA 기능 | Azure HA 기능 |
------------ | ------------- | ------------ | ------------ | ------------- |
| 디스크 오류 | YES | 실패한 노드의 빠른 교체 | [vSAN 기본 저장소 정책 정보](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| 팬 오류 | YES | 중복 팬, 실패한 노드의 빠른 교체 |  |  |
| NIC 실패 | YES | 중복 NIC, 실패한 노드의 빠른 교체
| 호스트 정전 | YES | 중복 전원 공급 장치 |  |  |
| ESXi 호스트 실패 | YES | 실패한 노드의 빠른 교체 | [VMware vSphere 고가용성](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM 오류 | YES | [부하 분산 장치](load-balancers.md)  | [VMware vSphere 고가용성](https://www.vmware.com/products/vsphere/high-availability.html) | 상태 비수기 VM웨어 VM에 대 한 Azure 로드 밸러저 |
| 리프 스위치 포트 오류 | YES | 중복 NIC |  |  |
| 리프 스위치 오류 | YES | 중복 리프 스위치 |  |  |
| 랙 고장 | YES | 배치 그룹 |  |  |
| 온-프레미스 DC에 대한 네트워크 연결 | YES  | 중복 네트워킹 서비스 |  | 중복 ER 회로 |
| Azure에 대한 네트워크 연결 | YES | |  | 중복 ER 회로 |
| 데이터 센터 오류 | YES |  |  | 가용성 영역 |
| 지역 실패 | YES  |  |  | Azure 지역 |

CloudSimple에 의한 Azure VMware 솔루션은 다음과 같은 고가용성 기능을 제공합니다.

## <a name="fast-replacement-of-failed-node"></a>실패한 노드의 빠른 교체

CloudSimple 제어 평면 소프트웨어는 VMware 클러스터의 상태를 지속적으로 모니터링하고 ESXi 노드가 실패하는 시기를 감지합니다. 그런 다음 쉽게 사용할 수 있는 노드 풀에서 영향을 받는 VMware 클러스터에 새 ESXi 호스트를 자동으로 추가하고 실패한 노드를 클러스터에서 제거합니다. 이 기능을 사용하면 VMware 클러스터의 예비 용량이 빠르게 복원되어 vSAN 및 VMware HA에서 제공하는 클러스터의 복원력이 복원됩니다.

## <a name="placement-groups"></a>배치 그룹

프라이빗 클라우드를 만드는 사용자는 선택한 리전 내의 Azure 지역 및 배치 그룹을 선택할 수 있습니다. 배치 그룹은 여러 랙에 분산되어 있지만 동일한 척추 네트워크 세그먼트 내에 분산된 노드 집합입니다. 동일한 배치 그룹 내의 노드는 최대 두 개의 추가 스위치 홉으로 서로 도달할 수 있습니다. 배치 그룹은 항상 단일 Azure 가용성 영역 내에 있으며 여러 랙에 걸쳐 있습니다. CloudSimple 제어 평면은 최상의 노력을 바탕으로 프라이빗 클라우드의 노드를 여러 랙에 배포합니다. 서로 다른 배치 그룹의 노드는 다른 랙에 배치될 수 있습니다.

## <a name="availability-zones"></a>가용성 영역

가용성 영역은 데이터 센터 오류로부터 응용 프로그램과 데이터를 보호하는 고가용성 제품입니다. 가용성 영역은 Azure 지역 내의 특수 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 각 지역에는 하나의 가용성 영역이 있습니다. 자세한 내용은 [Azure의 가용성 영역이란 무엇입니까?](../availability-zones/az-overview.md)

## <a name="redundant-azure-expressroute-circuits"></a>중복 Azure 익스프레스루트 회로

ExpressRoute를 사용하여 Azure vNet에 대한 데이터 센터 연결에는 고가용성 네트워크 연결 링크를 제공하는 중복 회로가 있습니다.

## <a name="redundant-networking-services"></a>중복 네트워킹 서비스

프라이빗 클라우드를 위한 모든 CloudSimple 네트워킹 서비스(VLAN, 방화벽, 공용 IP 주소, 인터넷 및 VPN 포함)는 가용성이 높고 서비스 SLA를 지원할 수 있도록 설계되었습니다.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>상태 비수 기 VM웨어 VM에 대 한 Azure 계층 7 로드 밸러저

사용자는 VMware 환경에서 실행되는 상태 비수기 웹 계층 VM 앞에 Azure 계층 7 로드 밸런서를 배치하여 웹 계층에 대한 고가용성을 달성할 수 있습니다.

## <a name="azure-regions"></a>Azure 지역

Azure 영역은 대기 시간 정의 경계 내에 배포되고 전용 지역 간시간 네트워크를 통해 연결된 데이터 센터 집합입니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions)을 참조하십시오.
