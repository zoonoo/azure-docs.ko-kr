---
title: 포털을 사용 하 여 근접 배치 그룹 만들기
description: Azure Portal를 사용 하 여 근접 배치 그룹을 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 6a14e2bd7385430c3d0fbec06259a876af556e38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82190404"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>포털을 사용 하 여 근접 배치 그룹 만들기

가능한 한 가까운 시간 내에 Vm을 가져오기 위해 가장 낮은 대기 시간을 달성 하려면 [근접 배치 그룹](co-location.md#proximity-placement-groups)내에 배포 해야 합니다.

근접 배치 그룹은 Azure 컴퓨팅 리소스가 물리적으로 서로 가까운 위치에 있도록 하는 데 사용되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구하는 작업에 유용합니다.

> [!NOTE]
> 근접 배치 그룹은 전용 호스트와 함께 사용할 수 없습니다.
>
> 배치 그룹과 함께 가용성 영역을 사용 하려는 경우 배치 그룹의 Vm도 모두 동일한 가용성 영역에 있는지 확인 해야 합니다.
>

## <a name="create-the-proximity-placement-group"></a>근접 배치 그룹 만들기

1. 검색에 **근접 배치 그룹** 을 입력 합니다.
1. 검색 결과의 **서비스** 에서 **근접 배치 그룹**을 선택 합니다.
1. **근접 배치 그룹** 페이지에서 **추가**를 선택 합니다.
1. **기본** 탭의 **프로젝트 세부 정보**에서 올바른 구독이 선택되어 있는지 확인합니다.
1. **리소스 그룹** 에서 **새로 만들기** 를 선택 하 여 새 그룹을 만들거나 드롭다운에서 이미 존재 하는 빈 리소스 그룹을 선택 합니다. 
1. **지역** 에서 근접 배치 그룹을 만들려는 위치를 선택 합니다.
1. **근접 배치 그룹 이름** 에 이름을 입력 한 다음 **검토 + 만들기**를 선택 합니다.
1. 유효성 검사를 통과 한 후 **만들기** 를 선택 하 여 근접 배치 그룹을 만듭니다.

    ![근접 배치 그룹을 만드는 스크린샷](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>VM 만들기

1. 포털에서 VM을 만드는 동안 **고급** 탭으로 이동 합니다. 
1. **근접 배치 그룹** 선택에서 올바른 배치 그룹을 선택 합니다. 

    ![포털에서 새 VM을 만들 때 근접 배치 그룹 섹션의 스크린샷](./media/ppg/vm-ppg.png)

1. 필요한 다른 모든 항목을 선택 했으면 **검토 + 만들기**를 선택 합니다.
1. 유효성 검사를 통과 한 후 **만들기** 를 선택 하 여 배치 그룹에 VM을 배포 합니다.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>가용성 집합의 Vm을 근접 배치 그룹에 추가

VM이 가용성 집합의 일부인 경우 Vm을 추가 하기 전에 가용성 집합을 배치 그룹에 추가 해야 합니다.

1. [포털](https://portal.azure.com) 에서 *가용성* 집합을 검색 하 고 결과에서 가용성 집합을 선택 합니다.
1. Vm을 선택한 다음 vm에 대 한 페이지에서 **중지** 를 선택 하 여 가용성 집합에서 각 vm의 할당을 취소 하 고 **확인** 을 선택 하 여 vm을 중지 합니다.
1. 가용성 집합에 대 한 페이지에서 모든 Vm의 **상태가** **중지 됨 (할당 취소 됨)** 으로 표시 되어 있는지 확인 합니다.
1. 왼쪽 메뉴에서 **구성**을 선택 합니다.
1. **근접 배치 그룹**의 드롭다운에서 배치 그룹을 선택 하 고 **저장**을 선택 합니다.
1. 왼쪽 메뉴에서 **개요** 를 선택 하 여 vm 목록을 다시 표시 합니다. 
1. 가용성 집합에서 각 VM을 선택 하 고 각 VM에 대 한 페이지에서 **시작** 을 선택 합니다. 


## <a name="add-existing-vm-to-placement-group"></a>배치 그룹에 기존 VM 추가 


1. VM에 대 한 페이지에서 **중지**를 선택 합니다.
1. VM의 상태가 **중지 됨 (할당 취소 됨)** 으로 표시 되 면 왼쪽 메뉴에서 **구성** 을 선택 합니다.
1. **근접 배치 그룹**의 드롭다운에서 배치 그룹을 선택 하 고 **저장**을 선택 합니다.
1. 왼쪽 메뉴에서 **개요** 를 선택 하 고 **시작** 을 선택 하 여 VM을 다시 시작 합니다.

 

## <a name="next-steps"></a>다음 단계

[Azure PowerShell](proximity-placement-groups.md) 를 사용 하 여 근접 배치 그룹을 만들 수도 있습니다.

