---
title: 네트워크 트래픽 라우팅 - 자습서 - Azure Portal
titlesuffix: Azure Virtual Network
description: 이 자습서에서는 Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽을 라우팅하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d630a41f9b83a852605ffad2a85ad6dd14bbac73
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079652"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽 라우팅

Azure에서는 기본적으로 가상 네트워크 내의 모든 서브넷 간에 트래픽을 라우팅합니다. 고유의 라우팅을 만들어 Azure의 기본 라우팅을 재정의할 수 있습니다. 예를 들어 NVA(네트워크 가상 어플라이언스)를 통해 트래픽을 서브넷 간에 라우팅하려는 경우 사용자 지정 경로가 유용합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 트래픽을 라우팅하는 NVA 만들기
> * 경로 테이블 만들기
> * 경로 만들기
> * 서브넷에 경로 테이블 연결
> * 다른 서브넷에 VM(가상 머신) 배포
> * NVA를 통해 한 서브넷에서 다른 서브넷으로 트래픽 라우팅

이 자습서에서는 [Azure Portal](https://portal.azure.com)을 사용합니다. [Azure CLI](tutorial-create-route-table-cli.md) 또는 [Azure PowerShell](tutorial-create-route-table-powershell.md)도 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-nva"></a>NVA 만들기

NVA(네트워크 가상 어플라이언스)는 라우팅 및 방화벽 최적화와 같은 네트워크 기능을 지원하는 가상 머신입니다. 이 자습서에서는 **Windows Server 2016 Datacenter**를 사용하고 있다고 가정합니다. 원하는 경우 다른 운영 체제를 선택할 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.

1. **보안** > **Windows Server 2016 Datacenter**를 차례로 선택합니다.

    ![Windows Server 2016 Datacenter, VM 만들기, Azure Portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. **가상 머신 만들기** 페이지의 **기본 사항** 아래에서 다음 정보를 입력하거나 선택합니다.

    | 섹션 | 설정 | 작업 |
    | ------- | ------- | ----- |
    | **프로젝트 세부 정보** | Subscription | 구독을 선택합니다. |
    | | Resource group | **새로 만들기**를 선택하고, *myResourceGroup*을 입력하고, **확인**을 선택합니다. |
    | **인스턴스 세부 정보** | 가상 머신 이름 | *myVmNva*를 입력합니다. |
    | | 지역 | **(미국) 미국 동부**를 선택합니다. |
    | | 가용성 옵션 | **인프라 중복이 필요하지 않습니다.** 를 선택합니다. |
    | | 이미지 | **Windows Server 2016 Datacenter**를 선택합니다. |
    | | 크기 | 기본값인 **표준 DS1 v2**를 그대로 유지합니다. |
    | **관리자 계정** | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | | 암호 | 선택한 암호를 입력합니다. 이는 12자 이상이고 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다. |
    | | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** | 공용 인바운드 포트 | **없음**을 선택합니다. |
    | **비용 절감** | 이미 Windows Server 라이선스가 있나요? | **아니요**를 선택합니다. |

    ![기본 사항, 가상 머신 만들기, Azure Portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    그런 다음, 페이지 아래쪽의 **다음: 디스크 >** 를 클릭합니다.

1. **디스크** 아래에서 요구 사항에 맞는 설정을 선택하고, **다음: 네트워킹 >** 을 클릭합니다.

1. **네트워킹** 아래에서 다음을 수행합니다.

    1. **가상 네트워크**에 대해 **새로 만들기**를 선택합니다.
    
    1. **가상 네트워크 만들기** 대화 상자의 **이름** 아래에서 *myVirtualNetwork*를 입력합니다.

    1. **주소 공간**에서 기존 주소 범위를 *10.0.0.0/16*으로 바꿉니다.

    1. **서브넷**에서 **삭제** 아이콘을 선택하여 기존 서브넷을 삭제하고, 다음 조합의 **서브넷 이름** 및 **주소 범위**를 입력합니다. 올바른 이름과 범위가 입력되면 그 아래에 새 빈 행이 나타납니다.

        | 서브넷 이름 | 주소 범위 |
        | ----------- | ------------- |
        | *공용* | *10.0.0.0/24* |
        | *프라이빗* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. **확인**을 선택하여 대화 상자를 종료합니다.

    1. **서브넷**에서 **DMZ(10.0.2.0/24)** 를 선택합니다.

    1. 이 VM은 인터넷을 통해 연결되지 않으므로 **공용 IP**에서 **없음**을 선택합니다.

    1. **다음: 관리 >** 를 클릭합니다.

1. **관리** 아래에서 다음을 수행합니다.

    1. **진단 스토리지 계정**에서 **새로 만들기**를 선택합니다.
    
    1. **스토리지 계정 만들기** 대화 상자에서 다음 정보를 입력하거나 선택합니다.

        | 설정 | 값 |
        | ------- | ----- |
        | Name | *mynvastorageaccount* |
        | 계정 종류 | **스토리지(범용 v1)** |
        | 성능 | **Standard** |
        | 복제 | **LRS(로컬 중복 스토리지)** |
    
    1. **확인**을 선택하여 대화 상자를 종료합니다.

    1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동하고, Azure에서 구성의 유효성을 검사합니다.

1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

    VM을 만드는 데 몇 분이 걸립니다. Azure에서 VM 만들기가 완료될 때까지 기다립니다. **배포 진행 중** 페이지에서 배포 세부 정보가 표시됩니다.

1. VM이 준비되면 **리소스로 이동**을 선택합니다.

## <a name="create-a-route-table"></a>경로 테이블 만들기

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.

2. 검색 상자에 *경로 테이블*을 입력합니다. 검색 결과에 **경로 테이블**이 나타나면 이를 선택합니다.

3. **경로 테이블** 페이지에서 **만들기**를 선택합니다.

4. **경로 테이블 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | *myRouteTablePublic* |
    | Subscription | 사용자의 구독 |
    | Resource group | **myResourceGroup** |
    | 위치 | **(미국) 미국 동부** |
    | 가상 네트워크 게이트웨이 경로 전파 | **Enabled** |

    ![경로 테이블 만들기, Azure Portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. **만들기**를 선택합니다.

## <a name="create-a-route"></a>경로 만들기

1. 경로 테이블을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블**을 검색하여 선택합니다.

1. 경로 테이블 이름(**myRouteTablePublic**)을 선택합니다.

1. **경로** > **추가**를 차례로 선택합니다.

    ![경로 추가, 경로 테이블, Azure Portal](./media/tutorial-create-route-table-portal/add-route.png)

1. **경로 추가**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 경로 이름 | *ToPrivateSubnet* |
    | 주소 접두사 | *10.0.1.0/24*(이전에 만든 *개인* 서브넷의 주소 범위) |
    | 다음 홉 유형 | **가상 어플라이언스** |
    | 다음 홉 주소 | *10.0.2.4*(*DMZ* 서브넷의 주소 범위 내 주소) |

1. **확인**을 선택합니다.

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

1. 가상 네트워크를 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 네트워크**를 검색하여 선택합니다.

1. 가상 네트워크 이름(**myVirtualNetwork**)을 선택합니다.

1. 가상 네트워크의 메뉴 모음에서 **서브넷**을 선택합니다.

1. 가상 네트워크의 서브넷 목록에서 **공용**을 선택합니다.

1. **경로 테이블**에서 만든 경로 테이블(**myRouteTablePublic**)을 선택한 다음, **저장**을 선택하여 경로 테이블을 *공용* 서브넷에 연결합니다.

    ![경로 테이블 연결, 서브넷 목록, 가상 네트워크, Azure Portal](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>IP 전달 설정

다음으로, 새 NVA 가상 머신(*myVmNva*)에 대한 IP 전달을 설정합니다. Azure에서 네트워크 트래픽을 *myVmNva*에 보낼 때 트래픽에서 다른 IP 주소를 대상으로 하는 경우 IP 전달은 트래픽을 올바른 위치에 보냅니다.

1. VM을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신**을 검색하여 선택합니다.

1. VM 이름(**myVmNva**)을 선택합니다.

1. NVA 가상 머신의 메뉴 모음에서 **네트워킹**을 선택합니다.

1. **myvmnva123**을 선택합니다. 이는 Azure에서 VM에 대해 만든 네트워크 인터페이스이며, Azure에서 고유 이름을 보장하기 위해 숫자를 추가합니다.

    ![네트워킹, NVA(네트워크 가상 어플라이언스) VM(가상 머신), Azure Portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. 네트워크 인터페이스 메뉴 모음에서 **IP 구성**을 선택합니다.

1. **IP 구성** 페이지에서 **IP 전달**을 **사용**으로 설정하고 **저장**을 선택합니다.

    ![IP 전달 사용, IP 구성, 네트워크 인터페이스, NVA(네트워크 가상 어플라이언스) VM(가상 머신), Azure Portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>공용 및 프라이빗 가상 머신 만들기

가상 네트워크에 공용 VM 및 프라이빗 VM을 만듭니다. 나중에 Azure에서 *공용* 서브넷 트래픽을 NVA를 통해 *프라이빗* 서브넷으로 라우팅하는지 확인하기 위해 이러한 VM을 사용합니다.

공용 VM 및 개인 VM을 만들려면 이전의 [NVA 만들기](#create-an-nva) 단계를 수행합니다. 배포가 완료될 때까지 기다리거나 VM 리소스로 이동할 필요가 없습니다. 아래에서 설명하는 설정을 제외하고는 대부분의 동일한 설정을 사용합니다.

**만들기**를 선택하여 공용 또는 개인 VM을 만들기 전에 다음 두 하위 섹션([공용 VM](#public-vm) 및 [개인 VM](#private-vm))으로 이동합니다. 이 경우 서로 다른 값을 표시해야 합니다. Azure에서 두 VM이 모두 배포되면 다음 섹션([NVA를 통한 트래픽 라우팅](#route-traffic-through-an-nva))으로 계속 진행할 수 있습니다.

### <a name="public-vm"></a>공용 VM

| 탭 | 설정 | 값 |
| --- | ------- | ----- |
| 기본 사항 | Resource group | **myResourceGroup** |
| | 가상 머신 이름 | *myVmPublic* |
| | 공용 인바운드 포트 | **선택한 포트 허용** |
| | 인바운드 포트 선택 | **RDP** |
| 네트워킹 | 가상 네트워크 | **myVirtualNetwork** |
| | 서브넷 | **공용(10.0.0.0/24)** |
| | 공용 IP 주소 | 기본값 |
| 관리 | 진단 스토리지 계정 | **mynvastorageaccount** |

### <a name="private-vm"></a>개인 VM

| 탭 | 설정 | 값 |
| --- | ------- | ----- |
| 기본 사항 | Resource group | **myResourceGroup** |
| | 가상 머신 이름 | *myVmPrivate* |
| | 공용 인바운드 포트 | **선택한 포트 허용** |
| | 인바운드 포트 선택 | **RDP** |
| 네트워킹 | 가상 네트워크 | **myVirtualNetwork** |
| | 서브넷 | **개인(10.0.1.0/24)** |
| | 공용 IP 주소 | 기본값 |
| 관리 | 진단 스토리지 계정 | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>NVA를 통한 트래픽 라우팅

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>원격 데스크톱을 통해 myVmPrivate에 로그인

1. 개인 VM을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신**을 검색하여 선택합니다.

1. 개인 VM 이름(**myVmPrivate**)을 선택합니다.

1. VM 메뉴 모음에서 **연결**을 선택하여 개인 VM에 대한 원격 데스크톱 연결을 만듭니다.

1. **RDP를 사용하여 연결** 페이지에서 **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. 다운로드한 *.rdp* 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. **기타 선택 사항** > **다른 계정 사용**을 차례로 선택한 다음, 개인 VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 표시되면 **예**를 선택하여 VM에 연결합니다.

### <a name="enable-icmp-through-the-windows-firewall"></a>Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

이후 단계에서는 추적 경로 도구를 사용하여 라우팅을 테스트합니다. 추적 경로는 Windows 방화벽에서 기본적으로 거부하는 ICMP(Internet Control Message Protocol)를 사용합니다. Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

1. *myVmPrivate*의 원격 데스크톱에서 PowerShell을 엽니다.

1. 다음 명령을 입력합니다.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    이 자습서에서는 추적 경로를 사용하여 라우팅을 테스트합니다. 프로덕션 환경에서는 Windows 방화벽을 통한 ICMP를 허용하지 않는 것이 좋습니다.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>myVmNva 내에서 IP 전달 설정

Azure를 사용하여 VM의 네트워크 인터페이스에 대한 [IP 전달을 설정](#turn-on-ip-forwarding)했습니다. 이제 VM의 운영 체제에서도 네트워크 트래픽을 전달해야 합니다. 다음 명령을 사용하여 *myVmNva* VM의 운영 체제에 대한 IP 전달을 설정합니다.

1. *myVmPrivate* VM의 명령 프롬프트에서 다음과 같이 *myVmNva* VM에 대한 원격 데스크톱을 엽니다.

    ```cmd
    mstsc /v:myvmnva
    ```

1. *myVmNva* VM의 PowerShell에서 다음 명령을 입력하여 IP 전달을 설정합니다.

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. *myVmNva* VM을 다시 시작합니다. 작업 표시줄에서 **시작** > **전원**, **기타(계획됨)**  > **계속**을 차례로 선택합니다.

    그러면 원격 데스크톱 세션의 연결도 끊어집니다.

1. *myVmNva* VM이 다시 시작되면 *myVmPublic* VM에 대한 원격 데스크톱 세션을 만듭니다. *myVmPrivate* VM에 연결된 상태에서 명령 프롬프트를 열고 다음 명령을 실행합니다.

    ```cmd
    mstsc /v:myVmPublic
    ```
1. *myVmPublic*의 원격 데스크톱에서 PowerShell을 엽니다.

1. 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>네트워크 트래픽의 라우팅 테스트

먼저 *myVmPublic* VM에서 *myVmPrivate* VM으로의 네트워크 트래픽 라우팅을 테스트해 보겠습니다.

1. *myVmPublic* VM의 PowerShell에서 다음 명령을 입력합니다.

    ```powershell
    tracert myVmPrivate
    ```

    응답은 다음 예제와 비슷합니다.

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    첫 번째 홉은 10.0.2.4이고 NVA의 개인 IP 주소임을 알 수 있습니다. 두 번째 홉은 *myVmPrivate* VM의 개인 IP 주소입니다. 10.0.1.4에 있습니다. 이전에 경로를 *myRouteTablePublic* 경로 테이블에 추가하여 *공용* 서브넷에 연결했습니다. 이에 따라 Azure에서 NVA를 통해 트래픽을 보냈지만, *프라이빗* 서브넷으로는 직접 보내지 않았습니다.

1. *myVmPublic* VM에 대한 원격 데스크톱 세션을 닫습니다. 그러면 *myVmPrivate* VM에 연결된 상태가 유지됩니다.

1. *myVmPrivate* VM의 명령 프롬프트에서 다음 명령을 입력합니다.

    ```cmd
    tracert myVmPublic
    ```

    다음 명령은 *myVmPrivate* VM에서 *myVmPublic* VM으로의 네트워크 트래픽 라우팅을 테스트합니다. 응답은 다음 예제와 비슷합니다.

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Azure에서 트래픽을 *myVmPrivate* VM에서 *myVmPublic* VM으로 직접 라우팅한다는 것을 알 수 있습니다. 기본적으로 Azure는 트래픽을 서브넷 간에 직접 라우팅합니다.

1. *myVmPrivate* VM에 대한 원격 데스크톱 세션을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹이 더 이상 필요하지 않은 경우 *myResourceGroup* 및 이 그룹에 포함된 모든 리소스를 삭제합니다.

1. 리소스 그룹을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **리소스 그룹**을 검색하여 선택합니다.

1. 리소스 그룹 이름(**myResourceGroup**)을 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. 확인 대화 상자에서 **리소스 그룹 이름 입력**에 대해 *myResourceGroup*을 입력한 다음, **삭제**를 선택합니다. Azure에서 경로 테이블, 스토리지 계정, 가상 네트워크, VM, 네트워크 인터페이스 및 공용 IP 주소를 포함하여 *myResourceGroup* 및 이 그룹에 연결된 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 경로 테이블을 만들고 서브넷에 연결했습니다. 공용 서브넷에서 프라이빗 서브넷으로 트래픽을 라우팅하는 간단한 NVA를 만들었습니다. 이제 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)에서 많은 유용한 네트워크 기능을 제공하도록 미리 구성된 다른 NVA를 배포할 수 있습니다. 라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md) 및 [경로 테이블 관리](manage-route-table.md)를 참조하세요.

가상 네트워크 내에 많은 Azure 리소스를 배포할 수 있지만, Azure에서는 일부 PaaS 서비스에 대한 리소스를 가상 네트워크에 배포할 수 없습니다. 일부 Azure PaaS 서비스의 리소스에 대한 액세스를 제한할 수 있지만 가상 네트워크 서브넷의 트래픽만 제한해야 합니다. Azure PaaS 리소스에 대한 네트워크 액세스를 제한하는 방법을 알아보려면 다음 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [PaaS 리소스에 대한 네트워크 액세스 제한](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Azure 서비스에는 비용이 듭니다. Azure Cost Management는 예산을 설정하고 경고를 구성하여 지출을 통제하는 데 유용합니다. Cost Management를 사용하여 Azure 비용을 분석하고 관리하고 최적화하세요. 자세한 내용은 [비용 분석을 위한 빠른 시작](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.