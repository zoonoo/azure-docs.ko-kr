---
title: 네트워크 트래픽 라우팅 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽을 라우팅하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽 라우팅

기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 자동으로 라우팅합니다. 고유의 라우팅을 만들어 Azure의 기본 라우팅을 재정의할 수 있습니다. 사용자 지정 경로를 만드는 기능은, 예를 들어 방화벽을 통해 서브넷 간 트래픽을 라우팅하려는 경우에 유용합니다. 이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 경로 테이블 만들기
> * 경로 만들기
> * 가상 네트워크 서브넷에 경로 테이블 연결
> * 테스트 라우팅
> * 라우팅 문제 해결

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="create-a-route-table"></a>경로 테이블 만들기

기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 라우팅합니다. Azure의 기본 경로를 자세히 알아보려면 [시스템 경로](virtual-networks-udr-overview.md)를 참조하세요. Azure의 기본 라우팅을 재정의하려면 경로를 포함하는 경로 테이블을 만들고 가상 네트워크 서브넷에 경로 테이블을 연결합니다.

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음, **경로 테이블**을 선택합니다.
3. **구독**을 선택하고 다음 정보를 선택하거나 입력한 다음, **만들기**를 선택합니다.
 
    ![경로 테이블 만들기](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>경로 만들기

경로 테이블에는 0개 또는 여러 개의 경로가 포함됩니다. 

1. 포털 맨 위에 있는 *리소스, 서비스 및 문서 검색* 상자에 *myRouteTablePublic*을 입력하기 시작합니다. 검색 결과에 **myRouteTablePublic**이 표시되면 선택합니다.
2. **설정**에서 **경로**를 선택한 다음, 다음 그림에 표시된 것처럼 **+ 추가**를 선택합니다.

    ![경로 추가](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. **경로 추가**에서 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.
    - **경로 이름**: *ToPrivateSubnet*
    - **주소 접두사**: 10.0.1.0/24
    - **다음 홉 형식**: **가상 어플라이언스**를 선택합니다.
    - **다음 홉 주소**: 10.0.2.4

    경로는 10.0.2.4 IP 주소를 사용하는 네트워크 가상 어플라이언스를 통해 10.0.1.0/24 주소 접두사를 향하는 모든 트래픽을 보냅니다. 지정된 주소 접두사가 있는 네트워크 가상 어플라이언스 및 서브넷은 이후 단계에서 만들어집니다. 경로는 직접 서브넷 간 트래픽을 라우팅하는 Azure의 기본 라우팅을 재정의합니다. 각 경로는 다음 홉 형식을 지정합니다. 다음 홉 형식은 Azure가 트래픽을 라우팅하는 방법을 지정합니다. 이 예제에서 다음 홉 형식은 *VirtualAppliance*입니다. Azure에서 사용 가능한 모든 다음 홉 형식 및 사용 시기에 대해 자세히 알아보려면 [다음 홉 형식](virtual-networks-udr-overview.md#custom-routes)을 참조하세요.

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

서브넷에 경로 테이블을 연결하려면 먼저 가상 네트워크 및 서브넷을 만들어야 합니다.

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. **가상 네트워크 만들기**에서 다음 정보를 선택하거나 입력한 다음, **만들기**를 선택합니다.
    
    - **이름**: *myVirtualNetwork*
    - **주소 공간**: *10.0.0.0/16*
    - **구독**: 사용자의 구독을 선택합니다.
    - **리소스 그룹**: **기존 항목 사용**을 선택하고 **myResourceGroup**을 선택합니다.
    - **위치**: *미국 동부*를 선택합니다.
    - **서브넷 이름**: *공용*
    - **주소 범위:** *10.0.0.0/24*

4. 포털 맨 위에 있는 **리소스, 서비스 및 문서 검색** 상자에 *myVirtualNetwork*를 입력하기 시작합니다. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.
5. 가상 네트워크에 두 개의 추가 서브넷을 추가합니다. 다음 그림과 같이 **설정**에서 **서브넷**을 선택한 다음, **+ 서브넷**을 선택합니다.

    ![서브넷 추가](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.
    - **이름**: 개인
    - **주소 공간**: *10.0.1.0/24*

7. 다음 정보를 입력하여 5단계와 6단계를 다시 완료합니다.
    - **이름**: DMZ
    - **주소 공간**: *10.0.2.0/24*

경로 테이블을 0개 또는 여러 개의 서브넷에 연결할 수 있습니다. 서브넷에는 0개 또는 1개의 경로 테이블이 연결될 수 있습니다. 서브넷의 아웃바운드 트래픽은 Azure의 기본 경로 및 서브넷에 연결한 경로 테이블에 추가한 모든 사용자 지정 경로에 따라 라우팅됩니다. *myRouteTablePublic* 경로 테이블을 *공용* 서브넷에 연결합니다.

1. 이전 단계를 완료하면 **myVirtualNetwork - 서브넷** 상자가 표시됩니다. **설정**에서 **서브넷**을 선택한 다음, **공용**을 선택합니다.
2. 다음 그림에 나와 있는 것처럼 **경로 테이블**을 선택한 다음, **MyRouteTablePublic**을 선택합니다.

    ![경로 테이블 연결](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. **저장**을 선택합니다.

## <a name="test-routing"></a>테스트 라우팅

라우팅을 테스트하려면 이전 단계에서 만든 경로를 통해 라우팅하는 네트워크 가상 어플라이언스로 사용되는 가상 머신을 만들어야 합니다. 네트워크 가상 어플라이언스를 만든 후에 가상 머신을 *공용* 및 *개인* 서브넷에 배포합니다. 그런 다음, *공용* 서브넷의 트래픽을 네트워크 가상 어플라이언스를 통해 *개인* 서브넷에 라우팅하게 됩니다.

### <a name="create-a-network-virtual-appliance"></a>네트워크 가상 어플라이언스 만들기

이전 단계에서 다음 홉 형식으로 네트워크 가상 어플라이언스를 지정하는 경로를 만들었습니다. 네트워크 응용 프로그램을 실행하는 가상 머신을 종종 네트워크 가상 어플라이언스라고 합니다. 프로덕션 환경에서 배포한 네트워크 가상 어플라이언스는 종종 미리 구성된 가상 머신입니다. 몇 가지 네트워크 가상 어플라이언스는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1)에서 확인할 수 있습니다. 이 문서에서는 기본 가상 머신이 생성됩니다.

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 다른 운영 체제를 선택할 수 있지만 나머지 단계에서는 **Windows Server 2016 Datacenter**를 선택했다고 가정합니다. 
3. **기본**에 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.
    - **이름**: *myVmNva*
    - **리소스 그룹**: **기존 항목 사용**을 선택한 다음, *myResourceGroup*을 선택합니다.
    - **위치**: *미국 동부*를 선택합니다.

    입력하는 **사용자 이름** 및 **암호**는 이후 단계에서 사용됩니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다. 선택한 **위치** 및 **구독**은 가상 네트워크가 있는 위치 및 구독과 동일해야 합니다. 가상 네트워크가 만들어진 리소스 그룹과 동일한 리소스 그룹을 선택할 필요는 없지만 이 자습서에서는 동일한 리소스 그룹이 선택됩니다.
4. **크기 선택**에서 VM 크기를 선택합니다.
5. **설정**에 다음 정보를 선택하거나 입력한 다음, **확인**을 선택합니다.
    - **가상 네트워크**: **myVirtualNetwork**가 선택되어 있는지 확인합니다. 그렇지 않은 경우에는 **가상 네트워크**를 선택한 다음, **가상 네트워크 선택**에서 **myVirtualNetwork**를 선택합니다.
    - **서브넷**: **서브넷**을 선택한 다음, **서브넷 선택**에서 **DMZ**를 선택합니다.
    - **공용 IP 주소**: **공용 IP 주소**를 선택하고 **공용 IP 주소 선택**에서 **없음**을 선택합니다. 인터넷에서 연결되어 있지 않아 이 가상 머신에 할당된 공용 IP 주소가 없습니다.
6. **요약**의 **만들기**에서 **만들기**를 선택하여 가상 머신 배포를 시작합니다.

가상 머신을 만드는 데 몇 분 정도 걸립니다. Azure가 가상 머신 만들기를 완료하고 가상 머신에 대한 정보가 포함된 상자를 열 때까지 다음 단계로 이동하지 마십시오.

또한 Azure가 가상 머신을 만들 때 *DMZ* 서브넷에 [네트워크 인터페이스](virtual-network-network-interface.md)를 만들고 네트워크 인터페이스를 가상 머신에 연결했습니다. 네트워크 인터페이스에 할당되지 않은 모든 IP 주소를 대상으로 하는 트래픽을 전달하는 각 Azure 네트워크 인터페이스에 대해 IP 전달을 사용하도록 설정해야 합니다.

1. 가상 머신이 생성된 후 이에 대해 열리는 상자에서 다음 그림과 같이 **설정**에 있는 **네트워킹**을 선택한 다음, **myvmnva158**(가상 머신에 대해 Azure가 만든 네트워크 인터페이스에는 **myvmnva** 뒤에 여러 다른 숫자가 옴)을 선택합니다.

    ![가상 머신 네트워킹](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    *DMZ* 서브넷에서 네트워크 가상 어플라이언스를 만들면 Azure에서 자동으로 네트워크 인터페이스를 만들고, 네트워크 인터페이스를 가상 머신에 연결하고, 개인 IP 주소 *10.0.2.4*를 네트워크 인터페이스에 할당합니다. 

2. 다음 그림에 표시된 것처럼 **설정**에서 **IP 구성을**을 선택하고 **IP 전달**에 **사용**을 선택한 다음, **저장**을 선택합니다.

    ![IP 전달을 사용하도록 설정](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>가상 머신 만들기

이후 단계에서 *공용* 서브넷의 해당 트래픽이 네트워크 가상 어플라이언스를 통해 *개인* 서브넷에 라우팅되는지 유효성을 검사할 수 있도록 가상 네트워크에 두 개의 가상 머신을 만듭니다.

[네트워크 가상 어플라이언스 만들기](#create-a-network-virtual-appliance)의 1-6단계를 완료합니다. 다음과 같은 변경 사항을 제외하고 3-5단계에서 동일한 설정을 사용합니다.

|가상 머신   |Name      |서브넷      | 공용 IP 주소     |
|---------         |--------- | -----------|---------              |
|가상 머신 1 | myVmWeb  | 공용     | 포털 기본값 수락 |
|가상 머신 2 | myVmMgmt | 개인    | 포털 기본값 수락 |

Azure에서 *myVmWeb* 가상 머신을 만드는 동안 *myVmMgmt* 가상 머신을 만들 수 있습니다. Azure가 두 가상 머신을 모두 만들 때까지 다음 단계를 진행하지 마십시오.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>네트워크 가상 어플라이언스를 통한 트래픽 라우팅

1. 포털 맨 위에 있는 *검색* 상자에 *myVmMgmt*를 입력하기 시작합니다. 검색 결과에 **myVmMgmt**가 표시되면 선택합니다.
2. 다음 그림과 같이 **연결**을 선택하여 *myVmMgmt* 가상 머신에 대한 원격 데스크톱 연결을 만듭니다.

    ![가상 머신에 연결](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.
4. 가상 머신을 만들 때 지정한 사용자 이름과 암호를 입력한 다음, **확인**을 선택합니다. (가상 머신을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있습니다.)
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예**를 선택하여 연결을 진행합니다.
6. 이후 단계에서 tracert.exe 명령은 라우팅을 테스트하는 데 사용됩니다. Tracert는 Windows 방화벽을 통해 거부되는 ICMP를 사용합니다. 명령 프롬프트에서 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 활성화합니다.

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    이 문서에서는 라우팅을 테스트하는 데 tracert가 사용되지만, 프로덕션 배포에 Windows 방화벽을 통한 ICMP는 허용하지 않는 것이 좋습니다.
7. [IP 전달을 사용하도록 설정](#enable-ip-forwarding)에서 가상 머신의 네트워크 인터페이스에 대해 Azure 내에서 IP 전달을 사용하도록 설정했습니다. 가상 머신 내 운영 체제 또는 가상 머신 내에서 실행 중인 응용 프로그램도 네트워크 트래픽을 전달할 수 있어야 합니다. 프로덕션 환경에서 네트워크 가상 어플라이언스를 배포할 때 어플라이언스는 일반적으로 트래픽을 전달하기 전에 몇 가지 다른 기능을 필터링, 로그 또는 수행합니다. 하지만 이 문서에서는 운영 체제가 수신한 모든 트래픽을 전달하기만 합니다. *myVmMgmt* 가상 머신에서 다음 단계를 완료하여 *myVmNva*의 운영 체제 내에서 IP 전달을 사용하도록 설정합니다.

    명령 프롬프트에서 다음 명령을 사용하는 *myVmNva* 가상 머신에 대한 원격 데스크톱:

    ``` 
    mstsc /v:myvmnva
    ```
    
    운영 체제 내에서 IP 전달을 사용하도록 설정하려면 PowerShell에서 다음 명령을 입력합니다.

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    가상 머신을 다시 시작합니다. 그러면 원격 데스크톱 세션의 연결도 끊어집니다.
8. 계속 *myVmMgmt* 가상 머신에 연결되어 있는 동안 *myVmNva* 가상 머신을 다시 시작 한 후, 다음 명령을 사용하여 *myVmWeb* 가상 머신에 대한 원격 데스크톱 세션을 만듭니다.

    ``` 
    mstsc /v:myVmWeb
    ```
    
    명령 프롬프트에서 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 활성화합니다.

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. *myVmWeb* 가상 머신에서 *myVmMgmt* 가상 머신에 대한 네트워크 트래픽의 라우팅을 테스트하려면 명령 프롬프트에 다음 명령을 입력합니다.

    ```
    tracert myvmmgmt
    ```

    응답은 다음 예제와 유사합니다.
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    첫 번째 홉이 네트워크 가상 어플라이언스의 개인 IP 주소인 10.0.2.4인 것을 확인할 수 있습니다. 두 번째 홉은 *myVmMgmt* 가상 머신의 개인 IP 주소인 10.0.1.4입니다. 경로가 *myRouteTablePublic* 경로 테이블에 추가되고 *공용* 서브넷에 연결되었으므로 Azure는 트래픽을 직접 *개인* 서브넷에 라우팅하는 대신 NVA를 통해 트래픽을 라우팅합니다.
10.  *myVmWeb* 가상 머신에 대한 원격 데스크톱 세션을 닫습니다. 그러면 *myVmMgmt* 가상 머신에 연결된 상태가 유지됩니다.
11. *myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신에 대한 네트워크 트래픽의 라우팅을 테스트하려면 명령 프롬프트에 다음 명령을 입력합니다.

    ```
    tracert myvmweb
    ```

    응답은 다음 예제와 유사합니다.

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    트래픽이 *myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신으로 직접 라우팅되는지 볼 수 있습니다. 기본적으로 Azure는 트래픽을 서브넷 간에 직접 라우팅합니다.
12. *myVmMgmt* 가상 머신에 대한 원격 데스크톱 세션을 닫습니다.

## <a name="troubleshoot-routing"></a>라우팅 문제 해결

이전 단계에서 설명한 대로 Azure는 사용자가 필요에 따라 자신 고유의 경로로 재정의할 수 있는 기본 경로를 적용합니다. 경우에 따라 트래픽이 예상 대로 라우팅하지 못할 수 있습니다. Azure Network Watcher의 [다음 홉](../network-watcher/network-watcher-check-next-hop-portal.md) 기능을 사용하여 Azure가 두 가상 머신 간에 트래픽을 라우팅하는 방식을 결정할 수 있습니다. 

1. 포털 맨 위에 있는 *검색* 상자에 *Network Watcher*를 입력하기 시작합니다. 검색 결과에 **Network Watcher**가 나타나면 이를 선택합니다.
2. **네트워크 진단 도구**에서 **다음 홉**을 선택합니다.
3. *myVmWeb*(10.0.0.4) 가상 머신에서 *myVmMgmt*(10.0.1.4) 가상 머신까지의 트래픽 라우팅을 테스트하려면 다음 그림에 나온 것처럼 정보를 입력한 다음(사용자의 **네트워크 인터페이스** 이름은 약간 다름), **다음 홉**을 선택합니다.

    ![다음 홉](./media/tutorial-create-route-table-portal/next-hop.png)  

    **결과** 출력에는 *myVmWeb*에서 *myVmMgmt*의 트래픽에 대한 다음 홉 IP 주소가 10.0.2.4(*myVmNva* 가상 머신)이며, 다음 홉 형식은 *VirtualAppliance*이고, 라우팅을 유발하는 경로 테이블은 *myRouteTablePublic*이라는 정보가 포함됩니다.

각 네트워크 인터페이스에 대한 유효한 경로는 Azure의 기본 경로와 사용자가 정의한 경로의 조합입니다. 가상 머신에서 네트워크 인터페이스에 유효한 모든 경로를 확인하려면 다음 단계를 완료합니다.

1. 포털 맨 위에 있는 *검색* 상자에 *myVmWeb*을 입력하기 시작합니다. 검색 결과에 표시된 **myVmWeb**을 선택합니다.
2. **설정**에서 **네트워킹**을 선택한 다음, **myvmweb369**를 선택합니다(사용자 가상 머신에 대해 Azure에서 만든 네트워크 인터페이스는 **myvmweb** 뒤에 다른 번호가 있음).
3. 다음 그림과 같이 **지원 및 문제 해결**에서 **유효한 경로**를 선택합니다.

    ![유효한 경로](./media/tutorial-create-route-table-portal/effective-routes.png) 

    Azure의 기본 경로 및 *myRouteTablePublic* 경로 테이블에 사용자가 추가한 경로를 확인합니다. Azure가 경로 목록에서 경로를 선택하는 방법에 대한 자세한 내용은 [Azure가 경로를 선택하는 방법](virtual-networks-udr-overview.md#how-azure-selects-a-route)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우, 리소스 그룹 및 이 그룹에 포함된 리소스를 모두 삭제합니다. 

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:**에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 경로 테이블을 만들고 서브넷에 연결했습니다. 공용 서브넷에서 개인 서브넷으로 트래픽을 라우팅하는 네트워크 가상 어플라이언스를 만들었습니다. 가상 네트워크 내에서 여러 Azure 리소스를 배포할 수 있는 반면, 일부 Azure PaaS 서비스에 대한 리소스는 가상 네트워크에 배포할 수 없습니다. 하지만 일부 Azure PaaS 서비스의 리소스에 대한 액세스를 가상 네트워크 서브넷의 트래픽만으로 제한할 수 있습니다. 다음 자습서에서 Azure PaaS 리소스에 대한 네트워크 액세스를 제한하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [PaaS 리소스에 대한 네트워크 액세스 제한](virtual-network-service-endpoints-configure.md#azure-portal)
