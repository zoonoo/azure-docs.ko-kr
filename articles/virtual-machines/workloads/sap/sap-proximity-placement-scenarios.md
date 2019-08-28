---
title: SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹 | Microsoft Docs
description: Azure 근접 배치 그룹을 사용 하는 SAP 배포 시나리오에 대해 설명 합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f97cce2d1a8b2de5634215629ddb997dc8f7196a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235198"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP 애플리케이션 네트워크 대기 시간을 최소화하는 Azure Proximity Placement Group
Sap NetWeaver 또는 SAP S/4HANA 아키텍처를 기반으로 하는 SAP 응용 프로그램은 SAP 응용 프로그램 계층과 SAP 데이터베이스 계층 간의 네트워크 대기 시간을 구분 합니다. 이러한 아키텍처의 이러한 민감도에 대 한 이유는 대부분의 비즈니스 논리가 응용 프로그램 계층에서 실행 된다는 사실입니다. 비즈니스 논리를 실행 한 결과로 SAP 응용 프로그램 계층에서 데이터베이스 계층에 대 한 쿼리를 실행 하는 데는 초당 수천 ~ 수십의 매우 많은 쿼리가 실행 됩니다. 대부분의 경우 이러한 쿼리의 특성은 간단 합니다. 그리고 데이터베이스 계층에서 500 마이크로초 미만으로 실행 될 수 있는 경우가 많습니다. 응용 프로그램 계층에서 데이터베이스 계층으로 이러한 쿼리를 전송 하 고 데이터베이스 계층에서 결과 집합을 다시 수신 하는 네트워크에 소요 된 시간은 비즈니스 프로세스를 실행 하는 데 걸리는 시간에 큰 영향을 미칩니다. 네트워크 대기 시간에 대 한 이러한 민감도는 최적의 네트워크 대기 시간을 얻기 위해 SAP 배포 프로젝트에 소요 되는 이유입니다. [SAP Note #1100926-FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)-SAP는 네트워크 대기 시간을 분류 하는 방법에 대 한 몇 가지 지침을 게시 했습니다.

한 쪽에서 많은 Azure 지역에서 증가 하는 데이터 센터의 수는 가용성 영역 도입으로도 트리거됩니다. 반면, 고객은 특히, 고성능 SAP 시스템에서 M 시리즈 제품군 또는 HANA 큰 인스턴스를 사용 하 여 더 많은 특수 한 VM Sku를 사용 했습니다. 특정 Azure 지역의 모든 데이터 센터에 없는 azure Virtual machines 유형입니다. 이러한 두 가지 성향을의 결과로, 네트워크 대기 시간이 최적 범위에 있지 않고 경우에 따라 SAP 시스템의 성능이 저하 되는 경우가 있습니다.

이러한 문제를 방지 하기 위해 Azure는 [Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)이라고 하는 구문을 제공 합니다. 이 새로운 기능은 다양 한 SAP 시스템을 이미 배포 하는 데 사용 되었습니다. 근접 배치 그룹의 제한 사항에 대 한 참조 문서를 확인 합니다. 이 문서에서는 Azure 근접 배치 그룹을 사용 하거나 사용 해야 하는 다양 한 SAP 시나리오를 다룹니다.

## <a name="what-are-proximity-placement-groups"></a>근접 배치 그룹 이란? 
Azure 근접 배치 그룹은 논리 구문으로, 정의 단계가 Azure 지역 및 Azure 리소스 그룹에 바인딩됩니다. Vm을 배포 하는 동안 다음에서 근접 배치 그룹을 참조 합니다.

- 데이터 센터에 대 한 첫 번째 배포 된 Azure VM입니다. 첫 번째 가상 머신은 Azure 할당 알고리즘을 기반으로 데이터 센터에 배포 되는 앵커 VM으로 볼 수 있으며,이는 궁극적으로 특정 Azure 가용성 영역에 대 한 사용자 정의와 결합 됩니다.
- 첫 번째 가상 머신과 동일한 데이터 센터에 배포 된 모든 Azure Vm을 배치 하기 위해 근접 배치 그룹을 참조 하는 모든 후속 Vm에 의해 배포 됩니다.

> [!NOTE]
> 첫 번째 VM을 배치 하는 것과 동일한 데이터 센터에서 특정 VM 유형을 실행할 수 있는 호스트 하드웨어가 배포 되지 않은 경우에는 요청 된 VM 유형의 배포가 실패 하 고 오류 메시지로 종료 됩니다. 이러한 경우는 첫 번째 VM 유형으로 배포 된 M 시리즈 VM과 같이 Gpu 또는 HPC VM 유형을 포함 하는 가상 머신과 같은 비 메인스트림 vm이 있는 경우입니다.

단일 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 은 자신에 게 여러 개의 근접 배치 그룹을 할당할 수 있습니다. 그러나 하나의 근접 배치 그룹은 하나의 Azure 리소스 그룹에만 할당할 수 있습니다.

근접 배치 그룹을 사용 하 여 다음 사항을 알고 있어야 합니다.

- SAP 시스템에 가장 적합 한 성능을 제공할 때 근접 배치 그룹을 사용 하 여이 시스템에 대 한 단일 Azure 데이터 센터로 제한 하는 경우 이러한 근접 배치 그룹 내에서 모든 유형의 VM 제품군을 결합 하지 못할 수 있습니다. 특정 VM 유형을 독점적으로 실행 하는 데 필요한 특정 호스트 하드웨어가 배치 그룹의 ' 앵커 VM '이 배포 된 데이터 센터에 없을 수도 있기 때문입니다.
- 이러한 SAP 시스템의 수명 주기에서 시스템을 다른 데이터 센터로 이동 해야 할 수 있습니다. 예를 들어, 규모 확장 HANA DBMS 계층이 네 개의 노드에서 16 개의 노드로 이동 하도록 결정 한 경우 이러한 이동이 강제 적용 될 수 있습니다. 그러나 더 이상 용량이 부족 하 여 동일한 데이터 센터에서 이미 사용 하 고 있는 형식의 Vm을 추가로 12 개 가져올 수 없습니다.
- 하드웨어의 서비스 해제로 인해 Microsoft는 동일한 데이터 센터 대신 다른 데이터 센터에서 사용한 VM 유형에 대 한 용량을 구축할 수 있습니다. 이러한 발생은 근접 배치 그룹의 모든 Vm을 다른 데이터 센터로 이동 하는 것을 의미할 수 있습니다.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Azure Vm을 독점적으로 사용 하는 SAP 시스템을 사용 하는 azure 근접 배치 그룹
Azure에서 SAP NetWeaver 및 S/4HANA 시스템 배포의 과반수는 [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 사용 하지 않습니다. 이러한 시스템을 배포 하는 경우 SAP 응용 프로그램 계층과 DBMS 계층 간에 가장 최적의 성능을 제공 하는 것이 중요 합니다. 이렇게 하려면이 시스템에 대해서만 Azure 근접 배치 그룹을 정의 합니다. 대부분의 고객 배포에서 고객은 SAP 시스템용 단일 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 을 빌드 하기로 했습니다. 이 경우, 예를 들어 프로덕션 ERP 시스템 리소스 그룹 및 해당 근접 배치 그룹 간의 1:1 관계가 있습니다. 다른 배포 사례에서 고객은 리소스 그룹을 수평으로 구성 하 고 모든 프로덕션 시스템을 단일 리소스 그룹으로 수집 했습니다. 이 경우 프로덕션 SAP 시스템에 대 한 리소스 그룹과 프로덕션 SAP ERP, SAP BW 등의 여러 근접 배치 그룹 간에는 1 대 다 관계가 있습니다. 단일 근접 배치 그룹에 여러 또는 모든 SAP 프로덕션 또는 비프로덕션 시스템을 번들로 묶는 것은 피해 야 합니다. 적은 수의 SAP 시스템 또는 SAP 시스템 및 일부 주변 응용 프로그램에서 대기 시간이 짧은 네트워크 통신을 수행 해야 하는 경우를 제외 하 고 이러한 시스템을 하나의 근접 배치 그룹으로 이동 하는 것을 고려할 수 있습니다. 이와 같은 권장 사항이 있는 이유는 근접 배치 그룹에서 그룹화 하는 더 많은 시스템에서 더 높은 가능성이 높습니다.

- 근접 배치 그룹이 고정 된 특정 데이터 센터에서 실행할 수 없는 VM 유형이 필요 합니다.
- 시간이 지남에 따라 기존 근접 배치 그룹에 소프트웨어를 추가 하는 경우 더 이상 요청 하는 경우에도 M 시리즈와 같은 특정 비 메인스트림 Vm의 리소스가 더 이상 충족 되지 않을 수 있습니다.

설명 된 대로 이상적인 사용은 다음과 같습니다.

![모든 Azure Vm에 대 한 근접 placemen 그룹](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

이 경우 단일 SAP 시스템은 각각 하나의 근접 배치 그룹을 사용 하는 하나의 리소스 그룹으로 그룹화 됩니다. HANA 확장 또는 DBMS 확장 구성을 사용 하는지 여부에 대 한 종속성은 없습니다.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Azure 근접 배치 그룹 및 HANA Large Instances
일부 SAP 시스템에서 [Hana Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 를 응용 프로그램 계층으로 사용 하는 경우 Hana large instance 단위와 Azure vm 간의 네트워크 대기 시간이 크게 향상 될 수 있습니다. [수정 버전 4 행 또는 스탬프](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)에 배포 됩니다. 향상 된 기능 중 하나는 HANA Large Instances 단위가 배포 된 후에 이미 배포 된 근접 배치 그룹을 가져오는 것입니다. 이러한 근접 배치 그룹을 사용 하 여 응용 프로그램 계층 Vm을 배포할 수 있습니다. 따라서 이러한 Vm은 HANA Large Instance 단위를 호스트 하는 동일한 데이터 센터에 배포 됩니다.

HANA 대량 인스턴스 단위가 수정 버전 4 스탬프 또는 행에 배포 되었는지 여부를 검색 하려면 [Azure Portal를 통해 AZURE Hana Large Instances 제어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)문서를 확인 합니다. HANA Large Instance 유닛의 특성 개요에서 HANA Large Instance 유닛에 대해 배포 시 생성 된 근접 배치 그룹의 이름을 찾을 수도 있습니다. 특성 개요에 표시 되는 이름은 근접 배치 그룹의 이름입니다 .를 사용 하 여 응용 프로그램 계층 Vm을에 배포 해야 합니다.

Azure virtual machines를 사용 하는 SAP 시스템의 경우와 달리 HANA Large 인스턴스를 사용 하는 경우 사용 해야 하는 [azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) 의 수를 결정 하는 것이 좋습니다. [Hana Large instance 테 넌 트](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) 의 모든 Hana large instance 단위는 [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)에 설명 된 대로 단일 Azure 리소스 그룹으로 그룹화 됩니다. 프로덕션 및 비프로덕션 또는 특정 시스템과 같이 서로 다른 테 넌 트에 배포 하는 경우를 제외 하 고 모든 HANA Large instance 장치는 하나의 HANA Large Instance 테 넌 트에 배포 되며,이는 Azure와의 1:1 관계가 다시 사용 됩니다. 리소스 그룹. 모든 단일 단위에는 별도의 근접 배치 그룹이 정의 되어 있습니다. 

따라서 단일 테 넌 트에 대 한 Azure 리소스 그룹과 근접 배치 그룹 간의 그룹화는 다음과 같습니다.

![모든 Azure Vm에 대 한 근접 배치 그룹](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Azure 근접 배치 그룹을 사용 하 여 배포 하는 간단한 예제
Azure 근접 배치 그룹을 사용 하 여 VM을 배포 하는 방법에 대 한 자세한 내용은 Azure 근접 배치 그룹을 사용 하 여 첫 번째 간단한 연습에 대 한 사용법을 보여 주는 PowerShell 명령 목록을 참조 하세요.

[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 에 로그인 한 후 첫 번째 단계는 다음 명령을 사용 하 여 배포 하는 데 사용 하려는 올바른 Azure 구독에 있는지 확인 하는 것입니다.

<pre><code>
Get-AzureRmContext
</code></pre>

다른 구독으로 변경 해야 하는 경우 다음 명령을 실행 하 여이 작업을 수행할 수 있습니다.

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

세 번째 단계로, 다음 명령을 사용 하 여 새 Azure 리소스 그룹을 만들려고 합니다.

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

이제를 사용 하 여 새 근접 배치 그룹을 만들 수 있습니다.

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

이제 다음과 같은 명령을 사용 하 여이 근접 배치 그룹에 첫 번째 VM 배포를 시작할 수 있습니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

위의 명령을 사용 하면 Windows 기반 VM이 배포 됩니다. 이 VM을 성공적으로 배포 하 고 나면 근접 배치 그룹의 데이터 센터 범위가 Azure 지역 내에서 정의 됩니다. 마지막 명령과 같이 근접 배치 그룹을 참조 하는 모든 후속 VM 배포는 해당 데이터 센터에 배치 된 하드웨어에서 VM 유형을 호스트 하 고 해당 VM 유형을 사용할 수 있는 경우에만 동일한 Azure 데이터 센터에 배포 됩니다.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>근접 배치 그룹을 사용 하 여 가용성 집합 및 가용성 영역 결합 
SAP 시스템 배포에 가용성 영역를 사용 하는 경우 단점 중 하나는 특정 영역 내에서 가용성 집합을 사용 하 여 SAP 응용 프로그램 계층을 배포할 수 없다는 점입니다. 단일 VM을 배포할 때 SAP 응용 프로그램 계층을 DBMS 계층과 동일한 영역에 배포 하 고 가용성 영역 및 가용성 집합을 참조 하는 것이 지원 되지 않으므로 영역을 참조 하 여 응용 프로그램 계층을 강제로 배포 해야 합니다. 따라서 응용 프로그램 계층 Vm이 다른 업데이트 및 장애 도메인에 걸쳐 확산 되도록 하는 기능이 손실 됩니다. 근접 배치 그룹의 도움으로 이러한 제한을 극복할 수 있습니다. 배포 시퀀스는 다음과 같습니다.

- 근접 배치 그룹 만들기
- 특정 Azure 가용성 영역을 참조 하 여 ' 앵커 VM ' (일반적으로 DBMS 서버)을 배포 합니다.
- Azure 근접 그룹을 참조 하는 가용성 집합 만들기 (아래 참조)
- 가용성 집합 및 근접 배치 그룹을 참조 하 여 응용 프로그램 계층 Vm을 배포 합니다.

위에서 설명한 대로 첫 번째 VM을 배포 하는 대신 다음과 같이 VM을 배포할 때 Azure 가용성 영역 및 근접 배치 그룹을 참조 합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

단일 Azure 가용성 영역에서 내 SAP 시스템의 데이터베이스 인스턴스를 호스트 하는이 가상 머신이 성공적으로 배포 되 면, 근접 배치 그룹의 범위는 사용자가 정의한 가용성 영역을 나타내는 데이터 센터 중 하나로 고정 됩니다. .

Dbms vm 및 동일한 근접 배치 그룹에 대해 동일한 영역을 참조 하 여 DBMs Vm과 동일한 방식으로 중앙 서비스 Vm을 배포 한다고 가정 합니다. 다음 단계에서는 SAP 시스템의 응용 프로그램 계층에 사용 하려는 가용성 집합을 만들어야 합니다.
근접 배치 그룹을 정의 하 고 만들어야 합니다. 가용성 집합을 만드는 명령은 근접 배치 그룹 ID (이름 아님)에 대 한 추가 참조가 필요 합니다. 다음을 사용 하 여 근접 배치 그룹의 ID를 가져올 수 있습니다.



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

가용성 집합을 만들 때 관리 디스크 (다르게 지정 되지 않은 경우 기본값) 및 근접 배치 그룹을 사용 하는 경우 추가 매개 변수를 고려해 야 합니다.

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

세 개의 장애 도메인을 사용 하는 것이 가장 좋습니다. 그러나 지원 되는 장애 도메인 수는 지역 마다 다를 수 있습니다. 이 경우 특정 지역에 사용할 수 있는 최대 장애 도메인 수는 2 개 였습니다. 응용 프로그램 계층 Vm을 배포 하려면 여기에 나와 있는 것 처럼 가용성 집합 이름 및 근접 배치 그룹 이름에 대 한 참조를 추가 해야 합니다.

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

이 시퀀스의 최종 결과는 DBMS VM과 동일한 Azure 데이터 센터의 가용성 집합을 통해 위치한 sap 응용 프로그램 계층 및 특정 가용성 영역에 있는 SAP 시스템의 DBMS 계층 및 중앙 서비스입니다. 구축한.

> [!NOTE]
> 하나 이상의 DBMS vm을 한 영역에 배포 하 고 두 번째 DBMS VM을 다른 영역에 배포 하 여 고가용성 구성을 만들 때 각 영역에 대해 서로 다른 근접 배치 그룹이 필요 합니다. 사용할 수 있는 가용성 집합에 대해 동일한 si true

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>기존 시스템을 Azure 근접 배치 그룹으로 가져오기
SAP 시스템이 이미 배포 되어 있는 경우에는 중요 한 시스템 중 일부의 네트워크 대기 시간을 최적화 하 고 동일한 데이터 센터에서 응용 프로그램 계층 및 DBMS 계층을 찾을 수 있습니다. 근접 배치 그룹 기능의 공개 미리 보기 단계에서 근접 배치 그룹으로의 이동을 수행 하려면 vm을 삭제 하 고 Vm을 새로 만들어야 합니다. 이 기능 단계에서 해당 종료 가상 컴퓨터를 근접 배치 그룹에 할당할 수 있도록 Vm을 종료 하는 것 만으로는 충분 하지 않습니다.


## <a name="next-steps"></a>다음 단계
다음 설명서를 참조하세요.

- [Azure의 SAP 워크 로드 계획 및 배포 검사 목록](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [미리 보기: Azure CLI를 사용 하 여 근접 배치 그룹에 Vm 배포](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [미리 보기: PowerShell을 사용 하 여 근접 배치 그룹에 Vm 배포](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

