---
title: 포털을 사용 하 여 Azure 스폿 Vm 배포
description: Azure PowerShell를 사용 하 여 비용을 절감 하는 별색 Vm을 배포 하는 방법입니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0da650646c35a9a663dd29589f963d23cbe552cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828488"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Azure Portal를 사용 하 여 스폿 Vm 배포

[스폿 vm](spot-vms.md) 을 사용 하면 비용을 크게 절약할 수 있는 사용 되지 않는 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스폿 Vm을 제거 합니다. 따라서 지점 Vm은 일괄 처리 작업, 개발/테스트 환경, 대규모 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

지점 Vm의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 VM 가격 책정을 참조 하세요. 최대 가격을 설정 하는 방법에 대 한 자세한 내용은 [지점 vm-가격 책정](spot-vms.md#pricing)을 참조 하세요.

VM에 대해 시간당 요금을 지불할 최대 가격을 설정 하는 옵션이 있습니다. 지점 VM의 최대 가격은 미국 달러 (USD)로 설정 하 여 최대 5 개의 소수 자릿수를 사용할 수 있습니다. 예를 들어 값은 `0.05701` 시간당 $0.05701 USD의 최대 가격이 됩니다. 최대 가격을로 설정 하는 경우 `-1` 가격에 따라 VM이 제거 되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과 하는 경우 더 작은 표준 VM의 현재 가격 또는 가격입니다.

VM을 제거할 때 vm 및 기본 디스크를 삭제 하거나 나중에 다시 시작할 수 있도록 VM의 할당을 취소 하는 옵션이 있습니다.


## <a name="create-the-vm"></a>VM 만들기

VM을 배포 하는 경우 Azure 스폿 인스턴스를 사용 하도록 선택할 수 있습니다.


**기본 탭의** **인스턴스 세부 정보** 섹션에서 **아니요** 는 Azure 스폿 인스턴스 사용에 대 한 기본값입니다.

![아니요를 선택 하기 위한 화면 캡처, Azure 스폿 인스턴스 사용 안 함](./media/spot-portal/no.png)

**예**를 선택 하면 섹션이 확장 되 고 [제거 유형 및 제거 정책을](spot-vms.md#eviction-policy)선택할 수 있습니다. 

![화면 캡처 예를 선택 하 고 Azure 스폿 인스턴스를 사용 합니다.](./media/spot-portal/yes.png)

가격 **책정 기록 보기를 선택 하 고 주변 지역의 가격을 비교**하 여 가격 책정 및 제거 비율을 다른 유사한 지역과 비교할 수도 있습니다.

이 예제에서 캐나다 중부 지역은 저렴 하 고 미국 동부 지역 보다 낮은 제거 요금이 발생 합니다.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="가격 책정 및 제거 비율의 차이를 포함 하는 지역 옵션의 스크린샷":::

가장 적합 한 옵션을 선택 하 고 **확인**을 선택 하 여 지역을 변경할 수 있습니다.

## <a name="simulate-an-eviction"></a>제거 시뮬레이션

응용 프로그램이 갑자기 제거 되는 것을 얼마나 잘 repond 테스트 하기 위해 지점 VM의 [제거를 시뮬레이션할](/rest/api/compute/virtualmachines/simulateeviction) 수 있습니다. 

다음을 사용자의 정보로 바꿉니다. 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>다음 단계

[PowerShell](./windows/spot-powershell.md), [CLI](./linux/spot-cli.md)또는 [템플릿을](./linux/spot-template.md)사용 하 여 스폿 vm을 만들 수도 있습니다.
