---
title: Azure 가용성 영역을 사용하는 SAP 워크로드 구성 | Microsoft Docs
description: Azure 가용성 영역을 사용하는 SAP NetWeaver의 고가용성 아키텍처 및 시나리오
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671629"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure 가용성 영역을 사용하는 SAP 워크로드 구성
또한 Azure 가용성 집합에서 다양 한 SAP 아키텍처 계층을 배포 하는 경우 SAP 워크 로드 배포에도 더 최근에 도입 된 [Azure 가용성 영역](../../../availability-zones/az-overview.md) 를 사용할 수 있습니다. Azure 가용성 영역은 "지역 내의 고유한 물리적 위치"로 정의 됩니다. 각 영역은 독립 된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성 됩니다. Azure 가용성 영역는 일부 지역에서 사용할 수 없습니다. 가용성 영역를 제공 하는 Azure 지역에 대해서는 [azure 지역 지도](https://azure.microsoft.com/global-infrastructure/geographies/)를 확인 하세요. 이 맵은 가용성 영역 제공 하기 위해 제공 되거나 발표 되는 지역을 표시 합니다. 

일반적인 SAP NetWeaver 또는 S/4HANA 아키텍처를 기준으로 다음과 같은 세 가지 계층을 보호 해야 합니다.

- SAP 응용 프로그램 계층-수십 개의 Vm 중 하나일 수 있습니다. Vm이 동일한 호스트 서버에 배포 될 가능성을 최소화 하려고 합니다. 또한 적절 한 기간 내에 네트워크 대기 시간을 유지 하기 위해 DBMS 계층에 허용 되는 이러한 Vm을 원합니다.
- Sap NetWeaver 및 S/4HANA 아키텍처에서 단일 실패 지점을 나타내는 SAP ASCS/SCS 계층입니다. 일반적으로 장애 조치 프레임 워크를 사용 하 여 처리할 두 Vm을 확인 합니다. 따라서 이러한 Vm은 서로 다른 인프라 장애 및 업데이트 도메인에 할당 되어야 합니다.
- 단일 실패 지점도 나타내는 SAP DBMS 계층. 일반적인 경우는 장애 조치 프레임 워크에서 적용 되는 두 개의 Vm으로 구성 됩니다. 따라서 이러한 Vm은 서로 다른 인프라 장애 및 업데이트 도메인에 할당 되어야 합니다. 두 개 이상의 Vm을 사용할 수 있는 스케일 아웃 배포 SAP HANA 예외는

가용성 집합 또는 가용성 영역을 통해 중요 한 Vm을 배포 하는 경우의 주요 차이점은 다음과 같습니다.

- 가용성 집합을 사용 하 여 배포 하는 것은 단일 영역 또는 데이터 센터 (특정 지역에 적용 됨)의 집합 내에서 Vm을 정렬 합니다. 따라서 가용성 집합을 통한 배포는 전체 영역 dataceter에 영향을 주는 전원, 냉각 또는 네트워킹 문제로 보호 되지 않습니다. 플러스 쪽에서 Vm은 해당 영역 또는 데이터 센터 내의 업데이트 및 장애 도메인에 정렬 됩니다. 특히 가용성 집합 당 두 개의 Vm을 보호 하는 SAP ASCS 또는 DBMS 계층의 경우 장애 도메인 및 업데이트 도메인을 사용 하 여 두 Vm이 모두 동일한 호스트 하드웨어에서 종료 되는 것을 방지 합니다. 
- Azure 가용성 영역을 통해 Vm을 배포 하 고 다른 영역 (지금까지 최대 3 개)을 선택 하는 것은 여러 물리적 위치에 Vm을 배포 하 고 전체 영역 dataceter에 영향을 주는 전원, 냉각 또는 네트워킹 문제의 추가 보호를 추가 하는 것입니다. 그러나 동일한 VM 제품군의 VM을 둘 이상 동일한 가용성 영역에 배포 하는 경우 동일한 호스트에서 종료 되는 vm 으로부터 보호 되지 않습니다. 따라서 가용성 영역을 통해 배포 하는 것은 일반적으로 두 개의 Vm을 확인 하는 SAP ASCS 및 DBMS 계층에 적합 합니다. 두 개 이상의 Vm이 될 수 있는 SAP 응용 프로그램 계층의 경우 다른 배포 모델로 대체 해야 할 수 있습니다 (뒷부분 참조).

Azure 가용성 영역에 대 한 배포에 대 한 동기는 중요 한 단일 VM의 실패를 포함 하거나, 중요 한 한 개 이상의 소프트웨어 패치 가동 중지 시간을 줄이는 기능, 하나 이상의 Azure 데이터 센터의 가용성에 영향을 줄 수 있는 대규모 인프라 문제를 방지 하는 것입니다. 

## <a name="considerations-for-deploying-across-availability-zones"></a>가용성 영역에 걸쳐 배포할 때의 고려 사항


가용성 영역을 사용하는 경우 다음을 고려합니다.

- Azure 지역 내의 다양한 가용성 영역 간 거리에 대한 보장은 없습니다.
- 가용성 영역은 이상적인 DR 솔루션이 아닙니다. 자연 재해는 전력 인프라의 과도한 손상을 비롯하여 세계 지역에 광범위한 손상을 가져올 수 있습니다. 다양한 영역 간 거리가 적절한 DR 솔루션을 구성할만큼 충분히 크지 않을 수 있습니다.
- 가용성 영역 간의 네트워크 대기 시간이 모든 Azure 지역에서 동일하지는 않습니다. 일부 경우에 하나의 영역에서 활성 DBMS VM으로의 네트워크 대기 시간이 허용 가능하기 때문에 다른 영역에서 SAP 애플리케이션 계층을 배포하고 실행할 수 있습니다. 그렇지만, 일부 Azure 지역에서는 다른 영역에 배포된 활성 DBMS VM과 SAP 애플리케이션 인스턴스 간의 대기 시간이 SAP 비즈니스 프로세스에 용인되는 수준이 아닐 수 있습니다. 이러한 경우 배포 아키텍처는 응용 프로그램에 대 한 활성/활성 아키텍처 또는 영역 간 네트워크 대기 시간이 너무 높은 능동/수동 아키텍처와 달라 야 합니다.
- 가용성 영역을 사용할 위치를 결정할 때는 영역 간 네트워크 대기 시간을 고려합니다. 네트워크 대기 시간은 다음과 같이 두 가지 영역에서 중요한 역할을 합니다.
    - 동기 복제를 유지해야 하는 두 DBMS 인스턴스 간의 대기 시간. 네트워크 대기 시간이 길수록 워크로드의 확장성에 영향을 미칠 가능성이 높아집니다.
    - 활성 DBMS 인스턴스가 있는 영역에서 SAP 대화 상자 인스턴스를 실행하는 VM과 다른 영역에 있는 유사한 VM 간의 네트워크 대기 시간 차이. 이 차이가 클수록, DBMS가 있는 영역에서 실행되는지 또는 다른 영역에서 실행되는지에 따라 비즈니스 프로세스 및 일괄 처리 작업의 실행 시간에 미치는 영향이 증가합니다.

가용성 영역 간에 Azure VM을 배포하고 동일한 Azure 지역 내에서 장애 조치(failover) 솔루션을 설정할 경우 다음과 같은 몇 가지 제한 사항이 있습니다.

- Azure 가용성 영역을 배포하는 경우 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용해야 합니다. 
- 물리적 영역에 대한 영역 열거형 매핑은 Azure 구독을 기준으로 고정됩니다. 다른 구독을 사용하여 SAP 시스템을 배포하는 경우 구독마다 적절한 영역을 정의해야 합니다.
- Azure [근접 배치 그룹](../../co-location.md)을 사용 하지 않는 한 azure 가용성 집합을 Azure 가용성 영역 내에 배포할 수 없습니다. 여러 영역에 걸쳐 SAP DBMS 계층과 중앙 서비스를 배포 하는 방법 및 동시에 가용성 집합을 사용 하 여 SAP 응용 프로그램 계층을 배포 하 고 Vm에 대 한 근접 하 게 유지 하는 방법은 [sap 응용 프로그램의 최적의 네트워크 대기 시간에 대 한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)문서에 설명 되어 있습니다. Azure 근접 배치 그룹을 사용 하지 않는 경우 가상 컴퓨터에 대 한 배포 프레임 워크로 하나를 선택 해야 합니다.
- [Azure 기본 Load Balancer](../../../load-balancer/load-balancer-overview.md)를 사용하여 Windows Server 장애 조치(failover) 클러스터링 또는 Linux Pacemaker를 기반으로 장애 조치(failover) 클러스터 솔루션을 만들 수 없습니다. 대신 [Azure 표준 LOAD BALANCER SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용 해야 합니다.



## <a name="the-ideal-availability-zones-combination"></a>이상적인 가용성 영역 조합
여러 영역에 SAP NetWeaver 또는 S/4HANA 시스템을 배포 하려는 경우 배포할 수 있는 두 가지 원칙 아키텍처가 있습니다.

- 활성/활성: ASCS/SCS를 실행 하는 Vm 쌍 및 DBMS 계층을 실행 하는 VM 쌍은 두 영역에 걸쳐 분산 됩니다. SAP 응용 프로그램 계층을 실행 하는 Vm의 수는 동일한 두 영역에서 짝수의 짝수에 배포 됩니다. DBMS 또는 ASCS/SCS VM이 장애 조치 (failover) 되 면 일부 오픈 트랜잭션과 활성 트랜잭션이 롤백될 수 있습니다. 그러나 사용자는 로그인 된 상태로 남아 있습니다. 활성 DBMS VM 및 응용 프로그램 인스턴스가 실행 되는 영역에는 중요 하지 않습니다. 이 아키텍처는 영역 간에 배포 하기 위한 기본 아키텍처입니다.
- 능동/수동: ASCS/SCS를 실행 하는 Vm 쌍과 DBMS 계층을 실행 하는 VM 쌍은 두 영역에 걸쳐 분산 됩니다. SAP 응용 프로그램 계층을 실행 하는 Vm의 수는 가용성 영역 중 하나에 배포 됩니다. 활성 ASCS/SCS 및 DBMS 인스턴스와 동일한 영역에서 응용 프로그램 계층을 실행 합니다. 이 배포 아키텍처는 여러 영역에서 네트워크 대기 시간이 너무 높아서 영역 전체에 분산 된 응용 프로그램 계층을 실행 하는 경우에 사용 합니다. 대신, SAP 응용 프로그램 계층은 활성 ASCS/SCS 및/또는 DBMS 인스턴스와 동일한 영역에서 실행 되어야 합니다. ASCS/SCS 또는 DBMS VM이 보조 영역으로 장애 조치 (failover) 되 면 처리량을 감소 시킬 때 네트워크 대기 시간이 더 길어질 수 있습니다. 이전 처리량 수준으로 다시 전환 하려면 가능한 한 빨리 이전에 장애 조치 (failover) 된 VM을 장애 복구 해야 합니다. 영역 중단이 발생 하면 응용 프로그램 계층을 보조 영역으로 장애 조치 (failover) 해야 합니다. 사용자가 시스템을 완전히 종료할 때 발생 하는 작업입니다. 일부 Azure 지역에서이 아키텍처는 가용성 영역를 사용 하려는 경우에만 사용 가능한 아키텍처입니다. 보조 영역으로 장애 조치 (failover) 하는 ASCS/SCS 또는 DBMS VM의 잠재적인 영향을 수락할 수 없는 경우 가용성 집합 배포를 유지 하는 것이 좋을 수 있습니다.


따라서 가용성 영역를 사용 하는 방법을 결정 하기 전에 다음 사항을 확인 해야 합니다.

- Azure 지역의 세 영역 간 네트워크 대기 시간. 영역 영역 간의 네트워크 대기 시간을 알면 영역 간 네트워크 트래픽에서 네트워크 대기 시간이 가장 적은 영역을 선택할 수 있습니다.
- 선택한 영역 중 하나의 VM 간 대기 시간과 선택한 두 영역 간 네트워크 대기 시간의 차이
- 선택한 두 영역에서 배포 해야 하는 VM 유형을 사용할 수 있는 여부를 결정합니다. 일부 VM, 특히 M 시리즈 VM을 사용하는 경우 일부 SKU를 세 영역 중 두 영역에서만 사용할 수 있는 경우가 발생할 수 있습니다.

## <a name="network-latency-between-and-within-zones"></a>영역 간 및 영역 내 네트워크 대기 시간
다른 영역 간 대기 시간을 확인하려면 다음을 수행해야 합니다.

- DBMS 인스턴스에 사용하려는 VM SKU를 세 가지 영역 모두에 배포합니다. 이러한 측정을 수행할 때 [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 사용하도록 설정해야 합니다.
- 최소 네트워크 대기 시간을 나타내는 두 영역을 찾으면 세 가용성 영역에 걸쳐 애플리케이션 계층 VM으로 사용하려는 VM SKU의 다른 3개 VM을 배포합니다. 선택한 두 개의 ‘DBMS’ 영역에서 두 ‘DBMS VM’의 네트워크 대기 시간을 측정합니다. 
- **`niping`** 측정 도구로 사용 합니다. SAP의 이 도구는 SAP support note [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에서 설명합니다. 대기 시간 측정에 대한 명령을 중심으로 참조하세요. **ping** 은 Azure Accelerated Networking 코드 경로를 통해 작동하지 않으므로 사용하지 않는 것이 좋습니다.

이러한 테스트는 수동으로 수행할 필요가 없습니다. 설명 된 대기 시간 테스트를 자동화 하는 PowerShell 절차 [가용성 영역 대기 시간 테스트](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) 를 찾을 수 있습니다. 

가용성 영역의 VM SKU 측정값 및 가용성에 따라, 다음과 같은 사항을 결정해야 합니다.

- DBMS 계층의 이상적인 영역을 정의합니다.
- 영역 내부 및 영역 간 네트워크 대기 시간 차이에 따라, 1개, 2개 또는 3개의 모든 영역에서 활성 SAP 애플리케이션 계층을 배포할 것인지를 결정합니다.
- 애플리케이션 관점에서 액티브/패시브 구성을 배포할지 또는 액티브/액티브 구성을 배포할지를 결정합니다. (이러한 구성은 이 문서의 뒷부분에서 설명합니다.)

이러한 결정을 내릴 때는 SAP note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 설명된 대로 SAP의 네트워크 대기 시간 권장 사항을 고려합니다.

> [!IMPORTANT]
> 수행하는 측정 및 의사 결정은 이러한 측정을 수행할 때 사용한 Azure 구독에 대해 유효합니다. 다른 Azure 구독을 사용하는 경우 측정을 반복해야 합니다. 열거된 영역의 매핑이 다른 Azure 구독과 다를 수 있습니다.


> [!IMPORTANT]
> 앞서 설명한 측정이 [가용성 영역](https://azure.microsoft.com/global-infrastructure/geographies/)을 지원하는 모든 Azure 지역에서 다른 결과를 제공할 수 있습니다. 네트워크 대기 시간에 대한 요구 사항은 같더라도, 영역 간 네트워크 대기 시간이 다를 수 있으므로 Azure 지역마다 다른 배포 전략을 채택해야 할 수 있습니다. 일부 Azure 지역에서는 3개의 다른 영역 간 네트워크 대기 시간이 크게 다를 수 있습니다. 반면에 다른 지역에서는 3개의 다른 영역 간 네트워크 대기 시간이 좀 더 균일할 수 있습니다. 항상 1~2밀리초 사이의 네트워크 대기 시간이 나타난다는 주장은 맞지 않습니다. Azure 지역의 가용성 영역 간 네트워크 대기 시간은 일반화할 수 없습니다.

## <a name="activeactive-deployment"></a>액티브/액티브 배포
두 개 또는 세 개의 영역에 활성 SAP 응용 프로그램 서버를 배포 하기 때문에이 배포 아키텍처는 능동/능동 이라고 합니다. 큐에 넣기 복제를 사용하는 SAP Central Services 인스턴스는 두 영역 간에 배포됩니다. SAP Central Services와 동일한 영역 간에 배포되는 DBMS 계층의 경우도 마찬가지입니다. 이러한 구성을 고려하려면 지역에서 사용자의 워크로드와 동기 DBMS 복제에 적합한 영역 간 네트워크 대기 시간을 제공하는 2개의 가용성 영역을 찾아야 합니다. 또한 선택한 영역 내의 네트워크 대기 시간과 영역 간 네트워크 대기 시간 간의 델타를 너무 크게 유지하지 않으려고 할 것입니다. 

SAP 아키텍처의 특성은 다르게 구성 하지 않는 한, 다른 응용 프로그램 인스턴스에서 사용자 및 batch 작업을 실행할 수 있다는 것입니다. 활성/활성 배포의 부작용은 batch 작업이 활성 DBMS를 사용 하 여 동일한 영역에서 실행 되는지 여부에 관계 없이 모든 SAP 응용 프로그램 인스턴스에서 실행 될 수 있다는 점입니다. 영역 내의 네트워크 대기 시간과 비교 하 여 차이 영역 간의 네트워크 대기 시간 차이가 작은 경우 일괄 처리 작업의 실행 시간 차이가 중요 하지 않을 수 있습니다. 그러나 영역 내에서의 네트워크 대기 시간 차이는 영역 네트워크 트래픽과 비교 하 여, 작업이 DBMS 인스턴스가 활성화 되지 않은 영역에서 실행 된 경우 일괄 처리 작업의 실행 시간에 더 큰 영향을 줄 수 있습니다. 사용자가 런타임 시 허용 되는 차이점을 결정 하는 것은 고객에 게 있습니다. 그리고 영역 간 트래픽에 대 한 네트워크 대기 시간이 지속할.

이와 같은 다양 한 가용성 영역에 배포 된 응용 프로그램 계층 내에서 런타임 및 처리량의 큰 차이가 없는 활성/활성 배포를 가능 하 게 하는 Azure 지역입니다.

- 서 부 미국 (세 영역 모두)
- 동부 미국 (세 영역 모두)
- 미국 중부 (세 영역 모두)
- 북아메리카 (세 영역 모두)
- 유럽 서부 (세 영역 중 두 개)
- 미국 동부 (세 영역 중 두 개)
- 미국 중 중부 (세 영역 중 두 개)
- 영국 남부 (세 영역 중 두 개)

이 SAP 배포 아키텍처가 영역에 있는 Azure 지역은 권장 되지 않습니다.

- 프랑스 중부 
- 남아프리카 북부
- 캐나다 중부
- 일본 동부

실행 시간에 대해 허용할 항목에 따라 나열 되지 않은 다른 지역도 한정할 수 있습니다.


두 영역에 걸친 액티브/액티브 배포의 단순화된 스키마는 다음과 같을 수 있습니다.

![액티브/액티브 영역 배포](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- [Azure 근접 배치 그룹](../../co-location.md)을 사용 하지 않는 경우에는 가용성 집합을 Azure 가용성 영역에 배포할 수 없기 때문에 모든 vm에 대해 Azure 가용성 영역를 장애 및 업데이트 도메인으로 처리 합니다.
- DBMS 계층과 중앙 서비스에 대해 영역 배포를 결합 하지만 응용 프로그램 계층에 대 한 Azure 가용성 집합을 사용 하려는 경우 [SAP 응용 프로그램의 최적의 네트워크 대기 시간에 대 한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)문서에 설명 된 대로 azure 근접 그룹을 사용 해야 합니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터를 위한 부하 분산 장치는 [표준 SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용해야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 필요하지 않습니다.
- 배포 하는 모든 가상 컴퓨터에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용 해야 합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure Premium Storage, [울트라 SSD 저장소](../../disks-types.md#ultra-disk)또는 anf는 영역 간 저장소 복제 유형을 지원 하지 않습니다. 애플리케이션(DBMS 또는 SAP Central Services)은 중요한 데이터를 복제해야 합니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 다음과 같은 기술이 지원됩니다.
  - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션
  - SUSE Linux에서, [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)에 설명된 대로 빌드한 NFS 공유
    
    현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)에 설명된 대로 Microsoft SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.
- 세 번째 영역은 [SUSE Linux Pacemaker 클러스터](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) 를 빌드하고 Azure 펜스 에이전트 대신 SBD 장치를 사용 하는 경우 SBD 장치를 호스트 하는 데 사용 됩니다. 추가 응용 프로그램 인스턴스의 경우 또는입니다.
- 중요 한 비즈니스 프로세스에 대 한 런타임 일관성을 얻기 위해 SAP batch 서버 그룹, SAP 로그온 그룹 또는 RFC 그룹을 사용 하 여 특정 batch 작업 및 사용자를 활성 DBMS 인스턴스와 영역 내 응용 프로그램 인스턴스로 안내할 수 있습니다. 그러나 영역 장애 조치(failover)의 경우 이러한 그룹을 활성 DB VM이 있는 영역 내 VM에서 실행되는 인스턴스로 수동으로 이동해야 합니다.  
- 각 영역에서 유휴 대화 상자 인스턴스를 배포할 수 있습니다. 

> [!IMPORTANT]
> 이 능동/능동 시나리오에서는 Microsoft에서 04/01/2020에 대 한 추가 요금 청구 요금이 발표 됩니다. 문서 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 확인 합니다. SAP 응용 프로그램 계층과 SAP DBMS 계층 간의 데이터 전송은 매우 많이 사용 됩니다. 따라서 활성/활성 시나리오는 상당한 비용에 기여할 수 있습니다. 정확한 비용을 얻으려면이 문서를 계속 확인 하세요. 


## <a name="activepassive-deployment"></a>액티브/패시브 배포
한 영역 내의 네트워크 대기 시간과 영역 간 네트워크 트래픽의 대기 시간 간에 적합한 델타를 찾을 수 없는 경우 SAP 애플리케이션 계층의 관점에서 액티브/패시브 특성이 있는 아키텍처를 배포할 수 있습니다. 완전한 애플리케이션 계층을 배포하고, 활성 DBMS 및 SAP Central Services 인스턴스를 둘 다 실행하려고 하는 *활성* 영역을 정의합니다. 이러한 구성을 사용할 경우 작업이 활성 DBMS 인스턴스가 있는 영역 내에서 실행되는지 여부에 따라, 비즈니스 트랜잭션 및 일괄 처리 작업의 런타임이 크게 달라지지 않는지 확인해야 합니다.

이 유형의 배포 아키텍처가 여러 영역에 있는 Azure 지역은 다음과 같은 것이 좋습니다.

- 동남 아시아
- 오스트레일리아 동부
- 브라질 남부
- 독일 중서부
- 남아프리카 북부
- 프랑스 중부 
- 캐나다 중부
- 일본 동부


이 아키텍처의 기본 레이아웃은 다음과 같습니다.

![액티브/패시브 영역 배포](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- Azure 가용성 영역에서 가용성 집합을 배포할 수 없습니다. 이를 보완 하기 위해 [SAP 응용 프로그램을 사용 하 여 네트워크 대기 시간을 최적화 하기 위해 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)문서에 설명 된 대로 azure 근접 배치 그룹을 사용할 수 있습니다.
- 이러한 아키텍처를 사용할 때는 상태를 면밀히 모니터링하고, 배포한 애플리케이션 계층과 동일한 영역에 활성 DBMS 및 SAP Central Services 인스턴스를 유지해야 합니다. SAP Central Service 또는 DBMS 인스턴스의 장애 조치(failover)가 수행될 경우 가능한 한 빨리, SAP 애플리케이션 계층이 배포된 영역으로 수동으로 장애 복구(failback)할 수 있습니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터를 위한 부하 분산 장치는 [표준 SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용해야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 필요하지 않습니다.
- 배포 하는 모든 가상 컴퓨터에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용 해야 합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure Premium Storage, [울트라 SSD 저장소](../../disks-types.md#ultra-disk)또는 anf는 영역 간 저장소 복제 유형을 지원 하지 않습니다. 애플리케이션(DBMS 또는 SAP Central Services)은 중요한 데이터를 복제해야 합니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 다음과 같은 기술이 지원됩니다.
    - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션
    - SUSE Linux에서, [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)에 설명된 대로 빌드한 NFS 공유
    
  현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)에 설명된 대로 Microsoft SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.
- 세 번째 영역은 [SUSE Linux Pacemaker 클러스터](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) 를 빌드하고 Azure 펜스 에이전트 대신 SBD 장치를 사용 하는 경우 SBD 장치를 호스트 하는 데 사용 됩니다. 추가 응용 프로그램 인스턴스의 경우 또는입니다.
- 영역 오류가 발생할 경우 응용 프로그램 리소스를 시작할 수 있도록 수동 영역 (DBMS 관점에서)에 유휴 Vm을 배포 해야 합니다.
    - 현재는 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)를 사용하여 영역 간 유휴 VM에 활성 VM을 복제할 수 없습니다. 
- 영역 중단이 발생 한 경우 두 번째 영역에서 SAP 응용 프로그램 계층을 자동으로 시작할 수 있도록 자동화에 투자 해야 합니다.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>높은 가용성 및 재해 복구 구성
Microsoft는 Azure 지역의 다른 Azure 가용성 영역을 호스트하는 시설 간의 지리적 거리에 대한 정보를 공유하지 않습니다. 아직도 일부 고객은 제로의 RPO(복구 지점 목표)를 약속하는 조합된 HA 및 DR 구성을 위해 영역을 사용하고 있습니다. RPO 0은 재해 복구의 경우에도 커밋된 데이터베이스 트랜잭션을 모두 손실 해서는 안 됨을 의미 합니다. 

> [!NOTE]
> 이와 같은 구성은 특정 상황에서만 사용하는 것이 좋습니다. 예를 들어, 보안 또는 규정 준수 때문에 Azure 지역 외부로 데이터를 이동할 수 없는 경우에 이 구성을 사용할 수 있습니다. 

다음은 이러한 구성의 작동 방식 예제입니다.

![영역의 결합된 고가용성 DR](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- 가용성 영역을 호스트하는 시설이 서로 상당히 멀리 떨어져 있거나 특정 Azure 영역을 벗어날 수 없다고 가정합니다. Azure 가용성 영역에서 가용성 집합을 배포할 수 없습니다. 이를 보완 하기 위해 [SAP 응용 프로그램을 사용 하 여 네트워크 대기 시간을 최적화 하기 위해 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)문서에 설명 된 대로 azure 근접 배치 그룹을 사용할 수 있습니다.
- 이러한 아키텍처를 사용할 때는 상태를 면밀히 모니터링하고, 배포한 애플리케이션 계층과 동일한 영역에 활성 DBMS 및 SAP Central Services 인스턴스를 유지해야 합니다. SAP Central Service 또는 DBMS 인스턴스의 장애 조치(failover)가 수행될 경우 가능한 한 빨리, SAP 애플리케이션 계층이 배포된 영역으로 수동으로 장애 복구(failback)할 수 있습니다.
- 프로덕션 애플리케이션 인스턴스를 활성 QA 애플리케이션 인스턴스를 실행하는 VM에 미리 설치해야 합니다.
- 영역 실패 사례에서 QA 응용 프로그램 인스턴스를 종료 하 고 대신 프로덕션 인스턴스를 시작 합니다. 이 작업을 수행 하려면 응용 프로그램 인스턴스에 대 한 가상 이름을 사용 해야 합니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터를 위한 부하 분산 장치는 [표준 SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용해야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 필요하지 않습니다.
- 배포 하는 모든 가상 컴퓨터에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용 해야 합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure Premium Storage 또는 [울트라 SSD 스토리지](../../disks-types.md#ultra-disk)는 영역 간에 어떤 유형의 스토리지 복제도 지원하지 않습니다. 애플리케이션(DBMS 또는 SAP Central Services)은 중요한 데이터를 복제해야 합니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 다음과 같은 기술이 지원됩니다.
    - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션
    - SUSE Linux에서, [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)에 설명된 대로 빌드한 NFS 공유

  현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)에 설명된 대로 Microsoft SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.
- 세 번째 영역은 [SUSE Linux Pacemaker 클러스터](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) 를 빌드하고 Azure 펜스 에이전트 대신 SBD 장치를 사용 하는 경우 SBD 장치를 호스트 하는 데 사용 됩니다. 





## <a name="next-steps"></a>다음 단계
Azure 가용성 영역에 걸쳐 배포하기 위한 다음 단계는 다음과 같습니다.

- [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)
- [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)