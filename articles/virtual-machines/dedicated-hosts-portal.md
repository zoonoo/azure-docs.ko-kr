---
title: Azure Portal를 사용 하 여 Azure 전용 호스트 배포
description: Azure Portal를 사용 하 여 Vm 및 확장 집합을 전용 호스트로 배포 합니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/04/2020
ms.author: cynthn
ms.openlocfilehash: a6bef4944207e26f2de93daa89fa1418c5c44c4f
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373155"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>포털을 사용 하 여 전용 호스트에 Vm 및 확장 집합 배포 

이 문서에서는 VM(가상 머신)을 호스팅하는 Azure [전용 호스트](dedicated-hosts.md)를 만드는 방법을 안내합니다. 


> [!IMPORTANT]
> 이 문서에서는 Vm 및 확장 집합 인스턴스를 자동으로 배치 하는 방법에 대해서도 설명 합니다. 자동 배치는 현재 공개 미리 보기로 제공 됩니다.
> 미리 보기에 참여 하려면에서 미리 보기 온 보 딩 설문 조사를 완료 [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) 합니다.
> Azure Portal의 미리 보기 기능을 사용 하려면 다음 URL을 사용 해야 [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 합니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="limitations"></a>제한 사항

- 전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

## <a name="create-a-host-group"></a>호스트 그룹 만들기

**호스트 그룹**은 전용 호스트의 모음을 나타내는 리소스입니다. 영역 및 가용성 영역에서 호스트 그룹을 만들고 여기에 호스트를 추가합니다. 고가용성을 계획할 때 추가 옵션을 사용할 수 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다를 사용할 수 있습니다. 
- 여러 가용성 영역으로 확장. 이 경우에는 사용하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 실제 랙에 매핑된 여러 장애 도메인으로 확장. 
 
두 경우 모두 호스트 그룹의 장애 도메인 수를 제공해야 합니다. 그룹의 장애 도메인을 확장하지 않으려면 장애 도메인 수 1을 사용합니다. 

가용성 영역 및 장애 도메인을 모두 사용하도록 결정할 수도 있습니다. 

이 예제에서는 1 개의 가용성 영역 및 2 개의 장애 도메인을 사용 하 여 호스트 그룹을 만듭니다. 


1. Azure [portal](https://portal.azure.com)을 엽니다. **자동 배치**에 대 한 미리 보기를 시도 하려면 다음 URL을 사용 [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 합니다.
1. 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
1. **호스트 그룹** 을 검색 하 고 결과에서 **호스트 그룹** 을 선택 합니다.
1. **호스트 그룹** 페이지에서 **만들기**를 선택 합니다.
1. 사용할 구독을 선택한 다음 **새로 만들기** 를 선택 하 여 새 리소스 그룹을 만듭니다.
1. **이름** 으로 *myDedicatedHostsRG* 를 입력 한 다음 **확인을**선택 합니다.
1. **호스트 그룹 이름**에 *myHostGroup*을 입력 합니다.
1. **위치**에 대해 **미국 동부**를 선택합니다.
1. **가용성 영역**에 대해 **1**을 선택 합니다.
1. **오류 도메인 수**에 대해 **2**를 선택 합니다.
1. **자동 배치** URL을 사용한 경우 vm 및 확장 집합 인스턴스를이 그룹의 사용 가능한 호스트에 자동으로 할당 하려면이 옵션을 선택 합니다.
1. **검토 + 만들기** 를 선택한 다음 유효성 검사를 기다립니다.
1. **유효성 검사 통과** 메시지가 표시 되 면 **만들기** 를 선택 하 여 호스트 그룹을 만듭니다.

호스트 그룹을 만드는 데 몇 분 밖에 걸리지 않습니다.


## <a name="create-a-dedicated-host"></a>전용 호스트 만들기

이제 호스트 그룹에서 전용 호스트를 만듭니다. 호스트 이름 외에 호스트의 SKU도 제공해야 합니다. 호스트 SKU는 지원되는 VM 시리즈뿐만 아니라 전용 호스트의 하드웨어 생성도 캡처합니다.

호스트 SKU 및 가격 책정에 대한 자세한 내용은 [Azure Dedicated Host 가격 책정](https://aka.ms/ADHPricing)을 참조하세요.

호스트 그룹의 장애 도메인 수를 설정하면 호스트의 장애 도메인을 지정하라는 메시지가 표시됩니다.  

1. 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
1. **전용 호스트** 를 검색 한 다음 결과에서 **전용 호스트** 를 선택 합니다.
1. **전용 호스트** 페이지에서 **만들기**를 선택 합니다.
1. 사용할 구독을 선택 합니다.
1. **리소스 그룹**으로 *myDedicatedHostsRG* 을 선택 합니다.
1. **인스턴스 세부 정보**에서 **이름** 에 *myhost* 를 입력 하 고 위치에 대해 *미국 동부* 를 선택 합니다.
1. **하드웨어 프로필**에서 **크기 제품군**에 대해 *Standard Es3 family-Type 1* 을 선택 하 고 **호스트 그룹** 에 대해 *myHostGroup* 를 선택한 다음 **장애 도메인**에 대해 *1* 을 선택 합니다. 나머지 필드에 대해서는 기본값을 그대로 둡니다.
1. 완료 되 면 **검토 + 만들기** 를 선택 하 고 유효성 검사를 기다립니다.
1. **유효성 검사 통과** 메시지가 표시 되 면 **만들기** 를 선택 하 여 호스트를 만듭니다.

## <a name="create-a-vm"></a>VM 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
1. Azure Marketplace 리소스 목록 위의 검색 상자에서 사용 하려는 이미지를 검색 하 고 선택한 다음 **만들기**를 선택 합니다.
1. **기본** 정보 탭의 **프로젝트 세부 정보**에서 올바른 구독을 선택 했는지 확인 한 다음 **리소스 그룹**으로 *myDedicatedHostsRG* 을 선택 합니다. 
1. **인스턴스 세부 정보** 아래에서 **가상 머신 이름**에 *myVM*을 입력하고 **위치**에 *미국 동부*를 선택합니다.
1. **가용성 옵션** 에서 **가용성 영역**을 선택 하 고 드롭다운에서 *1* 을 선택 합니다.
1. 크기에 대해 **크기 변경**을 선택 합니다. 사용 가능한 크기 목록에서 **표준 E2s v3**과 같은 Esv3 시리즈 중 하나를 선택 합니다. 사용 가능한 모든 크기를 보려면 필터를 지워야 할 수 있습니다.
1. 필요에 따라 **기본** 탭의 나머지 필드를 완료 합니다.
1. 페이지 위쪽에서 **고급** 탭을 선택 하 고 **호스트** 섹션에서 호스트에 대해 *myHostGroup* **를 선택 하 고 호스트** 에 대해 *myhost* 를 선택 **합니다.** 
    ![호스트 그룹 및 호스트 선택](./media/dedicated-hosts-portal/advanced.png)
1. 나머지 기본값을 그대로 둔 다음, 페이지의 아래에서 **검토 + 만들기** 단추를 선택합니다.
1. 유효성 검사에 통과 되었다는 메시지가 표시 되 면 **만들기**를 선택 합니다.

VM 배포에는 몇 분 정도 걸립니다.

## <a name="create-a-scale-set-preview"></a>크기 집합 만들기 (미리 보기)

> [!IMPORTANT]
> 전용 호스트의 Virtual Machine Scale Sets는 현재 공개 미리 보기로 제공 됩니다.
>
> 미리 보기에 참여 하려면에서 미리 보기 온 보 딩 설문 조사를 완료 [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) 합니다.
>
> Azure Portal의 미리 보기 기능을 사용 하려면 다음 URL을 사용 해야 [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 합니다.
>
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
>
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

확장 집합을 배포 하는 경우 호스트 그룹을 지정 합니다.

1. *확장 집합* 을 검색 하 고 목록에서 **가상 머신 확장 집합** 을 선택 합니다.
1. **추가** 를 선택 하 여 새 확장 집합을 만듭니다.
1. 일반적인 방법으로 **기본** 탭의 필드를 완성 하지만 **Standard E2s v3**와 같이 전용 호스트에 대해 선택한 시리즈의 VM 크기를 선택 해야 합니다.
1. **고급** 탭에서 **분배 알고리즘** 에 대해 **최대 분산**을 선택 합니다.
1. **호스트 그룹**의 드롭다운에서 호스트 그룹을 선택 합니다. 최근에 그룹을 만든 경우 목록에 추가 하는 데 1 분 정도 걸릴 수 있습니다.

## <a name="add-an-existing-vm"></a>기존 VM 추가 

종료 된 VM을 전용 호스트에 추가할 수 있지만 VM은 먼저 Stop\Deallocated. VM을 전용 호스트로 이동 하기 전에 VM 구성이 지원 되는지 확인 합니다.

- VM 크기는 전용 호스트와 동일한 크기의 패밀리에 있어야 합니다. 예를 들어 전용 호스트가 DSv3 인 경우 VM 크기를 Standard_D4s_v3 수 있지만 Standard_A4_v2 수는 없습니다. 
- VM은 전용 호스트와 동일한 지역에 있어야 합니다.
- VM은 근접 배치 그룹에 포함 될 수 없습니다. 전용 호스트로 이동 하기 전에 근접 배치 그룹에서 VM을 제거 합니다. 자세한 내용은 [근접 배치 그룹에서 VM 이동](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group) 을 참조 하세요.
- VM은 가용성 집합에 있을 수 없습니다.
- VM이 가용성 영역에 있는 경우 호스트 그룹과 동일한 가용성 영역 이어야 합니다. VM 및 호스트 그룹의 가용성 영역 설정이 일치 해야 합니다.

[포털](https://portal.azure.com)을 사용 하 여 VM을 전용 호스트로 이동 합니다.

1. VM에 대 한 페이지를 엽니다.
1. 중지를 선택 하 여 VM을 **중지** 합니다.
1. 왼쪽 메뉴에서 **구성** 을 선택 합니다.
1. 드롭다운 메뉴에서 호스트 그룹 및 호스트를 선택 합니다.
1. 완료 되 면 페이지 위쪽에서 **저장** 을 선택 합니다.
1. VM이 호스트에 추가 된 후 왼쪽 메뉴에서 **개요** 를 선택 합니다.
1. 페이지 위쪽에서 **시작** 을 선택 하 여 VM을 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- 지역의 복원력을 극대화하기 위해 영역 및 장애 도메인을 모두 사용하는 샘플 템플릿을 [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)에서 확인할 수 있습니다.

- [Azure CLI](./linux/dedicated-hosts-cli.md) 또는 [PowerShell](./windows/dedicated-hosts-powershell.md)을 사용 하 여 전용 호스트를 배포할 수도 있습니다.
