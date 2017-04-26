---
title: "Azure에서 Linux VM의 VM 다시 시작 유지 관리 | Microsoft Docs"
description: "Linux 가상 컴퓨터의 VM 다시 시작 유지 관리에 대해 다룹니다."
services: virtual-machines-linux
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c02b2878462afb107c81317c9ea0a317cb9ce838
ms.lasthandoff: 04/18/2017


---

# <a name="vm-restarting-maintenance"></a>VM 다시 시작 유지 관리

기본 인프라에 계획된 유지 관리로 인해 VM이 다시 부팅되는 경우는 거의 없습니다. Azure에서 호스팅되는 VM의 가용성에 큰 영향을 미치기 때문에 다음 방법을 사용할 수 있습니다.

-   영향을 미치기 최소 30일 전에 전송되는 알림.

-   VM당 유지 관리 기간에 대한 정보.

-   유지 관리가 VM에 영향을 미치는 정확한 시간을 유연하게 설정 및 제어.

Microsoft Azure의 유지 관리는 반복적으로 예약됩니다. 초기 반복에서는 새로운 픽스와 기능을 롤아웃하는 데 관련된 위험을 줄이기 위해 범위가 더 작습니다. 이후 반복은 여러 지역으로 확장될 수 있습니다(결코 동일한 지역 쌍에서 확장되지 않음). VM은 단일 유지 관리 반복에 포함됩니다. 반복이 중단되면 나머지 VM은 미래의 다른 반복에 포함됩니다.

계획된 유지 관리 반복에는 두 단계, 즉 선점형 유지 관리 기간과 예약된 유지 관리 기간이 있습니다.

**선점형 유지 관리 기간**은 VM에서 유지 관리를 시작할 수 있는 유연성을 제공합니다. 이렇게 하면 VM에 영향을 미치는 시기, 업데이트 순서 및 각 VM을 유지 관리하는 간격을 결정할 수 있습니다. 각 VM을 쿼리하여 유지 관리하도록 계획되어 있는지 확인하고 마지막으로 시작한 유지 관리 요청의 결과를 확인할 수 있습니다.

**예약된 유지 관리 기간**은 Azure에서 VM을 유지 관리하도록 예약한 시간입니다. 선점형 유지 관리 기간 뒤에 오는 이 기간 동안 유지 관리 기간을 계속 쿼리할 수 있지만, 더 이상 유지 관리를 오케스트레이션할 수 없습니다.

## <a name="availability-considerations-during-planned-maintenance"></a>계획된 유지 관리 기간의 가용성 고려 사항 

### <a name="paired-regions"></a>쌍을 이루는 지역

각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. 유지 관리를 실행할 때 Azure는 단일 지역의 해당 쌍이 되는 가상 컴퓨터 인스턴스만 업데이트합니다. 예를 들어 미국 중북부에 있는 가상 컴퓨터를 업데이트할 때 Azure는 미국 중남부의 가상 컴퓨터를 동시에 업데이트하지 않습니다. 이는 별도의 다른 시간에 예약하여 지역 간의 장애 조치(failover) 또는 부하 분산을 사용하도록 설정합니다. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다.
[Azure 지역 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)에 대해 자세히 알아보세요.

### <a name="single-instance-vms-vs-availability-set-or-vm-scale-set"></a>단일 인스턴스 VM과 가용성 집합 또는 VM 확장 집합의 차이

Azure에서 가상 컴퓨터를 사용하여 워크로드를 배포할 때 응용 프로그램에 고가용성을 제공하기 위해 가용성 집합 내에 VM을 만들 수 있습니다. 이 구성을 통해 가동 중단 또는 유지 관리 이벤트 중에도 하나 이상의 가상 컴퓨터를 사용할 수 있습니다.

가용성 집합 내에서 개별 VM은 최대 20개의 업데이트 도메인에 걸쳐 분산됩니다. 계획된 유지 관리 동안에는 단일 업데이트 도메인만 지정된 시간에 영향을 받습니다. 영향을 받는 업데이트 도메인의 순서는 계획된 유지 관리 동안 순차적으로 진행되지 않을 수 있습니다. 단일 인스턴스 VM(가용성 집합의 일부가 아님)의 경우 얼마나 많은 VM이 함께 영향을 받는지 예측하거나 결정할 수 있는 방법이 없습니다.

가상 컴퓨터 확장 집합은 동일한 VM 집합을 단일 리소스로 배포하고 관리할 수 있게 하는 Azure 계산 리소스입니다.
확장 집합은 업데이트 도메인의 형태로 가용성 집합에 유사한 보증을 제공합니다. 

