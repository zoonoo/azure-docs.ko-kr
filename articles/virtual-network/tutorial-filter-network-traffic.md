---
title: 네트워크 트래픽 필터링 - 자습서 - Azure Portal
titlesuffix: Azure Virtual Network
description: 이 자습서에서는 Azure Portal을 사용하여 네트워크 보안 그룹을 통해 서브넷에 네트워크 트래픽을 필터링하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: 4097d4fc46aac88cd44d21a4cdcf0d7d5093feea
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242730"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 네트워크 보안 그룹을 통해 네트워크 트래픽 필터링

네트워크 보안 그룹을 통해 가상 네트워크 서브넷에서 네트워크 트래픽을 인바운드 및 아웃바운드로 필터링할 수 있습니다. 네트워크 보안 그룹에는 IP 주소, 포트, 프로토콜에 따라 네트워크 트래픽을 필터링하는 보안 규칙이 포함됩니다. 보안 규칙은 서브넷에 배포된 리소스에 적용됩니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 네트워크 보안 그룹 및 보안 규칙 만들기
> * 가상 네트워크 만들기 및 서브넷에 네트워크 보안 그룹 연결
> * 서브넷에 VM(가상 머신) 배포
> * 트래픽 필터 테스트

원하는 경우 [Azure CLI](tutorial-filter-network-traffic-cli.md) 또는 [PowerShell](tutorial-filter-network-traffic-powershell.md)을 사용하여 이 자습서를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Name                    | myVirtualNetwork                                   |
    | 주소 공간           | 10.0.0.0/16                                        |
    | 구독            | 구독을 선택합니다.                          |
    | 리소스 그룹          | **새로 만들기**를 선택하고 *myResourceGroup*을 입력합니다. |
    | 위치                | **미국 동부**를 선택합니다.                                |
    | 서브넷 - 이름            | mySubnet                                           |
    | 서브넷 - 주소 범위  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>애플리케이션 보안 그룹 만들기

애플리케이션 보안 그룹을 사용하면 웹 서버와 같은 유사한 기능을 갖는 서버를 함께 그룹화할 수 있습니다.

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **Marketplace 검색** 상자에서 *애플리케이션 보안 그룹*을 입력합니다. 검색 결과에 표시되는 **애플리케이션 보안 그룹**을 선택하고 **모든 항목** 아래에서 **애플리케이션 보안 그룹**을 다시 선택한 다음, **만들기**를 선택합니다.
3. 다음 정보를 입력하거나 선택하고 **만들기**를 선택합니다.

    | 설정        | 값                                                         |
    | ---            | ---                                                           |
    | Name           | myAsgWebServers                                               |
    | 구독   | 구독을 선택합니다.                                     |
    | 리소스 그룹 | **기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다. |
    | 위치       | 미국 동부                                                       |

4. 다음 값을 지정하여 3단계를 다시 완료합니다.

    | 설정        | 값                                                         |
    | ---            | ---                                                           |
    | Name           | myAsgMgmtServers                                              |
    | 구독   | 구독을 선택합니다.                                     |
    | 리소스 그룹 | **기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다. |
    | 위치       | 미국 동부                                                       |

## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택하고 **네트워크 보안 그룹**을 선택합니다.
3. 다음 정보를 입력하거나 선택하고 **만들기**를 선택합니다.

    |설정|값|
    |---|---|
    |Name|myNsg|
    |구독| 구독을 선택합니다.|
    |리소스 그룹 | **기존 항목 사용**을 선택한 다음, *myResourceGroup*을 선택합니다.|
    |위치|미국 동부|

## <a name="associate-network-security-group-to-subnet"></a>서브넷에 네트워크 보안 그룹 연결

