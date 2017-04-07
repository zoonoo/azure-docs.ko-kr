---
title: "Azure에서 여러 IP 구성의 부하 분산 | Microsoft Docs"
description: "기본 및 보조 IP 구성에서 부하 분산."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: cf1e68c7b37b2506de007bdf24eea63a27187a33
ms.lasthandoff: 03/22/2017

---

# <a name="load-balancing-on-multiple-ip-configurations-using-the-azure-portal"></a>Azure Portal을 사용하여 여러 IP 구성의 부하 분산

> [!div class="op_single_selector"]
> * [포털](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

이 문서에서는 보조 NIC(네트워크 인터페이스)에 여러 IP 주소가 있는 Azure Load Balancer를 사용하는 방법을 설명합니다. 이 시나리오에는 Windows를 실행하는 VM이 둘 있고 각각 기본 및 보조 NIC가 있습니다. 보조 NIC에는 각각 두 가지 IP 구성이 있습니다. 각 VM은 contoso.com 및 fabrikam.com 웹 사이트를 둘 다 호스트합니다. 각 웹 사이트는 보조 NIC의 IP 구성 중 하나에 바인딩됩니다. Azure Load Balancer를 사용하여 웹 사이트의 각 IP 구성에 트래픽을 분산하기 위해 각 웹 사이트에 하나씩 두 개의 프런트 엔드 IP 주소를 노출합니다. 이 시나리오는 양 쪽 프런트 엔드는 물론 양쪽 백 엔드 풀 IP 주소에 동일한 포트 번호를 사용합니다.

![LB 시나리오 이미지](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

##<a name="prerequisites"></a>필수 조건
이 예제에서는 다음 구성을 포함하는 *contosofabrikam*이라는 리소스 그룹이 있다고 가정합니다.
 -  *myAvailset*이라는 동일한 가용성 집합 내에 각각 *VM1* 및 *VM2*라는 두 VM과 *myVNet*이라는 가상 네트워크를 포함합니다. 
 - 각 VM에는 주 NIC 및 보조 NIC가 있습니다. 주 NIC는 *VM1NIC1* 및 *VM2NIC1*로 이름이 지정되고 보조 NIC는 *VM1NIC2* 및 *VM2NIC2*로 이름이 지정됩니다. 여러 NIC가 있는 VM 만들기에 대한 자세한 내용은 [PowerShell을 사용하여 다중 NIC가 있는 VM 만들기](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)를 참조하세요.

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>여러 IP 구성의 부하를 분산하는 단계

아래 단계에 따라 이 문서에 설명된 시나리오를 수행합니다.

### <a name="step-1-configure-the-secondary-nics-for-each-vm"></a>1단계: 각 VM에 보조 NIC 구성

다음과 같이 가상 네트워크의 각 VM에 대해 보조 NIC에 대한 IP 구성 설정을 추가합니다.  

1. 브라우저에서 Azure Portal( http://portal.azure.com )로 이동하고 Azure 계정으로 로그인합니다.
2. 화면 왼쪽 맨 위에서 리소스 그룹 아이콘을 클릭한 후 VM이 있는 리소스 그룹(예: *contosofabrikam*)을 클릭합니다. VM에 대한 네트워크 인터페이스와 모든 리소스가 함께 나열되는 **리소스 그룹** 블레이드가 표시됩니다.
3. 각 VM의 보조 NIC를 설정하려면 다음과 같이 IP 구성을 추가합니다.
    1. IP 구성을 추가하려는 네트워크 인터페이스를 선택합니다.
    2. 선택한 NIC에 대해 표시되는 블레이드에서 **IP 구성**을 클릭합니다. 그런 다음 표시되는 블레이드 위쪽의 **추가** 단추를 클릭합니다.
    3. **IP 구성 추가** 블레이드에서 다음과 같이 NIC에 보조 IP 구성을 추가합니다. 
        1. 보조 IP 구성의 이름을 입력합니다(예를 들어, VM1 및 VM2 이름에 대해 IP 구성 이름을 각각 *VM1NIC2-ipconfig2* 및 *VM2NIC2-ipconfig2*로 지정).
        2. **개인 IP 주소**, **할당**에 대해 **정적**을 선택합니다.
        3. **확인**을 클릭합니다.
        4. 보조 NIC에 대한 보조 IP 구성을 완료하면 지정된 NIC에 대한 **IP 구성** 설정 블레이드에 그 내용이 표시됩니다.

### <a name="step-2-create-a-load-balancer"></a>2단계: 부하 분산 장치 만들기

다음과 같이 부하 분산 장치를 만듭니다.

1. 브라우저에서 Azure Portal( http://portal.azure.com )로 이동하고 Azure 계정으로 로그인합니다.
2. 화면 왼쪽 상단에서 **새로 만들기** > **네트워킹** > **부하 분산 장치**를 클릭합니다. 다음으로 **만들기**를 클릭합니다.
3. **부하 분산 장치 만들기** 블레이드에서 부하 분산 장치의 이름을 입력합니다. 여기서는 *mylb*라고 지칭합니다.
4. 공용 IP 주소 아래에서 **PublicIP1**이라는 새 공용 IP를 만듭니다.
5. 리소스 그룹 아래에서 VM의 기존 리소스 그룹을 선택합니다(예를 들어, *contosofabrikam*). 그런 다음 적절한 위치를 선택하고 **확인**을 클릭합니다. 부하 분산 장치는 배포를 시작하며 배포를 성공적으로 완료하는 데 몇 분 정도 걸립니다.
6. 배포된 후에는 리소스 그룹에 부하 분산 장치가 리소스로 표시됩니다.

### <a name="step-3-configure-the-frontend-ip-pool"></a>3단계: 프런트 엔드 IP 풀 구성

다음과 같이 각 웹 사이트(Contoso 및 Fabrikam)에 대해 프런트 엔드 IP 풀을 구성합니다.

1. 포털에서 **더 많은 서비스**를 클릭하고 필터 상자에 **공용 IP 주소**를 입력하고 **공용 IP 주소**를 클릭합니다. 표시되는 블레이드 위쪽의 **추가**를 클릭합니다.
2. 다음과 같이 두 웹 사이트(contoso 및 fabrikam)에 대해 두 개의 공용 IP 주소(*PublicIP1* 및 *PublicIP2*)를 구성합니다.
    1. 프런트 엔드 IP 주소의 이름을 입력합니다.
    2. **리소스 그룹**에서 VM의 기존 리소스 그룹을 선택합니다(예를 들어, *contosofabrikam*).
    3. **위치**에서 VM과 같은 위치를 선택합니다.
    4. **확인**을 클릭합니다.
    5. 두 개의 공용 IP 주소를 만들면 둘 다 **공용 IP** 주소 블레이드에 표시됩니다.
3. 포털에서 **더 많은 서비스**를 클릭하고 필터 상자에 **부하 분산 장치**를 입력하고 **부하 분산 장치**를 클릭합니다.  
4. 프런트 엔드 IP 풀을 추가할 부하 분산 장치(*mylb*)를 선택합니다.
5. **설정**에서 **프런트 엔드 풀**을 선택합니다. 그런 다음 표시되는 블레이드 위쪽의 **추가** 단추를 클릭합니다.
6. 프런트 엔드 IP 주소의 이름을 입력합니다(*farbikamfe* 또는 **contosofe*).
7. **IP 주소**를 클릭하고 **공용 IP 주소 선택** 블레이드에서 프런트 엔드에 대한 IP 주소를 선택합니다(*PublicIP1* 또는 *PublicIP2*).
8. 이 섹션 내의 3~7단계를 반복하여 보조 프런트 엔드 IP 주소를 만듭니다.
9. 프런트 엔드 IP 풀 구성이 완료되면 두 프런트 엔드 IP 주소가 부하 분산 장치의 **프런트 엔드 IP 풀** 블레이드에 표시됩니다. 
    
### <a name="step-4-configure-the-backend-pool"></a>4단계: 백 엔드 풀 구성   
다음과 같이 각 웹 사이트(Contoso 및 Fabrikam)에 대한 부하 분산 장치에서 백 엔드 주소 풀을 구성합니다.
        
1. 포털에서 **더 많은 서비스**를 클릭하고 필터 상자에 부하 분산 장치를 입력하고 **부하 분산 장치**를 클릭합니다.  
2. 백 엔드 풀을 추가할 부하 분산 장치(*mylb*)를 선택합니다.
3. **설정**에서 **백 엔드 풀**을 선택합니다. 백 엔드 풀에 대한 이름을 입력합니다(예를 들어 *contosopool* 또는 *fabrikampool*). 그런 다음 표시되는 블레이드 위쪽의 **추가** 단추를 클릭합니다. 
4. **Associated to**(연결 대상)에서 **가용성 집합**을 선택합니다.
5. **가용성 집합**에서 **myAvailset**을 선택합니다.
6. 다음과 같이 두 VM에 대해 대상 네트워크 IP 구성을 추가합니다(그림 2 참조).  
    1. **대상 가상 컴퓨터**에 대해 백 엔드 풀에 추가하려는 VM을 선택합니다(예를 들어, VM1 또는 VM2).
    2. **네트워크 IP 구성**에서 해당 VM에 대한 보조 NIC IP 구성을 선택합니다(예를 들어, VM1NIC2-ipconfig2 또는 VM2NIC2-ipconfig2).
    ![LB 시나리오 이미지](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
            
        **그림 2**: 백 엔드 풀로 부하 분산 장치 구성  
7. **확인**을 클릭합니다.
8. 백 엔드 풀 구성이 완료되면 두 백 엔드 주소 풀이 부하 분산 장치의 **백 엔드 풀 블레이드**에 표시됩니다.

### <a name="step-5-configure-a-health-probe-for-your-load-balancer"></a>5단계: 부하 분산 장치의 상태 프로브 구성
다음과 같이 부하 분산 장치의 상태 프로브를 구성합니다.
    1. 포털에서 더 많은 서비스를 클릭하고 필터 상자에 부하 분산 장치를 입력하고 **부하 분산 장치**를 클릭합니다.  
    2. 백 엔드 풀을 추가할 부하 분산 장치를 선택합니다.
    3. **설정**에서 **상태 프로브**를 선택합니다. 그런 다음 표시되는 블레이드 위쪽의 **추가** 단추를 클릭합니다.
    4. 상태 프로브에 대한 이름(예: HTTP)을 입력하고 **확인**을 클릭합니다.

### <a name="step-6-configure-load-balancing-rules"></a>6단계: 부하 분산 규칙 구성
다음과 같이 각 웹 사이트에 대한 부하 분산 규칙 (*HTTPc* 및 *HTTPf*)을 구성합니다.
    
1. **설정**에서 **상태 프로브**를 선택합니다. 그런 다음 표시되는 블레이드 위쪽의 **추가** 단추를 클릭합니다.
2. **이름**에 부하 분산 규칙의 이름을 입력합니다(예를 들면 Contoso의 경우 *HTTPc* 또는 Fabrikam의 경우 *HTTPf*).
3. 프런트 엔드 IP 주소에는 프런트 엔드 IP 주소를 선택합니다(예를 들면 *Contosofe* 또는 *Fabrikamfe*).
4. **포트** 및 **백 엔드 포트**에서 기본값인 **80**을 그대로 유지합니다.
5. **부동 IP(Direct Server Return)**에서**사용**을 클릭합니다.
6. **확인**을 클릭합니다.
7. 이 섹션 내의 1~6단계를 반복하여 보조 부하 분산 규칙을 만듭니다.
8. 부하 분산 규칙 구성을 완료하면 두 규칙(*HTTPc* 및 *HTTPf*)이 부하 분산 장치의 **부하 분산 규칙** 블레이드에 표시됩니다.

### <a name="step-7-configure-dns-records"></a>7단계: DNS 레코드 구성
마지막으로 DNS 리소스 레코드가 부하 분산 장치의 각 프런트 엔드 IP 주소를 가리키도록 구성해야 합니다. 도메인을 Azure DNS에 호스트할 수 있습니다. Load Balancer와 Azure DNS를 사용하는 방법에 대한 자세한 내용은 [다른 Azure 서비스와 함께 Azure DNS 사용](../dns/dns-for-azure-services.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- Azure에서 부하 분산 서비스를 결합하는 방법에 대한 자세한 내용은 [Azure에서 부하 분산 서비스 사용](../traffic-manager/traffic-manager-load-balancing-azure.md)을 참조하세요.
- [Azure Load Balancer에 대한 로그 분석](../load-balancer/load-balancer-monitor-log.md)을 통해 Azure에서 부하 분산 장치를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용하는 방법에 대해 알아보세요.

