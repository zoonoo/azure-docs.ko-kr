---
title: 네트워크 트래픽 라우팅 - 자습서 - Azure Portal
titlesuffix: Azure Virtual Network
description: 이 자습서에서는 Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽을 라우팅하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 153c692a8fb0fa538ec49c6eafa11815dd794b5d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681535"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽 라우팅

Azure에서는 기본적으로 가상 네트워크 내의 모든 서브넷 간에 트래픽을 라우팅합니다. 고유의 라우팅을 만들어 Azure의 기본 라우팅을 재정의할 수 있습니다. 사용자 지정 경로를 만드는 기능은 예를 들어 NVA(네트워크 가상 어플라이언스)를 통해 서브넷 간 트래픽을 라우팅하려는 경우에 유용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 경로 테이블 만들기
> * 경로 만들기
> * 여러 서브넷이 있는 가상 네트워크 만들기
> * 서브넷에 경로 테이블 연결
> * 트래픽을 라우팅하는 NVA 만들기
> * 다른 서브넷에 VM(가상 머신) 배포
> * NVA를 통해 한 서브넷에서 다른 서브넷으로 트래픽 라우팅

원하는 경우 이 자습서는 [Azure CLI](tutorial-create-route-table-cli.md) 또는 [Azure PowerShell](tutorial-create-route-table-powershell.md)을 사용하여 수행할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-route-table"></a>경로 테이블 만들기

1. 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **경로 테이블**을 차례로 선택합니다.

1. **경로 테이블 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | *myRouteTablePublic*을 입력합니다. |
    | 구독 | 구독을 선택합니다. |
    | 리소스 그룹 | **새로 만들기**를 선택하고, *myResourceGroup*을 입력하고, *확인*을 선택합니다. |
    | 위치 | 기본값인 **미국 동부**를 그대로 둡니다.
    | BGP 경로 전파 | 기본값인 **사용**을 그대로 둡니다. |

1. **만들기**를 선택합니다.

## <a name="create-a-route"></a>경로 만들기

1. 포털의 검색 창에서 *myRouteTablePublic*을 입력합니다.

1. 검색 결과에 **myRouteTablePublic**이 표시되면 선택합니다.

1. **myRouteTablePublic**의 **설정** 아래에서 **경로** >  **+ 추가**를 선택합니다.

    ![경로 추가](./media/tutorial-create-route-table-portal/add-route.png)

1. **경로 추가**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 경로 이름 | *ToPrivateSubnet*을 입력합니다. |
    | 주소 접두사 | *10.0.1.0/24*를 입력합니다. |
    | 다음 홉 유형 | **가상 어플라이언스**를 선택합니다. |
    | 다음 홉 주소 | *10.0.2.4*를 입력합니다. |

1. **확인**을 선택합니다.

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

서브넷에 경로 테이블을 연결하려면 먼저 가상 네트워크 및 서브넷을 만들어야 합니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.

1. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | *myVirtualNetwork*를 입력합니다. |
    | 주소 공간 | *10.0.0.0/16*을 입력합니다. |
    | 구독 | 구독을 선택합니다. |
    | 리소스 그룹 | ***기존 항목 선택*** > **myResourceGroup**을 차례로 선택합니다. |
    | 위치 | 기본값인 **미국 동부**를 그대로 둡니다. |
    | 서브넷 - 이름 | *공용*을 입력합니다. |
    | 서브넷 - 주소 범위 | *10.0.0.0/24*를 입력합니다. |

1. 나머지는 기본값으로 두고, **만들기**를 선택합니다.

### <a name="add-subnets-to-the-virtual-network"></a>가상 네트워크에 서브넷 추가

1. 포털의 검색 창에서 *myVirtualNetwork*를 입력합니다.

1. 검색 결과에 **myVirtualNetwork**가 표시되면 선택합니다.

1. **myVirtualNetwork**의 **설정** 아래에서 **서브넷** >  **+ 서브넷**을 차례로 선택합니다.

    ![서브넷 추가](./media/tutorial-create-route-table-portal/add-subnet.png)

