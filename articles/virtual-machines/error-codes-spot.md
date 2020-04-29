---
title: Azure 스폿 Vm 및 확장 집합 인스턴스의 오류 코드
description: 스폿 Vm 및 확장 집합 인스턴스를 사용할 때 나타날 수 있는 오류 코드에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80547809"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>지점 Vm 및 확장 집합에 대 한 오류 메시지

다음은 스폿 Vm 및 확장 집합을 사용할 때 받을 수 있는 몇 가지 오류 코드입니다.


| 키 | 메시지 | Description |
|-----|---------|-------------|
| SkuNotAvailable | '\<\>Resource ' 리소스에 대해 요청 된 계층은 현재 '\<\>\<subscriptionID\>' 구독에 대 한 ' location ' 위치에서 사용할 수 없습니다. 다른 계층을 시도 하거나 다른 위치에 배포 하세요. | 이 위치에는 VM 또는 확장 집합 인스턴스를 만들 수 있는 Azure 스폿 용량이 부족 합니다. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  제거 정책은 Azure 스폿 Virtual Machines에만 설정할 수 있습니다. | 이 VM은 스폿 VM이 아니므로 제거 정책을 설정할 수 없습니다. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Azure 스폿 가상 머신은 가용성 집합에서 지원 되지 않습니다. | 지점 VM을 사용 하거나 가용성 집합에서 VM을 사용 하도록 선택 해야 합니다. 둘 다 선택할 수는 없습니다. |
| AzureSpotFeatureNotEnabledForSubscription  |  구독이 Azure 스폿 기능으로 사용 하도록 설정 되지 않았습니다. | 지점 Vm을 지 원하는 구독을 사용 합니다. |
| VMPriorityCannotBeApplied  |  가상 컴퓨터를 만들 때{0}우선 순위를 지정 하지 않았으므로 지정 된 우선{1}순위 값 ' '을 (를) 가상 컴퓨터 ' '에 적용할 수 없습니다. | VM을 만들 때의 우선 순위를 지정 합니다. |
| SpotPriceGreaterThanProvidedMaxPrice  |  제공 된 최대 가격 '{0}{1} Usd '가 Azure 스폿 VM 크기 '{2} {3}'에 대 한 현재 스폿 가격 ' usd ' 보다 낮기 때문에 ' ' 작업을 수행할 수 없습니다. | 높은 최대 가격을 선택 합니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 또는 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 가격 정보를 참조 하세요.|
| MaxPriceValueInvalid  |  최대 가격 값이 잘못 되었습니다. 최대 가격에 대해 지원 되는 유일한 값은-1 또는 0 보다 큰 10 진수입니다. 최대 가격 값-1은 Azure 스폿 가상 머신이 가격 이유로 제거 되지 않음을 나타냅니다. | 유효한 최대 가격을 입력 하세요. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 또는 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 가격 책정을 참조 하세요. |
| MaxPriceChangeNotAllowedForAllocatedVMs | VM ' '이 ({0}가) 현재 할당 되어 있는 경우에는 최대 가격 변경이 허용 되지 않습니다. 할당을 취소 하 고 다시 시도 하세요. | 최대 가격을 변경할 수 있도록 VM을 할당 취소 합니다. |
| MaxPriceChangeNotAllowed | 최대 가격 변경이 허용 되지 않습니다. | 이 VM에 대 한 최대 가격은 변경할 수 없습니다. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure 스팟은이 API 버전에서 지원 되지 않습니다. | API 버전은 2019-03-01 이어야 합니다. |
| AzureSpotIsNotSupportedForThisVMSize  |  이 VM 크기 {0}에 대 한 Azure 스팟은 지원 되지 않습니다. | 다른 VM 크기를 선택 합니다. 자세한 내용은 [지점 Virtual Machines](./linux/spot-vms.md)를 참조 하세요. |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  최대 가격은 Azure 스폿 Virtual Machines에만 지원 됩니다. | 자세한 내용은 [지점 Virtual Machines](./linux/spot-vms.md)를 참조 하세요. |
| MoveResourcesWithAzureSpotVMNotSupported  |  리소스 이동 요청은 Azure 스폿 가상 머신을 포함 합니다. 현재는 지원되지 않습니다. 가상 컴퓨터 Id에 대 한 오류 정보를 확인 하세요. | 지점 Vm을 이동할 수 없습니다. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  리소스 이동 요청은 Azure 스폿 가상 머신 확장 집합을 포함 합니다. 현재는 지원되지 않습니다. 가상 머신 확장 집합 Id에 대 한 오류 세부 정보를 확인 하세요. | 별색 확장 집합 인스턴스를 이동할 수 없습니다. |
| EphemeralOSDisksNotSupportedForSpotVMs | 임시 OS 디스크는 별색 Vm에 대해 지원 되지 않습니다. | 지점 VM에 대 한 일반 OS 디스크를 사용 합니다. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Azure 스폿 가상 머신은 VM 오케스트레이션 모드를 사용 하는 가상 머신 확장 집합에서 지원 되지 않습니다. | 스폿 인스턴스를 사용 하려면 오케스트레이션 모드를 가상 머신 확장 집합으로 설정 합니다. |


**다음 단계** 자세한 내용은 [지점 Virtual Machines](./linux/spot-vms.md)를 참조 하세요.