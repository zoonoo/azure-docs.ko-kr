---
title: SAP 애플리케이션용 Azure 근접 배치 그룹 | Microsoft Docs
description: Azure 근접 배치 그룹을 사용하는 SAP 배포 시나리오에 대해 설명합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 924fdef475c43023c69c3006db19cd9a5aa15349
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669659"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP 애플리케이션에서 네트워크 대기 시간을 최적화하기 위한 Azure 근접 배치 그룹
SAP NetWeaver 또는 SAP S/4HANA 아키텍처를 기반으로 하는 SAP 애플리케이션은 SAP 애플리케이션 계층과 SAP 데이터베이스 계층 간의 네트워크 대기 시간에 민감합니다. 이러한 민감도는 애플리케이션 레이어에서 실행되는 대부분의 비즈니스 논리에 따른 결과입니다. SAP 애플리케이션 레이어는 비즈니스 논리를 실행하므로 데이터베이스 계층에 대한 쿼리를 초당 수천 또는 수만 개 속도의 높은 빈도로 실행합니다. 대부분의 경우 이러한 쿼리의 특성은 간단합니다. 즉 데이터베이스 계층에서 500마이크로초 이하로 실행되는 경우가 많습니다.

네트워크에서 이러한 쿼리를 애플리케이션 계층에서 데이터베이스 계층으로 보내고 결과 집합을 다시 받는 데 걸리는 시간은 비즈니스 프로세스를 실행하는 데 걸리는 시간에 큰 영향을 줍니다. 네트워크 대기 시간에 대한 이러한 민감도로 인해 SAP 배포 프로젝트에서 특정 최대 네트워크 대기 시간을 달성하려고 할 수 있습니다. 네트워크 대기 시간을 분류하는 방법에 대한 지침은 [SAP Note #1100926 - FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)을 참조하세요.

많은 Azure 지역에서 데이터 센터의 수가 증가했습니다. 이와 동시에, 특히 고성능 SAP 시스템의 고객은 M/Mv2 제품군 또는 HANA(대규모 인스턴스)의 더 특수한 VM SKU를 사용하고 있습니다. 이러한 Azure 가상 머신 유형은 Azure 지역을 보완하는 모든 데이터 센터에서 항상 사용할 수 있는 것은 아닙니다. 이러한 사실은 SAP 애플리케이션 레이어와 SAP DBMS 레이어 간의 네트워크 대기 시간을 최적화할 수 있는 기회를 창출할 수 있습니다.

Azure는 네트워크 대기 시간을 최적화할 수 있도록 [근접 배치 그룹](../../co-location.md)을 제공합니다. 근접 배치 그룹을 사용하여 여러 VM 유형을 단일 Azure 데이터 센터로 그룹화하도록 적용하여 이러한 여러 VM 유형 간의 네트워크 대기 시간을 최대한 최적화할 수 있습니다. 첫 번째 VM을 이러한 근접 배치 그룹에 배포하는 과정에서 해당 VM은 특정 데이터 센터에 바인딩됩니다. 이 전망만큼 매력적일 수 있지만 이러한 구성을 사용하는 경우에도 다음과 같은 몇 가지 제한 사항이 도입되었습니다.

- 모든 Azure 데이터 센터에서 모든 Azure VM 유형을 사용할 수 있다고 가정할 수는 없습니다. 따라서 하나의 근접 배치 그룹 내에서 서로 다른 VM 유형의 조합을 제한할 수 있습니다. 이러한 제한은 배치 그룹이 배포된 데이터 센터에 특정 VM 유형을 실행하는 데 필요한 호스트 하드웨어가 없을 수 있기 때문에 발생합니다.
- 하나의 근접 배치 그룹 내에 있는 VM의 일부 크기를 조정할 때 모든 경우에 새 VM 유형을 근접 배치 그룹에 속한 다른 VM과 동일한 데이터 센터에서 사용할 수 있다고 자동으로 가정할 수 없습니다.
- Azure에서 하드웨어 서비스를 해제할 때 근접 배치 그룹의 특정 VM을 다른 Azure 데이터 센터에 강제로 적용할 수 있습니다. 이 사례에 대한 자세한 내용은 [대기 시간을 향상시키기 위한 리소스 공동 배치](../../co-location.md#planned-maintenance-and-proximity-placement-groups) 문서를 참조하세요.  

> [!IMPORTANT]
> 다음과 같은 잠재적인 제한 사항으로 인해 근접 배치 그룹을 사용해야 합니다.
>
> - 필요한 경우에만
> - 전체 시스템 환경 또는 전체 SAP 환경이 아닌 단일 SAP 시스템의 세분성에서만
> - 다른 VM 유형과 근접 배치 그룹 내의 VM 수를 최소로 유지하는 방식에서

가용성 영역을 지정하여 VM을 배포하고 동일한 가용성 영역을 선택하는 경우 이러한 VM 간의 네트워크 대기 시간은 만족스러운 성능과 처리량으로 SAP NetWeaver 및 S/4HANA 시스템을 운영하는 데 충분하다고 가정합니다. 이 가정은 특정 영역이 하나의 데이터 센터 또는 여러 데이터 센터로 구성되는지 여부와는 관련이 없습니다. 영역 배포에서 근접 배치 그룹을 사용하는 유일한 이유는 Azure 가용성 집합에 배포된 VM을 영역에 배포된 VM과 함께 할당하려는 경우입니다.


## <a name="what-are-proximity-placement-groups"></a>근접 배치 그룹이란? 
Azure 근접 배치 그룹은 논리적 구조입니다. 근접 배치 그룹이 정의되면 Azure 지역 및 Azure 리소스 그룹에 바인딩됩니다. VM이 배포되면 다음에서 근접 배치 그룹을 참조합니다.

- 데이터 센터에 배포된 첫 번째 Azure VM. 첫 번째 가상 머신은 Azure 할당 알고리즘을 기반으로 데이터 센터에 배포되는 "범위 VM"이라고 할 수 있으며, 결국에는 특정 가용성 영역에 대한 사용자 정의와 결합됩니다.
- 근접 배치 그룹을 참조하는 배포된 모든 후속 VM. 이후에 배포된 모든 Azure VM은 첫 번째 가상 머신과 동일한 데이터 센터에 배치됩니다.

> [!NOTE]
> 첫 번째 VM이 배치된 데이터 센터에서 특정 VM 유형을 실행할 수 있는 호스트 하드웨어가 배포되지 않은 경우 요청된 VM 유형의 배포가 실패합니다. 이 경우 오류 메시지를 받게 됩니다.

하나의 [Azure 리소스 그룹](../../../azure-resource-manager/management/manage-resources-portal.md)에 여러 근접 배치 그룹을 할당할 수 있습니다. 그러나 근접 배치 그룹은 하나의 Azure 리소스 그룹에만 할당할 수 있습니다.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Azure VM만 사용하는 SAP 시스템이 포함된 근접 배치 그룹
Azure에 있는 대부분의 SAP NetWeaver 및 S/4HANA 시스템 배포는 [HANA(대규모 인스턴스)](./hana-overview-architecture.md)를 사용하지 않습니다. HANA(대규모 인스턴스)를 사용하지 않는 배포의 경우 SAP 애플리케이션 레이어와 DBMS 계층 간에 최적의 성능을 제공해야 합니다. 이렇게 하려면 Azure 근접 배치 그룹을 시스템에 대해서만 정의합니다.

대부분의 고객 배포에서 고객은 SAP 시스템에 대한 단일 [Azure 리소스 그룹](../../../azure-resource-manager/management/manage-resources-portal.md)을 구축합니다. 이 경우, 예를 들어 프로덕션 ERP 시스템 리소스 그룹과 그 근접 배치 그룹 간에 일대일 관계가 있습니다. 다른 경우에는 고객이 리소스 그룹을 수평으로 구성하고 모든 프로덕션 시스템을 단일 리소스 그룹에 수집합니다. 이 경우 프로덕션 SAP 시스템에 대한 리소스 그룹과 프로덕션 SAP ERP, SAP BW 등에 대한 여러 근접 배치 그룹 간에는 일대다 관계가 있습니다.

단일 근접 배치 그룹에서 여러 SAP 프로덕션 또는 비 프로덕션 시스템을 번들로 묶지 않습니다. 적은 수의 SAP 시스템 또는 SAP 시스템 및 일부 주변 애플리케이션에서 대기 시간이 짧은 네트워크 통신이 필요한 경우 이러한 시스템을 하나의 근접 배치 그룹으로 이동하는 것을 고려할 수 있습니다. 더 많은 시스템을 근접 배치 그룹에 그룹화할수록 다음과 같은 가능성이 높아지므로 시스템 번들을 사용하지 않습니다.

- 근접 배치 그룹의 범위가 지정된 특정 데이터 센터에서 실행할 수 없는 VM 유형이 필요합니다.
- 시간이 지남에 따라 소프트웨어를 근접 배치 그룹에 추가하므로 M 시리즈 VM과 같은 비주류 VM의 리소스가 더 필요한 경우 결국에는 충족되지 않을 수 있습니다.

설명한 대로 이상적인 구성은 다음과 같습니다.

![Azure VM만 포함된 근접 배치 그룹](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

이 경우 단일 SAP 시스템은 각각 하나의 리소스 그룹으로 그룹화되며, 각 리소스 그룹에는 하나의 근접 배치 그룹이 있습니다. HANA 스케일 아웃 또는 DBMS 스케일 업 구성을 사용하는지 여부와는 관련이 없습니다.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>근접 배치 그룹 및 HANA(대규모 인스턴스)
일부 SAP 시스템에서 [HANA(대규모 인스턴스)](./hana-overview-architecture.md)를 애플리케이션 레이어에 사용하는 경우 [수정 버전 4 행 또는 스탬프](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)에 배포된 HANA(대규모 인스턴스) 단위를 사용하면 HANA(대규모 인스턴스) 단위와 Azure VM 간의 네트워크 대기 시간이 크게 향상될 수 있습니다. 향상된 한 가지 사항은 HANA(대규모 인스턴스) 단위가 배포될 때 근접 배치 그룹과 함께 배포된다는 것입니다. 이 근접 배치 그룹을 사용하여 애플리케이션 레이어 VM을 배포할 수 있습니다. 따라서 이러한 VM은 HANA(대규모 인스턴스) 단위를 호스팅하는 동일한 데이터 센터에 배포됩니다.

HANA(대규모 인스턴스) 단위가 수정 버전 4 스탬프 또는 행에 배포되었는지 확인하려면 [Azure Portal을 통한 Azure HANA(대규모 인스턴스) 제어](./hana-li-portal.md#look-at-attributes-of-single-hli-unit) 문서를 확인합니다. HANA(대규모 인스턴스) 단위를 배포할 때 만들어졌으므로 HANA(대규모 인스턴스) 단위의 특성 개요에서 근접 배치 그룹의 이름을 확인할 수도 있습니다. 특성 개요에 표시되는 이름은 애플리케이션 레이어 VM을 배포해야 하는 근접 배치 그룹의 이름입니다.

Azure 가상 머신만 사용하는 SAP 시스템과 비교하여 HANA(대규모 인스턴스)를 사용하는 경우 사용할 [Azure 리소스 그룹](../../../azure-resource-manager/management/manage-resources-portal.md)의 수를 결정하는 데 있어 유연성이 떨어집니다. [이 문서](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal)에서 설명한 대로 [HANA(대규모 인스턴스) 테넌트](./hana-know-terms.md)의 모든 HANA(대규모 인스턴스) 단위는 단일 리소스 그룹으로 그룹화됩니다. 예를 들어 프로덕션 시스템과 비 프로덕션 시스템 또는 다른 시스템과 같이 분리하기 위해 다른 테넌트에 배포하는 경우를 제외하고는 모든 HANA(대규모 인스턴스) 단위가 하나의 HANA(대규모 인스턴스) 테넌트에 배포됩니다. 이 테넌트는 리소스 그룹과 일대일 관계에 있습니다. 그러나 각 단일 단위에 대해 별도의 근접 배치 그룹이 정의됩니다.

따라서 단일 테넌트에 대한 Azure 리소스 그룹과 근접 배치 그룹 간의 관계는 다음과 같습니다.

![근접 배치 그룹 및 HANA(대규모 인스턴스)](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>근접 배치 그룹을 사용한 배포의 예제
다음은 Azure 근접 배치 그룹을 사용하여 VM을 배포하는 데 사용할 수 있는 몇 가지 PowerShell 명령입니다.

[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)에 로그인한 후 첫 번째 단계는 배포에 사용하려는 Azure 구독에 있는지 확인하는 것입니다.

<pre><code>
Get-AzureRmContext
</code></pre>

다른 구독으로 변경해야 하는 경우 다음 명령을 실행하여 변경할 수 있습니다.

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

다음 명령을 실행하여 새 Azure 리소스 그룹을 만듭니다.

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

다음 명령을 실행하여 새 근접 배치 그룹을 만듭니다.

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

다음과 같은 명령을 사용하여 첫 번째 VM을 근접 배치 그룹에 배포합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

이전 명령에서는 Windows 기반 VM을 배포합니다. 이 VM이 성공적으로 배포되면 근접 배치 그룹의 데이터 센터 범위가 Azure 지역 내에서 정의됩니다. VM 유형을 데이터 센터에 배치된 하드웨어에서 호스팅할 수 있고 해당 VM 유형에 대한 용량을 사용할 수 있는 한 이전 명령과 같이 근접 배치 그룹을 참조하는 모든 후속 VM 배포는 동일한 Azure 데이터 센터에 배포됩니다.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>근접 배치 그룹과 가용성 집합 및 가용성 영역 결합
가용성 영역을 SAP 시스템 배포에 사용하는 경우의 단점 중 하나는 특정 영역 내에서 가용성 집합을 사용하여 SAP 애플리케이션 레이어를 배포할 수 없다는 것입니다. SAP 애플리케이션 레이어를 DBMS 레이어와 동일한 영역에 배포하려고 합니다. 단일 VM을 배포할 때 가용성 영역 및 가용성 집합을 참조하는 것은 지원되지 않습니다. 따라서 이전에는 영역을 참조하여 애플리케이션 레이어를 배포해야 했습니다. 애플리케이션 레이어 VM이 다른 업데이트 및 장애 도메인에 분산되었는지 확인하는 기능이 손실되었습니다.

근접 배치 그룹을 사용하면 이 제한을 무시할 수 있습니다. 배포 시퀀스는 다음과 같습니다.

- 근접 배치 그룹을 만듭니다.
- 가용성 영역을 참조하여 앵커 VM(일반적으로 DBMS 서버)을 배포합니다.
- Azure 근접 그룹을 참조하는 가용성 집합을 만듭니다. (이 문서의 뒷부분에 나오는 명령을 참조하세요.)
- 가용성 집합 및 근접 배치 그룹을 참조하여 애플리케이션 레이어 VM을 배포합니다.

이전 섹션에서 설명한 대로 첫 번째 VM을 배포하는 대신, VM을 배포할 때 가용성 영역 및 근접 배치 그룹을 참조합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

이 가상 머신이 성공적으로 배포되면 하나의 가용성 영역에서 SAP 시스템의 데이터베이스 인스턴스를 호스팅합니다. 근접 배치 그룹의 범위는 정의한 가용성 영역을 나타내는 데이터 센터 중 하나에 고정됩니다.

동일한 하나 이상의 영역 및 동일한 근접 배치 그룹을 참조하는 DBMS VM과 동일한 방식으로 중앙 서비스 VM을 배포한다고 가정합니다. 다음 단계에서는 SAP 시스템의 애플리케이션 레이어에 사용하려는 가용성 집합을 만들어야 합니다.

근접 배치 그룹을 정의하고 만듭니다. 가용성 집합을 만드는 명령에는 이름이 아닌 근접 배치 그룹 ID에 대한 추가 참조가 필요합니다. 다음 명령을 사용하여 근접 배치 그룹의 ID를 가져올 수 있습니다.

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

가용성 집합을 만들 때 관리 디스크(달리 지정되지 않은 경우 기본값) 및 근접 배치 그룹을 사용하는 경우 다음과 같은 추가 매개 변수를 고려해야 합니다.

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

세 개의 장애 도메인을 사용하는 것이 가장 좋습니다. 그러나 지원되는 장애 도메인의 수는 지역마다 다를 수 있습니다. 이 경우 특정 지역에 사용할 수 있는 최대 장애 도메인 수는 2개입니다. 애플리케이션 레이어 VM을 배포하려면 다음과 같이 가용성 집합 이름 및 근접 배치 그룹 이름에 대한 참조를 추가해야 합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

이 배포의 결과는 다음과 같습니다.
- 하나 이상의 특정 가용성 영역에 있는 SAP 시스템에 대한 DBMS 레이어 및 중앙 서비스
- DBMS VM 또는 VM과 동일한 Azure 데이터 센터의 가용성 집합을 통해 배치되는 SAP 애플리케이션 레이어

> [!NOTE]
> 고가용성 구성을 만들기 위해 하나의 DBMS VM을 한 영역에 배포하고 두 번째 DBMS VM을 다른 영역에 배포하므로 각 영역에 대해 서로 다른 근접 배치 그룹이 필요합니다. 사용하는 모든 가용성 집합에 대해서도 마찬가지입니다.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>기존 시스템을 근접 배치 그룹으로 이동
SAP 시스템을 이미 배포한 경우 일부 중요 시스템의 네트워크 대기 시간을 최적화하고 동일한 데이터 센터에서 애플리케이션 레이어와 DBMS 레이어를 배치할 수 있습니다. 전체 Azure 가용성 집합의 VM을 이미 범위가 지정된 기존 근접 배치 그룹으로 이동하려면 가용성 집합의 모든 VM을 종료하고 Azure Portal, PowerShell 또는 CLI를 통해 가용성 집합을 기존 근접 배치 그룹에 할당해야 합니다. 가용성 집합의 일부가 아닌 VM을 기존 근접 배치 그룹으로 이동하려면 VM을 종료하고 기존 근접 배치 그룹에 할당하기만 하면 됩니다. 


## <a name="next-steps"></a>다음 단계
다음 설명서를 확인하세요.

- [Azure의 SAP 워크로드: 계획 및 배포 검사 목록](./sap-deployment-checklist.md)
- [미리 보기: Azure CLI를 사용하여 근접 배치 그룹에 VM 배포](../../linux/proximity-placement-groups.md)
- [미리 보기: PowerShell을 사용하여 근접 배치 그룹에 VM 배포](../../windows/proximity-placement-groups.md)
- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](./dbms_guide_general.md)