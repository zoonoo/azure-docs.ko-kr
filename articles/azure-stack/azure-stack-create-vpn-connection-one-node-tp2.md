---
title: "다른 Azure Stack PoC 환경에서 두 가상 네트워크 사이에 사이트 간 VPN 만들기 | Microsoft Docs"
description: "클라우드 관리자가 TP2에서 두 개의 단일 노드 POC 환경 사이에 사이트 간 VPN 연결을 만들 수 있는 단계별 절차입니다."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732
ms.lasthandoff: 02/09/2017


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>다른 Azure Stack PoC 환경에서 두 가상 네트워크 사이에 사이트 간 VPN 만들기
## <a name="overview"></a>개요
이 문서는 두 개의 분리된 Azure Stack Proof-of-Concept(POC) 환경에 있는 두 가상 네트워크 사이에 사이트 간 VPN 연결을 만드는 방법을 보여줍니다. 연결을 구성하는 동안 VPN 게이트웨이가 Azure Stack에서 작동하는 방식을 알게 됩니다.

> [!NOTE]
> 이 문서는 Azure Stack TP2 POC에 특별히 적용됩니다.
> 
> 

### <a name="connection-diagram"></a>연결 다이어그램
다음 다이어그램은 구성이 완성된 후의 모양을 보여줍니다.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>시작하기 전에
이 구성을 완료하려면 시작하기 전에 다음 항목이 준비되어야 합니다.

* [Azure Stack 배포 필수 조건](azure-stack-deploy.md)에 정의된 Azure Stack POC 하드웨어 요구 사항을 충족하는 두 서버 및 이 문서에 정의된 기타 필수 구성 요소.
* Azure Stack Technical Preview 2 배포 패키지.

## <a name="deploy-the-poc-environments"></a>POC 환경 배포
이 구성을 완료하기 위해 두 개의 Azure Stack POC 환경을 배포하게 됩니다.

* 배포하는 각 POC에 대해 [Azure Stack POC 배포](azure-stack-run-powershell-script.md) 문서에 설명되어 있는 배포 지침을 따를 수 있습니다.
  이 문서의 각 POC 환경은 일반적으로 *POC1* 및 *POC2*라고 언급됩니다.

## <a name="configure-quotas-for-compute-network-and-storage"></a>Compute, 네트워크 및 Storage에 대한 할당량 구성
우선 Compute, 네트워크 및 Storage에 대한 *할당량*을 구성해야 합니다. 이러한 서비스는 *계획*과 연결된 후 테넌트가 구독할 수 있는 *제품*으로 연결될 수 있습니다.

> [!NOTE]
> 각 Azure Stack POC 환경에 대해 이러한 단계를 수행해야 합니다.
> 
> 

서비스에 대한 할당량 만들기는 TP1 이후로 변경되었습니다. TP2에서 할당량을 만드는 단계는 <http://aka.ms/mas-create-quotas>를 참조하세요. 이 연습의 모든 할당량 설정에 대해서는 기본 설정을 수락할 수 있습니다.

## <a name="create-a-plan-and-offer"></a>계획 및 제품 만들기
[계획](azure-stack-key-features.md)은 하나 이상의 서비스에 대한 그룹화입니다. 공급자는 계획을 만들어서 테넌트에게 제공할 수 있습니다. 차례로 테넌트는 제품을 구독하여 포함된 계획 및 서비스를 사용합니다.

> [!NOTE]
> 각 Azure Stack POC 환경에 대해 이러한 단계를 수행해야 합니다.
> 
> 

1. 우선 계획을 만듭니다. 이를 위해 [계획 만들기](azure-stack-create-plan.md) 온라인 문서의 단계를 따릅니다.
2. [Azure Stack에서 제품 만들기](azure-stack-create-offer.md)에 설명된 단계에 따라 제품을 만듭니다.
3. 테넌트 관리자로 포털에 로그인하고 [만든 제품을 구독합니다](azure-stack-subscribe-plan-provision-vm.md).