1. 포털 맨 위에 있는 *리소스, 서비스 및 문서 검색* 상자에 *myNsg*를 입력하기 시작합니다. 검색 결과에서 표시되는 **myNsg**를 선택합니다.
2. 다음 그림과 같이 **설정**에서 **서브넷**을 선택한 다음, **+ 연결**을 선택합니다.

    ![서브넷에 NSG 연결](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. **서브넷 연결**에서 **가상 네트워크**를 선택하고 **myVirtualNetwork**를 선택합니다. **서브넷**, **mySubnet**을 차례로 선택한 다음, **확인**을 선택합니다.

## <a name="create-security-rules"></a>보안 규칙 만들기

1. 다음 그림과 같이 **설정**에서 **인바운드 보안 규칙**을 선택한 다음, **+ 추가**를 선택합니다.

    ![인바운드 보안 규칙 추가](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. **myAsgWebServers** 애플리케이션 보안 그룹에 포트 80 및 443을 허용하는 보안 규칙을 만듭니다. **인바운드 보안 규칙 추가**에서 다음 값을 직접 입력 또는 선택하고 나머지 기본값을 적용한 다음, **추가**를 선택합니다.

    | 설정                 | 값                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | 대상             | **애플리케이션 보안 그룹**을 선택한 다음, **애플리케이션 보안 그룹**에 **myAsgWebServers**를 선택합니다.  |
    | 대상 포트 범위 | 80,443 입력                                                                                                    |
    | 프로토콜                | TCP 선택                                                                                                      |
    | Name                    | Allow-Web-All                                                                                                   |

3. 다음 값을 사용하여 2단계를 다시 완료합니다.

    | 설정                 | 값                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | 대상             | **애플리케이션 보안 그룹**을 선택한 다음, **애플리케이션 보안 그룹**에 **myAsgMgmtServers**를 선택합니다. |
    | 대상 포트 범위 | 3389 입력                                                                                                      |
    | 프로토콜                | TCP 선택                                                                                                      |
    | 우선 순위                | 110 입력                                                                                                       |
    | Name                    | Allow-RDP-All                                                                                                   |

    이 자습서에서 RDP(포트 3389)는 *myAsgMgmtServers* 애플리케이션 보안 그룹에 할당된 VM에 대해 인터넷에 노출됩니다. 프로덕션 환경에서는 포트 3389를 인터넷에 노출하는 대신 VPN 또는 프라이빗 네트워크 연결을 통해 관리하려는 Azure 리소스에 연결하는 것이 좋습니다.

1-3단계를 완료한 후 사용자가 만든 규칙을 검토합니다. 사용자 목록은 다음 그림의 목록과 같이 표시됩니다.

![보안 규칙](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

1. Azure Portal의 왼쪽 위 모서리에 있는 **+ 리소스 만들기**를 선택합니다.
2. **Compute**를 선택한 다음, **Windows Server 2016 Datacenter**를 선택합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |Name|myVmWeb|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    |구독| 구독을 선택합니다.|
    |리소스 그룹| **기존 항목 사용**을 선택하고, **myResourceGroup**을 선택합니다.|
    |위치| **미국 동부**를 선택합니다.|

4. VM에 대한 크기를 선택한 다음, **선택**을 선택합니다.
5. **설정**에서 다음 값을 선택하고, 나머지 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |가상 네트워크 |**myVirtualNetwork**를 선택합니다.|
    |네트워크 보안 그룹 | **고급**을 선택합니다.|
    |네트워크 보안 그룹(방화벽)| **(신규) myVmWeb-nsg**를 선택한 다음, **네트워크 보안 그룹 선택**에서 **없음**을 선택합니다. |

6. **요약**의 **만들기** 아래에서 **만들기**를 선택하여 VM 배포를 시작합니다.

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

1-6단계를 다시 수행하지만, 3단계에서 VM 이름을 *myVmMgmt*로 지정합니다. VM을 배포하는 데 몇 분이 걸립니다. VM이 배포될 때까지 다음 단계를 진행하지 마세요.

## <a name="associate-network-interfaces-to-an-asg"></a>네트워크 인터페이스를 ASG에 연결

포털에서 VM을 만들 때, 각 VM에 대한 네트워크 인터페이스가 생성되고 네트워크 인터페이스는 VM에 연결됩니다. 이전에 생성한 애플리케이션 보안 그룹 중 하나에 각 VM의 네트워크 인터페이스를 추가합니다.

1. 포털 맨 위에 있는 *리소스, 서비스 및 문서 검색* 상자에 *myVmWeb*을 입력하기 시작합니다. 검색 결과에 **myVmWeb** VM이 나타나면 선택합니다.
2. **설정**에서 **네트워킹**을 선택합니다.  다음 그림과 같이 **애플리케이션 보안 그룹 구성**을 선택하고, **애플리케이션 보안 그룹**으로 **myAsgWebServers**를 선택한 다음, **저장**을 선택합니다.

    ![ASG에 연결](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. 1 및 2단계를 다시 완료한 후 **myVmMgmt** VM을 검색하고 **myAsgMgmtServers** ASG를 선택합니다.

## <a name="test-traffic-filters"></a>트래픽 필터 테스트

1. *myVmMgmt* VM에 연결합니다. 포털 맨 위에 있는 검색 상자에 *myVmMgmt*를 입력합니다. 검색 결과에 **myVmMgmt**가 표시되면 선택합니다. **연결** 단추를 선택합니다.
2. **RDP 파일 다운로드**를 선택합니다.
3. 다운로드한 rdp 파일을 열고 **연결**을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **추가 선택 사항**, **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.
4. **확인**을 선택합니다.
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속합니다.

    포트 3389는 *myAsgMgmtServers* VM에 연결된 네트워크 인터페이스가 In인 *myVmMgmt* 애플리케이션 보안 그룹으로 가는 인터넷의 인바운드 트래픽을 허용하기 때문에 연결에 성공합니다.

6. PowerShell 세션에서 다음 명령을 입력하여 *myVmMgmt* VM에서 *myVmWeb* VM에 연결합니다.

    ``` 
    mstsc /v:myVmWeb
    ```

    동일한 가상 네트워크의 VM이 기본적으로 모든 포트를 통해 서로 통신할 수 있기 때문에 myVmMgmt VM에서 myVmWeb VM에 연결할 수 있습니다. *myAsgWebServers*에 대한 보안 규칙에서는 인터넷으로부터의 포트 3389 인바운드를 허용하지 않고 인터넷으로부터의 인바운드 트래픽이 기본적으로 모든 리소스에 대해 거부되므로 인터넷으로부터 *myVmWeb* VM에 대한 원격 데스크톱 연결을 만들 수 없습니다.

7. *myVmWeb* VM에 Microsoft IIS를 설치하려면 *myVmWeb* VM의 PowerShell 세션에 다음 명령을 입력합니다.

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. IIS 설치가 완료된 후 *myVmWeb* VM 연결을 끊으면 *myVmMgmt* VM 원격 데스크톱 연결이 종료됩니다.
9. *myVmMgmt* VM과의 연결을 끊습니다.
10. 포털 맨 위에 있는 *리소스, 서비스 및 문서 검색* 상자에 컴퓨터의 *myVmWeb*을 입력하기 시작합니다. 검색 결과에 표시된 **myVmWeb**을 선택합니다. VM의 **공용 IP 주소**를 적어둡니다. 다음 그림에 표시된 주소는 137.135.84.74이지만 사용자 주소는 다릅니다.

    ![공용 IP 주소](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. 인터넷에서 *myVmWeb* 웹 서버에 액세스할 수 있는지 확인하기 위해 컴퓨터에서 인터넷 브라우저를 열고 `http://<public-ip-address-from-previous-step>`로 이동합니다. 포트 80은 *myVmWeb* VM에 연결된 네트워크 인터페이스가 있는 *myAsgWebServers* 애플리케이션 보안 그룹으로 가는 인터넷의 인바운드를 허용하기 때문에 IIS 시작 화면이 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 네트워크 보안 그룹을 만들어 가상 네트워크 서브넷에 연결했습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md) 및 [네트워크 보안 그룹 관리](manage-network-security-group.md)를 참조하세요.

기본적으로 Azure는 서브넷 간에 트래픽을 라우팅합니다. 그 대신 방화벽 역할을 하는 VM 등을 통해 서브넷 간에 트래픽을 라우팅하도록 선택할 수 있습니다. 경로 테이블을 만드는 방법을 알아보려면 다음 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [경로 테이블 만들기](./tutorial-create-route-table-portal.md)
