---
title: 네트워크 트래픽 라우팅 - 자습서 - Azure Portal
titlesuffix: Azure Virtual Network
description: 이 자습서에서는 Azure Portal을 사용하여 경로 테이블이 포함된 네트워크 트래픽을 라우팅하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 7da59e996ec37d3653dbde68c5f56caa9e8261ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061913"
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

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 활성 구독이 포함된 Azure 계정이 필요합니다. 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal 메뉴에서 **리소스 만들기** 를 선택합니다. Azure Marketplace에서 **네트워킹** > **가상 네트워크** 를 선택하거나 검색 상자에서 **Virtual Network** 를 검색합니다.

2. **만들기** 를 선택합니다.

2. **가상 네트워크 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **새로 만들기** 를 선택하고, **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | Name | **myVirtualNetwork** 를 입력합니다. |
    | 위치 | **(미국) 미국 동부** 를 선택합니다.|

3. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

4. **IPv4 주소 공간** 에서 기존 주소 공간을 선택하고 **10.0.0.0/16** 으로 변경합니다.

4. **+서브넷 추가** 를 선택한 다음, **서브넷 이름** 에 **Public** 을 입력하고 **서브넷 주소 범위** 에 **10.0.0.0/24** 를 입력합니다.

5. **추가** 를 선택합니다.

6. **+서브넷 추가** 를 선택한 다음, **서브넷 이름** 에 **Private** 을 입력하고 **서브넷 주소 범위** 에 **10.0.1.0/24** 를 입력합니다.

7. **추가** 를 선택합니다.

8. **+서브넷 추가** 를 선택한 다음, **서브넷 이름** 에 **DMZ** 를 입력하고 **서브넷 주소 범위** 에 **10.0.2.0/24** 를 입력합니다.

9. **추가** 를 선택합니다.

10. **보안** 탭을 선택하거나 페이지 하단에 있는 **다음: 보안** 단추를 선택합니다.

11. **BastionHost** 에서 **사용** 을 선택합니다. 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | Bastion 이름 | **myBastionHost** 입력 |
    | AzureBastionSubnet 주소 공간 | **10.0.3.0/24** 를 입력합니다. |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다. </br> **이름** 에 대해 **myBastionIP** 를 입력합니다. </br> **확인** 을 선택합니다. |

12. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

13. **만들기** 를 선택합니다.

## <a name="create-an-nva"></a>NVA 만들기

NVA(네트워크 가상 어플라이언스)는 라우팅 및 방화벽 최적화와 같은 네트워크 기능을 지원하는 가상 머신입니다. 이 자습서에서는 **Windows Server 2019 Datacenter** 를 사용하고 있다고 가정합니다. 원하는 경우 다른 운영 체제를 선택할 수 있습니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 차례로 선택합니다. 
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVMNVA** 를 입력합니다. |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter** 를 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |    |
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
    |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVirtualNetwork** 를 선택합니다. |
    | 서브넷 | **DMZ** 를 선택합니다. |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본** 을 선택합니다.|
    | 퍼블릭 인바운드 포트 네트워크 | **없음** 을 선택합니다. |
   
5. **검토 + 만들기** 탭을 선택하거나 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

## <a name="create-a-route-table"></a>경로 테이블 만들기

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

2. 검색 상자에 **경로 테이블** 을 입력합니다. 검색 결과에 **경로 테이블** 이 나타나면 이를 선택합니다.

3. **경로 테이블** 페이지에서 **만들기** 를 선택합니다.

4. **기본** 탭의 **경로 테이블 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |    |
    | 지역 | **미국 동부** 를 선택합니다. |
    | Name | **myRouteTablePublic** 을 입력합니다. |
    | 게이트웨이 경로 전파 | **예** 를 선택합니다. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="경로 테이블 만들기, Azure Portal" border="true":::

5. **검토 + 만들기** 탭을 선택하거나 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.

## <a name="create-a-route"></a>경로 만들기

1. 경로 테이블을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **경로 테이블** 을 검색하여 선택합니다.

2. 경로 테이블 이름 **myRouteTablePublic** 을 선택합니다.

3. **myRouteTablePublic** 페이지의 **설정** 섹션에서 **경로** 를 선택합니다.

4. 경로 페이지에서 **+ 추가** 단추를 선택합니다.

5. **경로 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 경로 이름 | **ToPrivateSubnet** 을 입력합니다. |
    | 주소 접두사 | **10.0.1.0/24**(이전에 만든 **Private** 서브넷의 주소 범위)를 입력합니다. |
    | 다음 홉 유형 | **가상 어플라이언스** 를 선택합니다. |
    | 다음 홉 주소 | **10.0.2.4**(**DMZ** 서브넷의 주소 범위 내 주소)를 입력합니다. |