## <a name="create-the-network-resources-in-poc-1"></a>POC 1에 네트워크 리소스 만들기
이제 구성을 설정하는 데 필요한 리소스를 실제로 만들겠습니다. 다음 단계는 수행할 작업을 보여줍니다. 이 지침은 포털을 사용하여 리소스를 만드는 방법을 보여주지만 PowerShell을 사용해도 동일한 작업을 수행할 수 있습니다.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>테넌트로 로그인
서비스 관리자는 테넌트로 로그인하여 자신의 테넌트가 사용할 수 있는 계획, 제품 및 구독을 테스트할 수 있습니다. 테넌트가 아직 없는 경우 로그인하기 전에 [테넌트 계정을 만듭니다](azure-stack-add-new-user-aad.md) .

### <a name="create-the-virtual-network--vm-subnet"></a>가상 네트워크 및 VM 서브넷 만들기
1. 테넌트 계정을 사용하여 로그인합니다.
2. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
4. 메뉴에서 **가상 네트워크** 항목을 클릭합니다.
5. 리소스 설명 블레이드의 하단 근처에서 **만들기**를 클릭합니다. 이 테이블에 따라 적합한 필드에 다음 값을 입력합니다.
   
   | **필드** | **값** |
   | --- | --- |
   | 이름 |vnet-01 |
   | 주소 공간 |10.0.10.0/23 |
   | 서브넷 이름 |subnet-01 |
   | 서브넷 주소 범위 |10.0.10.0/24 |
6. 앞서 만든 구독이 **구독** 필드에 채워지는 것을 볼 수 있습니다.
7. 리소스 그룹의 경우 새 리소스 그룹을 만들거나 이미 그룹이 있으면 **기존 그룹 사용**을 선택합니다.
8. 기본 위치를 확인합니다.
9. **만들기**를 클릭합니다.

### <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기
1. 방금 만든 가상 네트워크 리소스(Vnet-01)를 대시보드에서 엽니다.
2. 설정 블레이드를 열고 **서브넷**을 선택합니다.
3. **게이트웨이 서브넷**을 클릭하여 가상 네트워크에 게이트웨이 서브넷을 추가합니다.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. 서브넷의 이름은 **GatewaySubnet**이라고 기본으로 설정됩니다.
   게이트웨이 서브넷은 특별하며 제대로 작동하려면 특정 이름이 있어야 합니다.
5. **주소 범위** 필드에 **10.0.11.0/24**를 입력합니다.
6. **만들기**를 클릭하여 게이트웨이 서브넷을 만듭니다.

### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기
1. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
2. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
3. 네트워크 리소스 목록에서 **가상 네트워크 게이트웨이**를 선택합니다.
4. 설명을 검토하고 **만들기**를 클릭합니다.
5. **이름** 필드에 **GW1**을 입력합니다.
6. **가상 네트워크** 항목을 클릭하여 가상 네트워크를 선택합니다.
   목록에서 **Vnet-01**을 선택합니다.
7. **공용 IP 주소** 메뉴 항목을 클릭합니다. **공용 IP 주소 선택** 블레이드가 열리면 **새로 만들기**를 클릭합니다.
8. **이름** 필드에 **GW1-PiP**를 입력하고 **확인**을 클릭합니다.
9. **게이트웨이 유형**에 **VPN**이 기본으로 선택되어 있습니다. 이 설정을 유지합니다.
10. **VPN 종류**에 **Route-based**(경로 기반)이 기본으로 선택되어 있습니다.
    이 설정을 유지합니다.
11. **구독** 및 **위치**가 올바른지 확인합니다. 원한다면 대시보드에 리소스를 고정할 수 있습니다. **만들기**를 클릭합니다.

### <a name="create-the-local-network-gateway"></a>로컬 네트워크 게이트웨이 만들기
Azure Stack 평가 배포에 *로컬 네트워크 게이트웨이*를 구현하는 것은 실제 Azure 배포에 구현하는 것과 약간 다릅니다.

Azure에서와 마찬가지로 로컬 네트워크 게이트웨이라는 개념은 있습니다. 하지만 Azure 배포의 경우 로컬 네트워크 게이트웨이는 Azure에서 가상 네트워크 게이트웨이에 연결하는 데 사용하는 온-프레미스(테넌트의) 물리적 장치를 나타냅니다. 하지만 Azure Stack 평가 배포에서는 연결의 양쪽 끝이 가상 네트워크 게이트웨이입니다.

