---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471461"
---
예약 된 VM 인스턴스를 구입 하는 경우 예를 들어 크기 유연성 또는 용량 우선 순위에 대 한 최적화하도록 선택할 수 있습니다. 예약된 VM 인스턴스에 대한 최적화 설정 또는 변경에 대한 자세한 내용은 [예약된 VM 인스턴스에 대한 최적화 설정 변경을](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)참조하십시오.

인스턴스 크기 유연성에 최적화된 예약된 가상 시스템 인스턴스를 사용하면 구매한 예약이 동일한 인스턴스 크기 유연성 그룹의 가상 컴퓨터(VM) 크기에 적용할 수 있습니다. 예를 들어 Standard_DS5_v2 같이 DSv2 시리즈에 나열된 VM 크기에 대한 예약을 구매하는 경우 동일한 인스턴스 크기 유연성 그룹에 나열된 다른 네 가지 크기에 예약 할인이 적용될 수 있습니다.

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

그러나 예약 할인은 DSv2 시리즈 하이 메모리: Standard_DS11_v2, Standard_DS12_v2 등 다양한 인스턴스 크기 유연성 그룹에 나열된 VM 크기에는 적용되지 않습니다.

인스턴스 크기 유연성 그룹 내에서 예약 할인이 적용되는 VM 수는 예약을 구매할 때 선택한 VM 크기에 따라 다릅니다. 또한 실행하는 VM의 크기에 따라서도 달라집니다. 비율 열은 해당 인스턴스 크기 유연성 그룹의 각 VM 크기에 대한 상대 적 공간을 비교합니다. 예약 할인을 실행하고 있는 VM에 적용하는 방법을 계산하기 위해 비율 값을 사용합니다.

## <a name="examples"></a>예

다음 예제에서는 DSv2 시리즈 테이블의 크기 및 비율을 사용합니다.

크기의 비율 또는 해당 계열에서 다른 크기에 비해 상대적 공간은 8 Standard_DS4_v2 예약 VM 인스턴스를 구입 합니다.

- 시나리오 1: 1의 비율로 8대의 Standard_DS1_v2 크기 VM을 실행합니다. 예약 할인은 8대의 모든 해당 VM에 적용됩니다.
- 시나리오 2: 각각 2의 비율로 2대의 Standard_DS2_v2 크기 VM을 실행합니다. 또한 4의 비율로 Standard_DS3_v2 크기 VM을 실행합니다. 총 공간은 2+2+4=8입니다. 따라서 예약 할인은 3대의 모든 해당 VM에 적용됩니다.
- 시나리오 3: 16의 비율로 한 대의 Standard_DS5_v2를 실행합니다. 예약 할인은 해당 VM의 컴퓨팅 비용 절반에 적용됩니다.

다음 섹션에서는 인스턴스 크기 유연성에 최적화된 예약 VM 인스턴스를 구매하는 경우 동일한 크기 시리즈 그룹에 있는 크기를 보여줍니다.

## <a name="instance-size-flexibility-ratio-for-vms"></a>VM의 인스턴스 크기 유연성 비율 

아래 CSV에는 인스턴스 크기 유연성 그룹, ArmSkuName 및 비율이 있습니다.  

[인스턴스 크기 유연성 비율](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

이 파일을 프로그래밍 방식으로 사용할 수 있도록 파일 URL과 스키마를 고정된 상태로 유지합니다. 데이터는 곧 API를 통해 사용할 수 있습니다.
