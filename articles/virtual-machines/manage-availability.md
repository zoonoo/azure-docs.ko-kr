---
title: VM의 가용성 관리
description: Azure에서 여러 가상 머신을 사용 하 여 응용 프로그램의 고가용성을 보장 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cynthn
ms.openlocfilehash: 9d9a9c878c96c7f5a38466c494e4b90287c984da
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92734943"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Linux 가상 머신의 가용성 관리

Azure에서 여러 가상 머신을 설정하고 관리하여 Linux 애플리케이션의 고가용성을 유지하는 방법에 대해 알아봅니다. 


## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>VM 다시 부팅 이해 - 유지 관리 및 가동 중지
Azure의 가상 컴퓨터가 초래할 수 있는 세 가지 시나리오, 즉, 계획되지 않은 하드웨어 유지 관리, 예기치 않은 가동 중지 및 계획된 유지 관리가 있습니다.

* **계획되지 않은 하드웨어 유지 관리 이벤트** 는 Azure 플랫폼에서 물리적 컴퓨터와 관련된 하드웨어 또는 플랫폼 구성 요소가 실패할 것으로 예측할 때 발생합니다. 플랫폼에서 오류를 예측하는 경우 계획되지 않은 하드웨어 유지 관리 이벤트를 발급하여 해당 하드웨어에서 호스팅되는 가상 머신에 미치는 영향을 줄입니다. Azure에서는 [실시간 마이그레이션](./maintenance-and-updates.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json%252c%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json%253ftoc%253d%252fazure%252fvirtual-machines%252flinux%252ftoc.json) 기술을 사용하여 오류가 발생한 하드웨어에서 정상적인 물리적 컴퓨터로 Virtual Machines를 마이그레이션합니다. 실시간 마이그레이션은 짧은 시간 동안 Virtual Machine을 일시 중지하는 VM 보존 작업입니다. 메모리, 열린 파일 및 네트워크 연결은 유지되지만 이벤트 전후에 성능이 저하될 수 있습니다. 실시간 마이그레이션을 사용할 수 없는 경우 아래에서 설명한 대로 VM에 예기치 않은 가동 중지가 발생합니다.