이것을 보다 포괄적으로 생각하는 방법은 로컬 네트워크 게이트웨이 리소스를 연결의 다른 쪽 끝에 있는 원격 게이트웨이를 가리키는 것으로 항상 간주하는 것입니다. POC가 설계된 방식으로 인해 다른 POC의 NAT VM에 있는 외부 네트워크 어댑터의 IP 주소를 로컬 네트워크 게이트웨이의 공용 IP 주소로 제공해야 합니다. 그런 다음 양쪽 끝이 제대로 연결될 수 있도록 NAT VM에 NAT 매핑을 만듭니다.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>NAT VM 외부 어댑터의 IP 주소 가져오기
1. POC2의 Azure Stack 물리적 컴퓨터에 로그인합니다.
2. [Windows 키] + R을 눌러서 **실행** 메뉴를 열고 **mstsc**를 입력하고 Enter를 누릅니다.
3. **컴퓨터** 필드에 이름 **MAS-BGPNAT01**을 입력하고  **연결**을 클릭합니다.
4. 시작 메뉴를 클릭하고 **Windows PowerShell**을 마우스 오른쪽 단추로 클릭하여 **관리자 권한으로 실행**을 선택합니다.
5. **ipconfig /all**을 입력합니다.
6. 온-프레미스 네트워크에 연결된 이더넷 어댑터를 찾아서 이 어댑터에 바인딩된 IPv4 주소를 기록해 둡니다. 이 예제 환경에서는 **10.16.167.195**이지만 사용자의 환경에서는 다를 수 있습니다.
7. 이 주소를 기록합니다. 이것이 바로 POC1에 만든 로컬 네트워크 게이트웨이 리소스의 공용 IP 주소로 사용할 정보입니다.

### <a name="create-the-local-network-gateway-resource"></a>로컬 네트워크 게이트웨이 리소스 만들기
1. POC1의 Azure Stack 물리적 컴퓨터에 로그인합니다.
2. **컴퓨터** 필드에 이름 **MAS-CON01**을 입력하고 **연결**을 클릭합니다.
3. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
4. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
5. 리소스 목록에서 **로컬 네트워크 게이트웨이**를 선택합니다.
6. **이름** 필드에 **POC2-GW**를 입력합니다.
7. 다른 게이트웨이의 IP 주소를 아직 모르지만 나중에 돌아와서 변경할 수 있으니 괜찮습니다. 지금은 **10.16.167.195**를 **IP 주소** 필드에 입력합니다.
8. **주소 공간** 필드에 POC2에 만들 Vnet의 주소 공간을 입력합니다. **10.0.20.0/23**을 사용할 예정이니 이 값을 입력합니다.
9. **구독**, **리소스 그룹** 및 **위치**가 모두 올바른지 확인하고 **만들기**를 클릭합니다.

### <a name="create-the-connection"></a>연결 만들기
1. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
2. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
3. 리소스 목록에서 **연결**을 선택합니다.
4. **기본** 설정 블레이드에서 **연결 형식**으로 **사이트 간(IPSec)**을 선택합니다.
5. **구독**, **리소스 그룹** 및 **위치**를 선택하고 **확인**을 클릭합니다.
6. **설정** 블레이드에서 이전에 만든 **가상 네트워크 게이트웨이**(**GW1**)를 선택합니다.
7. 이전에 만든 **로컬 네트워크 게이트웨이**(**POC2-GW**)를 선택합니다.
8. **연결 이름** 필드에 **POC1-POC2**를 입력합니다.
9. **공유 키(PSK)** 필드에 **12345**를 입력하고 **확인**을 클릭합니다.

### <a name="create-a-vm"></a>VM 만들기
VPN 연결을 통해 이동하는 데이터의 유효성을 검사하려면 각 POC에서 데이터를 송신하고 수신할 VM이 필요합니다. 이제 POC1에 VM을 만들어서 가상 네트워크의 VM 서브넷에 배치합니다.

1. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
2. Marketplace 메뉴에서 **Virtual Machines**를 선택합니다.
3. 가상 컴퓨터 이미지 목록에서 **Windows Server 2012 R2 데이터 센터** 이미지를 선택합니다.
4. **기본** 블레이드의 **이름** 필드에 **VM01**을 입력합니다.
5. 유효한 사용자 이름 및 암호를 입력합니다. VM을 만든 후 이 계정을 사용하여 VM에 로그인하게 됩니다.
6. **구독**, **리소스 그룹** 및 **위치**를 제공한 다음 **확인**을 클릭합니다.
7. **크기** 블레이드에서 이 인스턴스에 대한 VM 크기를 선택한 다음 **선택**을 클릭합니다.
8. **설정** 블레이드에서 기본 설정을 수락합니다. 다만 선택된 가상 네트워크가 **VNET-01**이고 서브넷이 **10.0.10.0/24**로 설정되어 있는지 확인합니다. **확인**을 클릭합니다.
9. **요약** 블레이드에서 설정을 검토한 다음 **확인**을 클릭합니다.

## <a name="create-the-network-resources-in-poc-2"></a>POC 2에 네트워크 리소스 만들기
### <a name="log-in-as-a-tenant"></a>테넌트로 로그인
서비스 관리자는 테넌트로 로그인하여 자신의 테넌트가 사용할 수 있는 계획, 제품 및 구독을 테스트할 수 있습니다. 테넌트가 아직 없는 경우 로그인하기 전에 [테넌트 계정을 만듭니다](azure-stack-add-new-user-aad.md) .

### <a name="create-the-virtual-network-and-vm-subnet"></a>가상 네트워크 및 VM 서브넷 만들기
1. 테넌트 계정을 사용하여 로그인합니다.
2. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
3. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
4. 메뉴에서 **가상 네트워크**를 클릭합니다.
5. 리소스 설명 블레이드의 하단 근처에서 **만들기**를 클릭합니다. 아래 테이블에 나열된 적합한 필드에 다음 값을 입력합니다.
   
   | **필드** | **값** |
   | --- | --- |
   | 이름 |vnet-02 |
   | 주소 공간 |10.0.20.0/23 |
   | 서브넷 이름 |subnet-02 |
   | 서브넷 주소 범위 |10.0.20.0/24 |
6. 이전에 만든 구독이 **구독** 필드에 채워지는 것을 볼 수 있습니다.
7. 리소스 그룹의 경우 새 리소스 그룹을 만들거나 이미 그룹이 있으면 **기존 그룹 사용**을 선택합니다.
8. 기본 **위치**를 확인합니다. 원하는 경우 간편한 액세스를 위해 가상 네트워크를 대시보드에 고정할 수 있습니다.
9. **만들기**를 클릭합니다.

### <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기
1. 만든 가상 네트워크 리소스(**Vnet-02**)를 대시보드에서 엽니다.
2. **설정** 블레이드를 열고 **서브넷**을 선택합니다.
3. **게이트웨이 서브넷**을 클릭하여 가상 네트워크에 게이트웨이 서브넷을 추가합니다.
   
4. 서브넷의 이름은 **GatewaySubnet**이라고 기본으로 설정됩니다.
   게이트웨이 서브넷은 특별하며 제대로 작동하려면 특정 이름이 있어야 합니다.
5. **주소 범위** 필드에 **10.0.20.0/24**를 입력합니다.
6. **만들기**를 클릭하여 게이트웨이 서브넷을 만듭니다.

### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기
1. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
2. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
3. 네트워크 리소스 목록에서 **가상 네트워크 게이트웨이**를 선택합니다.
4. 설명을 검토하고 **만들기**를 클릭합니다.
5. **이름** 필드에 **GW2**를 입력합니다.
6. **가상 네트워크**를 클릭하여 가상 네트워크를 선택합니다.
   목록에서 **Vnet-02**를 선택합니다.
7. **공용 IP 주소**를 클릭합니다. **공용 IP 주소 선택** 블레이드가 열리면 **새로 만들기**를 클릭합니다.
8. **이름** 필드에 **GW2-PiP**를 입력하고 **확인**을 클릭합니다.
9. **게이트웨이 유형**에 **VPN**이 기본으로 선택되어 있습니다. 이 설정을 유지합니다.
10. **VPN 종류**에 **Route-based**(경로 기반)이 기본으로 선택되어 있습니다.
    이 설정을 유지합니다.