고가용성을 위해 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 [*Windows 가상 컴퓨터의 가용성 관리*](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

### <a name="scheduled-events"></a>예약된 이벤트

Azure Metadata Service를 사용하면 Azure에서 호스팅되는 Virtual Machine에 대한 정보를 검색할 수 있습니다. 공개된 범주 중 하나인 Scheduled Events는 예정된 이벤트(예: 다시 부팅)에 대한 정보를 나타내므로 응용 프로그램에서 이벤트를 준비하고 중단을 제한할 수 있습니다.

예약된 이벤트에 대한 자세한 내용은 [Azure 메타데이터 서비스 - 예약된 이벤트](../virtual-machines-scheduled-events.md)를 참조하세요.

## <a name="maintenance-discovery-and-notifications"></a>유지 관리 검색 및 알림

유지 관리 일정은 개별 VM 수준에서 고객에게 표시됩니다. Azure Portal, API, PowerShell 또는 CLI를 사용하여 선점형 및 예약된 유지 관리 기간을 쿼리할 수 있습니다. 또한 프로세스 중에 하나 이상의 VM에 영향을 줄 경우 알림(전자 메일)을 받을 수 있어야 합니다.

선점형 유지 관리 및 예약된 유지 관리 단계는 모두 알림으로 시작합니다. Azure 구독당 하나의 알림을 받을 수 있어야 합니다. 알림은 기본적으로 구독의 관리자 및 공동 관리자에게 전송됩니다. 유지 관리 알림의 대상을 구성할 수도 있습니다.

### <a name="view-the-maintenance-window-in-the-portal"></a>포털에서 유지 관리 기간 보기 

Azure Portal을 사용하여 유지 관리하도록 예약된 VM을 찾아볼 수 있습니다.

1.  Azure 포털에 로그인합니다.

2.  **Virtual Machines** 블레이드를 클릭하여 엽니다.

3.  **열** 단추를 클릭하여 선택할 수 있는 사용 가능한 열 목록을 엽니다.

4.  **유지 관리 기간** 열을 선택하고 추가합니다. 유지 관리하도록 예약된 VM에는 유지 관리 기간이 표시됩니다. a에 대한 유지 관리가 완료되거나 중단되면 유지 관리 기간이 더 이상 표시되지 않습니다.

### <a name="query-maintenance-details-using-the-azure-api"></a>Azure API를 사용하여 유지 관리 세부 정보 쿼리

[VM 정보 가져오기 API](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get)를 사용하여 인스턴스 보기를 찾아 개별 VM의 유지 관리 세부 사항을 검색합니다. 응답에는 다음과 같은 요소가 포함되어 있습니다.

  - isCustomerInitiatedMaintenanceAllowed: 이제 VM에서 선점형 다시 배포를 시작할 수 있는지 여부를 나타냅니다.

  - preMaintenanceWindowStartTime: 선점형 유지 관리 기간의 시작 시간입니다.

  - preMaintenanceWindowEndTime: 선점형 유지 관리 기간의 종료 시간입니다. 이 시간 이후에는 이 VM에 대한 유지 관리를 더 이상 시작할 수 없습니다.
    
  - maintenanceWindowStartTime: VM에 영향을 미칠 때 예약된 유지 관리 기간의 시작 시간입니다.

  - maintenanceWindowEndTime: 예약된 유지 관리 기간의 종료 시간입니다.
  
  - lastOperationResultCode: 마지막 유지 관리 - 다시 배포 작업의 결과입니다.
 
  - lastOperationMessage: 마지막 유지 관리 - 다시 배포 작업의 결과를 설명하는 메시지입니다.


## <a name="pre-emptive-redeploy"></a>선점형 다시 배포

선점형 다시 배포 작업은 Azure에서 VM에 유지 관리가 적용되는 시간을 제어할 수 있는 유연성을 제공합니다. 계획된 유지 관리는 각 VM을 재부팅하는 정확한 시간을 결정할 수 있는 선점형 유지 관리 기간에서 시작합니다. 이러한 기능이 유용한 사용 사례는 다음과 같습니다.

-   유지 관리를 최종 고객과 함께 조정해야 합니다.

-   Azure에서 제공하는 예약된 유지 관리 기간이 충분하지 않습니다.
    유지 관리 기간이 일주일 중 가장 바쁜 시간과 겹치거나 너무 길 수도 있습니다.

-   다중 인스턴스 또는 다중 계층 응용 프로그램의 경우 두 VM 간에 충분한 시간이 필요하거나 특정 시퀀스를 따라야 합니다.

VM에 대한 선점형 다시 배포를 요청하는 경우 VM을 이미 업데이트된 노드로 이동한 다음 전원을 다시 켜서 모든 구성 옵션과 관련 리소스를 그대로 유지합니다. 이렇게 하면 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다.

선점형 다시 배포는 VM당 한 번만 활성화됩니다. 프로세스 중에 오류가 발생하면 작업이 중단되고 VM은 영향을 받지 않으며 계획된 유지 관리 반복에서 제외됩니다. 이후에 사용자에게 연락하여 새로운 일정을 알려주고 VM에 대한 영향을 예약하고 순서를 지정할 수 있는 새로운 기회를 제공합니다.

