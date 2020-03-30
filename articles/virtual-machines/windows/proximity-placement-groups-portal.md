---
title: 포털을 사용하여 근접 배치 그룹 만들기
description: Azure 포털을 사용하여 근접 배치 그룹을 만드는 방법에 대해 알아봅니다.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180252"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>포털을 사용하여 근접 배치 그룹 만들기

VM을 가능한 한 가깝게 설정하여 가능한 가장 낮은 대기 시간을 달성하려면 [근접 배치 그룹](co-location.md#proximity-placement-groups)내에 배포해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까이 있는지 확인하는 데 사용되는 논리적 그룹입니다. 근접 배치 그룹은 대기 시간이 짧은 워크로드에 유용합니다.


## <a name="create-the-proximity-placement-group"></a>근접 배치 그룹 만들기

1. 검색에서 **근접 배치 그룹을** 입력합니다.
1. 검색 결과의 **서비스에서** 근접 **배치 그룹을**선택합니다.
1. 근접 **배치 그룹 페이지에서** **추가를**선택합니다.
1. **기본** 탭의 **프로젝트 세부 정보**에서 올바른 구독이 선택되어 있는지 확인합니다.
1. **리소스 그룹에서** **새 만들기를** 선택하여 새 그룹을 만들거나 드롭다운에서 기존 리소스 그룹을 선택합니다.
1. **지역에서** 근접 배치 그룹을 만들 위치를 선택합니다.
1. **근접 배치 그룹 이름에서** 이름을 입력한 다음 검토 + **만들기를**선택합니다.
1. 유효성 검사가 통과되면 **만들기를** 선택하여 근접 배치 그룹을 작성합니다.

    ![근접 배치 그룹을 만드는 스크린샷](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>VM 만들기

1. 포털에서 VM을 만드는 동안 **고급** 탭으로 이동합니다. 
1. 근접 **배치 그룹** 선택에서 올바른 배치 그룹을 선택합니다. 

    ![포털에서 새 VM을 만들 때 근접 배치 그룹 섹션의 스크린샷](./media/ppg/vm-ppg.png)

1. 다른 모든 필수 선택 항목을 완료하면 **검토 + 만들기를**선택합니다.
1. 유효성 검사를 통과한 후 배치 그룹에 VM을 배포하려면 **만들기를** 선택합니다.




## <a name="next-steps"></a>다음 단계

[Azure PowerShell을](proximity-placement-groups.md) 사용하여 근접 배치 그룹을 만들 수도 있습니다.