* **예기치 않은 가동 중지 시간** 은 가상 머신의 하드웨어 또는 실제 인프라에 예기치 않게 문제가 발생하는 경우입니다. 여기에는 로컬 네트워크 오류, 로컬 디스크 오류 또는 기타 랙 수준의 오류가 포함될 수도 있습니다. 이러한 오류가 감지되면 Azure 플랫폼에서 가상 머신을 동일한 데이터 센터의 정상적인 물리적 컴퓨터로 자동으로 마이그레이션(복구)합니다. 복구 과정 중에 가상 머신에서 가동 중지(재부팅)가 발생하고 경우에 따라 임시 드라이브가 손실됩니다. 연결된 OS 및 데이터 디스크는 항상 유지됩니다.

  전체 데이터 센터 또는 전체 지역에 영향을 주는 가동 중단 또는 재해가 발생한 경우에 가상 머신에는 가동 중지 시간이 발생할 수도 있습니다. 이러한 시나리오의 경우 Azure에서는 [가용성 영역](../availability-zones/az-overview.md) 및 [쌍을 이루는 지역](regions.md#region-pairs)을 비롯한 보호 옵션을 제공합니다.

* **계획된 유지 관리 이벤트** 는 가상 머신이 실행되는 플랫폼 인프라의 전반적인 안정성, 성능 및 보안을 향상시키기 위해 Microsoft에서 기본 Azure 플랫폼에 적용하는 정기적인 업데이트입니다. 이러한 업데이트의 대부분은 Virtual Machines 또는 Cloud Services에 영향을 주지 않고 수행 됩니다 ( [재부팅을 요구 하지 않는 유지 관리](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)참조). Azure 플랫폼에서 가능한 모든 경우에 VM 보존 유지 관리를 사용하려고 시도하는 반면, 가상 머신을 다시 부팅해야 기본 인프라에 필요한 업데이트를 적용할 수 있는 경우가 드물게 있습니다. 이 경우 적절한 기간에 VM에 대한 유지 관리를 시작하여 유지 관리 - 재배포 작업이 포함된 Azure Planned Maintenance(Azure 계획된 유지 관리)를 수행할 수 있습니다. 자세한 내용은 [Virtual Machines에 대한 계획된 유지 관리](maintenance-and-updates.md)를 참조하세요.


이러한 이벤트로 인한 가동 중지 시간의 영향을 줄이기 위해 가상 머신에 다음과 같은 고가용성 모범 사례를 권장합니다.

* 가용성 영역를 사용 하 여 데이터 센터 오류 로부터 보호
* 중복성을 위해 가용성 집합에서 여러 가상 머신 구성
* 가용성 집합에서 VM에 Managed Disks 사용
* 예약된 이벤트를 사용하여 VM에 영향을 주는 이벤트에 사전 응답
* 각 애플리케이션 계층을 별도의 가용성 집합으로 구성
* 가용성 영역 또는 집합과 부하 분산 장치 결합
* 가용성 영역을 사용하여 데이터 센터 수준 오류로부터 사용자를 보호합니다.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>가용성 영역을 사용하여 데이터 센터 수준 오류로부터 사용자를 보호합니다.

[가용성 영역](../availability-zones/az-overview.md)은 VM에 있는 애플리케이션 및 데이터의 가용성을 유지해야 하는 컨트롤 수준을 확장합니다. 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 지역 내에서 가용성 영역의 물리적 구분은 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다. 영역 중복 서비스는 단일 지점 오류에서 보호하기 위해 가용성 영역에서 애플리케이션 및 데이터를 복제합니다.

Azure 지역의 가용성 영역은 **장애 도메인** 과 **업데이트 도메인** 의 조합입니다. 예를 들어 Azure 지역의 3개 영역에 VM을 3개 이상 만들면 장애 도메인 3개와 업데이트 도메인 3개에 VM이 효과적으로 분산됩니다. Azure 플랫폼은 업데이트 도메인에 분산된 VM을 인식하여 다른 영역에 있는 VM이 동시에 업데이트되지 않게 합니다.

Azure는 가용성 영역을 통해 업계 최고의 99.99% VM 작동 시간 SLA를 제공합니다. 영역에 복제된 VM을 사용하는 솔루션을 설계하여 데이터 센터 손실로부터 애플리케이션과 데이터를 보호할 수 있습니다. 하나의 영역이 손상되면 다른 영역에서 복제된 앱 및 데이터를 즉시 사용할 수 있습니다.

![가용성 영역](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

가용성 영역에서 [Windows](./windows/create-powershell-availability-zone.md) 또는 [Linux](./linux/create-cli-availability-zone.md) VM을 배포하는 방법에 대해 자세히 알아봅니다.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>중복성을 위해 가용성 집합에서 여러 가상 머신 구성
가용성 집합은 VM 중복성 및 가용성을 제공하는 또 다른 데이터 센터 구성입니다. 데이터 센터 내의 이러한 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 컴퓨터를 사용할 수 있고 99.95% Azure SLA가 충족되도록 합니다. 자세한 내용은 [Virtual Machines에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 참조하세요.

> [!IMPORTANT]
> 99.9% 이상의 가상 머신 연결을 위한 SLA를 충족하려면, 가용성 집합의 단일 인스턴스 가상 머신에서 순서의 모든 운영 체제 디스크 및 데이터 디스크에 대해 프리미엄 SSD 또는 울트라 SSD를 사용해야 합니다. 
> 
> 표준 SSD를 사용하는 단일 인스턴스 가상 머신의 SLA는 99.5% 이상인 반면, 표준 HDD를 사용하는 단일 인스턴스 가상 머신의 SLA는 95% 이상입니다.  [Virtual Machines에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 참조

기본 Azure 플랫폼에서는 가용성 집합에 포함된 각각의 가상 머신을 **업데이트 도메인** 및 **장애 도메인** 에 할당합니다. 특정 가용성 집합의 경우 기본적으로 사용자가 구성할 수 없는 다섯 개의 업데이트 도메인이 할당되어(그런 다음 최대 20개의 업데이트 도메인을 제공하도록 Resource Manager 배포를 늘릴 수 있음) 동시에 재부팅할 수 있는 가상 머신 및 기본 물리적 하드웨어 그룹을 나타냅니다. 단일 가용성 집합 내에 5개 이상의 가상 머신을 구성한 경우 6번째 가상 머신은 동일한 업데이트 도메인에 첫 번째 가상 머신으로 배치되고, 7번째 가상 머신은 동일한 업데이트 도메인에 두 번째 가상 머신으로 배치되는 식입니다. 재부팅되는 업데이트 도메인의 순서는 계획된 유지 보수 중 순차적으로 진행할 수 없으며 한 번에 하나의 업데이트 도메인만이 재부팅됩니다. 다시 부팅된 업데이트 도메인을 복구할 시간으로 30분이 제공되며 이 시간이 지나면 다른 업데이트 도메인에서 유지 관리가 시작됩니다.

장애 도메인은 공통 전원과 네트워크 스위치를 공유하는 가상 머신 그룹을 정의합니다. 기본적으로 가용성 집합 안에 구성된 가상 머신은 Resource Manager 배포의 경우 최대 3개의 장애 도메인(클래식의 경우 2개의 장애 도메인)으로 분리됩니다. 가상 머신을 가용성 집합에 배치한다고 해서 애플리케이션이 운영 체제 또는 애플리케이션 고유의 오류로부터 보호되는 것은 아닙니다. 잠재적인 물리적 하드웨어 오류, 네트워크 중단, 전력 차단의 영향이 제한될 뿐입니다.

<!--Image reference-->
   ![업데이트 도메인 및 장애 도메인 구성의 개념적 그림](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>가용성 집합에서 VM에 Managed Disks 사용
현재 관리 되지 않는 디스크가 있는 Vm을 사용 하는 경우 관리 되지 않는 [Linux](./linux/convert-unmanaged-to-managed-disks.md) 및 [Windows](./windows/convert-unmanaged-to-managed-disks.md)용 디스크로 변환 하는 것이 좋습니다.

[Managed Disks](./managed-disks-overview.md)는 단일 실패 지점을 피할 만큼 가용성 집합의 VM 디스크가 서로 충분히 격리되도록 하여 가용성 집합에 더 나은 안정성을 제공합니다. 디스크를 다른 스토리지 장애 도메인(스토리지 클러스터)에 자동으로 배치하고 VM 장애 도메인에 맞게 조정하여 작업을 수행합니다. 하드웨어나 소프트웨어 오류로 인해 스토리지 장애 도메인에 장애가 발생하면 스토리지 장애 도메인의 디스크가 있는 VM 인스턴스만 실패합니다.
![Managed Disks FD](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> 관리되는 가용성 집합에 대한 장애 도메인 수는 지역에 따라 다르며 - 지역마다 2개 또는 3개입니다. 다음 스크립트를 실행하여 각 지역의 장애 도메인을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> 특정 상황에서는 동일한 가용성 집합에 있는 두 VM에서 장애 도메인을 공유할 수 있습니다. 가용성 집합으로 이동하고 **장애 도메인** 열을 확인하여 공유 장애 도메인을 확인할 수 있습니다. VM을 배포할 때 다음 순서를 완료하면 공유 장애 도메인이 발생할 수 있습니다.
> 1. 첫 번째 VM을 배포합니다.
> 1. 첫 번째 VM을 중지/할당 취소합니다.
> 1. 두 번째 VM을 배포합니다.
>
> 이러한 경우 두 번째 VM의 OS 디스크가 첫 번째 VM과 동일한 장애 도메인에 만들어질 수 있으므로 두 VM이 동일한 장애 도메인에 있게 됩니다. 이 문제를 방지하려면 배포 간에 VM을 중지/할당 취소하지 않는 것이 좋습니다.

비관리 디스크에서 VM을 사용하려는 경우 VM의 VHD(가상 하드 디스크)를 [페이지 Blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)으로 저장하는 스토리지 계정에 대한 아래의 모범 사례를 따릅니다.

1. **동일한 스토리지 계정에서 VM과 모든 디스크(OS 및 데이터) 연결 유지**
2. **스토리지 계정에 더 많은 VHD를 추가하기 전에 Azure Storage** 계정에서 관리되지 않는 디스크의 수에 대한 [제한](../storage/blobs/scalability-targets-premium-page-blobs.md) 검토
3. **가용성 집합의 각 VM마다 별도의 스토리지 계정을 사용합니다.** 동일한 가용성 집합에서 여러 VM을 사용하여 Storage 계정을 공유하지 않습니다. 위의 모범 사례를 따르면 여러 가용성 집합의 VM에서 스토리지 계정을 공유할 수 있습니다. ![관리되지 않는 디스크 FD](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>예약된 이벤트를 사용하여 VM에 영향을 주는 이벤트에 사전 응답

[예약된 이벤트](./linux/scheduled-events.md)를 구독하면 VM에 영향을 줄 수 있는 예정된 유지 관리 이벤트에 대한 알림이 VM에 전달됩니다. 예약된 이벤트를 사용하도록 설정되면 유지 관리 작업을 수행하기 전에 가상 머신에 최소 시간을 제공할 수 있습니다. 예를 들어 VM에 영향을 줄 수 있는 호스트 OS 업데이트는 아무 작업을 수행하지 않을 경우 유지 관리가 수행되는 시간에 영향을 지정하는 이벤트로 큐에서 대기됩니다. 또한 Azure에서 VM에 영향을 줄 수 있는 임박한 하드웨어 장애를 감지하면 예약된 이벤트가 큐에 넣어지고, 이를 통해 복구를 수행할 시기를 결정할 수 있습니다. 고객은 유지 관리하기 전에 이 이벤트를 사용하여 상태 저장, 보조 스토리지로의 장애 조치 등과 같은 작업을 수행할 수 있습니다. 유지 관리 이벤트를 정상적으로 처리하기 위한 논리가 완료되면 플랫폼에서 유지 관리를 진행할 수 있도록 처리 중인 예약된 이벤트를 승인할 수 있습니다.


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>가용성 영역 또는 집합과 부하 분산 장치 결합
[Azure Load Balancer](../load-balancer/load-balancer-overview.md)를 가용성 영역 또는 집합과 결합하여 애플리케이션 복원력을 극대화합니다. Azure 부하 분산 장치는 트래픽을 여러 가상 머신에 분산시킵니다. 표준 계층 가상 머신의 경우 Azure 부하 분산 장치가 포함되어 있습니다. 모든 가상 머신 계층에 Azure Load Balancer가 포함되어 있는 것은 아닙니다. 가상 컴퓨터의 부하 분산에 대 한 자세한 내용은 [Linux](linux/tutorial-load-balancer.md) 또는 [Windows](windows/tutorial-load-balancer.md)에 대 한 **가상 컴퓨터 부하 분산** 을 참조 하세요.

부하 분산 장치가 트래픽을 여러 가상 머신에 분산시키도록 구성되지 않은 경우에는 계획된 유지 관리 이벤트가 트래픽 처리 가상 머신에만 영향을 줌으로써 애플리케이션 계층에 중단을 일으킬 수 있습니다. 같은 계층의 여러 가상 머신을 같은 부하 분산 장치와 가용성 집합 아래에 배치하면 언제든지 적어도 하나의 인스턴스에서는 트래픽을 계속 처리할 수 있습니다.

가용성 영역 간에 부하를 분산 하는 방법에 대 한 자습서는 [자습서: Azure Portal를 사용 하 여 표준 Load Balancer 가용성 영역 간 Vm 부하 분산](../load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal.md)을 참조 하세요.


## <a name="next-steps"></a>다음 단계
가상 머신 부하 분산에 대한 자세한 내용은 [가상 머신 부하 분산](../load-balancer/load-balancer-overview.md)을 참조하세요.