6. **확인** 을 선택합니다.

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

1. 가상 네트워크를 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 네트워크** 를 검색하여 선택합니다.

2. 가상 네트워크 이름 **myVirtualNetwork** 를 선택합니다.

3. **myVirtualNetwork** 페이지의 **설정** 섹션에서 **서브넷** 을 선택합니다.

4. 가상 네트워크의 서브넷 목록에서 **Public** 을 선택합니다.

5. **경로 테이블** 에서, 앞에서 만든 경로 테이블 **myRouteTablePublic** 을 선택합니다. 

6. **저장** 을 선택하여 경로 테이블을 **Public** 서브넷에 연결합니다.

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="경로 테이블 연결, 서브넷 목록, 가상 네트워크, Azure Portal" border="true":::

## <a name="turn-on-ip-forwarding"></a>IP 전달 설정

다음으로, 새 NVA 가상 머신 **myVMNVA** 에 대한 IP 전달을 켭니다. Azure에서 **myVMNVA** 에 네트워크 트래픽을 보낼 때 트래픽 대상이 다른 IP 주소인 경우 IP 전달은 트래픽을 올바른 위치로 보냅니다.

1. VM을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신** 을 검색하여 선택합니다.

2. 가상 머신 이름 **myVMNVA** 를 선택합니다.

3. **myVMNVA** 개요 페이지의 **설정** 에서 **네트워킹** 을 선택합니다.

4. **myVMNVA** 의 **네트워킹** 페이지에서 **네트워크 인터페이스** 옆에 있는 네트워크 인터페이스를 선택합니다.  인터페이스 이름은 **myvmnva** 로 시작합니다.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="네트워킹, NVA(네트워크 가상 어플라이언스) VM(가상 머신), Azure Portal" border="true":::

5. 네트워크 인터페이스 개요 페이지의 **설정** 에서 **IP 구성** 을 선택합니다.

6. **IP 구성** 페이지에서 **IP 전달** 을 **사용** 으로 설정한 다음, **저장** 을 선택합니다.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="IP 전달을 사용하도록 설정" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>공용 및 프라이빗 가상 머신 만들기

가상 네트워크에 공용 VM 및 프라이빗 VM을 만듭니다. 나중에 Azure에서 **공용** 서브넷 트래픽을 NVA를 통해 **프라이빗** 서브넷으로 라우팅하는지 확인하기 위해 이러한 VM을 사용합니다.


### <a name="public-vm"></a>공용 VM

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 차례로 선택합니다. 
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVMPublic** 을 입력합니다. |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter** 를 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |    |
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
    |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVirtualNetwork** 를 선택합니다. |
    | 서브넷 | **Public** 을 선택합니다. |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본** 을 선택합니다.|
    | 퍼블릭 인바운드 포트 네트워크 | **없음** 을 선택합니다. |
   
5. **검토 + 만들기** 탭을 선택하거나 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

### <a name="private-vm"></a>개인 VM

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 차례로 선택합니다. 
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVMPrivate** 을 입력합니다. |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter** 를 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |    |
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
    |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVirtualNetwork** 를 선택합니다. |
    | 서브넷 | **Private** 을 선택합니다. |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본** 을 선택합니다.|
    | 퍼블릭 인바운드 포트 네트워크 | **없음** 을 선택합니다. |
   
5. **검토 + 만들기** 탭을 선택하거나 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

## <a name="route-traffic-through-an-nva"></a>NVA를 통한 트래픽 라우팅

### <a name="sign-in-to-private-vm"></a>프라이빗 vm에 로그인

1. 개인 VM을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신** 을 검색하여 선택합니다.

2. 프라이빗 가상 머신 이름 **myVmPrivate** 을 선택합니다.

3. VM 메뉴 모음에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

4. **연결** 페이지에서 파란색 **Bastion 사용** 단추를 선택합니다.

5. **Bastion** 페이지에서 이전에 가상 머신에 대해 만든 사용자 이름과 암호를 입력합니다.

6. **연결** 을 선택합니다.

### <a name="configure-firewall"></a>방화벽 구성

이후 단계에서는 추적 경로 도구를 사용하여 라우팅을 테스트합니다. 추적 경로는 Windows 방화벽에서 기본적으로 거부하는 ICMP(Internet Control Message Protocol)를 사용합니다. 

Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

1. **myVMPrivate** 의 베스천 연결에서 관리자 권한으로 PowerShell을 엽니다.