11. **구독** 및 **위치**가 올바른지 확인합니다. 원한다면 대시보드에 리소스를 고정할 수 있습니다. **만들기**를 클릭합니다.

### <a name="create-the-local-network-gateway"></a>로컬 네트워크 게이트웨이 만들기
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>NAT VM 외부 어댑터의 IP 주소 가져오기
1. POC1의 Azure Stack 물리적 컴퓨터에 로그인합니다.
2. [Windows 키] + R을 길게 눌러서 **실행** 메뉴를 열고 **mstsc**를 입력하고 Enter를 누릅니다.
3. **컴퓨터** 필드에 이름 **MAS-BGPNAT01**을 입력하고  **연결**을 클릭합니다.
4. 시작 메뉴를 클릭하고 **Windows PowerShell**을 마우스 오른쪽 단추로 클릭하여 **관리자 권한으로 실행**을 선택합니다.
5. **ipconfig /all**을 입력합니다.
6. 온-프레미스 네트워크에 연결된 이더넷 어댑터를 찾아서 이 어댑터에 바인딩된 IPv4 주소를 기록해 둡니다. 예제 환경에서는 **10.16.169.131**이지만 사용자의 환경은 다릅니다.
7. 이 주소를 기록합니다. 이것이 바로 POC1에 만든 로컬 네트워크 게이트웨이 리소스의 공용 IP 주소로 나중에 사용할 정보입니다.

#### <a name="create-the-local-network-gateway-resource"></a>로컬 네트워크 게이트웨이 리소스 만들기
1. POC2의 Azure Stack 물리적 컴퓨터에 로그인합니다.
2. **컴퓨터** 필드에 이름 **MAS-CON01**을 입력하고 **연결**을 클릭합니다.
3. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
4. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
5. 리소스 목록에서 **로컬 네트워크 게이트웨이**를 선택합니다.
6. **이름** 필드에 **POC1-GW**를 입력합니다.
7. 이제 POC1의 가상 네트워크 게이트웨이에 대해 기록해둔 공용 IP 주소가 필요합니다. **IP 주소** 필드에 **10.16.169.131**을 입력합니다.
8. **주소 공간** 필드에 POC1 **Vnet-01**의 주소 공간 **10.0.0.0/16**을 입력합니다.
9. **구독**, **리소스 그룹** 및 **위치**가 모두 올바른지 확인하고 **만들기**를 클릭합니다.

## <a name="create-the-connection"></a>연결 만들기
1. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
2. Marketplace 메뉴에서 **네트워킹**을 선택합니다.
3. 리소스 목록에서 **연결**을 선택합니다.
4. **기본** 설정 블레이드에서 **연결 형식**으로 **사이트 간(IPSec)**을 선택합니다.
5. **구독**, **리소스 그룹** 및 **위치**를 선택하고 **확인**을 클릭합니다.
6. **설정** 블레이드에서 이전에 만든 **가상 네트워크 게이트웨이**(**GW1**)를 선택합니다.
7. 이전에 만든 **로컬 네트워크 게이트웨이**(**POC1-GW**)를 선택합니다.
8. **연결 이름** 필드에 **POC2-POC1**을 입력합니다.
9. **공유 키(PSK)** 필드에 **12345**를 입력합니다. 다른 값을 선택하는 경우 POC1에서 만든 공유 키의 값과 반드시 일치해야 한다는 것을 기억하세요. **확인**을 클릭합니다.

## <a name="create-a-vm"></a>VM 만들기
이제 POC1에 VM을 만들어서 가상 네트워크의 VM 서브넷에 배치합니다.

1. Azure Portal에서 **새로 만들기**를 클릭합니다.
   
