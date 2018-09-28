---
title: Azure Portal을 사용하여 Azure Firewall DNAT를 통해 인바운드 트래픽 필터링
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Firewall DNAT를 배포하고 구성하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 766ad04251fbe404d43734115e41e23ae0a4be28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982052"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 Azure Firewall DNAT를 통해 인바운드 트래픽 필터링

서브넷에 대한 인바운드 트래픽을 변환하고 필터링하도록 Azure Firewall DNAT(Destination Network Address Translation)를 구성할 수 있습니다. Azure Firewall에는 인바운드 규칙과 아웃바운드 규칙에 대한 개념이 없습니다. 응용 프로그램 규칙과 네트워크 규칙이 있으며, 이러한 규칙은 방화벽으로 들어오는 모든 트래픽에 적용됩니다. 네트워크 규칙이 먼저 적용되고, 다음으로 응용 프로그램 규칙이 적용된 후에 규칙이 종료됩니다.

>[!NOTE]
>Firewall DNAT 기능은 현재 Azure PowerShell 및 REST에서만 사용할 수 있습니다.

예를 들어 네트워크 규칙이 일치하면 응용 프로그램 규칙에 따라 패킷이 평가되지 않습니다. 네트워크 규칙이 일치하지 않고 패킷 프로토콜이 HTTP/HTTPS이면 응용 프로그램 규칙에 따라 패킷이 평가됩니다. 여전히 일치하는 항목이 없으면 [인프라 규칙 컬렉션](infrastructure-fqdns.md)과 비교하여 패킷이 평가됩니다. 여전히 일치하는 항목이 없으면 패킷이 기본적으로 거부됩니다.

