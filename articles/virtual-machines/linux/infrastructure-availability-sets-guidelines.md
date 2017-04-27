---
title: "Linux VM에 대한 Azure 가용성 집합 | Microsoft Docs"
description: "Azure 인프라 서비스에서 가용성 집합을 배포하기 위한 핵심 디자인 및 구현 지침에 대해 알아봅니다."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 24f1d91c-8cc0-4251-bb67-ac4c4c37e8cd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 05d5ae485f5a345ade59326ab77cb38127f06580
ms.lasthandoff: 04/14/2017


---
# <a name="azure-availability-sets-guidelines-for-linux-vms"></a>Linux VM에 대한 Azure 가용성 집합 지침

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

이 문서에서는 계획된 이벤트 또는 계획되지 않은 이벤트 동안 응용 프로그램에 계속 액세스할 수 있도록 하기 위해 가용성 집합에 대한 필수 계획 단계를 이해하는 데 주안점을 둡니다.

## <a name="implementation-guidelines-for-availability-sets"></a>가용성 집합에 대한 구현 지침
의사 결정:

* 응용 프로그램 인프라에서 다양한 역할 및 계층에 가용성 집합이 얼마나 필요한가?

작업:

* 각 응용 프로그램 계층에서 필요한 VM의 수를 정의합니다.
* 응용 프로그램에 사용할 장애 또는 업데이트 도메인의 수를 조정해야 하는지를 결정합니다.
* 명명 규칙을 사용하여 필요한 가용성 집합을 정의하고 해당 집합에 포함될 VM을 정의합니다. VM은 하나의 가용성 집합에만 포함될 수 있습니다. 

## <a name="availability-sets"></a>가용성 집합
Azure에서 VM(가상 컴퓨터)은 가용성 집합이라는 논리적 그룹에 배치될 수 있습니다. 가용성 집합 내에서 VM을 만들 경우 Azure 플랫폼은 기본 인프라 내에서 해당 VM을 분산 배치합니다. Azure 플랫폼 또는 기본 하드웨어/인프라 오류에 대한 계획된 유지 관리 이벤트가 있는 경우 가용성 집합을 사용하여 하나 이상의 VM을 실행 상태로 유지해야 합니다.

모범 사례로 응용 프로그램은 단일 VM에만 상주하지 않는 것이 좋습니다. 단일 VM을 포함하는 가용성 집합의 경우 Azure 플랫폼 내의 계획되거나 계획되지 않은 이벤트에서 보호 효과를 얻지 못합니다. 기본 인프라 내에서 VM을 분산할 수 있도록 하기 위해 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) 은 가용성 집합 내에서 두 개 이상의 VM을 요구합니다. [Azure Premium Storage](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용 중인 경우 Azure SLA는 단일 VM에 적용됩니다.

Azure의 기본 인프라는 여러 하드웨어 클러스터로 분할됩니다. 각 하드웨어 클러스터는 다양한 VM 크기를 지원할 수 있습니다. 가용성 집합은 언제든지 특정 시점에 단일 하드웨어 클러스터에만 호스트될 수 있습니다. 따라서 단일 가용성 집합에 존재할 수 있는 VM 크기 범위는 하드웨어 클러스터에서 지원하는 VM 크기 범위로 제한됩니다. 가용성 집합에 대한 하드웨어 클러스터는 가용성 집합의 첫 번째 VM이 배포되거나 모든 VM이 현재 중지-할당 해제 상태에 있는 가용성 집합에서 첫 번째 VM을 시작할 때 선택됩니다. CLI 명령인 “az vm list-sizes --location \<string\>”을 사용하여 가용성 집합에 사용할 수 있는 VM 크기 범위를 확인할 수 있습니다.

각 하드웨어 클러스터는 여러 개의 업데이트 도메인 및 장애 도메인을 구분됩니다. 이러한 도메인은 공통 업데이트 주기를 공유하거나 전력 및 네트워킹과 같은 비슷한 실제 인프라를 공유하는 호스트에 의해 정의됩니다. Azure는 가용성 및 내결함성을 유지하기 위해 가용성 집합 내의 VM을 도메인 간에 자동으로 분산합니다. 가용성 집합 내의 응용 프로그램 크기와 VM 수에 따라, 사용하려는 도메인 수를 조정할 수 있습니다. [업데이트 및 장애 도메인의 가용성 및 사용 관리](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대해 자세히 읽어보세요.

또한 응용 프로그램 인프라를 디자인할 때 사용할 응용 프로그램 계층을 계획합니다. 동일한 목적의 VM을 가용성 집합으로 그룹화합니다(예: nginx 또는 Apache를 실행하는 프런트 엔드 VM에 대한 가용성 집합). MongoDB 또는 MySQL을 실행하는 백 엔드 VM에 대한 별도의 가용성 집합을 만듭니다. 응용 프로그램의 각 구성 요소를 가용성 집합을 통해 보호하고 항상 하나 이상의 인스턴스가 실행 상태를 유지하도록 하는 것이 그 목표입니다.

각 응용 프로그램 계층 앞에서 부하 분산 장치를 사용하여 가용성 집합과 함께 작동되도록 하고, 항상 트래픽이 실행 중인 인스턴스로 라우팅되도록 할 수 있습니다. 부하 분산 장치가 없어도 VM이 계획되거나 계획되지 않은 유지 관리 이벤트에서 계속 실행될 수 있지만 주 VM을 사용할 수 없는 경우 최종 사용자가 이러한 이벤트를 해결하지 못할 수 있습니다.

저장소 계층에서 고가용성을 위해 응용 프로그램을 디자인합니다. 가장 좋은 방법은 [가용성 집합의 VM에 Managed Disks를 사용](../linux/manage-availability.md#use-managed-disks-for-vms-in-availability-set)하는 것입니다. 현재 관리되지 않는 디스크를 사용하는 경우 [가용성 집합에서 VM을 변환하여 Managed Disks를 사용](../linux/convert-unmanaged-to-managed-disks.md#convert-vm-in-an-availability-set-to-managed-disks)하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