1. **서브넷 추가**에서 다음 정보를 입력합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | *사설*을 입력합니다. |
    | 주소 공간 | *10.0.1.0/24*를 입력합니다. |

1. 나머지는 기본값으로 두고 **확인**을 선택합니다.

1. **+ 서브넷**을 다시 선택합니다. 이번에는 다음 정보를 입력합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | *DMZ*를 입력합니다. |
    | 주소 공간 | *10.0.2.0/24*를 입력합니다. |

1. 지난 번과 마찬가지로 나머지는 기본값으로 두고, **확인**을 선택합니다.

    Azure에는 세 개의 서브넷, 즉 **공용**, **사설** 및 **DMZ**가 표시됩니다.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>공용 서브넷에 myRouteTablePublic 연결

1. **공용**을 선택합니다.

1. **공용**에서 **경로 테이블** > **MyRouteTablePublic** > **저장**을 차례로 선택합니다.

    ![경로 테이블 연결](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>NVA 만들기

NVA는 라우팅 및 방화벽 최적화와 같은 네트워크 기능을 지원하는 VM입니다. 원하는 경우 다른 운영 체제를 선택할 수 있습니다. 이 자습서에서는 **Windows Server 2016 Datacenter**를 사용하고 있다고 가정합니다.

1. 화면의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **Windows Server 2016 Datacenter**를 차례로 선택합니다.

1. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | 구독 | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. |
    | **인스턴스 정보** |  |
    | 가상 머신 이름 | *myVmNva*를 입력합니다. |
    | 지역 | **미국 동부**를 선택합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다**를 그대로 둡니다. |
    | 이미지 | 기본값인 **Windows Server 2016 Datacenter**를 그대로 둡니다. |
    | 크기 | 기본값인 **표준 DS1 v2**를 그대로 둡니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | 기본값인 **없음**을 그대로 둡니다.
    | **비용 절감** |  |
    | 이미 Windows 라이선스가 있나요? | 기본값인 **아니요**를 그대로 둡니다. |

1. **다음: 디스크**를 선택합니다.

1. **가상 머신 만들기 - 디스크**에서 요구 사항에 맞는 설정을 선택합니다.

1. **다음: 네트워킹**을 선택합니다.

1. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | 기본값인 **myVirtualNetwork**를 그대로 둡니다. |
    | 서브넷 | **DMZ(10.0.2.0/24)** 를 선택합니다. |
    | 공용 IP | **없음**을 선택합니다. 공용 IP 주소는 필요하지 않습니다. VM은 인터넷을 통해 연결되지 않습니다.|

1. 나머지는 기본값으로 두고, **다음: 관리**를 선택합니다.

1. **가상 머신 만들기 - 관리**에서 **진단 스토리지 계정**에 대해 **새로 만들기**를 선택합니다.

1. **스토리지 계정 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | *mynvastorageaccount*를 입력합니다. |
    | 계정 종류 | 기본값인 **스토리지(범용 v1)** 를 그대로 둡니다. |
    | 성능 | 기본값인 **표준**을 그대로 둡니다. |
    | 복제 | 기본값인 **LRS(로컬 중복 스토리지)** 를 그대로 둡니다.

1. **확인**을 선택합니다.

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동되고, Azure에서 구성의 유효성을 검사합니다.

1. **유효성 검사 통과**가 표시되면 **만들기**를 선택합니다.

    VM을 만드는 데 몇 분이 걸립니다. Azure에서 VM 만들기가 완료되면 계속하세요. **배포 진행 중** 페이지에 배포 세부 정보가 표시됩니다.

1. VM이 준비되면 **리소스로 이동**을 선택합니다.

## <a name="turn-on-ip-forwarding"></a>IP 전달 설정

*myVmNva*에 대해 IP 전달을 설정합니다. Azure에서 네트워크 트래픽을 *myVmNva*에 보낼 때 트래픽이 다른 IP 주소로 지정된 경우 IP 전달은 트래픽을 올바른 위치에 보냅니다.

1. **myVmNva**의 **설정** 아래에서 **네트워킹**을 선택합니다.

1. **myvmnva123**을 선택합니다. 이는 Azure에서 VM에 대해 만든 네트워크 인터페이스이며, 사용자에게 고유한 숫자의 문자열이어야 합니다.

    ![VM 네트워킹](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. **설정** 아래에서 **IP 구성**을 선택합니다.

1. **myvmnva123 - IP 구성**에서 **IP 전달**에 대해 **사용**을 선택한 다음, **저장**을 선택합니다.

    ![IP 전달을 사용하도록 설정](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>공용 및 프라이빗 가상 머신 만들기

가상 네트워크에 공용 VM 및 프라이빗 VM을 만듭니다. 나중에 Azure에서 *공용* 서브넷 트래픽을 NVA를 통해 *프라이빗* 서브넷으로 라우팅하는지 확인하기 위해 이러한 VM을 사용합니다.

[NVA 만들기](#create-an-nva)의 1-12 단계를 수행합니다. 대부분의 동일한 설정을 사용합니다. 서로 달라야 하는 값은 다음과 같습니다.

| 설정 | 값 |
| ------- | ----- |
| **공용 VM** | |
| 기본 사항 |  |
| 가상 머신 이름 | *myVmPublic*을 입력합니다. |
| 네트워킹 | |
| 서브넷 | **공용(10.0.0.0/24)** 을 선택합니다. |
| 공용 IP 주소 | 기본값을 적용합니다. |
| 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
| 인바운드 포트 선택 | **HTTP** 및 **RDP**를 선택합니다. |
| 관리 | |
| 진단 스토리지 계정 | 기본값인 **mynvastorageaccount**를 그대로 둡니다. |
| **개인 VM** | |
| 기본 사항 |  |
| 가상 머신 이름 | *myVmPrivate*를 입력합니다. |
| 네트워킹 | |
| 서브넷 | **사설(10.0.1.0/24)** 을 선택합니다. |
| 공용 IP 주소 | 기본값을 적용합니다. |
| 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
| 인바운드 포트 선택 | **HTTP** 및 **RDP**를 선택합니다. |
| 관리 | |
| 진단 스토리지 계정 | 기본값인 **mynvastorageaccount**를 그대로 둡니다. |

Azure가 *myVmPublic* VM을 만드는 동안 *myVmPrivate* VM을 만들 수 있습니다. Azure에서 두 개의 VM이 모두 만들어지면 나머지 단계를 계속 진행하세요.

## <a name="route-traffic-through-an-nva"></a>NVA를 통한 트래픽 라우팅

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>원격 데스크톱을 통해 myVmPrivate에 로그인

1. 포털의 검색 창에서 *myVmPrivate*를 입력합니다.

1. 검색 결과에 **myVmPrivate** VM이 나타나면 선택합니다.

1. **연결**을 선택하여 *myVmPrivate* VM에 대한 원격 데스크톱 연결을 만듭니다.

1. **가상 머신에 연결**에서 **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. 다운로드한 *.rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. 프라이빗 VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

    1. 프라이빗 VM 자격 증명을 사용하려면 **기타 선택 사항** > **다른 계정 사용**을 차례로 선택해야 할 수도 있습니다.

1. **확인**을 선택합니다.

    로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다.

1. **예**를 선택하여 VM에 연결합니다.

### <a name="enable-icmp-through-the-windows-firewall"></a>Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

이후 단계에서는 추적 경로 도구를 사용하여 라우팅을 테스트합니다. 추적 경로는 Windows 방화벽에서 기본적으로 거부하는 ICMP(Internet Control Message Protocol)를 사용합니다. Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

1. *myVmPrivate*의 원격 데스크톱에서 PowerShell을 엽니다.

1. 다음 명령을 입력합니다.

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    이 자습서에서는 추적 경로를 사용하여 라우팅을 테스트합니다. 프로덕션 환경에서는 Windows 방화벽을 통한 ICMP를 허용하지 않는 것이 좋습니다.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>myVmNva 내에서 IP 전달 설정

Azure를 사용하여 VM의 네트워크 인터페이스에 대한 [IP 전달을 설정](#turn-on-ip-forwarding)했습니다. 이제 VM의 운영 체제에서도 네트워크 트래픽을 전달해야 합니다. 다음 명령을 사용하여 *myVmNva* VM의 운영 체제에 대한 IP 전달을 설정합니다.

1. *myVmPrivate* VM의 명령 프롬프트에서 다음과 같이 *myVmNva* VM에 대한 원격 데스크톱을 엽니다.

    ```cmd
    mstsc /v:myvmnva
    ```

1. *myVmNva*의 PowerShell에서 다음 명령을 입력하여 IP 전달을 설정합니다.

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. *myVmNva* VM을 다시 시작합니다. 작업 표시줄에서 **시작 단추** > **전원 단추**, **기타(계획됨)**  > **계속**을 차례로 선택합니다.

    그러면 원격 데스크톱 세션의 연결도 끊어집니다.

1. *myVmNva* VM이 다시 시작되면 *myVmPublic* VM에 대한 원격 데스크톱 세션을 만듭니다. *myVmPrivate* VM에 연결된 상태에서 명령 프롬프트를 열고 다음 명령을 실행합니다.

    ```cmd
    mstsc /v:myVmPublic
    ```
1. *myVmPublic*의 원격 데스크톱에서 PowerShell을 엽니다.

1. 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

    첫 번째 홉이 10.0.2.4에 있음을 알 수 있습니다. NVA의 개인 IP 주소입니다. 두 번째 홉은 *myVmPrivate* VM의 개인 IP 주소입니다. 10.0.1.4에 있습니다. 이전에 경로를 *myRouteTablePublic* 경로 테이블에 추가하여 *공용* 서브넷에 연결했습니다. 이에 따라 Azure에서 NVA를 통해 트래픽을 보냈지만, *프라이빗* 서브넷으로는 직접 보내지 않았습니다.

1. *myVmPublic* VM에 대한 원격 데스크톱 세션을 닫습니다. 그러면 *myVmPrivate* VM에 연결된 상태가 유지됩니다.

1. *myVmPrivate* VM의 명령 프롬프트에서 다음 명령을 입력합니다.

    ```cmd
    tracert myVmPublic
    ```

    *myVmPrivate* VM에서 *myVmPublic* VM으로의 네트워크 트래픽 라우팅을 테스트합니다. 응답은 다음 예제와 비슷합니다.

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Azure에서 트래픽을 *myVmPrivate* VM에서 *myVmPublic* VM으로 직접 라우팅하는 것을 알 수 있습니다. 기본적으로 Azure는 트래픽을 서브넷 간에 직접 라우팅합니다.

1. *myVmPrivate* VM에 대한 원격 데스크톱 세션을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 이 그룹에 있는 모든 리소스를 삭제합니다.

1. 포털의 검색 창에서 *myResourceGroup*을 입력합니다.

1. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. **리소스 그룹 이름 입력:** 에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 경로 테이블을 만들고 서브넷에 연결했습니다. 공용 서브넷에서 프라이빗 서브넷으로 트래픽을 라우팅하는 간단한 NVA를 만들었습니다. 이제 이러한 작업을 수행하는 방법을 알아보았으므로 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)에서 미리 구성된 여러 NVA를 배포할 수 있습니다. 이러한 NVA는 유용한 많은 네트워크 기능을 수행합니다. 라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md) 및 [경로 테이블 관리](manage-route-table.md)를 참조하세요.

가상 네트워크 내에 많은 Azure 리소스를 배포할 수 있지만, Azure에서는 일부 PaaS 서비스에 대한 리소스를 가상 네트워크에 배포할 수 없습니다. 일부 Azure PaaS 서비스의 리소스에 대한 액세스를 제한할 수 있습니다. 그러나 이 제한은 가상 네트워크 서브넷의 트래픽일 뿐입니다. Azure PaaS 리소스에 대한 네트워크 액세스를 제한하는 방법에 대해 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [PaaS 리소스에 대한 네트워크 액세스 제한](tutorial-restrict-network-access-to-resources.md)