DNAT를 구성하면 NAT 규칙 컬렉션 작업이 **DNAT(Destination Network Address Translation)** 로 설정됩니다. 방화벽 공용 IP 및 포트는 사설 IP 주소 및 포트로 변환됩니다. 그런 다음, 규칙이 평소대로 적용되고, 네트워크 규칙이 먼저 적용되고, 다음으로 응용 프로그램 규칙이 적용됩니다. 예를 들어 3389 TCP 포트에서 원격 데스크톱 트래픽을 허용하도록 네트워크 규칙을 구성할 수 있습니다. 주소 변환이 먼저 발생한 다음, 변환된 주소를 사용하여 네트워크 및 응용 프로그램 규칙이 적용됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 테스트 네트워크 환경 설정
> * 방화벽 배포
> * 기본 경로 만들기
> * DNAT 규칙 구성
> * 네트워크 규칙 구성
> * 방화벽 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 자습서에서는 두 개의 피어링된 VNet을 만듭니다.
- **VN-Hub** - 방화벽이 이 VNet에 있습니다.
- **VN-Spoke** - 워크로드 서버가 이 VNet에 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
1. [http://portal.azure.com](http://portal.azure.com)에서 Azure Portal에 로그인합니다.
1. Azure Portal 홈 페이지에서 **리소스 그룹** 및 **추가**를 차례로 클릭합니다.
2. **리소스 그룹 이름**에 대해 **RG-DNAT-Test**를 입력합니다.
3. **구독**의 경우 사용자의 구독을 선택합니다.
4. **리소스 그룹 위치**의 경우 위치를 선택합니다. 만든 모든 후속 리소스는 동일한 위치에 있어야 합니다.
5. **만들기**를 클릭합니다.

## <a name="set-up-the-network-environment"></a>네트워크 환경 설정
먼저 VNet을 만든 다음, 이를 피어링합니다.

### <a name="create-the-hub-vnet"></a>허브 VNet 만들기
1. Azure Portal 홈 페이지에서 **모든 서비스**를 클릭합니다.
2. **네트워킹**아래에서 **가상 네트워크**를 클릭합니다.
3. **추가**를 클릭합니다.
4. **이름**에 대해 **VN-Hub**를 입력합니다.
5. **주소 공간**에 **10.0.0.0/16**을 입력합니다.
7. **구독**의 경우 사용자의 구독을 선택합니다.
8. **리소스 그룹**에 대해 **기존 항목 사용**을 선택한 다음, **RG-DNAT-Test**를 선택합니다.
9. **위치**의 경우 전에 사용한 동일한 위치를 선택합니다.
10. **서브넷** 아래에서 **이름**에 **AzureFirewallSubnet**을 입력합니다.

     방화벽은 이 서브넷에 있고 해당 서브넷 이름은 AzureFirewallSubnet이 **되어야** 합니다.
     > [!NOTE]
     > AzureFirewallSubnet 서브넷의 최소 크기는/25입니다.
11. **주소 범위**에 **10.0.1.0/24**를 입력합니다.
12. 다른 기본 설정을 사용한 다음, **만들기**를 클릭합니다.

### <a name="create-a-spoke-vnet"></a>스포크 VNet 만들기

1. Azure Portal 홈 페이지에서 **모든 서비스**를 클릭합니다.
2. **네트워킹**아래에서 **가상 네트워크**를 클릭합니다.
3. **추가**를 클릭합니다.
4. **이름**에 대해 **VN-Spoke**를 입력합니다.
5. **주소 공간**에 대해 **192.168.0.0/16**을 입력합니다.
7. **구독**의 경우 사용자의 구독을 선택합니다.
8. **리소스 그룹**에 대해 **기존 항목 사용**을 선택한 다음, **RG-DNAT-Test**를 선택합니다.
9. **위치**의 경우 전에 사용한 동일한 위치를 선택합니다.
10. **서브넷** 아래의 **이름**에 대해 **SN-Workload**를 입력합니다.

    서버가 이 서브넷에 있게 됩니다.
1. **주소 범위**에 대해 **192.168.1.0/24**를 입력합니다.
2. 다른 기본 설정을 사용한 다음, **만들기**를 클릭합니다.

### <a name="peer-the-vnets"></a>VNet 피어링

이제 두 개의 VNet을 피어링합니다.

#### <a name="hub-to-spoke"></a>허브-스포크

1. **VN-Hub** 가상 네트워크를 클릭합니다.
2. **설정** 아래에서 **피어링**을 클릭합니다.
3. **추가**를 클릭합니다.
4. 이름에 대해 **Peer-HubSpoke**를 입력합니다.
5. 가상 네트워크에 대해 **VN-Spoke**를 선택합니다.
7. **확인**을 클릭합니다.

#### <a name="spoke-to-hub"></a>스포크-허브

1. **VN-Spoke** 가상 네트워크를 클릭합니다.
2. **설정** 아래에서 **피어링**을 클릭합니다.
3. **추가**를 클릭합니다.
4. 이름에 대해 **Peer-SpokeHub**를 입력합니다.
5. 가상 네트워크에 대해 **VN-Hub**를 선택합니다.
6. **전달된 트래픽 허용**을 클릭합니다.
7. **확인**을 클릭합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

워크로드 가상 머신을 만들어 **SN-Workload** 서브넷에 배치합니다.

1. Azure Portal 홈 페이지에서 **모든 서비스**를 클릭합니다.
2. **계산** 아래에서 **가상 머신**을 클릭합니다.
3. **추가**, **Windows Server**, **Windows Server 2016 Datacenter** 및 **만들기**를 차례로 클릭합니다.

**기본 사항**

1. **이름**에 대해 **Srv-Workload**를 입력합니다.
5. 사용자 이름 및 암호를 입력합니다.
6. **구독**의 경우 사용자의 구독을 선택합니다.
7. **리소스 그룹**에 대해 **기존 항목 사용**을 클릭한 다음, **RG-DNAT-Test**를 선택합니다.
8. **위치**의 경우 전에 사용한 동일한 위치를 선택합니다.
9. **확인**을 클릭합니다.

**크기**

1. Windows Server를 실행하는 테스트 가상 머신에 대해 적절한 크기를 선택합니다. 예를 들어 **B2ms**(8GB RAM, 16GB 저장소).
2. **선택**을 클릭합니다.

**설정**

1. **네트워크** 아래의 **가상 네트워크**에 대해 **VN-Spoke**를 선택합니다.
2. **서브넷**에 대해 **SN-Workload**를 선택합니다.
3. **공용 IP 주소**를 클릭한 다음, **없음**을 클릭합니다.
4. **공용 인바운드 포트 선택**에 대해 **공용 인바운드 포트 없음**을 선택합니다. 
2. 다른 기본 설정을 그대로 적용하고 **확인**을 클릭합니다.

**요약**

요약을 검토한 다음, **만들기**를 클릭합니다. 이 설정은 완료하는 데 몇 분 정도 걸립니다.

배포가 완료되면 가상 머신에 대한 사설 IP 주소를 적어 둡니다. 나중에 방화벽을 구성할 때 사용됩니다. 가상 머신 이름을 클릭하고, **설정** 아래에서 **네트워킹**을 클릭하여 사설 IP 주소를 찾습니다.


## <a name="deploy-the-firewall"></a>방화벽 배포

1. 포털 홈 페이지에서 **리소스 만들기**를 클릭합니다.
2. **네트워킹**을 클릭하고, **추천** 후에 **모두 보기**를 클릭합니다.
3. **방화벽**을 클릭한 다음, **만들기**를 클릭합니다. 
4. **방화벽 만들기** 페이지에서 다음 표를 사용하여 방화벽을 구성합니다.
   
   |설정  |값  |
   |---------|---------|
   |이름     |FW-DNAT-test|
   |구독     |\<구독\>|
   |리소스 그룹     |**기존 항목 사용**: RG-DNAT-Test |
   |위치     |전에 사용한 동일한 위치 선택|
   |가상 네트워크 선택     |**기존 항목 사용**: VN-Hub|
   |공용 IP 주소     |**새로 만듭니다**. 공용 IP 주소는 표준 SKU 형식이어야 합니다.|

2. **검토 + 만들기**를 클릭합니다.
3. 요약을 검토한 다음, **만들기**를 클릭하여 방화벽을 만듭니다.

   이 설정은 배포하는 데 몇 분 정도 걸립니다.
4. 배포가 완료되면 **RG-DNAT-Test** 리소스 그룹으로 이동하고 **FW-DNAT-test** 방화벽을 클릭합니다.
6. 개인 IP 주소를 참고합니다. 기본 경로를 만들 때 나중에 사용할 수 있습니다.


## <a name="create-a-default-route"></a>기본 경로 만들기

**SN-Workload** 서브넷의 경우 방화벽을 통과하도록 아웃바운드 기본 경로를 구성합니다.

1. Azure Portal 홈 페이지에서 **모든 서비스**를 클릭합니다.
2. **네트워킹** 아래에서 **경로 테이블**을 클릭합니다.
3. **추가**를 클릭합니다.
4. **이름**에 대해 **RT-FWroute**를 입력합니다.
5. **구독**의 경우 사용자의 구독을 선택합니다.
6. **리소스 그룹**에 대해 **기존 항목 사용**을 선택하고, **RG-DNAT-Test**를 선택합니다.
7. **위치**의 경우 전에 사용한 동일한 위치를 선택합니다.
8. **만들기**를 클릭합니다.
9. **새로 고침**을 클릭한 다음, **RT-FWroute** 경로 테이블을 클릭합니다.
10. **서브넷**을 클릭한 다음, **연결**을 클릭합니다.
11. **가상 네트워크**를 클릭한 다음, **VN-Spoke**를 선택합니다.
12. **서브넷**에 대해 **SN-Workload**를 클릭합니다.
13. **확인**을 클릭합니다.
14. **경로**를 클릭한 다음, **추가**를 클릭합니다.
15. **경로 이름**에 **FW-DG**를 입력합니다.
16. **주소 접두사**에 **0.0.0.0/0**을 입력합니다.
17. **다음 홉 형식**의 경우 **가상 어플라이언스**를 선택합니다.

    Azure Firewall은 실제로 관리되는 서비스이지만 가상 어플라이언스는 이 상황에서 작동합니다.
1. **다음 홉 주소**에 이전에 적어둔 방화벽에 대한 개인 IP 주소를 입력합니다.
2. **확인**을 클릭합니다.


## <a name="configure-a-dnat-rule"></a>DNAT 규칙 구성

```azurepowershell-interactive
 $rgName  = "RG-DNAT-Test"
 $firewallName = "FW-DNAT-test"
 $publicip = type the Firewall public ip
 $newAddress = type the private IP address for the Srv-Workload virtual machine 
 
# Get Firewall
    $firewall = Get-AzureRmFirewall -ResourceGroupName $rgName -Name $firewallName
  # Create NAT rule
    $natRule = New-AzureRmFirewallNatRule -Name RL-01 -SourceAddress * -DestinationAddress $publicip -DestinationPort 3389 -Protocol TCP -TranslatedAddress $newAddress -TranslatedPort 3389
  # Create NAT rule collection
    $natRuleCollection = New-AzureRmFirewallNatRuleCollection -Name RC-DNAT-01 -Priority 200 -Rule $natRule
  # Add NAT Rule collection to firewall:
    $firewall.AddNatRuleCollection($natRuleCollection)
  # Save:
    $firewall | Set-AzureRmFirewall
```
## <a name="configure-a-network-rule"></a>네트워크 규칙 구성

1. **RG-DNAT-Test**를 열고, **FW-DNAT-test** 방화벽을 클릭합니다.
1. **FW-DNAT-test** 페이지의 **설정** 아래에서 **규칙**을 클릭합니다.
2. **네트워크 규칙 컬렉션 추가**를 클릭합니다.

다음 표를 사용하여 규칙을 구성한 다음, **추가**를 클릭합니다.


|매개 변수  |값  |
|---------|---------|
|이름     |**RC-Net-01**|
|우선 순위     |**200**|
|조치     |**허용**|

**규칙** 아래에서:

|매개 변수  |설정  |
|---------|---------|
|이름     |**RL-RDP**|
|프로토콜     |**TCP**|
|원본 주소     |*|
|대상 주소     |**Srv-Workload** 사설 IP 주소|
|대상 포트|**3389**|


## <a name="test-the-firewall"></a>방화벽 테스트

1. 원격 데스크톱을 방화벽 공용 IP 주소에 연결합니다. **Srv-Workload** 가상 머신에 연결되어 있어야 합니다.
3. 원격 데스크톱을 닫습니다.
4. **RC-Net-01** 네트워크 규칙 컬렉션 작업을 **거부**로 변경합니다.
5. 방화벽 공용 IP 주소에 다시 연결합니다. 이번에는 **거부** 규칙으로 인해 성공하지 않아야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서에서 사용하기 위해 방화벽 리소스를 그대로 유지하거나, 더 이상 필요하지 않은 경우 **RG-DNAT-Test** 리소스 그룹을 삭제하여 모든 방화벽 관련 리소스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 테스트 네트워크 환경 설정
> * 방화벽 배포
> * 기본 경로 만들기
> * DNAT 규칙 구성
> * 네트워크 규칙 구성
> * 방화벽 테스트

그런 다음, Azure Firewall 로그를 모니터링할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
