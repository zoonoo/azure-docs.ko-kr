---
title: '자습서: 내부 부하 분산 장치 만들기 - Azure Portal'
titlesuffix: Azure Load Balancer
description: 이 자습서에서는 Azure Portal을 사용하여 내부 기본 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 56568cfb8fc659308475e581955e5acbdfd32b44
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489317"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>자습서: Azure Portal에서 기본 부하 분산 장치로 내부 트래픽 부하 분산

부하를 분산시키면 들어오는 요청을 VM(가상 머신)에 분산하여 높은 수준의 가용성과 크기 조정이 제공됩니다. Azure Portal을 사용하여 기본 부하 분산 장치를 만들고 여러 VM으로 내부 트래픽 부하를 분산시킬 수 있습니다. 이 빠른 시작에서는 기본 가격 계층에서 내부 부하 분산 장치, 백 엔드 서버 및 네트워크 리소스를 만들고 구성하는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

원할 경우 포털 대신 [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) 또는 [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md)을 사용하여 이러한 단계를 수행할 수 있습니다.

이 자습서를 사용하여 단계를 수행하려면 [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>VNet, 백 엔드 서버 및 테스트 VM 만들기

먼저 VNet(가상 네트워크)을 만듭니다. VNet에서 기본 부하 분산 장치의 백 엔드 풀에 사용할 VM을 두 개 만든 후, 부하 분산 장치를 테스트하는 데 사용할 세 번째 VM을 만듭니다. 

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.
   
1. **가상 네트워크 만들기** 창에서 다음 값을 입력하거나 선택합니다.
   
   - **이름**: *MyVNet*을 입력합니다.
   - **ResourceGroup**: **새로 만들기**를 선택하고 *MyResourceGroupLB*를 입력한 다음, **확인**을 선택합니다. 
   - **서브넷** > **이름**: *MyBackendSubnet*을 입력합니다.
   
1. **만들기**를 선택합니다.

   ![가상 네트워크 만들기](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>가상 머신 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **Windows Server 2016 Datacenter**를 차례로 선택합니다. 
   
1. **가상 머신 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: 드롭다운하고 **MyResourceGroupLB**를 선택합니다.
   - **인스턴스 정보** > **가상 머신 이름**: *MyVM1*을 입력합니다.
   - **인스턴스 정보** > **가용성 옵션**: 
     1. 드롭다운하고 **가용성 세트**를 선택합니다. 
     2. **새로 만들기**를 선택하고, *MyAvailabilitySet*를 입력한 후, **확인**을 선택합니다.
   
1. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹**을 차례로 선택합니다. 
   
   다음 항목이 선택되어 있는지 확인합니다.
   - **가상 네트워크**: **MyVNet**
   - **서브넷**: **MyBackendSubnet**
   
   **네트워크 보안 그룹**에서:
   1. **고급**을 선택합니다. 
   1. **네트워크 보안 그룹 구성**을 드롭다운하고 **None**을 선택합니다. 
   
1. **관리** 탭을 선택하거나 **다음** > **관리**를 선택합니다. **모니터링**에서 **부트 진단**을 **끄기**로 설정합니다.
   
1. **검토 + 만들기**를 선택합니다.
   
1. 설정을 검토한 다음, **만들기**를 선택합니다. 

1. 다음 단계에 따라 MyVM1과 동일한 다른 모든 설정을 사용하여 *MyVM2*이라는 두 번째 VM을 만듭니다. 

1. 단계에 따라 *MyTestVM*이라는 세 번째 VM을 만듭니다. 

## <a name="create-a-basic-load-balancer"></a>기본 부하 분산 장치 만들기

포털을 사용하여 기본 내부 부하 분산 장치를 만듭니다. 만드는 이름과 IP 주소는 자동으로 부하 분산 장치의 프런트 엔드로 구성됩니다.

1. 포털의 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Load Balancer**를 선택합니다.
   
2. **부하 분산 장치 만들기** 페이지의 **기본** 탭에서 다음 정보를 입력하거나 선택하고, 나머지 설정은 기본값을 그대로 적용한 다음, **리뷰 + 만들기**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 구독               | 구독을 선택합니다.    |    
    | 리소스 그룹         | **새로 만들기**를 선택하고 텍스트 상자에 *MyResourceGroupLB*를 입력합니다.|
    | Name                   | *myLoadBalancer*                                   |
    | 지역         | **유럽 서부**를 선택합니다.                                        |
    | Type          | **내부**를 선택합니다.                                        |
    | SKU           | **기본**을 선택합니다.                          |
    | 가상 네트워크           | *MyVNet*을 선택합니다.                          |    
    | IP 주소 할당              | **고정**을 선택합니다.   |
    | 개인 IP 주소|가상 네트워크 및 서브넷의 주소 공간에 있는 주소를 입력합니다(예: *10.3.0.7*).  |

3. **리뷰 + 만들기** 탭에서 **만들기**를 클릭합니다. 
   

## <a name="create-basic-load-balancer-resources"></a>기본 부하 분산 장치 리소스 만들기

이 섹션에서는 백 엔드 주소 풀 및 상태 프로브에 대한 부하 분산 장치 설정을 구성하고, 부하 분산 장치 규칙을 지정합니다.

### <a name="create-a-back-end-address-pool"></a>백 엔드 주소 풀 만들기

백 엔드 주소 풀은 트래픽을 VM에 배포하기 위해 부하 분산 장치에서 사용합니다. 백 엔드 주소 풀에는 부하 분산 장치에 연결된 가상 네트워크 인터페이스(NIC)의 IP 주소가 포함되어 있습니다. 

**VM1 및 VM2가 포함된 백 엔드 주소 풀을 만들려면 다음을 수행합니다.**

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정**에서 **백 엔드 풀**을 선택한 다음, **추가**를 선택합니다.
   
1. **백 엔드 풀 추가** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **이름**: *MyBackendPool*을 입력합니다.
   - **연결 대상**: 드롭다운하고 **가용성 세트**를 선택합니다.
   - **가용성 집합**: **MyAvailabilitySet**를 선택합니다.
   
1. **대상 네트워크 IP 구성 추가**를 선택합니다. 
   1. **MyVM1** 및 **MyVM2**를 백 엔드 풀에 추가합니다.
   2. 각 머신이 추가되면 드롭다운하고 해당 **네트워크 IP 구성**을 선택합니다. 
   
   >[!NOTE]
   >**MyTestVM**을 풀에 추가하지 마세요. 
   
1. **확인**을 선택합니다.
   
   ![백 엔드 주소 풀 추가](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. **백 엔드 풀** 페이지에서 **MyBackendPool**을 확장하고, **VM1** 및 **VM2**가 모두 나열되는지 확인합니다.

### <a name="create-a-health-probe"></a>상태 프로브 만들기

부하 분산 장치에서 VM 상태를 모니터링할 수 있게 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 

**VM 상태를 모니터링하는 상태 프로브를 만들려면 다음을 수행합니다.**

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정**에서 **상태 프로브**를 선택한 다음, **추가**를 선택합니다.
   
1. **상태 프로브 추가** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **이름**: *MyHealthProbe*를 입력합니다.
   - **프로토콜**: 드롭다운하고 **HTTP**를 선택합니다. 
   - **포트**: *80*을 입력합니다. 
   - **경로**: 기본 URI에 대해 */* 를 허용합니다. 이 값은 다른 URI로 바꿀 수 있습니다. 
   - **간격**: *15*를 입력합니다. 간격은 프로브 시도 사이의 시간(초)입니다.
   - **비정상 임계값**: *2*를 입력합니다. 이 값은 VM이 비정상 상태로 간주되는 데 필요한 연속 프로브 오류 횟수입니다.
   
1. **확인**을 선택합니다.
   
   ![프로브 추가](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성, 트래픽을 수신할 백 엔드 IP 풀, 필요한 원본 및 대상 포트를 정의합니다. 

**MyLoadBalancerRule**이라는 부하 분산 장치 규칙은 프런트 엔드 **LoadBalancerFrontEnd**의 포트 80에서 수신 대기합니다. 규칙은 포트 80에서 네트워크 트래픽을 백 엔드 주소 풀 **MyBackendPool**로 보냅니다. 

**부하 분산 장치 규칙을 만들려면 다음을 수행합니다.**

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정** 아래에서 **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
   
1. **부하 분산 규칙 추가** 페이지에서 다음 값이 아직 없는 경우 입력하거나 선택합니다.
   
   - **이름**: *MyLoadBalancerRule*을 입력합니다.
   - **프런트 엔드 IP 주소:** 아직 없는 경우 *LoadBalancerFrontEnd*를 입력합니다.
   - **프로토콜**: **TCP**를 선택합니다.
   - **포트**: *80*을 입력합니다.
   - **백 엔드 포트**: *80*을 입력합니다.
   - **백 엔드 풀**: **MyBackendPool**을 선택합니다.
   - **상태 프로브**: **MyHealthProbe**를 선택합니다. 
   
1. **확인**을 선택합니다.
   
   ![부하 분산 장치 규칙 추가](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

백 엔드 서버에서 IIS(인터넷 정보 서비스)를 설치한 다음, MyTestVM을 사용하여 해당 개인 IP 주소를 사용하여 부하 분산 장치를 테스트합니다. 부하 분산 장치가 두 VM 간에 요청을 분산하는 것을 확인할 수 있도록 각 백 엔드 VM은 기본 IIS 웹 페이지의 다른 버전을 제공합니다.

포털의 **MyLoadBalancer**에 대한 **개요** 페이지에 있는 **개인 IP 주소**에서 해당 IP 주소를 찾습니다. 주소를 마우스로 가리키고 **복사** 아이콘을 선택하여 복사합니다. 이 예제에서는 **10.3.0.7**입니다. 

### <a name="connect-to-the-vms-with-rdp"></a>RDP를 사용하여 VM에 연결

먼저, 3개의 모든 VM으로 RDP(원격 데스크톱)에 연결합니다. 

>[!NOTE]
>기본적으로 VM에는 원격 데스크톱 액세스를 허용하는 **RDP**(원격 데스크톱) 포트가 있습니다. 

**VM에 RDP(원격 데스크톱)을 연결하려면 다음을 수행합니다.**

1. 포털의 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **MyResourceGroupLB** 리소스 그룹에서 각 VM을 선택합니다.
   
1. **개요** 페이지에서 **연결**을 선택한 다음, **RDP 파일 다운로드**를 선택합니다. 
   
1. 다운로드한 RDP 파일을 열고, **연결**을 선택합니다.
   
1. Windows 보안 화면에서 **기타 선택 사항**을 선택한 다음, **다른 계정 사용**을 선택합니다. 
   
   사용자 이름과 암호를 입력한 후 **확인**을 선택합니다.
   
1. 인증서 프롬프트에서 **예**로 응답합니다. 
   
   VM 데스크톱이 새 창에서 열립니다. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>백 엔드 VM에서 IIS 설치 및 기본 IIS 페이지 바꾸기

각 백 엔드 서버에서 PowerShell을 사용하여 IIS를 설치하고 기본 IIS 웹 페이지를 사용자 지정된 페이지로 바꿉니다.

>[!NOTE]
>**서버 관리자**에서 **역할 및 기능 추가 마법사**를 사용하여 IIS를 설치할 수도 있습니다. 

**PowerShell을 사용하여 IIS를 설치하고 기본 웹 페이지를 업데이트하려면 다음을 수행합니다.**

1. MyVM1 및 MyVM2의 **시작** 메뉴에서 **Windows PowerShell**을 시작합니다. 

2. 다음 명령을 실행하여 IIS를 설치하고 기본 IIS 웹 페이지를 바꿉니다.
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. **연결 끊기**를 선택하여 MyVM1 및 MyVM2와의 RDP 연결을 닫습니다. VM은 종료하지 마세요.

### <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

1. MyTestVM에서 **Internet Explorer**를 열고 모든 구성에 메시지에 대해 **확인**으로 응답합니다. 
   
1. 브라우저의 주소 표시줄에 부하 분산 장치의 개인 IP 주소(*10.3.0.7*)를 입력하거나 붙여넣습니다. 
   
   사용자 지정된 IIS 웹 서버 기본 페이지가 브라우저에 표시됩니다. 메시지는 **MyVM1에서 Hello World** 또는 **MyVM2에서 Hello World**를 읽습니다.
   
1. VM 간에 트래픽을 분산하는 부하 분산 장치를 확인하려면 브라우저를 새로 고칩니다. 시도 간 브라우저 캐시를 지워야 할 수도 있습니다.

   부하 분산 장치가 각 백 엔드 VM에 요청을 분산하므로 어떤 때는 **MyVM1** 페이지가 표시되고, 또 어떤 때는 **MyVM2** 페이지가 표시됩니다. 

   ![새 IIS 기본 페이지](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 부하 분산 장치 및 모든 관련 리소스를 삭제하려면 **MyResourceGroupLB** 리소스 그룹을 열고 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 기본 계층 내부 부하 분산 장치를 만들었습니다. 부하 분산 장치에서 사용할 리소스 그룹, 백 엔드 서버, 상태 프로브 및 규칙을 만들고 구성했습니다. 백 엔드 VM에 IIS를 설치하고 테스트 VM을 사용하여 브라우저에서 부하 분산 장치를 테스트했습니다. 

다음으로, 가용성 영역에 VM 부하 분산 방법을 알아보겠습니다.

> [!div class="nextstepaction"]
> [가용성 영역에 VM 부하 분산](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
