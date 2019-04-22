---
title: 경계 네트워크 예제-방화벽, UDR 및 Nsg로 구성 된 경계 네트워크를 사용 하 여 네트워크 보호 | Microsoft Docs
description: 방화벽, 사용자 정의 라우팅 (UDR) 및 네트워크 보안 그룹 (Nsg)를 사용 하 여 경계 네트워크 (DMZ 라고도 함)을 빌드하십시오.
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 668862714b416bd89d3b5f82caf8b0305fccae54
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426531"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>예 3: 방화벽, UDR 및 Nsg를 사용 하 여 네트워크를 보호 하는 경계 네트워크 빌드

[보안 경계 모범 사례 페이지로 돌아가기][HOME]

이 예제 경계 네트워크를 만듭니다 (DMZ, 완충 지역 및 스크린 된 서브넷으로도 알려짐). 이 예제에서는 방화벽, 4 명의 Windows 서버, 사용자 정의 라우팅을 (UDR), IP 전달 및 네트워크 보안 그룹 (Nsg)을 구현합니다. 이 문서에서는 각 단계를 이해할 수 있도록 관련 명령을 통해 안내 합니다. 트래픽 시나리오 섹션에서는 또한 설명 방어에서 경계 네트워크에서 방어 계층을 통해 트래픽을 진행 하는 방법입니다. 마지막으로, 참조 섹션에서는 모든 코드 및 테스트 하 고 다양 한 시나리오를 실험할 수 있도록이 환경을 구축 하는 지침을 포함 합니다.

![NVA, NSG 및 UDR을 사용 하 여 양방향 경계 네트워크][1]

## <a name="environment-setup"></a>환경 설정

이 예제에서는 다음 구성 요소를 포함 하는 구독을 사용 합니다.

* 세 개의 클라우드 서비스: SecSvc001, FrontEnd001 및 BackEnd001
* 세 개의 서브넷을 사용 하 여 가상 네트워크 (CorpNetwork): SecNet, FrontEnd 및 BackEnd
* 네트워크 가상 어플라이언스: 방화벽 SecNet 서브넷에 연결
* 응용 프로그램 웹 서버를 나타내는 Windows 서버: IIS01
* 응용 프로그램 백 엔드 서버를 나타내는 두 Windows 서버: AppVM01, AppVM02
* DNS 서버를 나타내는 Windows 서버: DNS01