2. Marketplace 메뉴에서 **Virtual Machines**를 선택합니다.
3. 가상 컴퓨터 이미지 목록에서 **Windows Server 2012 R2 데이터 센터** 이미지를 선택합니다.
4. **기본** 블레이드의 **이름** 필드에 **VM02**를 입력합니다.
5. 유효한 사용자 이름 및 암호를 입력합니다. VM을 만든 후 이 계정을 사용하여 VM에 로그인하게 됩니다.
6. **구독**, **리소스 그룹** 및 **위치**를 제공한 다음 **확인**을 클릭합니다.
7. **크기** 블레이드에서 이 인스턴스에 대한 VM 크기를 선택한 다음 **선택**을 클릭합니다.
8. 설정 블레이드에서 기본 설정을 수락합니다. 다만 선택된 가상 네트워크가 **VNET-02**이고 서브넷이 **20.0.0.0/24**로 설정되어 있는지 확인합니다. **확인**을 클릭합니다.
9. **요약** 블레이드에서 설정을 검토한 다음 **확인**을 클릭합니다.

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>게이트웨이 통과를 위해 각 POC의 NAT VM 구성
POC는 실제 호스트가 배포되는 네트워크로부터 격리된 자체 포함 형식으로 설계되었기 때문에 게이트웨이가 연결되는 “외부” VIP 네트워크는 실제 외부 네트워크가 아니며 NAT(Network Address Translation)를 수행하는 라우터 뒤에 숨겨져 있습니다. 라우터는 실제로 POC 인프라에서 라우팅 및 원격 액세스 서비스(RRAS) 역할을 실행하는 Windows Server VM(**MAS-BGPNAT01**)입니다. 사이트 간 VPN 연결이 양쪽 끝에 연결되도록 하려면 MAS-BGPNAT01 VM에 NAT를 구성해야 합니다. 이렇게 하려면, VPN 연결에 필요한 포트를 위해 BGPNAT VM의 외부 인터페이스를 Edge 게이트웨이 풀의 VIP에 매핑하는 정적 NAT 매핑을 만들어야 합니다.

> [!NOTE]
> 이 구성은 POC 환경에만 필요합니다.
> 
> 

### <a name="configure-nat"></a>NAT 구성
두 POC 환경 모두에서 이 단계를 수행해야 합니다.

1. POC1의 Azure Stack 물리적 컴퓨터에 로그인합니다.
2. [Windows 키] + R을 길게 눌러서 **실행** 메뉴를 열고 **mstsc**를 입력하고 **Enter**를 누릅니다.
3. **컴퓨터** 필드에 이름 **MAS-BGPNAT01**을 입력하고 **연결**을 클릭합니다.
4. 시작 메뉴를 클릭하고 **Windows PowerShell**을 마우스 오른쪽 단추로 클릭하여 **관리자 권한으로 실행**을 선택합니다.
5. **ipconfig /all**을 입력합니다.
6. 온-프레미스 네트워크에 연결된 이더넷 어댑터를 찾아서 이 어댑터에 바인딩된 IPv4 주소를 기록해 둡니다. 예제 환경에서는 **10.16.169.131**(아래 빨간색 원으로 표시됨)이지만 사용자의 환경은 다릅니다.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. 다음 PowerShell 명령을 입력하여 IKE 인증 포트에 대한 외부 NAT 주소를 지정합니다. IP 주소를 사용자의 환경에 일치하는 주소로 변경해야 합니다.
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. 다음으로 IPSEC 터널의 PHASE 1에 대한 ISAKMP 포트 500을 매핑하기 위해 외부 주소를 게이트웨이 공용 IP 주소에 매핑하도록 정적 NAT 매핑을 만듭니다.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> 여기서 `-InternalAddress` 매개 변수는 앞서 만든 가상 네트워크 게이트웨이의 공용 IP 주소입니다.  이 IP 주소를 찾으려면 가상 네트워크 게이트웨이 블레이드의 속성을 살펴보고 공용 IP 주소에 대한 값을 찾습니다.       

9. 마지막으로 포트 4500을 사용하여 NAT 장치를 통한 완전한 IPEC 터널을 설정하는 NAT 통과를 구성해야 합니다.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> 여기서 `-InternalAddress` 매개 변수는 앞서 만든 가상 네트워크 게이트웨이의 공용 IP 주소입니다.  이 IP 주소를 찾으려면 가상 네트워크 게이트웨이 블레이드의 속성을 살펴보고 공용 IP 주소에 대한 값을 찾습니다.       

10. POC2의 1-9단계를 반복합니다.

