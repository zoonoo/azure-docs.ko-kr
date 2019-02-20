---
title: Azure 가용성 영역을 사용하는 SAP 워크로드 구성 | Microsoft Docs
description: Azure 가용성 영역을 사용하는 SAP NetWeaver의 고가용성 아키텍처 및 시나리오
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e319fb5bc1e0ed3354a09b685405656c7f3131bc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858808"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure 가용성 영역을 사용하는 SAP 워크로드 구성

Azure가 제공하는 고가용성 기능 중 하나는 [Azure 가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)입니다. 가용성 영역을 사용하면 Azure에서 SAP 워크로드의 전반적인 가용성이 향상됩니다. 가용성 영역은 여러 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 제공됩니다. 앞으로 더 많은 지역이 이러한 기능을 지원할 예정입니다. 

이 그림에 표시된 대로 SAP 고가용성의 기본 아키텍처를 설명할 수 있습니다.

![표준 HA 구성](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP 애플리케이션 계층은 하나의 Azure [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)에서 배포됩니다. SAP Central Services의 고가용성을 위해 별도의 가용성 집합에 두 개의 VM을 배포합니다. 인프라 또는 소프트웨어 문제 발생 시, 자동 장애 조치(Failover)를 제공하는 고가용성 프레임워크로서 Windows 장애 조치(failover) 클러스터 서비스 또는 Pacemaker(Linux)를 사용합니다. 이러한 배포를 자세히 설명하는 문서는 아래에 나와 있습니다.

- [클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [파일 공유를 사용하여 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스 클러스터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

비슷한 아키텍처가 SAP NetWeaver, S/4HANA 또는 Hybris 시스템의 DBMS 계층에 적용됩니다. 인프라 또는 소프트웨어가 오류로부터 보호하기 위해 DBMS 계층을 장애 조치(failover) 클러스터 솔루션을 사용하여 액티브/패시브 모드로 배포합니다. 장애 조치(Failover) 클러스터 솔루션은 DBMS 특정 장애 조치(Failover) 프레임워크, Windows 장애 조치(Failover) 클러스터 서비스 또는 Pacemaker일 수 있습니다. 

Azure 가용성 영역을 사용하여 동일한 아키텍처를 배포하려면 위에 대략적으로 설명된 아키텍처를 변경해야 합니다. 이러한 변경 내용은 이 문서에서 설명합니다.

## <a name="considerations-deploying-across-availability-zones"></a>가용성 영역에 걸쳐 배포할 때의 고려 사항

가용성 영역을 사용할 경우 다음과 같은 몇 가지 사항을 고려해야 합니다.

- Azure 가용성 영역은 한 Azure 지역 내의 다른 영역 간에 특정 거리를 보장하지 않습니다.
- Azure 가용성 영역은 이상적인 DR 솔루션이 아닙니다. 자연 재해는 전력 인프라의 과도한 손상을 비롯하여 세계 여러 지역에 광범위한 손상을 가져올 수 있습니다. 다른 영역 간 거리가 적절한 DR 솔루션이 될 만큼 충분히 크지 않을 수도 있습니다.
- 가용성 영역 간 네트워크 대기 시간은 Azure 지역마다 다릅니다. 하나의 영역에서 활성 DBMS VM으로의 네트워크 대기 시간은 비즈니스 프로세스 영향에서 허용 가능하므로 다른 영역에서 SAP 애플리케이션 계층을 배포하고 실행할 수 있는 경우가 있습니다. 그렇지만, 일부 Azure 지역에서는 다른 영역에 배포된 활성 DBMS VM과 SAP 애플리케이션 인스턴스 간의 대기 시간이 너무 방해가 되며 SAP 비즈니스 프로세스에 대해 허용되지 않을 수 있습니다. 결과적으로 배포 아키텍처는 영역 간 대기 시간이 너무 높은 경우 애플리케이션에 대한 활성/활성 아키텍처 또는 능동/수동 아키텍처와 달라야 합니다.
- 다른 영역 간에 측정한 네트워크 대기 시간에 따라 Azure 가용성 영역을 사용할 수 있는 구성을 결정합니다. 네트워크 대기 시간은 다음과 같이 두 가지 영역에서 중요한 역할을 합니다.
    - 동기 복제를 설정해야 하는 두 DBMS 인스턴스 간의 대기 시간. 네트워크 대기 시간이 길수록 워크로드의 확장성에 영향을 미칠 가능성이 높아집니다.
    - 활성 DBMS 인스턴스가 있는 영역에서 SAP 대화 상자 인스턴스를 실행하는 VM과 다른 영역에 있는 유사한 VM 간의 네트워크 대기 시간 차이. 이 차이가 클수록, DBMS가 있는 영역에서 실행되는지 또는 다른 영역에서 실행되는지에 따라 비즈니스 프로세스 및 일괄 처리 작업의 런타임에 미치는 영향이 증가합니다.


가용성 영역 간에 Azure 지역을 배포하고 동일한 Azure 지역 내에서 장애 조치(failover) 솔루션을 설정할 경우 몇 가지 제한 사항이 있습니다. 이러한 제한 사항은 다음 목록에서 설명합니다.

- [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 사용은 Azure 가용성 영역으로 배포할 때 반드시 필요합니다. 
- 물리적 영역에 대한 영역 열거형 매핑은 Azure 구독을 기준으로 고정됩니다. 다른 구독을 사용하여 SAP 시스템을 배포하는 경우 구독마다 적절한 영역을 정의해야 합니다.
- 가용성 영역 내에서 Azure 가용성 집합을 배포할 수 없습니다. 가상 머신용 배포 프레임으로 Azure 가용성 영역 또는 Azure 가용성 집합을 선택합니다.
- [Azure 기본 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)를 사용하여 Windows 장애 조치(failover) 클러스터 서비스 또는 Linux Pacemaker를 기반으로 장애 조치(failover) 클러스터솔루션을 만들 수 없습니다. 대신 [Azure 표준 Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)를 사용해야 합니다.



## <a name="ideal-availability-zones-combination"></a>이상적인 가용성 영역 조합
가용성 영역을 활용하는 방법을 결정하기 전에 다음과 같은 정보를 제공하는 조사를 수행해야 합니다.

- Azure 지역의 서로 다른 세 가지 영역 간 네트워크 대기 시간. 따라서 영역 간 네트워크 트래픽에서 네트워크 대기 시간이 최소인 영역을 선택할 수 있습니다.
- 선택한 영역 중 하나의 VM 간 대기 시간과 선택한 두 영역 간 네트워크 대기 시간의 차이
- 배포해야 하는 VM 유형을 선택한 두 영역에서 사용할 수 있는지 여부 특히 M 시리즈 가상 머신의 경우, 세 영역 중 두 곳에서만 다른 M 시리즈 VM SKU를 사용할 수 있는 상황이 발생할 수 있습니다.

### <a name="network-latency-between-zones-and-within-zone"></a>영역 간 및 영역 내 네트워크 대기 시간
다른 영역 간 대기 시간을 확인하려면 다음을 수행해야 합니다.

- DBMS 인스턴스에 사용하려는 VM SKU를 세 가지 영역 모두에 배포합니다. 이러한 측정을 수행할 때 [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 사용하도록 설정해야 합니다.
- 최소 네트워크 대기 시간을 나타내는 두 영역을 찾으면 세 가용성 영역에 걸쳐 애플리케이션 계층 VM으로 사용하려는 VM SKU의 다른 3개 VM을 배포합니다. 선택한 두 개의 다른 ‘DBMS’ 영역에서 두 ‘DBMS VM’의 네트워크 대기 시간을 측정합니다. 
- 측정 도구로 **niping**을 사용합니다. SAP support note [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 설명된 대로 작동하는 SAP의 도구입니다. 대기 시간 측정에 대한 SAP 명령을 참조하세요. **ping**은 Azure 가속 네트워크 코드 경로를 통해 작동하지 않으므로 권장되지 않습니다****.

가용성 영역의 VM SKU 측정값 및 가용성에 따라, 다음과 같은 사항을 결정해야 합니다.

- DBMS 계층의 이상적인 영역을 정의합니다.
- 영역 내부 또는 영역 간의 네트워크 대기 시간 차이에 따라, 1개, 2개 또는 3개의 모든 영역에서 활성 SAP 애플리케이션 계층을 배포할 것인지를 물어봅니다.
- 애플리케이션 관점에서 액티브/패시브 구성을 배포할지 또는 액티브/액티브 구성을 배포할지를 물어봅니다(뒷부분 참조).

SAP note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 설명된 대로 SAP의 네트워크 대기 시간 권장 사항을 기억하면서 이러한 결정을 내립니다.

### <a name="be-aware-of"></a>중요한 사항

> [!IMPORTANT]
> 수행하는 측정 및 의사 결정은 이러한 측정을 수행할 때 사용한 Azure 구독에 대해 유효합니다. 열거형 영역의 매핑이 Azure 구독마다 달라질 수 있으므로 다른 Azure 구독을 사용하는 경우 측정을 반복해서 수행해야 합니다.


> [!IMPORTANT]
> 위에 따라 수행한 측정이 [가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)을 지원하는 모든 Azure 지역에서 다른 결과를 나타낼 수 있습니다. 네트워크 대기 시간에 대한 요구 사항은 같더라도, 영역 간 네트워크 대기 시간이 다를 수 있으므로 Azure 지역마다 다른 배포 전략을 채택해야 할 수 있습니다. 일부 Azure 지역에서는 3개의 다른 영역 간 네트워크 대기 시간이 크게 다를 수 있습니다. 반면에 다른 지역에서는 3개의 다른 영역 간 네트워크 대기 시간이 좀 더 균일할 수 있습니다. 1밀리초~2밀리초의 네트워크 대기 시간이 **항상** 존재한다는 주장은 **잘못된** 것입니다. Azure 지역의 가용성 영역 간 네트워크 대기 시간은 일반화할 수 없습니다.


## <a name="activeactive-deployment"></a>액티브/액티브 배포
이 배포 아키텍처는 2개 또는 3개 영역에 걸쳐 액티브 SAP 대화 상자 인스턴스를 배포하므로 액티브/액티브라고 지칭합니다. 큐에 넣기 복제를 사용하는 SAP Central Services는 두 영역 간에 배포됩니다. SAP Central Services와 동일한 영역 간에 배포되는 DBMS 계층의 경우도 마찬가지입니다.

이러한 구성을 고려하려면 지역에서 사용자의 워크로드와 동기 DBMS 복제에 적합한 영역 간 네트워크 대기 시간을 제공하는 2개의 가용성 영역을 찾아야 합니다. 또한 선택한 영역 내의 네트워크 대기 시간과 영역 간 네트워크 대기 시간 간의 델타를 너무 크게 유지하지 않으려고 합니다. 후자의 이유라면, 작업이 DBMS 서버가 있는 영역 내에서 실행되는지 또는 영역에 걸쳐 수행되는지에 따라, 비즈니스 프로세스 또는 일괄 처리 작업의 런타임 변화가 크지 않은 것을 원할 것입니다. 약간의 차이 정도는 허용될 수 있습니다.

두 영역에 걸친 액티브/액티브 배포의 단순화된 스키마는 다음과 같을 수 있습니다.

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- 가용성 집합을 Azure 가용성 영역에 배포할 수 없으므로 Azure 가용성 영역을 모든 VM의 장애 및 업데이트 도메인으로 취급합니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터에 사용하는 Azure Load Balancer는 [표준 SKU Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)여야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 **필요하지 않습니다**.
- 배포하는 모든 가상 머신에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure 프리미엄 스토리지 또는 [울트라 SSD 스토리지](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)는 영역 간에 어떤 유형의 스토리지 복제도 지원하지 않습니다. 따라서 중요한 데이터를 복제하는 것은 애플리케이션(DBMS 또는 SAP Central Services)의 책임입니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 현재 다음과 같은 기술이 지원됩니다.
    - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션은 영역 간에 지원됩니다.
    - SUSE Linux에서는 [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)에 설명된 대로 빌드한 NFS 공유가 지원됩니다.
    - 현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)**에 설명된 대로 Windows SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.**
- 세 번째 영역은 [SUSE Linux pacemaker 클러스터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) 또는 추가 애플리케이션 인스턴스를 빌드하는 경우 SBD 디바이스의 호스트가 됩니다.
- 중요한 비즈니스 프로세스에 대해 런타임 일관성을 달성하기 위해 특정 일괄 처리 작업 및 사용자를 SAP 일괄 처리 서버 그룹, 로그온 그룹 또는 RFC 그룹을 사용하여 활성 DBMS 인스턴스가 있는 영역의 애플리케이션 인스턴스에 보내려고 할 수 있습니다. 그러나 영역 장애 조치(failover)의 경우 이러한 그룹을 활성 DB VM이 있는 영역 내 VM에서 실행되는 인스턴스로 수동으로 이동해야 합니다.  
- 애플리케이션 인스턴스의 부분에 사용되는 영역의 서비스가 중단될 경우, 즉시 이전 리소스 용량으로 회복될 수 있도록 각 서비스 영역에 일부 유휴 대화 상자 인스턴스를 배포할지 여부를 결정합니다.


## <a name="activepassive-deployment"></a>액티브/패시브 배포
한 영역 내의 네트워크 대기 시간과 영역 간 네트워크 트래픽 간에 적합한 델타를 찾을 수 없는 경우 배포할 수 있는 아키텍처는 SAP 애플리케이션 계층의 관점에서 액티브/패시브 특성을 갖습니다. 완전한 애플리케이션 계층을 배포하고, 활성 DBMS 및 SAP Central Services 인스턴스를 둘 다 실행하려고 하는 '활성' 영역을 정의합니다. 이러한 구성을 사용할 경우 작업이 활성 DBMS 인스턴스가 있는 영역 내에서 실행되는지 여부에 좌우되는 비즈니스 트랜잭션 및 일괄 처리 작업에서 런타임 차이가 크게 변동되지 않음을 확인해야 합니다.

이러한 아키텍처의 기본 레이아웃은 다음과 같습니다.

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- Azure 가용성 영역에서 가용성 집합을 배포할 수 없습니다. 따라서 이러한 경우에는 애플리케이션 계층에 하나의 업데이트 및 장애 도메인을 갖게 됩니다. 이러한 도메인은 하나의 영역에만 배포되기 때문입니다. 이 구성은 사용자가 Azure 가용성 집합에 애플리케이션 계층을 배포할 것을 예상하는 참조 아키텍처에 비해 약간 후퇴한 느낌이 있습니다.
- 이러한 아키텍처를 작동할 때는 면밀히 모니터링하고, 배포한 애플리케이션 계층과 동일한 영역에 활성 DBMS 및 SAP Central Services 인스턴스를 유지해야 합니다. SAP Central Service 또는 DBMS 인스턴스의 장애 조치(failover)가 수행될 경우 가능한 한 빨리, SAP 애플리케이션 계층이 배포된 영역으로 수동으로 장애 복구(failback)할 수 있습니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터에 사용하는 Azure Load Balancer는 [표준 SKU Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)여야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 **필요하지 않습니다**.
- 배포하는 모든 가상 머신에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용해야 합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure 프리미엄 스토리지 또는 [울트라 SSD 스토리지](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)는 영역 간에 어떤 유형의 스토리지 복제도 지원하지 않습니다. 따라서 중요한 데이터를 복제하는 것은 애플리케이션(DBMS 또는 SAP Central Services)의 책임입니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 현재 다음과 같은 기술이 지원됩니다.
    - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션은 영역 간에 지원됩니다.
    - SUSE Linux에서는 [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)에 설명된 대로 빌드한 NFS 공유가 지원됩니다.
    - 현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)**에 설명된 대로 Windows SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.**
- 세 번째 영역은 [SUSE Linux pacemaker 클러스터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) 또는 추가 애플리케이션 인스턴스를 빌드하는 경우 SBD 디바이스의 호스트가 됩니다.
- 영역 오류 발생 시 애플리케이션 리소스를 시작할 수 있도록 유휴 VM을 패시브 영역(DBMS의 관점에서)에 배포합니다.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)를 사용하여 영역 간 유휴 VM에 활성 VM을 복제할 수 없습니다. 현재, Azure Site Recovery는 이러한 기능을 수행할 수 없습니다.
- 영역 오류가 발생할 경우 두 번째 영역에서 SAP 애플리케이션 계층을 자동으로 시작하도록 하는 자동화 기능에 투자하세요.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>높은 가용성 및 재해 복구 구성
Microsoft는 Azure 지역의 다른 Azure 가용성 영역을 호스트하는 시설 간의 지리적 거리에 대한 정보를 공유하지 않습니다. 이러한 사실에도 불구하고, 일부 고객은 제로의 RPO(**복**구 **지**점 **목**표)를 약속하는 조합된 HA 및 DR 구성을 위해 영역을 활용합니다. 즉, 재해 복구 경우에도 커밋된 데이터베이스 트랜잭션을 손실하지 않게 됩니다. 

> [!NOTE]
> 다음과 같은 구성은 특정 상황에서만 권장됩니다. 예를 들어, 보안 및 규정 준수 이유로 인해 데이터가 Azure 지역을 벗어날 수 없는 경우. 

이러한 구성을 보여 주는 예제는 다음 그래픽에 나와 있습니다.

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- 가용성 영역을 호스트하는 시설이 서로 상당히 멀리 떨어져 있다고 가정합니다. 또는 특정 Azure 지역에 강제로 머물러야 합니다. Azure 가용성 영역에서 가용성 집합을 배포할 수 없습니다. 따라서 이러한 경우에는 애플리케이션 계층에 대해 하나의 업데이트 및 장애 도메인을 구축합니다. 이러한 도메인은 하나의 영역에만 배포되기 때문입니다. 이러한 방식은 Azure 가용성 집합에 애플리케이션 계층을 배포할 것으로 예상되는 참조 아키텍처에 비해 단점이 있습니다.
- 이러한 아키텍처를 작동할 때는 면밀히 모니터링하고, 배포한 애플리케이션 계층과 동일한 영역에 활성 DBMS 및 SAP Central Services 인스턴스를 유지해야 합니다. SAP Central Service 또는 DBMS 인스턴스의 장애 조치(failover)가 수행될 경우 가능한 한 빨리, SAP 애플리케이션 계층이 배포된 영역으로 수동으로 장애 복구(failback)할 수 있습니다.
- 프로덕션 애플리케이션 인스턴스가 활성 QA 애플리케이션 인스턴스를 실행하는 VM에 미리 설치되어 있습니다.
- 영역 오류 발생 시 QA 애플리케이션 인스턴스를 종료하고, 대신 프로덕션 인스턴스를 시작합니다. 이렇게 하려면 반드시 애플리케이션 인스턴스의 가상 이름을 사용해야 합니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터에 사용하는 Azure Load Balancer는 [표준 SKU Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)여야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 **필요하지 않습니다**.
- 배포하는 모든 가상 머신에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용해야 합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure 프리미엄 스토리지 또는 [울트라 SSD 스토리지](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)는 영역 간에 어떤 유형의 스토리지 복제도 지원하지 않습니다. 따라서 중요한 데이터를 복제하는 것은 애플리케이션(DBMS 또는 SAP Central Services)의 책임입니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 현재 다음과 같은 기술이 지원됩니다.
    - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션은 영역 간에 지원됩니다.
    - SUSE Linux에서는 [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)에 설명된 대로 빌드한 NFS 공유가 지원됩니다.
    - 현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)**에 설명된 대로 Windows SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.**
- 세 번째 영역은 [SUSE Linux pacemaker 클러스터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) 또는 추가 애플리케이션 인스턴스를 빌드하는 경우 SBD 디바이스의 호스트가 됩니다.





## <a name="next-steps"></a>다음 단계
Azure 가용성 영역에 걸쳐 배포하려면 다음 단계를 참조하세요.

- [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






