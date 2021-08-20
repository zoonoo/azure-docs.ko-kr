---
title: VNet 피어링으로 가상 네트워크 연결 - 자습서 - Azure Portal
description: 이 자습서에서는 Azure Portal을 사용하여 가상 네트워크 피어링으로 가상 네트워크를 연결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/06/2021
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: ca8829a5f71dabfe33f013c354e083cae08e4566
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430764"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 가상 네트워크 피어링으로 가상 네트워크 연결

가상 네트워크 피어링을 사용하여 가상 네트워크를 서로 연결할 수 있습니다. 이러한 가상 네트워크는 동일한 지역일 수도 있고 다른 지역(글로벌 VNet 피어링이라고도 함)일 수도 있습니다. 가상 네트워크가 피어링되면 두 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대기 시간 및 대역폭으로 서로 통신할 수 있습니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 두 가상 네트워크 만들기
> * 가상 네트워크 피어링을 사용하여 두 가상 네트워크 연결
> * 각 가상 네트워크에 VM(가상 머신) 배포
> * VM 간 통신

원하는 경우 [Azure CLI](tutorial-connect-virtual-networks-cli.md) 또는 [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md)을 사용하여 이 자습서를 완료할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 활성 구독이 포함된 Azure 계정이 필요합니다. 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

## <a name="create-virtual-networks"></a>가상 네트워크 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **+ 리소스 만들기** 를 선택합니다.

1. **가상 네트워크** 를 검색한 다음, **만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vnet.png" alt-text="가상 네트워크용 리소스 만들기 스크린샷":::

1. **기본 사항** 탭에서 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용합니다.

    |설정|값|
    |---|---|
    |Subscription| 구독을 선택합니다.|
    |Resource group| **새로 만들기** 를 선택하고 *myResourceGroup* 을 입력합니다.|
    |지역| **미국 동부** 를 선택합니다.|
    |Name|myVirtualNetwork1|

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-basic-tab.png" alt-text="가상 네트워크 만들기 기본 사항 탭 스크린샷":::

1. **IP 주소** 탭에서 **주소 공간** 필드에 *10.0.0.0/16* 을 입력합니다. 아래 **서브넷 추가** 단추를 클릭한 다음, **서브넷 이름** 에 *Subnet1* 을 입력하고 **서브넷 주소 범위** 에 *10.0.0.0/24* 를 입력합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/ip-addresses-tab.png" alt-text="가상 네트워크 만들기 IP 주소 탭 스크린샷":::

1. **검토 및 만들기** 를 선택한 후 **만들기** 를 선택합니다.
   
1. 1~5단계를 다시 반복하여 다음 설정으로 두 번째 가상 네트워크를 만듭니다.

    | 설정 | 값 |
    | --- | --- |
    | 이름 | myVirtualNetwork2 |
    | 주소 공간 | 10.1.0.0/16 |
    | Resource group | **기존 항목 사용** 을 선택한 다음, **myResourceGroup** 을 선택합니다.|
    | 서브넷 이름 | Subnet2 |
    | 서브넷 주소 범위 | 10.1.0.0/24 |

## <a name="peer-virtual-networks"></a>가상 네트워크 피어링

1. Azure Portal 맨 위에 있는 검색 상자에서 *myVirtualNetwork1* 을 찾습니다. 검색 결과에 **myVirtualNetwork1** 이 표시되면 선택합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vnet.png" alt-text="myVirtualNetwork1 검색 스크린샷":::

1. 다음 그림과 같이 **설정** 에서 **피어링** 을 선택하고 **+ 추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-peering.png" alt-text="myVirtualNetwork1에 대한 피어링 만들기 스크린샷":::
    
1. 다음 정보를 입력하거나 선택하고 나머지 설정은 기본값을 그대로 적용한 다음, **추가** 를 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | 이 가상 네트워크 - 피어링 링크 이름 | myVirtualNetwork1에서 원격 가상 네트워크로의 피어링 이름입니다.  이 연결에는 *myVirtualNetwork1-myVirtualNetwork2* 가 사용됩니다.|
    | 원격 가상 네트워크 - 피어링 링크 이름 |  원격 가상 네트워크에서 myVirtualNetwork1로의 피어링 이름입니다. 이 연결에는 *myVirtualNetwork2-myVirtualNetwork1* 이 사용됩니다. |
    | Subscription | 구독을 선택합니다.|
    | 가상 네트워크  | 동일한 지역 또는 다른 지역의 가상 네트워크를 선택할 수 있습니다. 드롭다운 목록에서 *myVirtualNetwork2* 를 선택합니다. |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png" alt-text="가상 네트워크 피어링 구성 스크린샷" lightbox="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional-expanded.png":::

    **피어링 상태** 는 다음 그림에 표시된 대로 *연결됨* 입니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png" alt-text="가상 네트워크 피어링 연결 상태 스크린샷":::

    ‘연결됨’ 상태가 표시되지 않으면 **새로 고침** 단추를 선택합니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크 간 통신을 테스트할 수 있도록 각 가상 네트워크에서 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

1. Azure Portal에서 **+ 리소스 만들기** 를 선택합니다.

