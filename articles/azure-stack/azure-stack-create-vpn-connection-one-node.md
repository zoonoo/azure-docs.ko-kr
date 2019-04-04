---
title: 다른 Azure Stack 개발 키트 환경의 두 가상 네트워크 간의 사이트 간 VPN 연결 만들기 | Microsoft Docs
description: 클라우드 관리자가 두 개의 단일 노드 Azure Stack 개발 키트 환경 간에 사이트 간 VPN 연결을 만드는 데 사용 하는 단계별 절차입니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 432231d7b571cfda9ec4d71d24aa8bd05346c6b2
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803751"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>다른 Azure Stack 개발 키트 환경의 두 가상 네트워크 간의 사이트 간 VPN 연결 만들기

## <a name="overview"></a>개요

이 문서에는 두 가지 별도 Azure Stack 개발 키트 (ASDK) 환경에서 두 가상 네트워크 간의 사이트 간 VPN 연결을 만드는 방법을 설명 합니다. 연결을 구성 하면 VPN 게이트웨이가 Azure Stack에서 작동 하는 방법을 알아봅니다.

### <a name="connection"></a>연결

다음 그림에서는 연결 구성 모양을 완료 되 면

![사이트 간 VPN 연결 구성](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>시작하기 전에

연결 구성을 완료 하려면 시작 하기 전에 다음 항목이 있는지 확인 합니다.

* 두 서버 및 기타 필수 구성 요소에 설명 된 대로 ASDK 하드웨어 요구 사항에 맞는 [빠른 시작: Azure Stack 개발 키트 평가](azure-stack-deploy-overview.md)합니다.
* 합니다 [Azure Stack 개발 키트](https://azure.microsoft.com/overview/azure-stack/try/) 배포 패키지 있습니다.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Azure Stack 개발 키트 환경 배포

연결 구성을 완료 하려면 두 가지 ASDK 환경 배포 해야 합니다.

> [!NOTE]
> 배포 하는 각 ASDK를 수행 합니다 [배포 지침](azure-stack-run-powershell-script.md)합니다. 이 문서에서는 ASDK 환경 이라고 **POC1** 하 고 **POC2**합니다.

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>POC1에 POC2 제품 준비

사용자 제품에 구독 하 고 가상 컴퓨터를 배포할 수 있도록 POC1 및 POC2에서 제품을 준비 합니다. 제품을 만드는 방법에 대 한 자세한 내용은 [Azure Stack 사용자에 게 가상 컴퓨터를 사용할 수 있도록](azure-stack-tutorial-tenant-vm.md)입니다.

## <a name="review-and-complete-the-network-configuration-table"></a>검토 하 고 네트워크 구성 테이블을 완료 합니다.

다음 표에서 ASDK 환경 모두에 대 한 네트워크 구성을 보여 줍니다. 네트워크에 대 한 관련 된 외부 BGPNAT 주소를 추가 하려면 테이블 뒤에 나오는 절차를 따르십시오.

### <a name="network-configuration-table"></a>네트워크 구성 테이블

|   |POC1|POC2|
|---------|---------|---------|
|가상 네트워크 이름     |VNET-01|VNET-02 |
|가상 네트워크 주소 공간 |10.0.10.0/23|10.0.20.0/23|
|서브넷 이름     |Subnet-01|Subnet-02|
|서브넷 주소 범위|10.0.10.0/24 |10.0.20.0/24 |
|게이트웨이 서브넷      |10.0.11.0/24|10.0.21.0/24|
|외부 BGPNAT 주소     |         |         |

> [!NOTE]
> 예제 환경의 외부 BGPNAT IP 주소를 POC1에 대 한 10.16.167.195 되며 10.16.169.131 POC2에 대 한 합니다. ASDK 호스트용 외부 BGPNAT IP 주소를 확인 하려면 다음 절차를 사용 하 고 이전 네트워크 구성 테이블에 해당를 추가 합니다.

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>NAT VM 외부 어댑터의 IP 주소 가져오기

1. POC1에 대 한 Azure Stack 물리적 컴퓨터에 로그인 합니다.
2. 관리자 암호를 바꾸려면 다음 PowerShell 코드를 편집 하 고 POC 호스트에 코드를 실행 하십시오.

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```

3. 이전 섹션에 표시 되는 네트워크 구성 테이블에 IP 주소를 추가 합니다.

4. POC2에서이 절차를 반복 합니다.

## <a name="create-the-network-resources-in-poc1"></a>POC1의 네트워크 리소스 만들기

이제 게이트웨이가 설정 하는 데 필요한 POC1 네트워크 리소스를 만들 수 있습니다. 다음 지침은 Azure Stack 사용자 포털을 사용 하 여 리소스를 만드는 방법을 설명 합니다. 또한 리소스를 만들려면 PowerShell 코드를 사용할 수 있습니다.

![리소스를 만드는 데 사용 되는 워크플로](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>테 넌 트에 로그인

서비스 관리자는 테스트 계획, 제품 및 해당 테 넌 트를 사용할 수 있는 구독에 테 넌 트 로그인 수 있습니다. 하는 하나, 아직 없는 경우 [테 넌 트 계정 만들기](azure-stack-add-new-user-aad.md) 로그인 하기 전에 합니다.

### <a name="create-the-virtual-network-and-vm-subnet"></a>가상 네트워크 및 VM 서브넷 만들기

1. 사용자 포털에 로그인 할 테 넌 트 계정을 사용 합니다.
2. 사용자 포털에서 선택 **+ 리소스 만들기**합니다.
3. 로 이동 **Marketplace**를 선택한 후 **네트워킹**합니다.
4. **가상 네트워크**를 선택합니다.
5. 에 대 한 **이름을**를 **주소 공간**를 **서브넷 이름**, 및 **서브넷 주소 범위**, 네트워크의 앞부분에서 표시 되는 값을 사용 하 여 구성 테이블입니다.
6. **구독**, 앞서 만든 구독이 표시 됩니다.
7. 에 대 한 **리소스 그룹**, 리소스 그룹 만들기 또는 이미 있는 경우 하나를 선택할 수 있습니다 **기존 항목 사용**합니다.
8. 기본 위치를 확인합니다.
9. **대시보드에 고정**을 선택합니다.
10. **만들기**를 선택합니다.

### <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기

1. 대시보드에서 이전에 만든 vnet-01 가상 네트워크 리소스를 엽니다.
2. **설정** 블레이드를 열고 **서브넷**을 선택합니다.
3. 가상 네트워크에 게이트웨이 서브넷을 추가 하려면 **게이트웨이 서브넷**합니다.

    ![게이트웨이 서브넷 추가](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. 기본적으로 서브넷 이름을 설정할지 **GatewaySubnet**합니다. 게이트웨이 서브넷은 특별 합니다. 제대로 작동 하려면 사용 해야 합니다 **GatewaySubnet** 이름입니다.
5. **주소 범위**, 주소 인지 확인 **10.0.11.0/24**합니다.
6. 선택 **확인** 게이트웨이 서브넷을 만들어야 합니다.

### <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

1. Azure portal에서 선택 **+ 리소스 만들기**합니다.
2. 로 이동 **Marketplace**를 선택한 후 **네트워킹**합니다.
3. 네트워크 리소스의 목록에서 선택 **가상 네트워크 게이트웨이**합니다.
4. **이름을**를 입력 **GW1**합니다.
5. 선택 된 **가상 네트워크** 가상 네트워크를 선택 하는 항목입니다. 선택 **vnet-01** 목록에서.
6. 선택 된 **공용 IP 주소** 메뉴 항목입니다. 경우는 **공용 IP 주소 선택** 창이 열립니다 **새로 만들기**합니다.
7. **이름을**를 입력 **GW1-PiP**를 선택한 후 **확인**합니다.
8. 기본적으로 대 한 **VPN 유형**를 **route-based** 을 선택 합니다. 유지 된 **route-based** VPN 유형입니다.
9. **구독** 및 **위치**가 올바른지 확인합니다. 리소스를 대시보드에 고정할 수 있습니다. **만들기**를 선택합니다.

### <a name="create-the-local-network-gateway"></a>로컬 네트워크 게이트웨이 만들기

Azure Stack 평가 배포에 *로컬 네트워크 게이트웨이*를 구현하는 것은 실제 Azure 배포에 구현하는 것과 약간 다릅니다.

Azure 배포에서 로컬 네트워크 게이트웨이 온-프레미스 (테 넌 트)에서 물리적 장치를 Azure에서 가상 네트워크 게이트웨이에 연결 하는 데 사용할 수 있는 나타냅니다. 이 Azure Stack 평가 배포에서는 연결의 양쪽 끝이 가상 네트워크 게이트웨이입니다.

이 더 일반적으로 생각 하는 방법은 로컬 네트워크 게이트웨이 리소스를 연결의 다른 끝에 있는 원격 게이트웨이 항상 표시 되는지 것입니다. Azure Stack 개발 키트 디자인 된 방식으로 인해 네트워크 주소 변환 (NAT) 로컬 네트워크 게이트웨이의 공용 IP 주소와 다른 ASDK의 VM에서 외부 네트워크 어댑터의 IP 주소를 제공 해야 합니다. 그런 다음 양쪽 제대로 연결 되어 있는지 확인 하려면 NAT VM에 NAT 매핑의 만듭니다.

### <a name="create-the-local-network-gateway-resource"></a>로컬 네트워크 게이트웨이 리소스 만들기

1. POC1에 대 한 Azure Stack 물리적 컴퓨터에 로그인 합니다.
2. 사용자 포털에서 선택 **+ 리소스 만들기**합니다.
3. 로 이동 **Marketplace**를 선택한 후 **네트워킹**합니다.
4. 리소스 목록에서 선택 **로컬 네트워크 게이트웨이**합니다.
5. **이름을**를 입력 **POC2-GW**합니다.
6. **IP 주소**에 POC2에 대 한 외부 BGPNAT 주소를 입력 합니다. 이 주소가 이전 네트워크 구성 테이블에에서 나타납니다.
7. **주소 공간**, 나중에 만드는 POC2 VNET의 주소 공간을 입력 **10.0.20.0/23**합니다.
8. 확인 프로그램 **구독**, **리소스 그룹**, 및 **위치** 값이 올바르고 선택한 **만들기**합니다.

### <a name="create-the-connection"></a>연결 만들기

1. 사용자 포털에서 선택 **+ 리소스 만들기**합니다.
2. 로 이동 **Marketplace**를 선택한 후 **네트워킹**합니다.
3. 리소스 목록에서 선택 **연결**합니다.
4. 에 **기본 사항** 설정 블레이드에서 대 한 합니다 **연결 형식**를 선택 **사이트-사이트 간 (IPSec)** 합니다.
5. 선택 합니다 **구독**를 **리소스 그룹**, 및 **위치**를 선택한 후 **확인**합니다.
6. 에 **설정을** 블레이드에서 **가상 네트워크 게이트웨이**를 선택한 후 **GW1**합니다.
7. 선택 **로컬 네트워크 게이트웨이**를 선택한 후 **POC2-GW**합니다.
8. **연결 이름**를 입력 **POC1-POC2**합니다.
9. **공유 키 (PSK)** 를 입력 **12345**를 선택한 후 **확인**합니다.
10. 에 **요약** 블레이드에서 **확인**합니다.

### <a name="create-a-vm"></a>VM 만들기

VPN 연결을 통해 전송 되는 데이터의 유효성을 검사 하려면 각 Azure Stack 개발 키트에서 데이터를 받고 보내는 데 가상 컴퓨터를 해야 합니다. 이제 POC1의 가상 머신 만들기 및 다음에서 가상 네트워크에 배치 VM 서브넷입니다.

1. Azure portal에서 선택 **+ 리소스 만들기**합니다.
2. 로 이동 **Marketplace**를 선택한 후 **계산**합니다.
3. 가상 머신 이미지 목록에서 선택 합니다 **Windows Server 2016 Datacenter 평가판** 이미지입니다.
4. 에 **기본 사항** 블레이드, **이름**를 입력 **VM01**합니다.
5. 유효한 사용자 이름과 암호를 입력 합니다. 이 계정을 사용 하 여이 만들어진 후 VM에 로그인 합니다.
6. 제공을 **구독**를 **리소스 그룹**, 및 **위치**를 선택한 후 **확인**합니다.
7. 에 **크기** 블레이드에서이 인스턴스에 대 한 가상 머신 크기를 선택한 후 **선택**합니다.
8. 에 **설정을** 블레이드에서 기본값을 사용 합니다. 있는지 확인 합니다 **vnet-01** 가상 네트워크가 선택 됩니다. 서브넷으로 설정 되어 있는지 확인 하십시오 **10.0.10.0/24**합니다. 그런 다음 **확인**을 선택합니다.
9. 에 **요약** 블레이드에서 설정을 검토 한 다음 선택 **확인**합니다.

## <a name="create-the-network-resources-in-poc2"></a>POC2의 네트워크 리소스 만들기

다음 단계 POC2에 대 한 네트워크 리소스를 만드는 것입니다. 다음 지침에는 사용자 포털을 사용 하 여 리소스를 만드는 방법을 보여 줍니다.

### <a name="sign-in-as-a-tenant-again"></a>테 넌 트로 다시 로그인

서비스 관리자는 테스트 계획, 제품 및 해당 테 넌 트를 사용할 수 있는 구독에 테 넌 트 로그인 수 있습니다. 하는 하나, 아직 없는 경우 [테 넌 트 계정 만들기](azure-stack-add-new-user-aad.md) 로그인 하기 전에 합니다.

### <a name="create-virtual-network-and-vm-subnet"></a>가상 네트워크 및 VM 서브넷 만들기

1. 테 넌 트 계정을 사용 하 여 로그인 합니다.
2. 사용자 포털에서 선택 **+ 리소스 만들기**합니다.
3. 로 이동 **Marketplace**를 선택한 후 **네트워킹**합니다.
4. **가상 네트워크**를 선택합니다.
5. 네트워크 구성 테이블 앞에 표시 되는 정보를 사용 하 여는 POC2에 대 한 값을 식별 **이름을**를 **주소 공간**를 **서브넷 이름**, 및 **서브넷 주소 범위**합니다.
6. **구독**, 앞서 만든 구독이 표시 됩니다.
7. 에 대 한 **리소스 그룹**, 새 리소스 그룹을 만들거나 이미 있는 경우 하나를 선택 **기존 항목 사용**합니다.
8. 기본 확인 **위치**합니다.
9. **대시보드에 고정**을 선택합니다.
10. **만들기**를 선택합니다.

### <a name="create-gateway-subnet"></a>게이트웨이 서브넷 만들기

1. 사용자가 만든 가상 네트워크 리소스를 엽니다 (**vnet-02**) 대시보드에서.
2. **설정** 블레이드를 열고 **서브넷**을 선택합니다.
3. 선택 **게이트웨이 서브넷** 가상 네트워크에 게이트웨이 서브넷을 추가 합니다.
4. 서브넷의 이름은 **GatewaySubnet**이라고 기본으로 설정됩니다. 게이트웨이 서브넷은 특별하며 제대로 작동하려면 특정 이름이 있어야 합니다.
5. 에 **주소 범위** 필드에서 주소 확인 **10.0.21.0/24**합니다.
6. 선택 **확인** 게이트웨이 서브넷을 만들어야 합니다.

### <a name="create-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

1. Azure portal에서 선택 **+ 리소스 만들기**합니다.  
2. 로 이동 **Marketplace**를 선택한 후 **네트워킹**합니다.
3. 네트워크 리소스의 목록에서 선택 **가상 네트워크 게이트웨이**합니다.
4. **이름을**를 입력 **GW2**합니다.
5. 가상 네트워크를 선택 하려면 **가상 네트워크**합니다. 선택한 **vnet-02** 목록에서.
6. **공용 IP 주소**를 선택합니다. 경우는 **공용 IP 주소 선택** 블레이드가 열리고 **새로 만들기**합니다.
7. **이름을**를 입력 **GW2-PiP**를 선택한 후 **확인**합니다.
8. 기본적으로 대 한 **VPN 유형**를 **route-based** 을 선택 합니다. 유지 된 **route-based** VPN 유형입니다.
9. **구독** 및 **위치**가 올바른지 확인합니다. 리소스를 대시보드에 고정할 수 있습니다. **만들기**를 선택합니다.

### <a name="create-local-network-gateway-resource"></a>로컬 네트워크 게이트웨이 리소스 만들기

1. POC2 사용자 포털에서 선택 **+ 리소스 만들기**합니다.
2. 로 이동 **Marketplace**를 선택한 후 **네트워킹**합니다.
3. 리소스 목록에서 선택 **로컬 네트워크 게이트웨이**합니다.
4. **이름을**를 입력 **POC1-GW**합니다.
5. **IP 주소**, 이전에 네트워크 구성 테이블에 나열 된 POC1에 대 한 외부 BGPNAT 주소를 입력 합니다.
6. **주소 공간**, POC1에서 다음을 입력 합니다 **10.0.10.0/23** 의 주소 공간이 **vnet-01**합니다.
7. 확인 프로그램 **구독**를 **리소스 그룹**, 및 **위치** 올바른지를 선택한 **만들기**합니다.

## <a name="create-connection"></a>연결 만들기

1. 사용자 포털에서 선택 **+ 리소스 만들기**합니다.
2. 로 이동 **Marketplace**를 선택한 후 **네트워킹**합니다.
3. 리소스 목록에서 선택 **연결**합니다.
4. 에 **기본** 설정 블레이드에서 대 한 합니다 **연결 형식**, 선택 **사이트-사이트 간 (IPSec)** 합니다.
5. 선택 합니다 **구독**를 **리소스 그룹**, 및 **위치**를 선택한 후 **확인**합니다.
6. 에 **설정을** 블레이드에서 **가상 네트워크 게이트웨이**를 선택한 후 **GW2**합니다.
7. 선택 **로컬 네트워크 게이트웨이**를 선택한 후 **POC1-GW**합니다.
8. **연결 이름**를 입력 **POC2-POC1**합니다.
9. **공유 키 (PSK)** 를 입력 **12345**합니다. 다른 값을 선택 하는 경우 POC1에서 만든 공유 키 값과 일치 해야 해야 합니다. **확인**을 선택합니다.
10. 검토 합니다 **요약** 블레이드에서 선택한 후 **확인**합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

이제 POC2에서 가상 컴퓨터를 만듭니다 및 가상 네트워크의 VM 서브넷에 배치 합니다.

1. Azure portal에서 선택 **+ 리소스 만들기**합니다.
2. 로 이동 **Marketplace**를 선택한 후 **계산**합니다.
3. 가상 머신 이미지 목록에서 선택 합니다 **Windows Server 2016 Datacenter 평가판** 이미지입니다.
4. 에 **기본 사항을** 블레이드에서 대 한 **이름**를 입력 **VM02**합니다.
5. 유효한 사용자 이름과 암호를 입력 합니다. 이 계정을 사용 하 여이 만든 후 가상 머신에 로그인 합니다.
6. 제공을 **구독**를 **리소스 그룹**, 및 **위치**를 선택한 후 **확인**합니다.
7. 에 **크기** 블레이드에서이 인스턴스에 대 한 크기 및 선택한 가상 머신을 **선택**합니다.
8. 에 **설정을** 블레이드에서 기본값을 사용할 수 있습니다. 있는지 확인 합니다 **vnet-02** 가상 네트워크를 선택한 서브넷으로 설정 되어 있는지 확인 하십시오 **10.0.20.0/24**합니다. **확인**을 선택합니다.
9. 설정을 검토 합니다 **요약** 블레이드에서 선택한 후 **확인**합니다.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>게이트웨이 통과 위해 각 Azure Stack 개발 키트에서 NAT 가상 컴퓨터를 구성 합니다.

ASDK 독립적인와 실제 호스트를 배포 하는 네트워크에서 격리 되므로 합니다 *외부* VIP 네트워크에 게이트웨이가 연결 되는 실제 외부 아닙니다. 대신 VIP 네트워크는 네트워크 주소 변환을 수행 하는 라우터 뒤에 숨겨져 있습니다.

라우터는 호출 하는 Windows Server 가상 머신을 **AzS-bgpnat01**, ASDK 인프라에서 라우팅 및 원격 액세스 서비스 (RRAS) 역할을 실행 하는 합니다. 양쪽 끝에 연결 하려면 사이트 간 VPN 연결을 허용 하도록 AzS-bgpnat01 가상 머신에서 NAT를 구성 해야 합니다.

VPN 연결을 구성 하려면 BGPNAT 가상 머신에서 외부 인터페이스를 edge 게이트웨이 풀의 VIP에 매핑하는 정적 NAT 지도의 경로 만들어야 합니다. 정적 NAT 지도의 경로 각 포트를 VPN 연결에 필요 합니다.

> [!NOTE]
> 이 구성은 Azure Stack 개발 키트 환경에만 필요 합니다.
>
>

### <a name="configure-the-nat"></a>NAT 구성

> [!IMPORTANT]
> ASDK 환경 모두에 대해이 절차를 완료 해야 합니다.

1. 확인 합니다 **내부 IP 주소** 다음 PowerShell 스크립트에서 사용 합니다. (GW1 및 GW2) 가상 네트워크 게이트웨이 엽니다 한 후는 **개요** 블레이드에서 값을 저장 합니다 **공용 IP 주소** 나중에 사용할 수입니다.

   ![내부 IP 주소](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. POC1에 대 한 Azure Stack 물리적 컴퓨터에 로그인 합니다.
3. 복사 하 고 다음 PowerShell 스크립트를 편집 합니다. 각 Azure Stack 개발 키트에서 NAT를 구성 하려면를 관리자 권한으로 Windows PowerShell ISE에서 스크립트를 실행 합니다. 스크립트에서 값을 추가 합니다 `External BGPNAT address` 고 `Internal IP address` 자리 표시자:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. POC2에서이 절차를 반복 합니다.

## <a name="test-the-connection"></a>연결 테스트

사이트 간 연결이 설정 되 면 했으므로 통해 트래픽이 흐르도록를 가져올 수 있는지를 확인 해야 합니다. 유효성을 검사할 ASDK 환경 중 하나에서 만든 가상 컴퓨터 중 하나에 로그인 합니다. 그런 다음 다른 환경에서 만든 가상 컴퓨터를 ping 합니다.

사이트 간 연결을 통해 트래픽을 전송 하는 되도록 VIP가 아닌 원격 서브넷에서 가상 컴퓨터의 직접 IP (DIP) 주소를 ping 수를 확인 합니다. 이렇게 하려면 연결의 한쪽 끝에 DIP 주소를 찾습니다. 나중에 사용할 주소를 저장 합니다.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>테 넌 트 POC1의 VM에 로그인

1. POC1에 대 한 Azure Stack 물리적 컴퓨터에 로그인 하 고 테 넌 트 계정을 사용 하 여 사용자 포털에 로그인 합니다.
2. 왼쪽된 탐색 모음에서 선택 **계산**합니다.
3. Vm 목록에서 찾을 **VM01** 는 이전에 만든 하 고 선택 합니다.
4. 가상 컴퓨터에 대 한 블레이드에서 클릭 **Connect**, VM01.rdp 파일을 엽니다.

     ![연결 단추](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. 가상 머신을 만들 때 구성 된 계정으로 로그인 합니다.
6. 관리자 권한 엽니다 **Windows PowerShell** 창입니다.
7. 입력 **ipconfig /all**합니다.
8. 출력에서 찾을 합니다 **IPv4 주소**, 한 다음 나중에 사용할 주소를 저장 합니다. 이 주소가 POC2에서 ping 할 됩니다. 예제 환경의 경우 주소가 **10.0.10.4**이지만 사용자의 환경에서는 주소가 다를 수 있습니다. 내에 포함 해야 합니다 **10.0.10.0/24** 이전에 만든 서브넷입니다.
9. 가상 머신을 ping에 응답할 수 있도록 방화벽 규칙을 만들려면 다음 PowerShell 명령을 실행 합니다.

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>POC2의 VM에서에서 테 넌 트에 로그인

1. POC2에 대 한 Azure Stack 물리적 컴퓨터에 로그인 한 다음 테 넌 트 계정을 사용 하 여 사용자 포털에 로그인 합니다.
2. 왼쪽된 탐색 모음에서 클릭 **계산**합니다.
3. 가상 컴퓨터의 목록에서 찾을 **VM02** 는 이전에 만든 하 고 선택 합니다.
4. 가상 머신 블레이드에서 **연결**을 클릭합니다.
5. 가상 머신을 만들 때 구성 된 계정으로 로그인 합니다.
6. 관리자 권한 엽니다 **Windows PowerShell** 창입니다.
7. 입력 **ipconfig /all**합니다.
8. 내에 포함 되는 IPv4 주소가 표시 됩니다 **10.0.20.0/24**합니다. 주소는 예제 환경의 **10.0.20.4**, 하지만 주소는 다를 수 있습니다.
9. 가상 머신을 ping에 응답할 수 있도록 방화벽 규칙을 만들려면 다음 PowerShell 명령을 실행 합니다.

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. POC2의 가상 머신에서 터널을 통해 POC1에서 가상 컴퓨터를 ping 합니다. 이렇게 하려면 VM01에서 기록해둔 DIP를 ping 합니다. 예제 환경에서 이것이 **10.0.10.4**, 랩에서 기록해둔 주소를 ping 해야 합니다. 다음 예제와 같이 결과 표시 됩니다.

    ![성공적인 ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. 원격 가상 컴퓨터의 응답을 테스트에 실패를 나타냅니다. 가상 컴퓨터 창을 닫을 수 있습니다. 연결을 테스트 하려면 다른 종류의 데이터 전송의 경우 예: 파일 복사를 시도할 수 있습니다.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>게이트웨이 연결을 통한 데이터 전송 통계 보기

이 정보는에서 사용할 수 있는 사이트 간 연결을 통해 데이터의 양을 전달 알고 싶다면 합니다 **연결** 블레이드입니다. 이 테스트 방금 보낸 ping이 VPN 연결을 통해 발생 실제로 확인 하는 다른 방법 이기도 합니다.

1. POC2의 테 넌 트 가상 컴퓨터에 로그인 하는 동안 테 넌 트 계정을 사용 하 여 사용자 포털에 로그인 합니다.
2. 로 이동 **모든 리소스**를 선택한 후 합니다 **POC2-POC1** 연결 합니다. **연결** 나타납니다.
3. 에 **연결** 창에서에 대 한 통계 **의 데이터를** 및 **데이터** 표시 합니다. 다음 스크린샷에서 많은 추가 파일 전송에 소요 되 합니다. 일부 0이 아닌 값이 표시 됩니다.

    ![들어오는/나가는 데이터](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
