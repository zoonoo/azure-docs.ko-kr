---
title: '빠른 시작: Azure Portal을 사용하여 공용 기본 부하 분산 장치 만들기'
titlesuffix: Azure Load Balancer
description: 이 빠른 시작에서는 Azure Portal을 사용하여 공용 기본 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: db781899a3fe0d13d030943ed3ab4ebd3d105ad1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64727585"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 기본 Load Balancer 만들기

부하를 분산시키면 들어오는 요청을 VM(가상 머신)에 분산하여 높은 수준의 가용성과 크기 조정이 제공됩니다. Azure Portal을 사용하여 부하 분산 장치를 만들고 VM 간의 트래픽 부하를 분산할 수 있습니다. 이 빠른 시작에서는 기본 가격 계층에서 부하 분산 장치, 백 엔드 서버 및 네트워크 리소스를 만들고 구성하는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

이 빠른 시작의 작업을 수행하려면 [Azure Portal](https://portal.azure.com)에 로그인해야 합니다.

## <a name="create-a-basic-load-balancer"></a>기본 부하 분산 장치 만들기

먼저 포털을 사용하여 공용 기본 Load Balancer를 만듭니다. 만드는 이름과 공용 IP 주소는 자동으로 부하 분산 장치의 프런트 엔드로 구성됩니다.

1. 화면의 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **부하 분산 장치**를 클릭합니다.
2. **부하 분산 장치 만들기** 페이지의 **기본** 탭에서 다음 정보를 입력하거나 선택하고, 나머지 설정은 기본값을 그대로 유지한 다음, **검토 + 만들기**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 구독               | 구독을 선택합니다.    |    
    | 리소스 그룹         | **새로 만들기**를 선택하고 텍스트 상자에 *MyResourceGroupLB*를 입력합니다.|
    | 이름                   | *myLoadBalancer*                                   |
    | 지역         | **유럽 서부**를 선택합니다.                                        |
    | Type          | **공용**을 선택합니다.                                        |
    | SKU           | **기본**을 선택합니다.                          |
    | 공용 IP 주소 | **새로 만들기**를 선택합니다. |
    | 공용 IP 주소 이름              | *MyPublicIP*   |
    | 할당| 공용|

3. **리뷰 + 만들기** 탭에서 **만들기**를 클릭합니다.   


## <a name="create-back-end-servers"></a>백 엔드 서버 만들기

다음으로, 기본 부하 분산 장치의 백 엔드 풀에 대한 가상 네트워크와 두 개의 가상 머신을 만듭니다. 

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.
   
1. **가상 네트워크 만들기** 창에서 다음 값을 입력하거나 선택합니다.
   
   - **이름**: *MyVnet*을 입력합니다.
   - **ResourceGroup**: **기존 항목 선택**을 드롭다운하고, **MyResourceGroupLB**를 선택합니다. 
   - **서브넷** > **이름**: *MyBackendSubnet*을 입력합니다.
   
1. **만들기**를 선택합니다.

   ![가상 네트워크 만들기](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

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
   - **가상 네트워크**: **MyVnet**
   - **서브넷**: **MyBackendSubnet**
   - **공용 IP**: **MyVM1-ip**
   
   방화벽 유형인 NSG(네트워크 보안 그룹)를 만들려면 **네트워크 보안 그룹** 아래에서 **고급**을 선택합니다. 
   1. **네트워크 보안 그룹 구성** 필드에서 **새로 만들기**를 선택합니다. 
   1. *MyNetworkSecurityGroup*을 입력하고, **확인**을 선택합니다. 
   
1. **관리** 탭을 선택하거나 **다음** > **관리**를 차례로 선택합니다. **모니터링**에서 **부트 진단**을 **끄기**로 설정합니다.
   
1. **검토 + 만들기**를 선택합니다.
   
1. 설정을 검토한 다음, **만들기**를 선택합니다. 

1. 다음 단계에 따라 *MyVM2-ip*의 **공용 IP** 주소와 MyVM1과 동일한 다른 모든 설정을 사용하여 *MyVM2*이라는 두 번째 VM을 만듭니다. 

### <a name="create-nsg-rules-for-the-vms"></a>VM에 대한 NSG 규칙 만들기

이 섹션에서는 인바운드 인터넷(HTTP) 및 원격 데스크톱(RDP) 연결을 허용하는 VM에 대한 NSG(네트워크 보안 그룹) 규칙을 만듭니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **MyResourceGroupLB** 리소스 그룹에서 **MyNetworkSecurityGroup**을 선택합니다.
   
1. **설정** 아래에서 **인바운드 보안 규칙**을 선택한 다음, **추가**를 선택합니다.
   
1. **인바운드 보안 규칙 추가** 대화 상자에서 HTTP 규칙에 대해 다음을 입력하거나 선택합니다.
   
   - **원본**: **서비스 태그**를 선택합니다.  
   - **원본 서비스 태그**: **인터넷**을 선택합니다. 
   - **대상 포트 범위**: *80*을 입력합니다.
   - **프로토콜**: **TCP**를 선택합니다. 
   - **작업**: **허용**을 선택합니다.  
   - **우선 순위**: *100*을 입력합니다. 
   - **이름**: *MyHTTPRule*을 입력합니다. 
   - **설명**: ‘HTTP 허용’을 입력합니다.  
   
1. **추가**를 선택합니다. 
   
   ![NSG 규칙 만들기](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. 다음과 같은 다른 값을 사용하여 인바운드 RDP 규칙에 대한 단계를 반복합니다.
   - **대상 포트 범위**: *3389*를 입력합니다.
   - **우선 순위**: *200*을 입력합니다. 
   - **이름**: *MyRDPRule*을 입력합니다. 
   - **설명**: *RDP 허용*을 입력합니다. 

## <a name="create-resources-for-the-load-balancer"></a>부하 분산 장치에 대한 리소스 만들기

이 섹션에서는 백 엔드 주소 풀, 상태 프로브 및 부하 분산 장치 규칙에 대한 부하 분산 장치 설정을 구성합니다.

### <a name="create-a-backend-address-pool"></a>백 엔드 주소 풀 만들기

백 엔드 주소 풀은 트래픽을 VM에 배포하기 위해 부하 분산 장치에서 사용합니다. 백 엔드 주소 풀에는 부하 분산 장치에 연결된 가상 네트워크 인터페이스(NIC)의 IP 주소가 포함되어 있습니다. 

**VM1 및 VM2가 포함된 백 엔드 주소 풀을 만들려면:**

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정**에서 **백 엔드 풀**을 선택한 다음, **추가**를 선택합니다.
   
1. **백 엔드 풀 추가** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **이름**: *MyBackEndPool*을 입력합니다.
   - **다음에 연결됨**: 드롭다운하고 **가용성 세트**를 선택합니다.
   - **가용성 집합**: **MyAvailabilitySet**를 선택합니다.
   
1. **대상 네트워크 IP 구성 추가**를 선택합니다. 
   1. 백 엔드 풀에 만든 각각의 가상 머신(**MyVM1** 및 **MyVM2**)을 추가합니다.
   2. 각각의 머신이 추가되면 드롭다운하고 **네트워크 IP 구성**을 선택합니다. 
   
1. **확인**을 선택합니다.
   
   ![백 엔드 주소 풀 추가](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. **백 엔드 풀** 페이지에서 **MyBackendPool**을 확장하고, **VM1** 및 **VM2**가 모두 나열되는지 확인합니다.

### <a name="create-a-health-probe"></a>상태 프로브 만들기

부하 분산 장치에서 VM 상태를 모니터링할 수 있게 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 

**VM 상태를 모니터링하는 상태 프로브를 만들려면:**

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
   
   ![프로브 추가](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성, 트래픽을 수신할 백 엔드 IP 풀, 필요한 원본 및 대상 포트를 정의합니다. 

**MyLoadBalancerRule**이라는 부하 분산 장치 규칙은 프런트 엔드 **LoadBalancerFrontEnd**의 80 포트에서 수신 대기합니다. 또한 이 규칙은 80 포트에서 네트워크 트래픽을 **MyBackEndPool** 백 엔드 주소 풀로 보냅니다. 

**부하 분산 장치를 만들려면**


1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정** 아래에서 **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
   
1. **부하 분산 규칙 추가** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **이름**: *MyLoadBalancerRule*을 입력합니다.
   - **프런트 엔드 IP 주소:** *LoadBalancerFrontend*를 입력합니다.
   - **프로토콜**: **TCP**를 선택합니다.
   - **포트**: *80*을 입력합니다.
   - **백 엔드 포트**: *80*을 입력합니다.
   - **백 엔드 풀**: **MyBackendPool**을 선택합니다.
   - **상태 프로브**: **MyHealthProbe**를 선택합니다. 
   
1. **확인**을 선택합니다.
   
   ![부하 분산 장치 규칙 추가](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

공용 IP 주소를 사용하여 VM에서 부하 분산 장치를 테스트합니다. 

포털의 **MyLoadBalancer**에 대한 **개요** 페이지에 있는 **공용 IP 주소** 아래에서 해당 공용 IP 주소를 찾습니다. 주소 위를 마우스로 가리키고 **복사** 아이콘을 선택하여 복사합니다. 

### <a name="install-iis-on-the-vms"></a>VM에 IIS 설치

부하 분산 장치를 테스트하는 데 도움이 되는 IIS(인터넷 정보 서비스)를 가상 머신에 설치합니다.

**VM에 원격 데스크톱(RDP)을 연결하려면**

1. 포털의 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **MyResourceGroupLB** 리소스 그룹에서 **MyVM1**을 선택합니다.
   
1. **개요** 페이지에서 **연결**을 선택한 다음, **RDP 파일 다운로드**를 선택합니다. 
   
1. 다운로드한 RDP 파일을 열고, **연결**을 선택합니다.
   
1. Windows 보안 화면에서 **기타 선택 사항**을 선택한 다음, **다른 계정 사용**을 선택합니다. 
   
   사용자 이름과 암호를 입력하고, **확인**을 선택합니다.
   
1. 인증서 프롬프트에서 **예**로 응답합니다. 
   
   VM 데스크톱이 새 창에서 열립니다. 
   
**IIS를 설치 하려면**

1. 왼쪽 메뉴에서 **모든 서비스**를 선택한 다음, **모든 리소스**를 선택하고 리소스 목록에서 *myResourceGroupSLB* 리소스 그룹에 있는 **myVM1**을 선택합니다.
2. **개요** 페이지에서 **연결**을 선택하여 VM에 RDP로 연결합니다.
5. 이 VM을 만드는 동안 입력한 자격 증명을 사용하여 VM에 로그인합니다. 그러면 가상 머신 *myVM1*을 사용하여 원격 데스크톱 세션을 시작합니다.
6. 서버 바탕 화면에서 **Windows 관리 도구**>**Windows PowerShell**로 이동합니다.
7. PowerShell 창에서 다음 명령을 실행하여 IIS 서버를 설치하고, 기본 iisstart.htm 파일을 제거한 다음, VM 이름을 표시하는 새 iisstart.htm 파일을 추가합니다.

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. *myVM1*이 포함된 RDP 세션을 닫습니다.
7. 1~6단계를 반복하여 *myVM2*에 IIS 및 업데이트된 iisstart.htm 파일을 설치합니다.
   
1. 대상 서버를 **MyVM2**로 설정하는 것을 제외하고는 **MyVM2** 가상 머신에 대해 단계를 반복합니다.

### <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

브라우저를 열고, 부하 분산 장치의 공용 IP 주소를 브라우저의 주소 표시줄에 붙여넣습니다. IIS 웹 서버 기본 페이지가 브라우저에 표시됩니다.

![IIS 웹 서버](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

부하 분산 장치가 앱을 실행 중인 두 VM에 트래픽을 분산하고 있는지 확인하려면 웹 브라우저를 강제로 새로 고칩니다.
## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 부하 분산 장치 및 모든 관련 리소스를 삭제하려면 **MyResourceGroupLB** 리소스 그룹을 열고 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 계층 부하 분산 장치를 만들었습니다. 부하 분산 장치에서 사용할 리소스 그룹, 네트워크 리소스, 백 엔드 서버, 상태 프로브 및 규칙을 만들고 구성했습니다. VM에 IIS를 설치하고, 이를 사용하여 부하 분산 장치를 테스트했습니다. 

Azure Load Balancer에 대해 자세히 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Load Balancer 자습서](tutorial-load-balancer-basic-internal-portal.md)
