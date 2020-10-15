---
title: VNet 피어링으로 가상 네트워크 연결 - 자습서 - Azure Portal
description: 이 자습서에서는 Azure Portal을 사용하여 가상 네트워크 피어링으로 가상 네트워크를 연결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: e95441aab6c8ce7de37ba5f6b08d5f7d54e13347
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77201301"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 가상 네트워크 피어링으로 가상 네트워크 연결

가상 네트워크 피어링을 사용하여 가상 네트워크를 서로 연결할 수 있습니다. 이러한 가상 네트워크는 동일한 지역일 수도 있고 다른 지역(글로벌 VNet 피어링이라고도 함)일 수도 있습니다. 가상 네트워크가 피어링되면 두 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대기 시간 및 대역폭으로 서로 통신할 수 있습니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 두 가상 네트워크 만들기
> * 가상 네트워크 피어링을 사용하여 두 가상 네트워크 연결
> * 각 가상 네트워크에 VM(가상 머신) 배포
> * VM 간 통신

원하는 경우 [Azure CLI](tutorial-connect-virtual-networks-cli.md) 또는 [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md)을 사용하여 이 자습서를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal ( https://portal.azure.com ) 에 로그인합니다.

## <a name="create-virtual-networks"></a>가상 네트워크 만들기

1. Azure Portal에서 **리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. **기본 사항** 탭에서 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용합니다.

    |설정|값|
    |---|---|
    |Subscription| 구독을 선택합니다.|
    |Resource group| **새로 만들기**를 선택하고 *myResourceGroup*을 입력합니다.|
    |지역| **미국 동부**를 선택합니다.|
    |속성|myVirtualNetwork1|

4. **IP 주소** 탭에서 **주소 공간** 필드에 10.0.0.0/16을 입력합니다. 아래 **서브넷 추가** 단추를 클릭하고 **서브넷 이름**에 *Subnet1*을 입력하고 **서브넷 주소 범위**에 대해 10.0.0.0/24를 입력합니다.
5. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다.
   
5. 다음과 같은 변경 내용을 사용하여 1~5단계를 다시 완료합니다.

    |설정|값|
    |---|---|
    |속성|myVirtualNetwork2|
    |주소 공간|10.1.0.0/16|
    |Resource group| **기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다.|
    |서브넷 이름 | Subnet2|
    |서브넷 주소 범위|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>가상 네트워크 피어링

1. Azure Portal 맨 위에 있는 검색 상자에 *MyVirtualNetwork1*을 입력하기 시작합니다. 검색 결과에 **myVirtualNetwork1**이 표시되면 선택합니다.
2. **설정**에서 **피어링**을 선택한 후, 다음 그림에 표시된 대로 **추가**를 선택합니다.

    ![피어링 만들기](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |myVirtualNetwork1에서 원격 가상 네트워크로의 피어링 이름|myVirtualNetwork1-myVirtualNetwork2 - 페이지가 처음 로드될 때 여기에 "원격 가상 네트워크"라는 구가 표시됩니다. 원격 가상 네트워크를 선택한 후 "원격 가상 네트워크"라는 구가 원격 가상 네트워크의 이름으로 바뀝니다.|
    |Subscription| 구독을 선택합니다.|
    |가상 네트워크|myVirtualNetwork2 - *myVirtualNetwork2* 가상 네트워크를 선택하려면 **가상 네트워크**을 선택한 다음, **myVirtualNetwork2(myResourceGroup)** 를 선택합니다. 동일한 지역 또는 다른 지역의 가상 네트워크를 선택할 수 있습니다.|
    |myVirtualNetwork2에서 myVirtualNetwork1로 피어링 이름 지정|myVirtualNetwork2-myVirtualNetwork1|

    ![공용 설정](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    **피어링 상태**는 다음 그림에 표시된 대로 *연결됨*입니다.

    ![피어링 상태](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    상태가 표시되지 않으면 브라우저를 새로 고칩니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

이후 단계에서 서로 통신할 수 있도록 각 가상 네트워크에서 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

1. Azure Portal에서 **리소스 만들기**를 선택합니다.
2. **Compute**를 선택한 다음, **Windows Server 2016 Datacenter**를 선택합니다. 다른 운영 체제를 선택할 수 있지만 나머지 단계에서는 **Windows Server 2016 Datacenter**를 선택했다고 가정합니다. 
3. **기본 사항**에 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    |설정|값|
    |---|---|
    |Resource group| **기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다.|
    |속성|myVm1|
    |위치| **미국 동부**를 선택합니다.|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
   
4. **크기** 옵션에 대해 VM 크기를 선택합니다.
5. **네트워킹**에서 다음 값을 선택합니다.

    |설정|값|
    |---|---|
    |가상 네트워크| myVirtualNetwork1 - 아직 선택하지 않은 경우 **가상 네트워크**를 선택한 다음, **myVirtualNetwork1**을 선택합니다.|
    |서브넷| Subnet1 - 아직 선택하지 않은 경우 **서브넷**을 선택한 다음, **Subnet1**을 선택합니다.|
   
6. **네트워킹**을 선택합니다. **공용 인바운드 포트** 옵션에 대해 **선택한 포트 허용**을 선택합니다. 아래 **인바운드 포트 선택** 옵션에 대해 **RDP**를 선택합니다. 

7. 왼쪽 아래 모서리에 있는 **검토 + 만들기** 단추를 선택하여 VM 배포를 시작합니다.

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

다음과 같은 변경 내용을 사용하여 다시 1~6단계를 완료합니다.

|설정|값|
|---|---|
|속성 | myVm2|
|가상 네트워크 | myVirtualNetwork2|

VM을 만드는 데 몇 분이 걸릴 수 있습니다. 두 VM이 모두 만들어질 때까지 나머지 단계를 수행하지 마세요.

## <a name="communicate-between-vms"></a>VM 간 통신

1. 포털 맨 위에 있는 *검색* 상자에 *myVm1*을 입력하기 시작합니다. 검색 결과에서 표시되는 **myVm1**을 선택합니다.
2. 다음 그림과 같이 **연결**을 선택하여 *myVm1* VM에 대한 원격 데스크톱 연결을 만듭니다.

    ![가상 머신에 연결](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.
4. VM을 만들 때 지정한 사용자 이름과 암호를 입력(VM을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있음)한 다음, **확인**을 선택합니다.
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예**를 선택하여 연결을 진행합니다.
6. 이후 단계에서는 ping을 사용하여 *myVm1* VM의 *myVm2* VM과 통신합니다. ping은 ICMP(Internet Control Message Protocol)를 사용하는데, ICMP는 기본적으로 Windows 방화벽에서 허용되지 않습니다. *myVm1* VM에서 이후 단계에서 PowerShell을 사용하여 *myVm2*에서 이 VM을 ping할 수 있도록 Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    이 자습서에서는 VM 간 통신에 ping이 사용되지만, 프로덕션 배포에 Windows 방화벽을 통한 ICMP는 허용하지 않는 것이 좋습니다.

7. *myVm2* VM에 연결하려면 *myVm1* VM의 명령 프롬프트에서 다음 명령을 입력합니다.

    ```
    mstsc /v:10.1.0.4
    ```
    
8. 이제 *myVm1*에 ping을 사용하므로 IP 주소에서 ping할 수 있습니다.

    ```
    ping 10.0.0.4
    ```
    
9. *myVm1* 및 *myVm2*에 대한 RDP 세션 연결을 모두 끊습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우, 리소스 그룹 및 이 그룹에 포함된 리소스를 모두 삭제합니다. 

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 네트워크 피어링을 사용하여 동일한 Azure 지역에 있는 두 네트워크를 연결하는 방법을 배웠습니다. 다른 [지원되는 지역](virtual-network-manage-peering.md#cross-region)과 [다른 Azure 구독](create-peering-different-subscriptions.md#portal)에 있는 가상 네트워크를 피어링하고 피어링을 사용하여 [허브 및 스포크 네트워크 디자인](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering)을 만들 수도 있습니다. 가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링 개요](virtual-network-peering-overview.md) 및 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md)를 참조하세요.

VPN을 통해 고유한 컴퓨터를 가상 네트워크에 연결하고 가상 네트워크 또는 피어링된 가상 네트워크의 리소스와 상호 작용하려면 [가상 네트워크에 컴퓨터 연결](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.
