---
title: SAP 응용 프로그램에 대한 Azure 근접 배치 그룹 | 마이크로 소프트 문서
description: Azure 근접 배치 그룹으로 SAP 배포 시나리오설명
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277604"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹
SAP NetWeaver 또는 SAP S/4HANA 아키텍처를 기반으로 하는 SAP 응용 프로그램은 SAP 응용 프로그램 계층과 SAP 데이터베이스 계층 간의 네트워크 대기 시간에 민감합니다. 이 민감도는 응용 프로그램 계층에서 실행되는 대부분의 비즈니스 논리의 결과입니다. SAP 응용 프로그램 계층은 비즈니스 논리를 실행하기 때문에 초당 수천 또는 수만 의 속도로 높은 빈도로 데이터베이스 계층에 쿼리를 실행합니다. 대부분의 경우 이러한 쿼리의 특성은 간단합니다. 데이터베이스 계층에서 500마이크로초 이하로 실행할 수 있습니다.

응용 프로그램 계층에서 데이터베이스 계층으로 이러한 쿼리를 보내고 결과 집합을 다시 받는 데 네트워크에서 소요되는 시간은 비즈니스 프로세스를 실행하는 데 걸리는 시간에 큰 영향을 미칩니다. 네트워크 대기 시간에 대한 이러한 민감도는 SAP 배포 프로젝트에서 최적의 네트워크 대기 시간을 달성해야 하는 이유입니다. [SAP 참고 #1100926 - FAQ: 네트워크](https://launchpad.support.sap.com/#/notes/1100926/E) 대기 시간을 분류하는 방법에 대한 지침은 네트워크 성능을 참조하십시오.

많은 Azure 지역에서 데이터 센터 수가 증가했습니다. 이러한 성장은 가용성 영역의 도입에 의해 촉발되었습니다. 동시에, 특히 하이엔드 SAP 시스템의 고객은 M 시리즈 제품군 또는 HANA 대형 인스턴스에서 더 특별한 VM SCO를 사용하고 있습니다. 이러한 Azure 가상 시스템 형식은 특정 Azure 지역의 모든 데이터 센터에서 사용할 수 없습니다. 이러한 두 가지 경향 으로 인해 고객은 최적의 범위에 없는 네트워크 대기 시간을 경험했습니다. 경우에 따라 이 대기 시간으로 인해 SAP 시스템의 성능이 최적이 아닌 경우가 발생합니다.

이러한 문제를 방지하기 위해 Azure는 [근접 배치 그룹을](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)제공합니다. 이 새로운 기능은 이미 다양한 SAP 시스템을 배포하는 데 사용되었습니다. 근접 배치 그룹에 대한 제한사항은 이 단락의 시작 부분에 참조된 문서를 참조하십시오. 이 문서에서는 Azure 근접 배치 그룹을 사용할 수 있거나 사용해야 하는 SAP 시나리오를 다룹니다.

## <a name="what-are-proximity-placement-groups"></a>근접 배치 그룹은 무엇입니까? 
Azure 근접 배치 그룹은 논리적 구문입니다. 하나가 정의되면 Azure 지역 및 Azure 리소스 그룹에 바인딩됩니다. VM이 배포될 때 근접 배치 그룹은 다음을 참조합니다.

- 데이터 센터에 배포된 첫 번째 Azure VM입니다. 첫 번째 가상 머신은 Azure 할당 알고리즘을 기반으로 데이터 센터에 배포되는 "범위 VM"으로 생각할 수 있으며, 결국 특정 가용성 영역에 대한 사용자 정의와 결합됩니다.
- 이후의 모든 VM은 근접 배치 그룹을 참조하여 이후 배포된 모든 Azure VM을 첫 번째 가상 컴퓨터와 동일한 데이터 센터에 배치하도록 배포했습니다.

> [!NOTE]
> 첫 번째 VM이 배치된 데이터 센터에서 특정 VM 형식을 실행할 수 있는 호스트 하드웨어가 배포되지 않으면 요청된 VM 형식의 배포가 성공하지 못합니다. 실패 메시지가 표시됩니다.

단일 [Azure 리소스 그룹에는](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 여러 근접 배치 그룹이 할당될 수 있습니다. 그러나 근접 배치 그룹은 하나의 Azure 리소스 그룹에만 할당할 수 있습니다.

근접 배치 그룹을 사용하는 경우 다음 사항을 염두에 두어야 합니다.

- SAP 시스템에 대한 최적의 성능을 목표로 하고 근접 배치 그룹을 사용하여 시스템의 단일 Azure 데이터 센터로 제한하는 경우 배치 그룹 내에서 모든 유형의 VM 패밀리를 결합하지 못할 수 있습니다. 이러한 제한은 특정 VM 형식을 실행하는 데 필요한 호스트 하드웨어가 배치 그룹의 "범위 별 VM"이 배포된 데이터 센터에 없을 수 있기 때문에 발생합니다.
- 이러한 SAP 시스템의 수명 주기 동안 시스템을 다른 데이터 센터로 이동해야 할 수 있습니다. 예를 들어 확장 형 HANA DBMS 계층이 4 개의 노드에서 16 개의 노드로 이동하고 데이터 센터에서 사용한 유형의 12 VM을 추가로 얻을 수있는 용량이 충분하지 않다고 결정하는 경우 이 이동이 필요할 수 있습니다.
- 하드웨어 의 서비스 해제로 인해 Microsoft는 처음에 사용한 데이터 센터가 아닌 다른 데이터 센터에서 사용한 VM 형식에 대한 용량을 구축할 수 있습니다. 이 시나리오에서는 모든 근접 배치 그룹의 VM을 다른 데이터 센터로 이동해야 할 수 있습니다.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Azure VM만 사용하는 SAP 시스템을 사용하는 근접 배치 그룹
Azure의 대부분의 SAP NetWeaver 및 S/4HANA 시스템 배포는 [HANA 대형 인스턴스를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)사용하지 않습니다. HANA 대형 인스턴스를 사용하지 않는 배포의 경우 SAP 응용 프로그램 계층과 DBMS 계층 간에 최적의 성능을 제공하는 것이 중요합니다. 이렇게 하려면 시스템에 대한 Azure 근접 배치 그룹을 정의합니다.

대부분의 고객 배포에서 고객은 SAP 시스템에 대한 단일 [Azure 리소스 그룹을](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 빌드합니다. 이 경우 프로덕션 ERP 시스템 리소스 그룹과 근접 배치 그룹 간에 일대일 관계가 있습니다. 다른 경우에는 고객이 리소스 그룹을 수평으로 구성하고 단일 리소스 그룹의 모든 프로덕션 시스템을 수집합니다. 이 경우 프로덕션 SAP 시스템에 대한 리소스 그룹과 프로덕션 SAP ERP, SAP BW 등을 위한 여러 근접 배치 그룹 간에 일대다 관계가 있습니다.

여러 SAP 프로덕션 또는 비프로덕션 시스템을 단일 근접 배치 그룹으로 묶지 마십시오. 소수의 SAP 시스템이나 SAP 시스템 및 일부 주변 응용 프로그램에 짧은 대기 시간 네트워크 통신이 필요한 경우 이러한 시스템을 하나의 근접 배치 그룹으로 이동하는 것이 좋습니다. 근접 배치 그룹에서 그룹화하는 시스템이 많을수록 시스템 번들을 피할 수 있습니다.

- 근접 배치 그룹이 범위가 조정된 특정 데이터 센터에서 실행할 수 없는 VM 유형이 필요합니다.
- M-시리즈 VM과 같이 비주류 VM의 리소스는 시간이 지남에 따라 근접 배치 그룹에 소프트웨어를 추가하기 때문에 더 많은 리소스가 필요할 때 결국 충족되지 않을 수 있습니다.

설명된 대로 이상적인 구성은 다음과 같습니다.

![Azure VM만 있는 근접 배치 그룹](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

이 경우 단일 SAP 시스템은 각각 하나의 리소스 그룹으로 그룹화되며 각각 하나의 근접 배치 그룹이 있습니다. HANA 확장 확장 또는 DBMS 확장 구성을 사용하는지 여부에 대한 종속성은 없습니다.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>근접 배치 그룹 및 HANA 대형 인스턴스
일부 SAP 시스템이 응용 프로그램 계층에 [대한 HANA 대형 인스턴스를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 사용하는 경우 [개정 4 행 또는 스탬프에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)배포된 HANA 대형 인스턴스 단위를 사용하는 경우 HANA 대형 인스턴스 단위와 Azure VM 간의 네트워크 대기 시간이 크게 향상될 수 있습니다. 한 가지 개선사항은 HANA 대형 인스턴스 단위가 배포될 때 근접 배치 그룹으로 배포한다는 것입니다. 해당 근접 배치 그룹을 사용하여 응용 프로그램 계층 VM을 배포할 수 있습니다. 따라서 이러한 VM은 HANA 대형 인스턴스 단위를 호스팅하는 동일한 데이터 센터에 배포됩니다.

HANA 대형 인스턴스 단위가 개정 4 스탬프 또는 행에 배포되는지 확인하려면 [Azure 포털을 통해 아티클 Azure HANA 대형 인스턴스 제어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)문서를 확인합니다. HANA 대형 인스턴스 단위의 특성 개요에서 HANA 대형 인스턴스 단위를 배포할 때 만들어졌기 때문에 근접 배치 그룹의 이름을 확인할 수도 있습니다. 특성 개요에 나타나는 이름은 응용 프로그램 계층 VM을 배포해야 하는 근접 배치 그룹의 이름입니다.

Azure 가상 시스템만 사용하는 SAP 시스템과 비교하여 HANA 대형 인스턴스를 사용할 때 사용할 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 수를 유연하게 결정할 수 있습니다. [HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)대형 인스턴스 [테넌트의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) 모든 HANA 대형 인스턴스 단위는 이 문서에서 설명한 대로 단일 리소스 그룹으로 그룹화됩니다. 프로덕션 및 비프로덕션 시스템 또는 기타 시스템과 같은 다른 테넌트에 배포하지 않는 한 모든 HANA 대형 인스턴스 단위는 하나의 HANA 대형 인스턴스 테넌트에 배포됩니다. 이 테넌트는 리소스 그룹과 일대일 관계를 가합니다. 그러나 각 단일 단위에 대해 별도의 근접 배치 그룹이 정의됩니다.

따라서 단일 테넌트에 대한 Azure 리소스 그룹 및 근접 배치 그룹 간의 관계는 다음과 같습니다.

![근접 배치 그룹 및 HANA 대형 인스턴스](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>근접 배치 그룹이 있는 배포 예
다음은 Azure 근접 배치 그룹으로 VM을 배포하는 데 사용할 수 있는 몇 가지 PowerShell 명령입니다.

[Azure Cloud Shell에](https://azure.microsoft.com/features/cloud-shell/)로그인한 후 첫 번째 단계는 배포에 사용할 Azure 구독에 있는지 여부를 확인하는 것입니다.

<pre><code>
Get-AzureRmContext
</code></pre>

다른 구독으로 변경해야 하는 경우 다음 명령을 실행하여 변경할 수 있습니다.

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

이 명령을 실행 하 여 새 Azure 리소스 그룹을 만듭니다.

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

이 명령을 실행하여 새 근접 배치 그룹을 만듭니다.

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

다음과 같은 명령을 사용하여 첫 번째 VM을 근접 배치 그룹에 배포합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

앞의 명령은 Windows 기반 VM을 배포합니다. 이 VM 배포가 성공하면 근접 배치 그룹의 데이터 센터 범위가 Azure 지역 내에 정의됩니다. 이전 명령에 표시된 것처럼 근접 배치 그룹을 참조하는 모든 후속 VM 배포는 해당 데이터 센터에 배치된 하드웨어에 VM 형식을 호스팅할 수 있는 한 동일한 Azure 데이터 센터에 배포됩니다. 사용할 수 있습니다.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>가용성 집합 및 가용성 영역을 근접 배치 그룹과 결합
SAP 시스템 배포에 가용성 영역을 사용할 때의 단점 중 하나는 특정 영역 내에서 가용성 집합을 사용하여 SAP 응용 프로그램 계층을 배포할 수 없다는 것입니다. SAP 응용 프로그램 계층을 DBMS 계층과 동일한 영역에 배포하려고 합니다. 단일 VM을 배포할 때 가용성 영역 및 가용성 집합을 참조하는 것은 지원되지 않습니다. 이전에는 영역을 참조하여 응용 프로그램 계층을 배포해야 했습니다. 응용 프로그램 계층 VM이 서로 다른 업데이트 및 실패 도메인에 분산되었는지 확인하는 기능이 손실되었습니다.

근접 배치 그룹을 사용하면 이 제한을 우회할 수 있습니다. 배포 순서는 다음과 같습니다.

- 근접 배치 그룹을 만듭니다.
- 가용성 영역을 참조하여 앵커 VM(일반적으로 DBMS 서버)을 배포합니다.
- Azure 근접 그룹을 참조하는 가용성 집합을 만듭니다. (이 문서의 후반부 명령을 참조하십시오.)
- 가용성 집합 및 근접 배치 그룹을 참조하여 응용 프로그램 계층 VM을 배포합니다.

이전 섹션에서 설명한 대로 첫 번째 VM을 배포하는 대신 VM을 배포할 때 가용성 영역 및 근접 배치 그룹을 참조합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

이 가상 시스템을 성공적으로 배포하면 SAP 시스템의 데이터베이스 인스턴스가 하나의 가용성 영역에서 호스팅됩니다. 근접 배치 그룹의 범위는 정의한 가용성 영역을 나타내는 데이터 센터 중 하나에 고정됩니다.

동일한 영역 또는 영역과 동일한 근접 배치 그룹을 참조하여 DBMS VM과 동일한 방식으로 중앙 서비스 VM을 배포한다고 가정합니다. 다음 단계에서는 SAP 시스템의 응용 프로그램 계층에 사용할 가용성 집합을 만들어야 합니다.

근접 배치 그룹을 정의하고 만들어야 합니다. 가용성 집합을 만드는 명령에는 이름이 아닌 근접 배치 그룹 ID에 대한 추가 참조가 필요합니다. 다음 명령을 사용하여 근접 배치 그룹의 ID를 얻을 수 있습니다.

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

가용성 집합을 만들 때 관리 디스크(달리 지정되지 않은 경우 기본값)와 근접 배치 그룹을 사용할 때 추가 매개 변수를 고려해야 합니다.

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

이상적으로는 세 개의 장애 도메인을 사용해야 합니다. 그러나 지원되는 장애 도메인의 수는 지역에 따라 다를 수 있습니다. 이 경우 특정 영역에 대해 가능한 최대 장애 도메인 수는 2개입니다. 응용 프로그램 계층 VM을 배포하려면 다음과 같이 가용성 집합 이름 및 근접 배치 그룹 이름에 대한 참조를 추가해야 합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

이 배포의 결과는 다음과 같은 것입니다.
- 특정 가용성 영역 또는 가용성 영역에 있는 SAP 시스템에 대한 DBMS 계층 및 중앙 서비스입니다.
- DBMS VM 또는 VM과 동일한 Azure 데이터 센터의 가용성 집합을 통해 있는 SAP 응용 프로그램 계층입니다.

> [!NOTE]
> 한 DBMS VM을 한 영역에 배포하고 두 번째 DBMS VM을 다른 영역에 배포하여 고가용성 구성을 만들려면 각 영역에 대해 다른 근접 배치 그룹이 필요합니다. 사용하는 모든 가용성 집합에 대해도 마찬가지입니다.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>기존 시스템을 근접 배치 그룹으로 이동
이미 SAP 시스템을 배포한 경우 일부 중요한 시스템의 네트워크 대기 시간을 최적화하고 동일한 데이터 센터에서 응용 프로그램 계층과 DBMS 계층을 찾을 수 있습니다. 전체 Azure 가용성 집합의 VM을 이미 범위가 지정된 기존 근접 배치 그룹으로 이동하려면 가용성 집합의 모든 VM을 종료하고 Azure 포털을 통해 기존 근접 배치 그룹에 가용성 집합을 할당해야 합니다. 파워쉘 또는 CLI. 가용성 집합의 일부가 아닌 VM을 기존 근접 배치 그룹으로 이동하려면 VM을 종료하고 기존 근접 배치 그룹에 할당하기만 하면 됩니다. 


## <a name="next-steps"></a>다음 단계
설명서를 확인하십시오.

- [Azure의 SAP 워크로드: 계획 및 배포 검사 목록](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [미리 보기: Azure CLI를 사용하여 근접 배치 그룹에 VM 배포](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [미리 보기: PowerShell을 사용하여 근접 배치 그룹에 VM 배포](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [SAP 워크로드에 대한 Azure 가상 시스템 DBMS 배포에 대한 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

