---
title: 가상 네트워크 피어링으로 가상 네트워크 연결 - Azure Portal | Microsoft Docs
description: 가상 네트워크 피어링을 사용하여 가상 네트워크를 연결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 네트워크 피어링으로 가상 네트워크 연결

가상 네트워크 피어링을 사용하여 가상 네트워크를 서로 연결할 수 있습니다. 가상 네트워크가 피어링되면 두 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대기 시간 및 대역폭으로 서로 통신할 수 있습니다. 이 아티클에서는 두 가상 네트워크의 생성 및 피어링에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 두 가상 네트워크 만들기
> * 가상 네트워크 간에 피어링 만들기
> * 피어링 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="create-virtual-networks"></a>가상 네트워크 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. 다음 그림과 같이 **이름**으로 *myVirtualNetwork1*을, **주소 공간**으로 *10.0.0.0/16*을, **리소스 그룹**으로 **myResourceGroup**을, 서브넷 **이름**으로 *Subnet1*을, 서브넷 **주소 범위**로 10.0.0.0/24를 입력하고, **위치** 및 **구독**을 선택하고, 나머지 기본값은 그대로 적용한 다음, **만들기**를 선택합니다.

    ![가상 네트워크 만들기](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. 다음과 같은 변경 내용을 사용하여 다시 1~3단계를 완료합니다.
    - **이름**: *myVirtualNetwork2*
    - **리소스 그룹**: **기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다.
    - **주소 공간**: *10.1.0.0/16*
    - **서브넷 주소 범위**: *10.1.0.0/24*

    *myVirtualNetwork2* 가상 네트워크의 주소 접두사는 *myVirtualNetwork1* 가상 네트워크의 주소 공간과 겹치지 않습니다. 겹치는 주소 공간이 있는 가상 네트워크는 피어링할 수 없습니다.

## <a name="peer-virtual-networks"></a>가상 네트워크 피어링

1. Azure Portal 맨 위에 있는 검색 상자에 *MyVirtualNetwork1*을 입력하기 시작합니다. 검색 결과에 **myVirtualNetwork1**이 표시되면 선택합니다.
2. **설정**에서 **피어링**을 선택한 다음, 다음 그림에 표시된 대로 **+추가**를 선택합니다.

    ![피어링 만들기](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. 또는 다음 그림에 표시되는 정보를 입력하고 선택한 다음, **확인**을 선택합니다. *myVirtualNetwork2* 가상 네트워크를 선택하려면 **가상 네트워크**을 선택한 다음, *myVirtualNetwork2*를 선택합니다.

    ![공용 설정](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **피어링 상태**는 다음 그림에 표시된 대로 *시작됨*입니다.

    ![피어링 상태](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    상태가 표시되지 않으면 브라우저를 새로 고칩니다.

4. *myVirtualNetwork2* 가상 네트워크를 검색합니다. 해당 리소스 종류를 검색 결과에서 반환하면 선택합니다.
5. 다음과 같은 변경 내용을 사용하여 다시 1~3단계를 완료한 다음, **OK**를 선택합니다.
    - **이름**: *myVirtualNetwork2-myVirtualNetwork1*
    - **가상 네트워크**: *myVirtualNetwork1*

    **피어링 상태**는 *연결됨*입니다. 또한 Azure에서 *myVirtualNetwork2-myVirtualNetwork1* 피어링의 피어링 상태가 *시작됨*에서 *연결됨*으로 변경되었습니다. 두 가상 네트워크 피어링의 피어링 상태가 *연결됨*이 될 때까지 가상 네트워크 피어링이 완전히 설정되지 않습니다. 

피어링은 두 가상 네트워크 간에 이루어지지만 전이적이지 않습니다. 따라서, 예를 들어 *myVirtualNetwork2*를 *myVirtualNetwork3*에 피어링하려는 경우 가상 네트워크 *myVirtualNetwork2* 및 *myVirtualNetwork3* 간에 추가 피어링을 만들어야 합니다. *myVirtualNetwork1*이 *myVirtualNetwork2*와 피어링되더라도, *myVirtualNetwork1*도 *myVirtualNetwork3*와 피어링되면 *myVirtualNetwork1* 내의 리소스는 *myVirtualNetwork3*의 리소스에만 액세스할 수 있습니다. 

프로덕션 가상 네트워크를 피어링하기 전에, [피어링 개요](virtual-network-peering-overview.md), [피어링 관리](virtual-network-manage-peering.md) 및 [가상 네트워크 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)에 충분히 익숙해지는 것이 좋습니다. 이 아티클에서는 동일한 구독 및 위치에 있는 두 가상 네트워크 간의 피어링을 보여주지만, [서로 다른 지역](#register) 및 [각기 다른 Azure 구독](create-peering-different-subscriptions.md#portal)에 있는 가상 네트워크를 피어링할 수도 있습니다. 피어링을 사용하여 [허브 및 스포크 네트워크 디자인](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)을 만들 수도 있습니다.

## <a name="test-peering"></a>피어링 테스트

피어링을 통해 서로 다른 가상 네트워크의 가상 머신 간 네트워크 통신을 테스트하려면 각 서브넷에 가상 머신을 배포하고 가상 머신 간에 통신을 수행합니다. 

### <a name="create-virtual-machines"></a>가상 머신 만들기

각 가상 네트워크에서 가상 머신을 만들어 이후 단계에서 둘 간의 통신 유효성을 검사할 수 있도록 합니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 다른 운영 체제를 선택할 수 있지만 나머지 단계에서는 **Windows Server 2016 Datacenter**를 선택했다고 가정합니다. 
3. **기본**에 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.
    - **이름**: *myVm1*
    - **리소스 그룹**: **기존 항목 사용**을 선택한 다음, *myResourceGroup*을 선택합니다.
    - **위치**: *미국 동부*를 선택합니다.

    입력하는 **사용자 이름** 및 **암호**는 이후 단계에서 사용됩니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다. 선택한 **위치** 및 **구독**은 가상 네트워크가 있는 위치 및 구독과 동일해야 합니다. 가상 네트워크가 만들어진 리소스 그룹과 동일한 리소스 그룹을 선택할 필요는 없지만 이 아티클에서는 동일한 리소스 그룹이 선택됩니다.
4. **크기 선택**에서 VM 크기를 선택합니다.
5. **설정**에 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.
    - **가상 네트워크**: **myVirtualNetwork1**이 선택되어 있는지 확인합니다. 그렇지 않은 경우에는 **가상 네트워크**를 선택한 다음, **가상 네트워크 선택**에서 **myVirtualNetwork1**을 선택합니다.
    - **서브넷**: **Subnet1**이 선택되어 있는지 확인합니다. 그렇지 않은 경우에는 다음 그림과 같이 **서브넷**을 선택한 다음, **서브넷 선택**에서 **Subnet1**을 선택합니다.
    
        ![가상 머신 설정](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. **요약**의 **만들기**에서 **만들기**를 선택하여 가상 머신 배포를 시작합니다.
7. 1-6 단계를 다시 완료합니다. 단, 가상 머신의 **이름**에 *myVm2*를 입력하고 **Virtual network**에 *myVirtualNetwork2*을 선택합니다.

Azure는 *10.0.0.4*를 *myVm1* 가상 머신의 개인 IP 주소로 할당하고 10.1.0.4를 *myVm2* 가상 머신에 할당했습니다. 해당하는 주소가 *myVirtualNetwork1* 및 *myVirtualNetwork2* 가상 네트워크의 *Subnet1*에서 각각 처음으로 제공되는 IP 주소이기 때문입니다.

가상 머신을 만드는 데 몇 분 정도 걸립니다. 가상 머신 두 개가 모두 만들어 질 때까지 나머지 단계를 수행하지 마십시오.

### <a name="test-virtual-machine-communication"></a>가상 머신 통신 테스트

1. 포털 맨 위에 있는 *검색* 상자에 *myVm1*을 입력하기 시작합니다. 검색 결과에서 표시되는 **myVm1**을 선택합니다.
2. 다음 그림과 같이 **연결**을 선택하여 *myVm1* 가상 머신에 대한 원격 데스크톱 연결을 만듭니다.

    ![가상 머신에 연결](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.
4. 가상 머신을 만들 때 지정한 사용자 이름과 암호를 입력한 다음, **확인**을 선택합니다. (가상 머신을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있습니다.)
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예**를 선택하여 연결을 진행합니다.
6. 이후 단계에서는 ping을 사용하여 *myVmWeb* 가상 머신의 *myVm2* 가상 머신과 통신합니다. Ping은 기본적으로 Windows 방화벽을 통해 거부되는 ICMP를 사용합니다. 명령 프롬프트에서 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 활성화합니다.

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    이 아티클에서는 ping이 테스트용으로 사용되지만, 프로덕션 배포에 Windows 방화벽을 통한 ICMP는 허용하지 않는 것이 좋습니다.

7. *myVm2* 가상 머신에 연결하려면 *myVm1* 가상 머신의 명령 프롬프트에서 다음 명령을 입력합니다.

    ```
    mstsc /v:10.1.0.4
    ```
    
8. 이제 *myVm1*에 ping을 사용하므로 IP 주소에서 ping할 수 있습니다.

    ```
    ping 10.0.0.4
    ```
    
    4개의 응답을 받게 됩니다. 해당 IP 주소가 아닌 가상 머신의 이름(*myVm1*)으로 ping하면 *myVm1*이 알 수 없는 호스트 이름이므로 ping이 실패합니다. Azure의 기본 이름 확인은 다른 가상 네트워크의 가상 머신 간이 아니라 동일한 가상 네트워크의 가상 머신 간에 작동합니다. 여러 가상 네트워크에서 이름을 확인하려면 [자체 DNS 서버를 배포](virtual-networks-name-resolution-for-vms-and-role-instances.md)하거나 [Azure DNS 개인 도메인](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 사용해야 합니다.

9. *myVm1* 및 *myVm2*에 대한 RDP 세션 연결을 모두 끊습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우, 리소스 그룹 및 이 그룹에 포함된 리소스를 모두 삭제합니다. 

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:**에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

**<a name="register"></a>전역 가상 네트워크 피어링 미리 보기에 등록**

동일한 지역의 가상 네트워크 피어링은 일반 공급됩니다. 서로 다른 지역에서 가상 네트워크를 피어링하는 기능은 현재 미리 보기 상태입니다. 사용 가능한 지역에 대해서는 [가상 네트워크 업데이트](https://azure.microsoft.com/updates/?product=virtual-network)를 참조하세요. 지역에 걸쳐 가상 네트워크를 피어링하려면 먼저 미리 보기에 등록해야 합니다. 포털을 사용하여 등록할 수 없지만 [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) 또는 [Azure CLI](tutorial-connect-virtual-networks-cli.md#register)를 사용하여 등록할 수 있습니다. 기능을 등록하기 전에 다른 지역에서 가상 네트워크를 피어링하려고 하면 실패합니다.

## <a name="next-steps"></a>다음 단계

이 아티클에서는 가상 네트워크 피어링을 사용하여 두 네트워크를 연결하는 방법을 배웠습니다.

계속하여 VPN을 통해 고유한 컴퓨터를 가상 네트워크에 연결하고, 가상 네트워크 또는 피어링된 가상 네트워크의 리소스와 상호 작용할 수 있습니다.

> [!div class="nextstepaction"]
> [가상 네트워크에 컴퓨터 연결](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