합니다 [참조 섹션](#references) 대부분의 여기에 설명 된 환경 작성 하는 PowerShell 스크립트를 포함 합니다. 이 문서에서는 가상 머신 (Vm) 및 가상 네트워크를 구축 하기 위한 자세한 지침을 제공 그렇지 않으면 하지 않습니다.

환경을 구축하려면

1. 에 포함 된 네트워크 구성 XML 파일을 저장 합니다 [참조 섹션](#references)합니다. 이름, 위치 및 특정된 시나리오에 맞게 IP 주소를 사용 하 여 업데이트 해야 합니다.
1. 특정 환경 (예: 구독, 서비스 이름 및 등)에 맞게 전체 스크립트의 사용자 변수를 업데이트 합니다.
1. PowerShell에서 스크립트를 실행 합니다.

> [!NOTE]
> PowerShell 스크립트에서 지정 된 지역에 네트워크 구성 XML 파일에 지정 된 지역을 일치 해야 합니다.

스크립트가 성공적으로 실행 한 후 다음 단계를 수행 합니다.

1. 방화벽 규칙을 설정 합니다. 참조 된 [방화벽 규칙](#firewall-rules) 섹션입니다.
1. 필요에 따라 참조 섹션의 두 스크립트를 사용 하 여이 DMZ 구성을 이용한 테스트를 허용 하도록 웹 서버와 앱 서버에서 웹 응용 프로그램을 설정 하려면.

## <a name="user-defined-routing"></a>사용자 정의 라우팅

기본적으로 다음과 같은 시스템 경로는 다음으로 정의되어 있습니다.

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal은 항상 특정 가상 네트워크에 대 한 정의 된 주소 접두사가 있습니다. 예를 들어, 각 특정 가상 네트워크를 정의 하는 방법에 따라 가상 네트워크에 가상 네트워크에서 변경 됩니다. 나머지 시스템 경로 정적 및 표시 된 것 처럼 기본 합니다.

우선 순위에 경로가 LPM 가장 긴 접두사 일치 () 메서드를 통해 처리 됩니다. 따라서 테이블의 가장 구체적인 경로 주어진된 대상 주소에 적용 됩니다.

따라서 트래픽이 DNS01 같은 서버 (10.0.2.4) 로컬 네트워크 (10.0.0.0/16)는 10.0.0.0/16 경로 때문에 가상 네트워크를 통해 라우팅됩니다.  10.0.2.4에서는 10.0.0.0/16 경로가 가장 구체적인 경로입니다. 이 규칙에 0.0.0.0/0 고 10.0.0.0/8 수도 해당 하는 경우에 적용 됩니다. 그러나이 덜 구체적인 이므로이 트래픽에 영향을 주지 않습니다. 10.0.2.4 트래픽을 해당 다음 홉 대상으로 라우팅됩니다 하므로 로컬 가상 네트워크를 있습니다.

예를 들어 10.0.0.0/16 경로 10.1.1.1 트래픽에 적용 되지 않습니다. 10.0.0.0/8 시스템 경로 이므로 가장 구체적인 트래픽을 삭제 하거나 "블랙홀" 다음 홉이 Null입니다.

대상이 Null 접두사 또는 VNETLocal 접두사에 적용 되지 않습니다, 트래픽을 가장 덜 구체적인 경로 (0.0.0.0/0)을 따릅니다. 라우팅됩니다 인터넷에 다음 홉으로 및 Azure의 인터넷에 지를 벗어났습니다.

경로 테이블에 두 개의 동일한 접두사가 없으면 우선 순위 경로의 원본 특성을 기반으로 합니다.

1. VirtualAppliance: 사용자 정의 경로 테이블에 수동으로 추가 합니다.
1. VPNGateway: 동적 네트워크 프로토콜에 동적 경로 (하이브리드 네트워크를 사용 하는 경우 BGP)를 추가 합니다. 이러한 경로 피어 링 된 네트워크의 변경 내용이 자동으로 반영 하는 프로토콜을 동적으로 시간이 지남에 따라 변경 될 수 있습니다.
1. Default: 시스템 경로, 로컬 가상 네트워크 및 위의 경로 테이블에 표시 된 것 처럼 정적 항목.

> [!NOTE]
> 이제 ExpressRoute 및 VPN Gateway를 사용 하 여 아웃 바운드 및 인바운드 크로스-프레미스 네트워크 가상 어플라이언스 (NVA)으로 라우팅되는 트래픽을 강제 적용 하려면 사용자 정의 라우팅 UDR ()를 사용할 수 있습니다.

### <a name="create-local-routes"></a>로컬 경로 만들기

이 예제에서는 프런트 엔드 및 백 엔드 서브넷에 대해 각각 하나씩 두 개의 라우팅 테이블을 사용 합니다. 각 테이블에는 지정된 서브넷에 적절한 정적 경로가 로드되어 있습니다. 이 예제의 목적상 각 테이블에 3개의 경로가 있습니다.

1. 정의 된 다음 홉 없는 로컬 서브넷 트래픽입니다. 이 경로 로컬 서브넷 트래픽이 방화벽을 통과할 수 있습니다.
2. 방화벽으로 정의 된 다음 홉 가상 네트워크 트래픽입니다. 이 경로 로컬 virtual network 트래픽이 직접 라우팅되도록 하는 기본 규칙을 재정의 합니다.
3. 방화벽으로 정의 된 다음 홉을 사용 하 여 모든 나머지 트래픽 (0/0).

라우팅 테이블을 만든 후 해당 서브넷에 바인딩됩니다. 프런트 엔드 서브넷 라우팅 테이블이 같습니다.

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

이 예제에서는 경로 테이블을 빌드, 사용자 정의 경로 추가 및 서브넷에 경로 테이블을 바인딩하고 다음 명령을 사용 합니다. 로 시작 하는 항목 `$`와 같은 `$BESubnet`, 참조 섹션에서 스크립트의 사용자 정의 변수입니다.

1. 먼저 기본 라우팅 테이블을 만듭니다. 다음 코드 조각은 백 엔드 서브넷에 대 한 테이블을 만듭니다. 전체 스크립트는 또한 프런트 엔드 서브넷에 해당 하는 테이블을 만듭니다.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. 경로 테이블을 만든 후에 특정 사용자 정의 경로 추가할 수 있습니다. 다음 코드 조각은 모든 트래픽 (0.0.0.0/0)는 가상 어플라이언스를 통해 라우팅되도록 지정 합니다. 변수 `$VMIP[0]` 스크립트에서 이전 가상 어플라이언스를 만들었을 때 할당 된 IP 주소를 전달 하는 데 사용 됩니다. 전체 스크립트는 또한 프런트 엔드 테이블에 해당 규칙을 만듭니다.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. 이전 경로 항목이 기본 "0.0.0.0/0" 경로 재정의 하지만 기본 10.0.0.0/16 규칙에는 여전히 네트워크 가상 어플라이언스가 아닌 대상에 직접 경로를 가상 네트워크 내에서 트래픽을 허용 합니다. 이 문제를 해결 하려면 다음 규칙을 추가 해야 합니다.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. 이 시점에서 선택 해야 합니다. 이전 두 규칙 평가 단일 서브넷 내의 트래픽을 포함 하 여 방화벽으로 모든 트래픽을 라우팅합니다. 이 동작을 수 있습니다. 그러나 이렇게 하지 않으면 방화벽의 관여 없이 로컬로 라우팅되도록 하려는 서브넷 내의 트래픽을 허용할 수 있습니다. 세 번째, 로컬 서브넷으로 보내는 모든 주소를 직접 라우팅하는 특정 규칙을 추가 (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. 마지막으로, 라우팅 테이블을 만들고 사용자 정의 경로 사용 하 여 채워진 후 테이블을 서브넷 바인딩할 해야 합니다. 다음 코드 조각은 백 엔드 서브넷에 대 한 테이블을 바인딩합니다. 전체 스크립트는 또한 프런트 엔드 서브넷에 프런트 엔드 경로 테이블을 바인딩합니다.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>IP 전달

IP 전달은 udr의 도우미 기능입니다. 이 설정은 가상 어플라이언스의 기기에 지정 되지 않은 트래픽 수신 및 다음 해당 트래픽을 최종 대상으로 전달할 수 있습니다.

예를 들어 AppVM01에서 트래픽이 DNS01 서버를 요청 하면 UDR 트래픽을 방화벽으로 라우팅합니다. IP 전달을 사용 하도록 설정한, DNS01 대상 (10.0.2.4)를 사용 하 여 트래픽은 방화벽 어플라이언스 (10.0.0.4)에서 허용 이며 다음 최종 대상 (10.0.2.4)으로 전달 합니다. 트래픽도 IP 전달을 방화벽에서 사용 하도록 설정 하지 않고 하지 경로 테이블에 방화벽이 다음 홉으로 하는 경우에 어플라이언스에서 허용 됩니다.

> [!IMPORTANT]
> 사용자 정의 라우팅이 함께 IP 전달을 사용 하도록 설정 해야 합니다.

IP 전달은 VM 생성 시 단일 명령으로 사용할 수 있습니다. 방화벽 가상 어플라이언스 및 IP 전달을 사용 하도록 설정 하는 VM 인스턴스를 호출 합니다. 로 시작 하는 빨간색 항목 염두에서에 둡니다 `$`같은 `$VMName[0]`에이 문서의 참조 섹션에서 스크립트의 사용자 정의 변수입니다. 대괄호 안의 0 `[0]`, Vm 어레이에서 첫 번째 VM을 나타냅니다. 수정 없이 작동 하도록 예제 스크립트에서는 첫 번째 VM (VM 0) 방화벽 이어야 합니다. 전체 스크립트의 끝 UDR 명령을 사용 하 여 관련 코드 조각을 그룹화 됩니다.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>네트워크 보안 그룹

이 예제에서는 네트워크 보안 그룹 (NSG)을 빌드하고 단일 규칙을 사용 하 여 로드 합니다. 다음 예제에서는 프런트 엔드 및 백 엔드 서브넷 (SecNet 제외)에 NSG를 바인딩합니다. NSG를 로드 하는 규칙은 다음과 같습니다.

* 전체 가상 네트워크 (모든 서브넷)에 인터넷에서 모든 트래픽 (모든 포트) 거부 되었습니다.

이 예제에서는 Nsg는 사용 되는 있지만 해당 주요 목적은 수동 잘못 된 구성에 대 한 계층입니다. 프런트 엔드 또는 백 엔드 서브넷에 인터넷에서 모든 인바운드 트래픽을 차단 하려고 합니다. 해야만 트래픽은 SecNet 서브넷에 방화벽을 통해 후 적절 한 트래픽만 해야 라우팅됩니다 프런트 엔드 또는 백 엔드 서브넷에 로그온 합니다. 또한 UDR 규칙을 방화벽에 프런트 엔드 또는 백 엔드 서브넷에 도달 하는 모든 트래픽을 라우팅합니다. 방화벽 비대칭 흐름으로 표시 되 고 아웃 바운드 트래픽을 삭제 합니다.

세 보안 계층이 프런트 엔드 및 백 엔드 서브넷을 보호 합니다.

1. FrontEnd001 및 BackEnd001 cloud services에서 열린 끝점이 없으며
1. Nsg는 인터넷에서 트래픽을 거부합니다
1. 방화벽은 비대칭 트래픽을 삭제합니다

이 예제의 NSG에 대 한 흥미로운 점을 아래에 표시 된 하나의 규칙만 포함 한다는 점입니다. 이 규칙에는 보안 서브넷을 포함 하는 전체 가상 네트워크에 인터넷 트래픽을 거부 합니다.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

만 프런트 엔드 및 백 엔드 서브넷에 NSG에 바인딩되기 때문에 규칙은 보안 서브넷으로 인바운드 트래픽에 적용 되지 않습니다. 그 결과 트래픽이 보안 서브넷으로 이동합니다.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>방화벽 규칙

방화벽 전달 규칙을 만들어야 합니다. 방화벽 차단 또는 모든 인바운드, 아웃 바운드 및 intra virtual-네트워크 트래픽을 전달 하기 때문에 많은 방화벽 규칙이 필요 합니다. 또한 방화벽 보안 서비스 공용 IP 주소 (서로 다른 포트)에서 모든 인바운드 트래픽을 처리 해야 합니다. 재작업을 방지 하도록 나중에, 서브넷 및 방화벽 규칙을 설정 하기 전에 논리적 흐름을 다이어그램으로 모범 사례를 따릅니다. 다음 그림은 이 예제의 방화벽 규칙을 논리적으로 나타낸 것입니다.

![방화벽 규칙에 대한 논리 뷰][2]

> [!NOTE]
> 관리 포트는 네트워크 가상 어플라이언스에 따라 달라 집니다. 이 예제에서는 포트 22, 801, 807을 사용 하는 Barracuda NextGen Firewall을 보여 줍니다. 장치를 관리 하는 정확한 포트를 찾으려면 어플라이언스 공급 업체 설명서를 참조 하세요.

### <a name="logical-rule-description"></a>논리적 규칙 설명

위의 논리적 다이어그램 방화벽이 해당 서브넷에 유일한 리소스 이므로 보안 서브넷에 표시 되지 않습니다. 이 다이어그램에서는 방화벽 규칙을 보여 줍니다. 어떻게 이러한 논리적으로 허용 하거나 거부 트래픽 흐름을 없지만 없습니다 실제 라우팅될 경로입니다. 또한 원격 데스크톱 프로토콜 (RDP) 트래픽에 대 한 선택 된 외부 포트는 로컬 IP 주소의 마지막 두 8 진수와 맞게 판독할 수 있도록 선택한 더 높은 범위 지정 된 포트 (8014 – 8026). 예를 들어, 로컬 서버 주소 10.0.1.4는 외부 포트 8014와 연결 합니다. 그러나 높은 충돌 하지 않는 포트를 사용할 수 있습니다.

이 예제에 대 한 다음과 같은 유형의 규칙이 필요합니다.

* 인바운드 트래픽 외부 규칙:
  1. 방화벽 관리 규칙: 트래픽이 네트워크 가상 어플라이언스의 관리 포트로 전달 되도록 허용 합니다.
  2. 각 windows server에 대 한 RDP 규칙: RDP 통한 개별 서버의 관리를 허용 합니다.  네트워크 가상 어플라이언스의 기능에 따라 규칙 하나로 묶을 수 있습니다.
  3. 응용 프로그램 트래픽 규칙: 프런트 엔드 웹 트래픽용 및 백 엔드 트래픽 (예를 들어, 웹 서버에서 데이터 계층으로). 이러한 규칙의 구성은 네트워크 아키텍처 및 트래픽에 따라 흐름입니다.

     * 첫 번째 규칙에는 실제 응용 프로그램 트래픽이 응용 프로그램 서버에 연결할 수 있습니다. 보안, 관리 등에 대 한 규칙을 달리 응용 프로그램 규칙에는 외부 사용자 또는 서비스가 응용 프로그램에 액세스 하도록 허용 합니다. 이 예제는 단일 웹 서버가 포트 80에서 단일 방화벽 응용 프로그램 규칙이 대신 웹 서버의 내부 IP 주소를 라우팅하는 외부 IP 주소에 대 한 지정 된 트래픽을 리디렉션할 수 있습니다. 리디렉션된 트래픽 세션은 내부 서버로 NAT에서 다시 매핑됩니다.
     * 두 번째 응용 프로그램 트래픽 규칙은 웹 서버에서 모든 포트를 사용 하 여 AppVM02 서버 아니라 AppVM01 서버로 트래픽을 라우팅할 수 있도록 하려면 백 엔드 규칙입니다.

* 내부 virtual intra 네트워크 트래픽 규칙:
  1. 인터넷 아웃 바운드 규칙: 선택한 네트워크로 전달 되도록 모든 네트워크에서 트래픽을 허용 합니다. 이 규칙은 일반적으로 방화벽에 있지만 사용 안 함 상태의 기본값. 이 예제에 대 한이 규칙을 사용 하도록 설정 합니다.
  2. DNS 규칙: DNS (포트 53) 트래픽만 DNS 서버로 전달 되도록 허용 합니다. 이 환경에서는 프런트 엔드에서 백 엔드로 이동하는 대부분의 트래픽이 차단됩니다. 이 규칙은 로컬 서브넷의 DNS만 특별히 허용합니다.
  3. 서브넷 대 서브넷 규칙: 프런트 엔드 서브넷, 그 반대의 경우에 모든 서버에 연결 하는 백 엔드 서브넷에서 모든 서버를 허용 합니다.

* 위 조건 중 하나라도 충족 하지 않는 트래픽에 대 한 유사 시 대기 규칙:
  1. 모든 트래픽 거부 규칙: 우선 순위 측면에서 마지막 규칙이 항상 있습니다. 트래픽 흐름이 위의 규칙에 맞게 실패 하면이 규칙에서 차단 합니다. 기본 규칙을 것입니다. 일반적으로 활성화 된 경우 때문에 수정 없이 필요 합니다.

> [!TIP]
> 두 번째 응용 프로그램 트래픽 규칙의 단순하게 유지 하기 위해이 예제에서는 모든 포트를 허용 합니다. 실제 시나리오에서는이 규칙의 공격 표면을 줄이기 위해 특정 포트와 주소 범위를 사용 해야 합니다.


> [!IMPORTANT]
> 규칙을 만든 후에 트래픽을 허용 또는 거부 원하는 대로 있는지 확인 하는 각 규칙의 우선 순위를 검토 하는 중요 합니다. 이 예제에서는 규칙이 우선 순위 순서로 나와 있습니다. 규칙 순서를 잘못 정할 경우 방화벽에서 차단 하는 것이 쉽습니다. 절대 가장 높은 우선 순위로 방화벽 관리 규칙을 설정 해야 합니다.

### <a name="rule-prerequisites"></a>규칙 필수 조건

공용 끝점은 방화벽을 실행 하는 가상 컴퓨터에 대 한 필수입니다. 방화벽이 트래픽을 처리할 수 있도록 이러한 공용 끝점이 열려 있어야 합니다. 이 예제에서 트래픽의 다음과 같은 세 종류가 있습니다.

1. 관리 트래픽은 방화벽 및 방화벽 규칙을 제어할 수
1. Windows 서버를 제어할 수 있는 RDP 트래픽
1. 애플리케이션 트래픽

트래픽 유형 방화벽 규칙 위의 논리적 다이어그램의 위쪽 절반에 표시 됩니다.

> [!IMPORTANT]
> 유의 해야 *모든* 트래픽이 방화벽을 통해 제공 됩니다. IIS01 서버에 원격 데스크톱 포트 8014에서 방화벽에 연결한 다음 방화벽에서 RDP 요청을 IIS01 RDP 포트로 내부적으로 경로 허용 해야 합니다. Azure portal의 **Connect** 단추는 포털을 볼 수와 관련해 서에서 iis01로 직접적인 RDP 경로가 없기 때문에 작동 하지 않습니다. 인터넷의 모든 연결은 보안 서비스 및 포트 (예: secscv001.cloudapp.net:xxxx)) 됩니다. 위 다이어그램의 외부 포트 및 내부 IP 및 포트 매핑에 대 한 참조입니다.

빌드 후 또는 VM 만들 때 끝점을 열 수 있습니다. 다음 코드 조각 및 예제 스크립트는 VM을 만든 후 끝점을 엽니다.

로 시작 하는 항목이 있음을 명심 `$`와 같은 `$VMName[$i]`, 참조 섹션에서 스크립트의 사용자 정의 변수입니다. `[$i]` Vm의 배열에서 특정 VM의 번호를 나타냅니다.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

이 분명히 표시 되어 있지 여기 변수 때문에 보안 클라우드 서비스의 끝점을 열어야 합니다. 이 예방 조치는 라우팅, NAT에서 다시 매핑할 또는 삭제 여부를 방화벽에서 모든 인바운드 트래픽을 처리 하는지 확인 하는 데 도움이 됩니다.

필요한 구성을 만들고 방화벽 관리 PC에 관리 클라이언트를 설치 합니다. 방화벽 또는 다른 NVA를 관리 하는 방법에 대 한 자세한 내용은 공급 업체의 설명서를 참조 하십시오. 이 섹션의 나머지 부분은 뿐만 **방화벽 규칙 만들기** 섹션에서는 방화벽 구성에 설명 합니다. 공급 업체의 관리 클라이언트, 하지 Azure portal 또는 PowerShell을 사용 합니다.

로 이동 [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin) 관리 클라이언트를 다운로드 및 Barracuda 방화벽에 연결 하는 방법을 알아봅니다.

방화벽에 로그인 한 후 방화벽 규칙을 만들기 전에 네트워크 및 서비스 개체를 정의 합니다. 이러한 두 가지 필수 구성 요소 개체 클래스 규칙을 쉽게 만드는 것이 가능 합니다.

예를 들어 각각에 대 한 세 개의 명명 된 네트워크 개체를 정의 합니다.

* 프런트 엔드 서브넷
* 백 엔드 서브넷
* DNS 서버의 IP 주소

Barracuda NG Admin 클라이언트 대시보드에서 명명된 된 네트워크를 만들려면:

1. 로 이동 합니다 **구성 탭**합니다.
1. 선택 **Ruleset** 에 **작동 구성** 섹션
1. 선택 **Networks** 아래 합니다 **방화벽 개체** 메뉴.
1. 선택 **새로 만들기** 에 **편집 네트워크** 메뉴.
1. 이름 및 접두사를 추가 하 여 네트워크 개체를 만듭니다.

   ![Front ed 네트워크 개체 만들기][3]

앞의 단계에는 프런트 엔드 서브넷에 대 한 명명 된 네트워크를 만듭니다. 백 엔드 서브넷 에서도 유사한 개체를 만듭니다. 이제 방화벽 규칙에서 서브넷을 이름으로 더욱 쉽게 참조할 수 있습니다.

DNS 서버 개체:

![DNS 서버 개체 만들기][4]

이 단일 IP 주소 참조 문서에서 나중에 DNS 규칙에서 사용 됩니다.

다른 개체 클래스에는 각 서버에 대 한 RDP 연결 포트를 나타내는 서비스 개체를 포함 합니다. 기존 RDP 서비스 개체에 바인딩되어 기본 RDP 포트인 3389 합니다. 따라서 외부 포트 (8014-8026)의 트래픽을 허용 하도록 새 서비스를 만들 수 있습니다. 또한 기존 RDP 서비스에 새 포트를 추가할 수 있습니다. 그러나 데모 편의성을 위해 각 서버에 대해 개별 규칙을 만들 수 있습니다. Barracuda NG Admin 클라이언트 대시보드에서를 서버의 새 RDP 규칙을 만들려면

1. 로 이동 합니다 **구성 탭**합니다.
1. 선택 **Ruleset** 에 **작동 구성** 섹션입니다.
1. 선택 **Services** 아래의 합니다 **방화벽 개체** 메뉴.
1. 서비스 및 선택 목록 아래로 스크롤하여 **RDP**합니다.
1. 마우스 오른쪽 단추로 클릭 하 고 복사를 선택 다음 마우스 오른쪽 단추로 클릭 붙여넣기를 선택 합니다.
1. 편집할 수 있는 RDP-Copy1 서비스 개체가 되었습니다. 마우스 오른쪽 단추로 클릭 **RDP-Copy1** 선택한 **편집**합니다.
1. 합니다 **서비스 개체 편집** 창이 다음과 같이 최대:

   ![기본 RDP 규칙 복사본][5]

1. 특정 서버에 대 한 RDP 서비스를 나타내는 값을 편집 합니다. AppVM01에 대 한 기본 RDP 규칙을 새 서비스를 반영 하도록 수정 해야 **이름을**를 **설명**, 및 그림 8 다이어그램에 사용 되는 외부 RDP 포트입니다. 이 특정 서버에 대 한 외부 포트를 포트 RDP 기본값인 3389에서 변경 되는 것을 염두에 두십시오. 예를 들어 AppVM01에 대 한 외부 포트는 8025입니다. 수정 된 서비스로 규칙은 다음과 같습니다.

   ![AppVM01 규칙][6]

나머지 서버에 대 한 RDP 서비스를 만드는이 프로세스를 반복 합니다. AppVM02, DNS01, 및 IIS01 이러한 서비스를 만드는 간단 하 고 보다 명확 하 게 다음 섹션에서 규칙을 확인 합니다.

> [!NOTE]
> RDP 대신 VM 관리용 포트 22에 SSH를 사용 하도록 VM 방화벽에는 Linux 기반 이미지 이므로 방화벽에 대 한 RDP 서비스를 필요 하지 않습니다. 또한 포트 22 및 관리 연결에 대 한 다른 두 포트를 허용 합니다. 참조를 **방화벽 관리 규칙** 다음 섹션에 있습니다.

### <a name="firewall-rules-creation"></a>방화벽 규칙 만들기

이 예제에서는 모든 고유 아이콘에에서 사용 되는 방화벽 규칙는 다음과 같은 세 종류가 있습니다.

응용 프로그램 리디렉션 규칙: ![애플리케이션 리디렉션 아이콘][7]

대상 NAT 규칙: ![대상 NAT 아이콘][8]

전달 규칙: ![전달 아이콘][9]

이러한 규칙에 대 한 자세한 내용은 Barracuda 웹 사이트에서 찾을 수 있습니다.

다음 규칙을 만들거나 기존 기본 규칙을 확인:

1. Barracuda NG Admin 클라이언트 대시보드에서 이동 합니다 **구성** 탭 합니다.
1. 에 **작동 구성** 섹션에서 **Ruleset**합니다.
1. 합니다 **Main Rules** 표 기존 활성 나타나며이 방화벽 규칙을 비활성화 합니다. 고 녹색 **+** 는 오른쪽 위 모서리에 새 규칙을 만듭니다. 표시 된 단추가 표시 방화벽 변경 내용을 잠겨 있으면 **잠금** 및 만들거나 규칙을 편집할 수 없습니다. 선택 된 **잠금** 규칙 집합의 잠금을 해제 하 고 편집을 허용 하는 단추입니다. 편집 및 선택 하려는 규칙을 마우스 오른쪽 단추로 클릭 **규칙 편집**합니다.

를 만들거나 규칙 수정 후 푸시합니다 방화벽 및 활성화 하 합니다. 이 고, 그렇지 규칙 변경 내용이 적용 되지 않습니다. 푸시 및 활성화 프로세스에 설명 된 [활성화 규칙](#rule-activation)합니다.

이 예제를 완료 하는 데 필요한 각 규칙의 세부 정보는 다음과 같습니다.

* **방화벽 관리 규칙**: 이 앱 리디렉션 규칙에는이 예제에서는 Barracuda NextGen 방화벽 네트워크 가상 어플라이언스의 관리 포트로 전달 하는 트래픽을 허용 합니다. 관리 포트는 801, 807을 이며 22는 합니다. 외부 및 내부 포트가 동일한 포트 변환을 하지 않음입니다. 이 규칙에는 설치-MGMT-액세스 라고 합니다. 기본 규칙 이므로 Barracuda NextGen Firewall 버전 6.1에서에서 기본적으로 사용 합니다.
  
    ![방화벽 관리 규칙][10]

  > [!TIP]
  > 이 규칙에는 원본 주소 공간이 **모든**합니다. 관리 IP 주소 범위에 알고 있는 경우이 범위를 줄이면 관리 포트에 대 한 공격을 줄어듭니다.

* **RDP 규칙**:  이러한 대상 NAT 규칙에 RDP 통한 개별 서버의 관리를 허용 합니다. 이러한 규칙에 대 한 중요 한 필드입니다.
  * 소스입니다. 어디에서 나 RDP를 허용 하려면 참조를 사용 하 여 **모든** 원본 필드에 있습니다.
  * 서비스. 이전에 만든 RDP 서비스 개체를 사용 합니다. **AppVM01 RDP**. 외부 포트는 서버의 로컬 IP 주소 및 기본 RDP 포트 3386로 리디렉션합니다. 이 규칙은 특히 RDP에서 AppVM01에 액세스하기 위한 규칙입니다.
  * 대상입니다. 방화벽에서 로컬 포트를 사용 합니다. **DCHP 1 Local IP** 나 **eth0** 고정 Ip를 사용 하는 경우. 서 수 (eth0에서 eth1, 및 등)는 네트워크 어플라이언스에 여러 로컬 인터페이스가 있는 경우 달라질 수 있습니다. 방화벽이이 포트를 사용 하 여 송신 및 수신 포트와 같을 수 있습니다. 실제 라우트된 대상이 합니다 **대상 목록** 필드입니다.
  * 리디렉션입니다. 가상 어플라이언스를 궁극적으로이 트래픽을 리디렉션할 위치를 구성 합니다. 가장 간단한 리디렉션은 IP 대상 목록 필드에 배치 하는 것입니다. 해당 포트를 지정할 수도 있습니다 및 NAT 포트 및 IP 주소를 모두 다시 라우팅합니다. 포트를 지정 하지 않으면 가상 어플라이언스는 인바운드 요청에 대상 포트를 사용 합니다.

    ![방화벽 RDP 규칙][11]

    4 개의 RDP 규칙을 만듭니다.

    | 규칙 이름 | 서버 | 서비스 | 대상 목록 |
    | --- | --- | --- | --- |
    | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
    | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > 원본 및 서비스 필드의 범위를 좁히는 공격 노출을 영역이 줄어듭니다. 기능을 허용 하는 가장 제한적인된 범위를 사용 합니다.

* **응용 프로그램 트래픽 규칙**: 두 응용 프로그램 트래픽 규칙이 있습니다. 하나는 프런트 엔드 웹 트래픽용입니다. 다른 웹 서버에서 데이터 계층으로 같은 백 엔드 트래픽에 대해 설명합니다. 이러한 규칙은 네트워크 아키텍처에 따라 달라 집니다 및 트래픽 흐름입니다.
  
  * 웹 트래픽에 대 한 프런트 엔드 규칙:
  
    ![방화벽 웹 규칙][12]
  
    이 대상 NAT 규칙에는 실제 응용 프로그램 트래픽이 응용 프로그램 서버에 도달 하도록 허용 합니다. 보안, 관리 및 항목에 대 한 규칙을 달리 응용 프로그램 규칙에는 외부 사용자 또는 서비스가 응용 프로그램에 액세스 하도록 허용 합니다. 이 예제는 단일 웹 서버가 포트 80에서 단일 방화벽 응용 프로그램 규칙이 대신 웹 서버의 내부 IP 주소를 라우팅하는 외부 IP 주소에 대 한 지정 된 트래픽을 리디렉션할 수 있습니다. 리디렉션된 트래픽 세션은 내부 서버로 NAT에서 다시 매핑됩니다.

    > [!NOTE]
    > 할당 된 포트를 **대상 목록** 필드입니다. 따라서 인바운드 포트 80 (또는 선택한 서비스의 경우 443)는 웹 서버의 리디렉션에 사용 됩니다. 웹 서버 8080과 같은 다른 포트에서 수신 하는 경우에 포트 리디렉션 수 있도록 10.0.1.4:8080 대상 목록 필드를 업데이트할 수 있습니다.
  
  * 백 엔드 규칙은 웹 서버를 통해 AppVM01 서버 하지만 하지 AppVM02 통신을 허용 **모든** 서비스:
  
    ![방화벽 AppVM01 규칙][13]
  
    이 전달 규칙은 모든 IIS 서버 AppVM01에 연결할 프런트 엔드 서브넷에 허용 (10.0.2.5) 포트를 웹 응용 프로그램에서 데이터에 액세스할 수 있도록 모든 프로토콜을 사용 합니다.
  
    이 스크린샷에서 `<explicit-dest>` 에 사용 되는 **대상** 필드를 대상으로 10.0.2.5를 합니다. 스크린샷에 표시 된 것 처럼 명시적 IP 주소를 지정할 수 있습니다. 또한 DNS 서버에 대 한 필수 구성 요소에서와 같은 명명된 된 네트워크 개체를 사용할 수 있습니다. 방화벽 관리자에는 사용할 방법을 선택할 수 있습니다. 아래의 첫 번째 빈 행에서 두 번 클릭을 명시적 대상으로 10.0.2.5를 추가 하려면 `<explicit-dest>` 열리는 대화 상자에서 주소를 입력 합니다.
  
    이 전달 규칙을 사용 하 여 없는 NAT 내부 트래픽을 처리 하는 때문에 필요 합니다. 설정할 수 있습니다는 **연결 방법이** 를 `No SNAT`입니다.
  
    > [!NOTE]
    > 이 규칙의 원본 네트워크는 하나만 있으면 프런트 엔드 서브넷에는 리소스입니다. 웹 서버의 알려진된 수를 지정 하는 아키텍처 전체 프런트 엔드 서브넷 대신 정확한 해당 IP 주소를 더 구체적으로 네트워크 개체 리소스를 만들 수 있습니다.

    > [!TIP]
    > 이 규칙은 서비스 **모든** 샘플 응용 프로그램을 설정 및 사용을 쉽게 수행할 수 있도록 합니다. ICMPv4 수 있도록 단일 규칙에 (ping). 그러나 공격을 줄이기 위해이 경계를 넘어 노출 좋습니다 포트 및 프로토콜 서비스 응용 프로그램 작업을 허용 하는 최소 수를 제한 합니다.

    > [!TIP]
    > 이 예제에서는 규칙을 사용 하지만 `<explicit-dest>` 참조, 방화벽 구성에서 일관 된 방법을 사용 해야 합니다. 쉽게 가독성 및 지원 가능성에 대 한 명명 된 네트워크 개체를 사용 하는 것이 좋습니다. `<explicit-dest>` 를에서는 대체 참조 방법을 보여주기가 여기에 표시 됩니다. 없는 일반적으로 좋습니다, 특히 복잡 한 구성에 대 한 합니다.

* **인터넷에 아웃 바운드 규칙**: 이 전달 규칙에는 선택한 대상 네트워크로 전달 되도록 원본 네트워크 트래픽을 허용 합니다. Barracuda NextGen 방화벽 일반적으로 규칙이이 "on" 비활성화 된 상태로 있지만 기본적으로 합니다. 에 액세스 하려면이 규칙을 마우스 오른쪽 단추로 클릭 합니다 **활성화 규칙** 명령입니다. 이 규칙의 원본 특성에 백 엔드 및 프런트 엔드 서브넷에 대 한 네트워크 개체를 추가 하려면 스크린샷에 표시 된 규칙을 수정 합니다. 이 문서의 필수 구성 요소 섹션에서 이러한 네트워크 개체를 만들었습니다.
  
    ![방화벽 아웃바운드 규칙][14]

* **DNS 규칙**: 이 전달 규칙은 DNS (포트 53) 트래픽만을 DNS 서버로 전달할 수 있습니다. 이 환경에 대 한이 규칙은 특히 DNS 트래픽을 허용 하도록 백 엔드에 프런트 엔드에서 대부분의 트래픽이 차단 됩니다.
  
    ![방화벽 DNS 규칙][15]
  
    > [!NOTE]
    > **서버의 연결 방법이** 로 설정 되어 `No SNAT` 이 규칙은 내부 IP 주소 트래픽을 내부 IP 및 NAT를 통해 수행할지 없습니다 필요 하기 때문에.

* **서브넷 대 서브넷 규칙**: 이 기본 전달 규칙 활성화 및 프런트 엔드 서브넷에서 모든 서버에 연결 하는 백 엔드 서브넷에서 모든 서버를 허용 하도록 수정 합니다. 이 규칙에만 내부 트래픽을 coves 하므로 **연결 방법이** 로 설정할 수 있습니다 `No SNAT`합니다.

    ![방화벽 인트라-VNet 규칙][16]
  
    > [!NOTE]
    > 합니다 **양방향** 없습니다 확인란이 여기 하므로 한 방향 으로만이 규칙이 적용 됩니다. 프런트 엔드 네트워크를 그 반대의 경우에 백 엔드 서브넷에서 연결을 시작할 수 있습니다. 해당 확인란을 선택한 경우이 규칙 논리 다이어그램에서는 바람직하지 않은로 지정 했기 때문에 양방향 트래픽을 사용 하도록 설정 됩니다.

* **모든 트래픽 거부 규칙**: 이 규칙에는 항상 우선 순위 측면에서 마지막 규칙이 되어야 합니다. 트래픽 흐름이 위의 규칙 일치 하지 않는 경우이 규칙 삭제 하면 됩니다. 규칙은 수정 없이 필요 하므로 일반적으로 기본적으로 활성화 됩니다.
  
    ![방화벽 거부 규칙][17]

> [!IMPORTANT]
> 모든 이전 규칙을 만든 후 트래픽을 허용 하거나 필요에 따라 거부 되도록 각 규칙의 우선 순위를 검토 합니다. 예를 들어 규칙 Barracuda Management 클라이언트의 전달 규칙의 주 눈금에 표시 되어야 하는 순서로 나열 됩니다.

## <a name="rule-activation"></a>규칙 활성화

규칙 집합의 논리적 다이어그램 사양에 맞게 수정한 후 방화벽 규칙 집합을 업로드 하 고 활성화 해야 합니다.

![방화벽 규칙 활성화][18]

관리 클라이언트 창의 상단 오른쪽 모서리에서 찾아 선택할 **변경 내용 보내기** 수정 된 규칙을 방화벽에 업로드 합니다. **활성화**를 선택합니다.

방화벽 규칙 집합을 활성화 하면이 예시 환경 완료 되었습니다.

## <a name="traffic-scenarios"></a>트래픽 시나리오

> [!IMPORTANT]
> 유의 해야 *모든* 트래픽이 방화벽을 통해 제공 됩니다. IIS01 서버에 원격 데스크톱 포트 8014에서 방화벽에 연결한 다음 방화벽에서 RDP 요청을 IIS01 RDP 포트로 내부적으로 경로 허용 해야 합니다. Azure portal의 **Connect** 단추는 포털을 볼 수와 관련해 서에서 iis01로 직접적인 RDP 경로가 없기 때문에 작동 하지 않습니다. 인터넷의 모든 연결은 보안 서비스 및 포트 (예: secscv001.cloudapp.net:xxxx)) 됩니다. 위 다이어그램의 외부 포트 및 내부 IP 및 포트 매핑에 대 한 참조입니다.

이 시나리오에서는 다음 방화벽 규칙을 적용해야 합니다.

1. 방화벽 관리 (관리)
2. RDP to IIS01
3. RDP to DNS01
4. RDP to AppVM01
5. RDP to AppVM02
6. 웹에 대한 앱 트래픽
7. AppVM01에 대한 앱 트래픽
8. 인터넷에 아웃 바운드
9. DNS01에 프런트 엔드
10. 인트라-서브넷 트래픽(프런트 엔드에 대한 백 엔드만)
11. 모두 거부

실제 방화벽 규칙 집합에는이 예제 보다 더 많은 규칙 대부분 필요 합니다. 다른 우선 순위 번호가 있을 가능성이 있습니다. 이 목록 및 관련된 번호 서로 상대적 우선 순위에 대 한 참조 해야 합니다. 예를 들어, "IIS01에 RDP" 규칙 수 있습니다 숫자 5의 아래 "방화벽 관리"으로 하지만 실제 방화벽에서 규칙 위에 "RDP to DNS01" 규칙을 위해이 목록 집합을 정렬 합니다. 이 목록 다음에 나오는 시나리오에 대 한 지침을 간소화 하기도 합니다. 예를 들어, "방화벽 규칙 9 (DNS)"입니다. 4 개의 RDP 규칙 "RDP 규칙" 이라고 주의 트래픽 시나리오가 RDP와 관련이 없는 경우.

또한 네트워크 보안 그룹 (Nsg)는 프런트 엔드 및 백 엔드 서브넷에서 인바운드 인터넷 트래픽에 적용 됩니다.

### <a name="allowed-internet-to-web-server"></a>(허용) 인터넷에서 웹 서버

1. 인터넷 사용자가 SecSvc001.CloudApp.Net (인터넷 연결 클라우드 서비스)에서 HTTP 페이지를 요청 합니다.
1. 포트 80에서 열린 끝점을 통해 트래픽을 10.0.0.4: 80의 방화벽 인터페이스로 전달 하는 클라우드 서비스입니다.
1. NSG가 없으므로 시스템 NSG 규칙을 방화벽에 트래픽을 허용 하도록 보안 서브넷에 할당 됩니다.
1. 트래픽이는 방화벽 (10.0.1.4)의 내부 IP 주소에 도달 합니다.
1. 방화벽 규칙 처리를 수행합니다.
   1. 방화벽 규칙 1 (관리)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 2-5 (2-5(RDP 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 6 (앱: 웹)은 적용 됩니다. 트래픽이 허용 됩니다. 방화벽 다시 10.0.1.4 (IIS01) NAT 통해 트래픽을 라우팅합니다.
1. 프런트 엔드 서브넷에 인바운드 규칙 처리를 수행합니다.
   1. NSG 규칙 1 (인터넷 차단)이 적용 되지 않습니다. 방화벽 원본 주소를 방화벽 이제 이므로 NAT 통해이 트래픽을 중지 합니다. 방화벽이 보안 서브넷에 프런트 엔드 서브넷 NSG에 트래픽을 로컬 요소로 때문에 트래픽이 허용 됩니다. 다음 규칙으로 이동 합니다.
   1. 이 트래픽을 허용 하므로 서브넷 대 서브넷 트래픽을 허용 하는 기본 NSG 규칙입니다. NSG 규칙 처리를 중지 합니다.
1. IIS01이 웹 트래픽을 수신 대기 합니다. 이 요청을 수신 하 고 요청 처리를 시작 합니다.
1. IIS01은 백 엔드 서브넷에서 AppVM01에 대 한 FTP 세션을 시작 하려고 합니다.
1. 프런트 엔드 서브넷의 UDR 경로 방화벽이 다음 홉입니다.
1. 가지 아웃 바운드 규칙이 없으므로 프런트 엔드 서브넷에는 트래픽이 허용 됩니다.
1. 방화벽에서 규칙 처리를 시작합니다.
   1. 방화벽 규칙 1 (관리)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 2-5 (2-5(RDP 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 6 (앱: 웹)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 7 (앱: 백 엔드)에 적용 됩니다. 트래픽이 허용 됩니다. 방화벽은 10.0.2.5 (AppVM01)에 트래픽을 전달합니다.
1. 백 엔드 서브넷의 인바운드 규칙 처리를 수행합니다.
    1. NSG 규칙 1 (인터넷 차단)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
    1. 기본 NSG 규칙에는 서브넷 대 서브넷 트래픽을 허용합니다. 트래픽이 허용 됩니다. NSG 규칙 처리를 중지 합니다.
1. AppVM01 요청을 수신 하며 세션을 시작 하며 응답 합니다.
1. 백 엔드 서브넷의 UDR 경로 방화벽이 다음 홉입니다.
1. 많습니다 아웃 바운드 NSG 규칙이 없으므로 백 엔드 서브넷에 응답이 허용 됩니다.
1. 이 경우 연결된 된 세션에서 트래픽이 반환, 되므로 웹 서버 (iis01로)에 응답을 방화벽에 전달 합니다.
1. 프런트 엔드 서브넷 인바운드 규칙 처리를 수행합니다.
    1. NSG 규칙 1 (인터넷 차단)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
    1. 기본 NSG 규칙은이 트래픽을 허용 하므로 서브넷 대 서브넷 트래픽을 허용 합니다. NSG 규칙 처리를 중지 합니다.
1. IIS 서버 응답을 받아 AppVM01로 트랜잭션을 완료 합니다. 그런 다음 서버는 HTTP 응답 빌드를 완료 하 고 요청자에 게 보냅니다.
1. 많습니다 아웃 바운드 NSG 규칙이 없으므로 프런트 엔드 서브넷에 응답이 허용 됩니다.
1. HTTP 응답 방화벽에 도달 합니다. 연결된 된 NAT 세션에 대 한 응답 이기 때문에 방화벽으로 수락 합니다.
1. 방화벽이는 인터넷 사용자에 게 다시 응답을 리디렉션합니다.
1. 개의 프런트 엔드 서브넷에서 UDR 홉 없거나 아웃 바운드 NSG 규칙이 있으므로 응답이 허용 됩니다. 인터넷 사용자는 요청 된 웹 페이지를 받습니다.

### <a name="allowed-internet-rdp-to-back-end"></a>(허용) 백 엔드를 인터넷 RDP

1. 인터넷에서 서버 관리자가 SecSvc001.CloudApp.Net:8025 통해 AppVM01에 대 한 RDP 세션을 요청합니다. 8025의 방화벽 규칙 4 (AppVM01 RDP)에 대 한 사용자 할당 된 포트입니다.
1. 10.0.0.4:8025의 방화벽 인터페이스로 포트 8025에서 열린 끝점을 통해 트래픽을 전달 하는 클라우드 서비스입니다.
1. NSG가 없으므로 시스템 NSG 규칙을 방화벽에 트래픽을 허용 하도록 보안 서브넷에 할당 됩니다.
1. 방화벽 규칙 처리를 수행합니다.
   1. 방화벽 규칙 1 (관리)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 2 (2(RDP IIS)가 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 3 (RDP DNS01) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 트래픽을 허용 하도록 방화벽 규칙 4 (RDP AppVM01) 적용지 않습니다. 방화벽 조정 하 고 NAT를 통해 10.0.2.5:3386를 (AppVM01에 RDP 포트).
1. 백 엔드 서브넷의 인바운드 규칙 처리를 수행합니다.
   1. NSG 규칙 1 (인터넷 차단)이 적용 되지 않습니다. 방화벽 원본 주소가 보안 서브넷에 있는 방화벽 이제 이므로 NAT 통해이 트래픽을 중지 합니다. 이 백 엔드 서브넷 NSG에서 로컬 트래픽으로 표시 하 고 허용 됩니다. 다음 규칙으로 이동 합니다.
   1. 이 트래픽을 허용 하므로 서브넷 대 서브넷 트래픽을 허용 하는 기본 NSG 규칙입니다. NSG 규칙 처리를 중지 합니다.
1. AppVM01이 RDP 트래픽을 수신 대기 하 고 응답 합니다.
1. 많습니다 아웃 바운드 NSG 규칙이 없으므로 기본 규칙이 적용 됩니다. 반환 트래픽이 허용 됩니다.
1. UDR은 다음 홉으로 방화벽 아웃 바운드 트래픽을 라우팅합니다.
1. 이 경우 연결된 된 세션에서 트래픽이 반환, 되므로 방화벽 인터넷 사용자에 게 다시 응답을 전달 합니다.
1. RDP 세션을 사용할 수 있습니다.
1. AppVM01은 사용자 이름 암호를 묻습니다.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(허용) DNS 서버에서 웹 서버 DNS 조회

1. Http 피드에서 데이터를 요청 하는 웹 서버 IIS01\:\/\/www.data.gov, 하지만 주소를 확인 해야 합니다.
1. 네트워크 구성 DNS01 가상 네트워크 목록에 대 한 (백 엔드 서브넷에서 10.0.2.4)으로 주 DNS 서버입니다. IIS01은 DNS01로 DNS 요청을 보냅니다.
1. UDR은 다음 홉으로 방화벽 아웃 바운드 트래픽을 라우팅합니다.
1. 아웃 바운드 NSG 규칙이 없으므로 프런트 엔드 서브넷에 바인딩됩니다. 트래픽이 허용 됩니다.
1. 방화벽 규칙 처리를 수행합니다.
   1. 방화벽 규칙 1 (관리)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 2-5 (2-5(RDP 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 6-7 (앱 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 8 (인터넷)에 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 9 (DNS)은 적용 됩니다. 트래픽이 허용 됩니다. 방화벽은 10.0.2.4 (DNS01)에 트래픽을 전달합니다.
1. 백 엔드 서브넷의 인바운드 규칙 처리를 수행합니다.
   1. NSG 규칙 1 (인터넷 차단)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 기본 NSG 규칙에는 서브넷 대 서브넷 트래픽을 허용합니다. 트래픽이 허용 됩니다. NSG 규칙 처리를 중지 합니다.
1. DNS 서버는 요청을 받습니다.
1. DNS 서버 캐시 된 주소가 없고 인터넷에서 루트 DNS 서버를 요청 합니다.
1. UDR 다음 홉으로 방화벽 아웃 바운드 트래픽을 라우팅합니다.
1. 트래픽 백 엔드 서브넷에 아웃 바운드 NSG 규칙이 없으므로 허용 됩니다.
1. 방화벽 규칙 처리를 수행합니다.
   1. 방화벽 규칙 1 (관리)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 2-5 (2-5(RDP 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 6-7 (앱 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 8 (인터넷)에 적용 됩니다. 트래픽이 허용 됩니다. 세션은 SNAT를 통해 인터넷에서 루트 DNS 서버로 라우팅됩니다.
1. 인터넷 DNS 서버가 응답 합니다. 이 세션은 방화벽에서 응답을 수락 하도록 방화벽에서 시작 되었습니다.
1. 이 세션은 이미 방화벽 시작 서버인 DNS01에 대 한 응답을 전달 하도록 설정 됩니다.
1. 백 엔드 서브넷의 인바운드 규칙 처리를 수행합니다.
    1. NSG 규칙 1 (인터넷 차단)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
    1. 기본 NSG 규칙 허용이 트래픽에 서브넷 대 서브넷 트래픽을 허용 합니다. NSG 규칙 처리를 중지 합니다.
1. DNS 서버는 응답을 캐시 받고 그런 다음 다시 IIS01로 초기 요청에 응답 합니다.
1. 백 엔드 서브넷의 UDR 경로 방화벽이 다음 홉입니다.
1. 아웃 바운드 NSG 규칙이 없고 트래픽이 허용 됩니다 있도록 백 엔드 서브넷에 존재 합니다.
1. 이 세션이 있으므로 IIS 서버에 다시 응답을 조정 하는 방화벽에 이미 방화벽에서 설정 됩니다.
1. 프런트 엔드 서브넷에 인바운드 규칙 처리를 수행합니다.
    1. NSG 규칙을 하나도 적용 하므로 백 엔드 서브넷에서 인바운드 트래픽 프런트 엔드 서브넷에 대 한 NSG 규칙이 없는 합니다.
    1. 기본 시스템 규칙에는 서브넷 간 트래픽을 허용합니다. 트래픽이 허용 됩니다.
1. IIS01은 DNS01에서 응답을 받습니다.

### <a name="allowed-back-end-server-to-front-end-server"></a>(허용) 프런트 엔드 서버로 백 엔드 서버

1. RDP 통해 AppVM02에 로그인 하는 관리자는 windows 파일 탐색기를 통해 IIS01 서버에서 직접 파일을 요청 합니다.
1. 백 엔드 서브넷의 UDR 경로 방화벽이 다음 홉입니다.
1. 많습니다 아웃 바운드 NSG 규칙이 없으므로 백 엔드 서브넷에 응답이 허용 됩니다.
1. 방화벽 규칙 처리를 수행합니다.
   1. 방화벽 규칙 1 (관리)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 2-5 (2-5(RDP 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 6-7 (앱 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 8 (인터넷)에 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 9 (DNS) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 10 (인트라-서브넷)에 적용 됩니다. 트래픽이 허용 됩니다. 방화벽은 10.0.1.4 (iis01로)에 트래픽을 전달 합니다.
1. 프런트 엔드 서브넷에 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1 (인터넷 차단)가 적용 되지 않고 규칙으로 이동
   1. 기본 NSG 규칙은 트래픽이 허용 됩니다 서브넷 대 서브넷 트래픽을 허용 합니다. NSG 규칙 처리를 중지 합니다.
1. IIS01 요청을 수락 하 고 응답 적절 한 인증 및 권한 부여를 가정 합니다.
1. 프런트 엔드 서브넷의 UDR 경로 방화벽이 다음 홉입니다.
1. 많습니다 아웃 바운드 NSG 규칙이 없으므로 프런트 엔드 서브넷에 응답이 허용 됩니다.
1. 이 응답이 허용 되는 하므로 방화벽에서이 세션이 이미 있습니다. 방화벽은 AppVM02로 응답을 반환합니다.
1. 백 엔드 서브넷의 인바운드 규칙 처리를 수행합니다.
    1. NSG 규칙 1 (인터넷 차단)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
    2. 기본 NSG 규칙은 트래픽이 허용 됩니다 서브넷 대 서브넷 트래픽을 허용 합니다. NSG 규칙 처리를 중지 합니다.
1. AppVM02 응답을 받습니다.

### <a name="denied-internet-direct-to-web-server"></a>(거부) 인터넷 웹 서버로 직접

1. 인터넷 사용자 FrontEnd001.CloudApp.Net 서비스를 통해 IIS01 웹 서버에 액세스 하려고 합니다.
1. 개의 끝점이 HTTP 트래픽에 대해 열려 있으므로이 트래픽은 클라우드 서비스를 통해 전달 하지 않습니다. 트래픽이 서버에 도달 하지 않습니다.
1. 어떤 이유로 끝점이 열려 있는 경우 프런트 엔드 서브넷에 NSG (인터넷 차단)이이 트래픽을 차단 합니다.
1. 마지막으로 프런트 엔드 서브넷 UDR 경로가 모든 아웃 바운드 트래픽을 보냅니다 IIS01에서 방화벽에 다음 홉으로. 방화벽을 비대칭 트래픽으로 표시 되 고 아웃 바운드 응답을 삭제 합니다.

>따라서 개 인터넷과 IIS01 간 3 개 이상의 독립 계층이 있습니다. 클라우드 서비스에 무단으로 또는 부적절 한 액세스할 수 없습니다.

### <a name="denied-internet-to-back-end-server"></a>(거부) 인터넷을 통해 백 엔드 서버

1. 인터넷 사용자 BackEnd001.CloudApp.Net 서비스를 통해 AppVM01에서 파일에 액세스 하려고 합니다.
2. 이 요청에서 클라우드 서비스를 통과 하지 못한 하므로 파일 공유를 위해 열린 끝점이 있습니다. 트래픽이 서버에 도달 하지 않습니다.
3. 어떤 이유로 끝점이 열려 있는 경우 NSG (인터넷 차단)이이 트래픽을 차단 합니다.
4. 마지막으로 UDR 경로가 보냅니다 아웃 바운드 트래픽을 AppVM01에서 방화벽에 다음 홉으로. 방화벽을 비대칭 트래픽으로 표시 되 고 아웃 바운드 응답을 삭제 합니다.

> 따라서 개 인터넷과 AppVM01 간 3 개 이상의 독립 계층이 있습니다. 클라우드 서비스에 무단으로 또는 부적절 한 액세스할 수 없습니다.

### <a name="denied-front-end-server-to-back-end-server"></a>(거부) 백 엔드 서버에 프런트 엔드 서버

1. IIS01 손상 되었고 백 엔드 서브넷 서버를 스캔 하려는 악성 코드를 실행 합니다.
1. 프런트 엔드 서브넷 UDR 경로가 아웃 바운드 트래픽을 IIS01에서 방화벽이 다음 홉으로 보냅니다. 손상된 된 VM이 라우팅은 변경할 수 없습니다.
1. 방화벽이 트래픽을 처리합니다. AppVM01 또는 DNS 서버 DNS 조회에 대 한 요청인 경우 방화벽 트래픽을 7 및 9 방화벽 규칙으로 인해에 허용할 수 있습니다. 다른 모든 트래픽은 방화벽 규칙 11 (모두 거부)에 의해 차단 됩니다.
1. 방화벽에서 고급 위협 감지를 사용 하는 경우 알려진된 서명 또는 고급 위협 규칙 플래그는 패턴을 포함 하는 트래픽이 되지 않을 수 있습니다. 이 측정값은이 문서에서 설명 하는 기본 전달 규칙에 따라 트래픽이 허용 되어 있더라도 작업할 수 있습니다. 고급 위협 요소 탐지는이 문서에 적용 되지 않습니다. 특정 네트워크 어플라이언스의 고급 위협 기능에 대 한 공급 업체 설명서를 참조 하세요.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(거부) DNS 서버에서 인터넷 DNS 조회

1. 인터넷 사용자 BackEnd001.CloudApp.Net 서비스를 통해 DNS01에서 내부 DNS 레코드를 조회 하려고 합니다.
1. DNS 트래픽을 위해 열린 끝점이 없으므로이 트래픽은 클라우드 서비스를 통해 전달 하지 않습니다. 이 서버에 도달 하지 않습니다.
1. 어떤 이유로 끝점이 열려 있는 경우 프런트 엔드 서브넷의 NSG 규칙 (인터넷 차단)이이 트래픽을 차단 합니다.
1. 마지막으로 백 엔드 서브넷 UDR 경로가 모든 아웃 바운드 트래픽을 보냅니다 DNS01에서 방화벽에 다음 홉으로. 방화벽이이 비대칭 트래픽으로 인식 하 고 아웃 바운드 응답을 삭제 합니다.

> 따라서 개 인터넷과 DNS01 간 3 개 이상의 독립 계층이 있습니다. 클라우드 서비스에 무단으로 또는 부적절 한 액세스할 수 없습니다.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(거부) 인터넷을 통해 방화벽을 통해 SQL 액세스

1. 인터넷 사용자 SecSvc001.CloudApp.Net 인터넷 연결 클라우드 서비스에서 SQL 데이터를 요청 합니다.
1. 개의 끝점이 SQL에 대해 열려 있으므로이 트래픽은 클라우드 서비스를 전달 하지 않습니다. 방화벽에 도달 하지 것입니다.
1. 어떤 이유로 SQL 끝점이 열려 있는 경우 방화벽 규칙 처리를 수행 합니다.
   1. 방화벽 규칙 1 (관리)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 2-5 (2-5(RDP 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 6-7 (응용 프로그램 규칙)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 8 (인터넷)에 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 9 (DNS) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 10 (인트라-서브넷)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   1. 방화벽 규칙 11 (모두 거부)은 적용 됩니다. 트래픽이 차단 됩니다. 규칙 처리를 중지 합니다.

## <a name="references"></a>참조

이 섹션에는 다음 항목이 포함 됩니다.

* 전체 스크립트입니다. PowerShell 스크립트 파일에 저장 합니다.
* 네트워크 구성 합니다. Networkconf2.xml"이라는 파일에 저장 합니다.

필요에 따라 파일에서 사용자 정의 변수를 수정 합니다. 스크립트를 실행 하 고이 문서의 앞부분에 나열 된 방화벽 규칙 설정 지침을 따릅니다.

### <a name="full-script"></a>전체 스크립트

사용자 정의 변수를 설정한 후이 스크립트를 실행 합니다.

1. Azure 구독에 연결
1. 새 저장소 계정 만들기
1. 새 가상 네트워크 및 네트워크 구성 파일에 정의 된 대로 세 개의 서브넷 만들기
1. 5 개의 가상 머신을 빌드: 방화벽 및 4 개의 Windows Server Vm
1. UDR을 구성 합니다.
   1. 두 개의 새 경로 테이블 만들기
   1. 테이블에 경로를 추가합니다.
   1. 적절한 서브넷에 테이블을 바인딩합니다.
1. NVA에서 IP 전달을 사용하도록 설정합니다.
1. NSG를 구성 합니다.
   1. NSG를 만들려면
   1. 규칙 추가
   1. 적절 한 서브넷에 NSG 바인딩

이 PowerShell 실행 인터넷에서 로컬로 스크립트 PC 또는 서버를 연결 합니다.

> [!IMPORTANT]
> 이 스크립트를 실행 하면 경고 또는 기타 정보 메시지가 있습니다 팝업 PowerShell에서. 빨간색 오류 메시지만 문제의 원인이 됩니다.

```powershell
    <#
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwarding from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Associate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>네트워크 구성 파일

업데이트 된 위치로이 XML 파일을 저장 합니다. 변경 된 `$NetworkConfigFile` 저장 된 네트워크 구성 파일에 연결 하려면 위의 전체 스크립트의 변수입니다.

```xml
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

## <a name="next-steps"></a>다음 단계

이 경계 네트워크 예에 도움이 되는 샘플 응용 프로그램을 설치할 수 있습니다.

> [!div class="nextstepaction"]
> [샘플 응용 프로그램 스크립트](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "NVA, NSG, UDR을 사용하는 양방향 DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "방화벽 규칙에 대한 논리적 보기"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "프런트 엔드 네트워크 개체 만들기"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "DNS 서버 개체 만들기"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "기본 RDP 규칙의 복사본"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 규칙"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "애플리케이션 리디렉션 아이콘"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "대상 NAT 아이콘"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "전달 아이콘"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "방화벽 관리 규칙"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "방화벽 RDP 규칙"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "방화벽 웹 규칙"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "방화벽 AppVM01 규칙"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "방화벽 아웃바운드 규칙"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "방화벽 DNS 규칙"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "VNet 내 방화벽 규칙"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "방화벽 거부 규칙"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "방화벽 규칙 활성화"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
