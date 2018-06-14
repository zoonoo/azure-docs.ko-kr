---
title: 여러 서브넷이 있는 Azure Virtual Network 만들기 - Portal | Microsoft Docs
description: Azure Portal을 사용하여 여러 서브넷이 있는 가상 네트워크를 만드는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880483"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Azure Portal을 사용하여 여러 서브넷이 있는 가상 네트워크 만들기

가상 네트워크를 사용하면 여러 유형의 Azure 리소스가 인터넷과 통신할 수 있고 서로 비공개적으로 통신할 수 있습니다. 가상 네트워크에 여러 서브넷을 만들면 네트워크를 분할하여 서브넷 간의 트래픽 흐름을 필터링하거나 제어할 수 있습니다. 이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 서브넷 만들기
> * 가상 머신 간의 네트워크 통신 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. 다음 그림과 같이 **이름**으로 *myVirtualNetwork*를, **주소 공간**으로 *10.0.0.0/16*을, **리소스 그룹**으로 **myResourceGroup**을, 서브넷 **이름**에 *Public*을, 서브넷 **주소 범위**에 10.0.0.0/24를 입력하고 **위치** 및 **구독**을 선택한 다음, 나머지 기본값은 그대로 적용하고 **만들기**를 선택합니다.

    ![가상 네트워크 만들기](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    **주소 공간** 및 **주소 범위**는 CIDR 표기법으로 지정됩니다. 지정된 **주소 공간**에는 IP 주소 10.0.0.0-10.0.255.254가 포함됩니다. 서브넷에 지정된 **주소 범위**는 가상 네트워크에 대해 정의된 **주소 공간** 내에 있어야 합니다. Azure DHCP는 서브넷 주소 범위의 IP 주소를 서브넷에 배포된 리소스에 할당합니다. Azure는 **Public** 서브넷에 배포된 리소스에 10.0.0.4-10.0.0.254 주소만 할당합니다. Azure에서 각 서브넷에 앞쪽 주소 4개(이 예제의 경우 서브넷에 10.0.0.0-10.0.0.3)와 마지막 주소(이 예제의 경우 서브넷에 10.0.0.255)를 예약하기 때문입니다.

## <a name="create-a-subnet"></a>서브넷 만들기

1. 포털 맨 위에 있는 **리소스, 서비스 및 문서 검색** 상자에 *myVirtualNetwork*를 입력하기 시작합니다. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.
2. 다음 그림과 같이 **서브넷**을 선택한 다음, **+ 서브넷**을 선택합니다.

     ![서브넷 추가](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. **서브넷 추가** 상자가 표시되면 **이름**에 *Private*을 입력하고 **주소 범위**에 *10.0.1.0/24*를 입력한 다음, **확인**을 선택합니다.  서브넷 주소 범위는 가상 네트워크 내 다른 서브넷의 주소 범위와 겹칠 수 없습니다. 

프로덕션용으로 Azure 가상 네트워크와 서브넷을 배포하기 전에 주소 공간 [고려 사항](manage-virtual-network.md#create-a-virtual-network) 및 [가상 네트워크 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 철저히 숙지하는 것이 좋습니다. 리소스가 서브넷에 배포된 후에, 일부 가상 네트워크 및 서브넷이 변경되어(예: 주소 범위 변경) 서브넷에 배포된 기존 Azure 리소스를 다시 배포하는 것이 필요할 수 있습니다.

## <a name="test-network-communication"></a>네트워크 통신 테스트

가상 네트워크를 사용하면 여러 유형의 Azure 리소스가 인터넷과 통신할 수 있고 서로 비공개적으로 통신할 수 있습니다. 가상 네트워크에 배포할 수 있는 한 가지 유형의 리소스는 가상 머신입니다. 가상 네트워크에 가상 머신을 두 개 만들어서 나중에 인터넷 및 가상 머신 사이의 네트워크 통신을 테스트 할 수 있습니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 다른 운영 체제를 선택할 수 있지만 나머지 단계에서는 **Windows Server 2016 Datacenter**를 선택했다고 가정합니다. 
3. **기본**에 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.
    - **이름**: *myVmWeb*
    - **리소스 그룹**: **기존 항목 사용**을 선택한 다음, *myResourceGroup*을 선택합니다.
    - **위치**: *미국 동부*를 선택합니다.

    입력하는 **사용자 이름** 및 **암호**는 이후 단계에서 사용됩니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다. 선택한 **위치** 및 **구독**은 가상 네트워크가 있는 위치 및 구독과 동일해야 합니다. 가상 네트워크가 만들어진 리소스 그룹과 동일한 리소스 그룹을 선택할 필요는 없지만 이 자습서에서는 동일한 리소스 그룹이 선택됩니다.
4. **크기 선택**에서 VM 크기를 선택합니다.
5. **설정**에 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.
    - **가상 네트워크**: **myVirtualNetwork**가 선택되어 있는지 확인합니다. 그렇지 않은 경우에는 **가상 네트워크**를 선택한 다음, **가상 네트워크 선택**에서 **myVirtualNetwork**를 선택합니다.
    - **서브넷**: **Public**이 선택되어 있는지 확인합니다. 그렇지 않은 경우에는, 다음 그림과 같이 **서브넷**을 선택한 다음, **서브넷 선택**에서 **Public**을 선택합니다.
    
        ![가상 머신 설정](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. **요약**의 **만들기**에서 **만들기**를 선택하여 가상 머신 배포를 시작합니다.
7. 1-6 단계를 다시 완료합니다. 단, 가상 머신의 **이름**에 *myVmMgmt*를 입력하고 **서브넷**에 **Private**을 선택합니다.

가상 머신을 만드는 데 몇 분 정도 걸립니다. 가상 머신 두 개가 모두 만들어 질 때까지 나머지 단계를 수행하지 마십시오.

이 문서에서 만든 가상 머신에는 네트워크 인터페이스에 동적으로 할당된 하나의 IP 주소가 있는 [네트워크 인터페이스](virtual-network-network-interface.md)가 하나 있습니다. VM을 배포한 후에는 [공용 및 비공개 IP 주소를 여러 개 추가하거나 IP 주소 할당 방법을 정적으로 변경](virtual-network-network-interface-addresses.md#add-ip-addresses)할 수 있습니다. 가상 머신을 만들 때 선택한 [VM 크기](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)로 지원되는 한도까지 [네트워크 인테페이스를 추가](virtual-network-network-interface-vm.md#vm-add-nic)할 수 있습니다. VM에 대해 [SR-IOV(단일 루트 I/O 가상화)를 사용하도록 설정](create-vm-accelerated-networking-powershell.md)할 수 있지만 용량을 지원하는 VM 크기로 VM을 만들 때만 가능합니다.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>가상 머신 간 통신 및 인터넷 통신

1. 포털 맨 위에 있는 *검색* 상자에 *myVmMgmt*를 입력하기 시작합니다. 검색 결과에 **myVmMgmt**가 표시되면 선택합니다.
2. 다음 그림과 같이 **연결**을 선택하여 *myVmMgmt* 가상 머신에 대한 원격 데스크톱 연결을 만듭니다.

    ![가상 머신에 연결](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.
4. 가상 머신을 만들 때 지정한 사용자 이름과 암호를 입력한 다음, **확인**을 선택합니다. (가상 머신을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있습니다.)
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예**를 선택하여 연결을 진행합니다.
6. 이후 단계에서는 ping을 사용하여 *myVmWeb* 가상 머신의 *myVmMgmt* 가상 머신과 통신합니다. Ping은 기본적으로 Windows 방화벽을 통해 거부되는 ICMP를 사용합니다. 명령 프롬프트에서 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 활성화합니다.

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    이 문서에서는 ping이 사용되지만, 프로덕션 배포에 Windows 방화벽을 통한 ICMP는 허용하지 않는 것이 좋습니다.
7. 보안상의 이유로 가상 네트워크에서 원격으로 연결할 수 있는 가상 머신의 수를 제한하는 것이 일반적입니다. 이 자습서에서는 가상 네트워크의 *myVmWeb* 가상 머신을 관리하는 데 *myVmMgmt* 가상 머신이 사용됩니다. *myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신에 원격 데스크톱을 설정하려면 명령 프롬프트에 다음 명령을 입력합니다.

    ``` 
    mstsc /v:myVmWeb
    ```
8. *myVmWeb* 가상 머신에서 *myVmMgmt* 가상 머신으로 통신하려면 명령 프롬프트에 다음 명령을 입력합니다.

    ```
    ping myvmmgmt
    ```

    다음 예제 출력과 유사한 출력이 표시됩니다.
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    *myVmMgmt* 가상 머신의 주소가 10.0.1.4로 표시됩니다. 10.0.1.4는 이전 단계에서 *myVmMgmt* 가상 머신을 배포한 *Private* 서브넷의 주소 범위에서 첫 번째로 사용 가능한 IP 주소입니다.  가상 머신의 정규화된 도메인 이름이 *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*으로 표시됩니다. 도메인 이름의 *dar5p44cif3ulfq00wxznl3i3f* 부분은 가상 머신과 다르지만 도메인 이름의 나머지 부분은 동일합니다. 

    기본적으로 모든 Azure 가상 머신은 기본 Azure DNS 서비스를 사용합니다. 가상 네트워크 내의 모든 가상 머신은 Azure의 기본 DNS 서비스를 사용하여 동일한 가상 네트워크에 있는 다른 모든 가상 머신의 이름을 확인할 수 있습니다. Azure의 기본 DNS 서비스를 사용하는 대신 자신의 DNS 서버 또는 Azure DNS 서비스의 비공개 도메인 기능을 사용할 수 있습니다. 자세한 내용은 [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)이나 [개인 도메인에 Azure DNS 사용](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

9. *myVmWeb* 가상 머신에 Windows Server용 IIS(인터넷 정보 서비스)를 설치하려면 PowerShell 세션에 다음 명령을 입력합니다.

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. IIS 설치가 완료된 후에 *myVmWeb* 원격 데스크톱 세션 연결을 끊으면 *myVmMgmt* 원격 데스크톱 세션이 종료됩니다. 웹 브라우저를 열고 http://myvmweb으로 이동합니다. IIS 시작 페이지가 보입니다.
11. *myVmMgmt* 원격 데스크톱 세션 연결을 끊습니다.
12. *myVmWeb* 가상 머신의 공용 IP 주소를 찾습니다. Azure에서 *myVmWeb* 가상 머신이 만들어질 때 *myVmWeb*이라는 공용 IP 주소 리소스도 만들어지고 가상 머신에 할당됩니다. 2단계의 그림에서 *myVmMgmt* 가상 머신에 대한 **공용 IP 주소**에 52.170.5.92가 할당된 것을 볼 수 있습니다. *myVmWeb* 가상 머신에 할당된 공용 IP 주소를 찾으려면 포털의 검색 상자에서 *myVmWeb*을 검색한 다음, 검색 결과에 나타나면 선택합니다.

    가상 머신에 공용 IP 주소가 할당되어 있어야 하는 것은 아니지만 Azure는 만들어진 가상 머신마다 공용 IP 주소를 기본적으로 할당합니다. 인터넷에서 가상 머신으로 통신하려면 가상 머신에 공용 IP 주소를 할당해야 합니다. 가상 머신에 공용 IP 주소가 할당되어 있는지 여부와 상관없이 모든 가상 머신은 인터넷으로 아웃 바운드 통신이 가능합니다. Azure의 아웃바운드 인터넷 연결에 대해 자세히 알아보려면 [Azure에서 아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.
13. 컴퓨터에서 *myVmWeb* 가상 머신의 공용 IP 주소로 이동합니다. 자신의 컴퓨터에서 IIS 시작 페이지를 보려는 시도가 실패합니다. 가상 머신이 배포될 때 Azure에서 기본적으로 가상 머신마다 네트워크 보안 그룹을 만들었기 때문에 시도가 실패합니다. 

     네트워크 보안 그룹에는 포트 및 IP 주소별로 인바운드 및 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙이 포함됩니다. Azure에서 생성되는 기본 네트워크 보안 그룹은 동일한 가상 네트워크에 있는 리소스 사이의 모든 포트를 거치는 통신을 허용합니다. Windows 가상 머신의 경우 기본 네트워크 보안 그룹은 TCP 포트 3389(RDP)를 제외한 모든 포트를 거치는 인터넷의 모든 인바운드 트래픽을 거부합니다. 따라서 포트 3389를 웹 서버에 개방하지 않으려는 경우에도 기본적으로 인터넷에서 *myVmWeb* 가상 머신에 직접 RDP가 가능합니다. 웹 탐색은 포트 80을 통해 통신하기 때문에 인터넷 통신은 실패합니다. 이것은 기본 네트워크 보안 그룹에 포트 80을 거치는 트래픽을 허용하는 규칙이 없기 때문입니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우, 리소스 그룹 및 이 그룹에 포함된 리소스를 모두 삭제합니다. 

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 여러 서브넷이 있는 가상 네트워크를 배포하는 방법을 알아보았습니다. 또한 Windows 가상 머신을 만들 때 Azure는 가상 머신에 연결되는 네트워크 인터페이스를 만들고 인터넷에서 포트 3389를 거치는 트래픽만 허용하는 네트워크 보안 그룹을 만드는 것을 알아보았습니다. 개별 가상 머신이 아닌 서브넷에 대한 네트워크 트래픽을 필터링하는 방법을 알아보려면 다음 자습서로 이동하세요.

> [!div class="nextstepaction"]
> [서브넷에 대한 네트워크 트래픽 필터링](./virtual-networks-create-nsg-arm-pportal.md)
