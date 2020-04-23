---
title: 포털을 사용 하 여 근접 배치 그룹 만들기
description: Azure Portal를 사용 하 여 근접 배치 그룹을 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: aaecfbd14289840e795c6323737877e267586e16
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098648"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>포털을 사용 하 여 근접 배치 그룹 만들기

가능한 한 가까운 시간 내에 Vm을 가져오기 위해 가장 낮은 대기 시간을 달성 하려면 [근접 배치 그룹](co-location.md#proximity-placement-groups)내에 배포 해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까운 위치에 있는지 확인 하는 데 사용 되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구 하는 작업에 유용 합니다.


## <a name="create-the-proximity-placement-group"></a>근접 배치 그룹 만들기

1. 검색에 **근접 배치 그룹** 을 입력 합니다.
1. 검색 결과의 **서비스** 에서 **근접 배치 그룹**을 선택 합니다.
1. **근접 배치 그룹** 페이지에서 **추가**를 선택 합니다.
1. **기본** 탭의 **프로젝트 세부 정보**에서 올바른 구독이 선택되어 있는지 확인합니다.
1. **리소스 그룹** 에서 **새로 만들기** 를 선택 하 여 새 그룹을 만들거나 드롭다운에서 기존 리소스 그룹을 선택 합니다.
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




## <a name="next-steps"></a>다음 단계

[Azure PowerShell](proximity-placement-groups.md) 를 사용 하 여 근접 배치 그룹을 만들 수도 있습니다.