## <a name="test-the-connection"></a>연결 테스트
사이트 간 연결이 설정되었으니 이를 통해 트래픽이 흐르도록 할 수 있는지 확인해야 합니다. 이 작업은 POC 환경에서 만든 VM 중 하나에 로그인하여 다른 쪽 환경에서 만든 VM에 ping을 실행하기만 하면 되는 간단한 작업입니다. 사이트 간 연결을 통해 트래픽이 배치되는지 확인하려면 VIP가 아닌 원격 서브넷에서 VM의 직접 IP(DIP) 주소를 ping하는 것이 좋습니다. 이렇게 하려면 연결 다른 쪽 끝의 주소를 찾아서 기록해 둬야 합니다.

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>POC1의 테넌트 VM에 로그인
1. POC1의 Azure Stack 물리적 컴퓨터에 로그인하고 테넌트 계정을 사용하여 포털에 로그인합니다.
2. 왼쪽 탐색 모음에서 **Virtual Machines**를 클릭합니다.
3. 이전에 만든 **VM01**을 VM 목록에서 찾아서 클릭합니다.
4. 가상 컴퓨터의 블레이드에서 **연결**을 클릭합니다.
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. VM 내부에서 명령 프롬프트를 열고 **ipconfig /all**을 입력합니다.
6. 출력 내용에서 **IPv4 주소**를 찾아서 기록해 둡니다. 이 주소가 POC2에서 ping할 주소입니다. 예제 환경의 경우 주소가 **10.0.10.4**이지만 사용자의 환경에서는 주소가 다를 수 있습니다. 하지만 이전에 만든 **10.0.10.0/24** 서브넷의 범위에 포함되어야 합니다.

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>POC2의 테넌트 VM에 로그인
1. POC2의 Azure Stack 물리적 컴퓨터에 로그인하고 테넌트 계정을 사용하여 포털에 로그인합니다.
2. 왼쪽 탐색 모음에서 **Virtual Machines**를 클릭합니다.
3. 이전에 만든 **VM02**를 VM 목록에서 찾아서 클릭합니다.
4. 가상 컴퓨터의 블레이드에서 **연결**을 클릭합니다.
   
5. VM 내부에서 명령 프롬프트를 열고 **ipconfig /all**을 입력합니다.
6. 10.0.20.0/24 범위 안에 드는 IPv4 주소가 표시됩니다. 예제 환경의 경우 주소가 10.0.20.4이지만 사용자 환경의 주소는 다를 수 있습니다.
7. 이제 POC2의 VM에서 터널을 통해 POC1의 VM을 ping합니다. 이렇게 하려면 VM01에서 기록해둔 DIP를 ping합니다.
   이 예제 환경의 경우 10.0.10.4이지만 사용자의 환경에서 기록해둔 주소를 ping해야 합니다. 결과가 다음과 같이 표시됩니다.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. 원격 VM의 응답을 통해 테스트가 성공한 것을 알 수 있습니다. 이제 VM 연결 창을 닫습니다. 또는 연결을 테스트하기 위해 파일 복사와 같은 다른 데이터 전송을 시도해 볼 수 있습니다.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>게이트웨이 연결을 통한 데이터 전송 통계 보기
사이트 간 연결을 통해 전송되는 데이터의 양에 대한 정보는 연결 블레이드에 제공됩니다. 이 테스트는 방금 보낸 ping이 VPN 연결을 실제로 통과했는지 확인할 수 있는 다른 좋은 방법이기도 합니다.

1. POC2의 테넌트 VM에 로그인된 상태에서 테넌트 계정을 사용하여 **Microsoft Azure Stack POC 포털**에 로그인합니다.
2. **찾아보기** 메뉴 항목을 클릭하고 **연결**을 선택합니다.
3. 목록에서 **POC2-POC1** 연결을 클릭합니다.
4. 연결 블레이드에서 **데이터 입력** 및 **데이터 출력**에 대한 통계를 볼 수 있습니다. 다음 스크린샷에는 ping만으로 생성되기에는 큰 숫자가 있습니다. 이것은 파일 전송이 추가로 있었기 때문입니다. 일부&0;이 아닌 값을 볼 수 있습니다.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)