2. 다음 명령을 입력합니다.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    이 자습서에서는 추적 경로를 사용하여 라우팅을 테스트합니다. 프로덕션 환경에서는 Windows 방화벽을 통한 ICMP를 허용하지 않는 것이 좋습니다.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>myVMNVA 내에서 IP 전달 켜기

Azure를 사용하여 VM의 네트워크 인터페이스에 대한 [IP 전달을 설정](#turn-on-ip-forwarding)했습니다. 가상 머신의 운영 체제에서도 네트워크 트래픽을 전달해야 합니다. 

다음 명령을 사용하여 **myVMNVA** 의 IP 전달을 켭니다.

1. **myVMPrivate** VM의 PowerShell에서 다음과 같이 **myVMNVA** VM에 대한 원격 데스크톱을 엽니다.

    ```powershell
    mstsc /v:myvmnva
    ```

2. **myVMNVA** VM의 PowerShell에서 다음 명령을 입력하여 IP 전달을 켭니다.

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. **myVMNVA** 를 다시 시작합니다.

    ```powershell
    Restart-Computer
    ```

4. **myVMNVA** 를 다시 시작한 후에는 **myVMPublic** 에 대한 원격 데스크톱 세션을 만듭니다. 
    
    **myVMPrivate** 에 연결된 동안 PowerShell을 열고 다음 명령을 실행합니다.

    ```powershell
    mstsc /v:myvmpublic
    ```
5. **myVMPublic** 의 원격 데스크톱에서 PowerShell을 엽니다.

6. 다음 명령을 입력하여 Windows 방화벽을 통해 ICMP를 사용하도록 설정합니다.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>네트워크 트래픽의 라우팅 테스트

먼저 **myVMPublic** 에서 **myVMPrivate** 으로 네트워크 트래픽 라우팅을 테스트하겠습니다.

1. **myVMPublic** 의 PowerShell에서 다음 명령을 입력합니다.

    ```powershell
    tracert myvmprivate
    ```

    응답은 다음 예제와 비슷합니다.

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * 첫 번째 홉은 10.0.2.4이고 **myVMNVA** 의 개인 IP 주소임을 알 수 있습니다. 

    * 두 번째 홉은 **myVMPrivate** 의 개인 IP 주소 10.0.1.4입니다. 

    이전에 경로를 **myRouteTablePublic** 경로 테이블에 추가하여 *공용* 서브넷에 연결했습니다. Azure에서 NVA를 통해 트래픽을 보냈지만, *Private* 서브넷으로는 직접 보내지 않았습니다.

2. **myVMPublic** 에 대한 원격 데스크톱 세션을 닫습니다. 닫아도 **myVMPrivate** 에 연결된 상태가 유지됩니다.

3. **myVMPrivate** 에서 PowerShell을 열고 다음 명령을 입력합니다.

    ```powershell
    tracert myvmpublic
    ```

    다음 명령은 *myVmPrivate* VM에서 *myVmPublic* VM으로의 네트워크 트래픽 라우팅을 테스트합니다. 응답은 다음 예제와 비슷합니다.

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Azure가 트래픽을 *myVMPrivate* 에서 *myVMPublic* 으로 직접 라우팅한다는 것을 알 수 있습니다. 기본적으로 Azure는 트래픽을 서브넷 간에 직접 라우팅합니다.

4. **myVMPrivate** 에 대한 베스천 세션을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹이 더 이상 필요하지 않은 경우 **myResourceGroup** 및 이 그룹에 포함된 모든 리소스를 삭제합니다.

1. 리소스 그룹을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **리소스 그룹** 을 검색하여 선택합니다.

2. 리소스 그룹 이름 **myResourceGroup** 을 선택합니다.

3. **리소스 그룹 삭제** 를 선택합니다.

4. 확인 대화 상자에서 **리소스 그룹 이름 입력** 에 대해 **myResourceGroup** 을 입력한 다음, **삭제** 를 선택합니다. 


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행했습니다.

* 경로 테이블을 만들고 서브넷에 연결했습니다.
* 퍼블릭 서브넷에서 프라이빗 서브넷으로 트래픽을 라우팅하는 간단한 NVA를 만들었습니다. 

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)에서 많은 유용한 네트워크 기능을 제공하도록 미리 구성된 다른 NVA를 배포할 수 있습니다. 

라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md) 및 [경로 테이블 관리](manage-route-table.md)를 참조하세요.

가상 네트워크에서 네트워크 트래픽을 필터링하려면 다음 문서를 참조하세요.
> [!div class="nextstepaction"]
> [자습서: Azure Portal을 사용하여 네트워크 보안 그룹을 통해 네트워크 트래픽 필터링](tutorial-filter-network-traffic.md)