1. **컴퓨팅** 을 선택한 다음, ‘가상 머신’에서 **만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm.png" alt-text="가상 머신용 리소스 만들기 스크린샷":::

1. **기본 사항** 탭에서 다음 정보를 입력하거나 선택하고 나머지 설정은 기본값을 그대로 적용한 다음, **만들기** 를 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | Resource group| **기존 항목 사용** 을 선택한 다음, **myResourceGroup** 을 선택합니다. |
    | 속성 | myVm1 |
    | 위치 | **미국 동부** 를 선택합니다. |
    | 이미지 | OS 이미지를 선택합니다. 이 VM의 경우 *Windows Server 2019 Datacenter - Gen1* 이 선택되었습니다. |
    | 크기 | VM 크기를 선택합니다. 이 VM의 경우 *Standard_D2s_v3* 이 선택되었습니다. |
    | 사용자 이름 | 사용자 이름을 입력합니다. 이 예제에서는 사용자 이름 *azure* 가 선택되었습니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다. |
   
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab.png" alt-text="가상 머신 기본 사항 탭 구성 스크린샷" lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab-expanded.png":::

1. **네트워킹** 탭에서 다음 값을 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | 가상 네트워크 | myVirtualNetwork1 - 아직 선택하지 않은 경우 **가상 네트워크** 를 선택한 다음, **myVirtualNetwork1** 을 선택합니다. |
    | 서브넷 | Subnet1 - 아직 선택하지 않은 경우 **서브넷** 을 선택한 다음, **Subnet1** 을 선택합니다. |
    | 공용 인바운드 포트 | *선택한 포트 허용* |
    | 인바운드 포트 선택 | *RDP(3389)* |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab.png" alt-text="가상 머신 네트워킹 탭 구성 스크린샷" lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab-expanded.png":::

1. **검토 + 만들기**, **만들기** 를 차례로 선택하여 VM 배포를 시작합니다.

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

1~6단계를 다시 반복하여 다음과 같이 변경된 두 번째 가상 머신을 만듭니다.

| 설정 | 값 |
| --- | --- |
| 속성 | myVm2 |
| 가상 네트워크 | myVirtualNetwork2 |

VM을 만드는 데 몇 분이 걸릴 수 있습니다. 두 VM이 모두 만들어질 때까지 나머지 단계를 수행하지 마세요.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="communicate-between-vms"></a>VM 간 통신

1. 포털 맨 위에 있는 검색 상자에서 *myVm1* 을 찾습니다. 검색 결과에서 표시되는 **myVm1** 을 선택합니다.
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vm.png" alt-text="myVm1 검색 스크린샷":::

1. 가상 머신에 연결하려면 **연결** 을 선택한 다음, 드롭다운에서 **RDP** 를 선택합니다. **RDP 파일 다운로드** 를 선택하여 원격 데스크톱 파일을 다운로드합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png" alt-text="가상 머신에 연결 단추 스크린샷"::: 

1. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결** 을 선택합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-connect.png" alt-text="원격 데스크톱 연결 화면 스크린샷":::

1. VM을 만들 때 지정한 사용자 이름과 암호를 입력(VM을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택** 을 선택한 다음, **다른 계정 사용** 을 선택해야 할 수도 있음)한 다음, **확인** 을 선택합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-credentials.png" alt-text="RDP 자격 증명 화면 스크린샷":::

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 를 선택하여 연결을 계속 진행합니다.

1. 이후 단계에서 ping을 사용하여 *myVm2* VM에서 *myVm1* VM과 통신합니다. ping은 ICMP(Internet Control Message Protocol)를 사용하는데, ICMP는 기본적으로 Windows 방화벽에서 허용되지 않습니다. *myVm1* VM에서 이후 단계에서 PowerShell을 사용하여 *myVm2* 에서 이 VM을 ping할 수 있도록 Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    이 자습서에서는 VM 간 통신에 ping을 사용하지만, 프로덕션 배포의 경우 Windows 방화벽을 통한 ICMP를 허용하지 않는 것이 좋습니다.

1. *myVm2* VM에 연결하려면 *myVm1* VM의 명령 프롬프트에서 다음 명령을 입력합니다.

    ```
    mstsc /v:10.1.0.4
    ```
    
1. 이제 *myVm1* 에 ping을 사용하므로 IP 주소에서 ping할 수 있습니다.

    ```
    ping 10.0.0.4
    ```
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/myvm2-ping-myvm1.png" alt-text="myVM1을 ping하는 myVM2 스크린샷":::

1. *myVm1* 및 *myVm2* 에 대한 RDP 세션 연결을 모두 끊습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우, 리소스 그룹 및 이 그룹에 포함된 리소스를 모두 삭제합니다. 

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup* 을 입력합니다. 검색 결과에 **myResourceGroup** 이 보이면 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. **리소스 그룹 이름 입력:** 에 *myResourceGroup* 을 입력하고 **삭제** 를 선택합니다.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/delete-resource-group.png" alt-text="리소스 그룹 삭제 페이지 스크린샷":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [가상 네트워크 피어링에 대한 자세한 정보](virtual-network-peering-overview.md)
