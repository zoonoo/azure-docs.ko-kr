---
title: "Azure 가상 컴퓨터에 온-프레미스 Active Directory 도메인용 복제본 도메인 컨트롤러 설치 | Microsoft Docs"
description: "Azure 가상 네트워크의 Azure VM(가상 컴퓨터)에 온-프레미스 Active Directory 도메인용 복제본 DC를 설치하는 방법입니다."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7624d588e958985a73c5b40e8010e18e8879cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치
이 문서에서는 Azure 가상 네트워크의 Azure VM(가상 컴퓨터)에 온-프레미스 Active Directory 도메인의 복제본 DC로 사용할 추가 DC(도메인 컨트롤러)를 설치하는 방법을 설명합니다. [Azure 가상 네트워크에 Windows Server Active Directory 포리스트를 설치](active-directory-new-forest-virtual-machine.md)할 수도 있습니다. Azure 가상 네트워크에 AD DS(Active Directory Domain Services)를 설치하는 방법은 [Azure Virtual Machines에 Windows Server Active Directory 배포에 대한 지침](https://msdn.microsoft.com/library/azure/jj156090.aspx)을 참조하세요.

## <a name="scenario-diagram"></a>시나리오 다이어그램
이 시나리오에서는 외부 사용자가 도메인에 가입된 서버에서 실행되는 응용 프로그램에 액세스해야 합니다. 응용 프로그램 서버 및 복제본 DC를 실행하는 VM은 Azure 가상 네트워크에 설치됩니다. 가상 네트워크는 [ExpressRoute](../expressroute/expressroute-locations-providers.md)를 통해 온-프레미스 네트워크에 연결하거나 다음과 같이 [사이트 간 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) 연결을 사용할 수 있습니다. 

![Azure VNet에서 복제 Active Directory 도메인 컨트롤러의 다이어그램][1]

응용 프로그램 서버 및 DC는 계산 처리 분산을 위해 별개의 클라우드 서비스에 배포되고 내결함성 향상을 위해 가용성 집합 내에 배포됩니다.
DC는 Active Directory 복제를 사용하여 온-프레미스 DC 및 서로 간에 복제됩니다. 동기화 도구가 필요하지 않습니다.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Azure 가상 네트워크에 대한 온-프레미스 Active Directory 사이트 만들기
가상 네트워크에 해당하는 네트워크 지역을 나타내는 사이트를 Active Directory에 만들 수 있습니다. 이 사이트는 인증, 복제 및 기타 DC 위치 작업을 최적화하는 데 도움이 될 수 있습니다. 다음 단계에서는 사이트를 만드는 방법을 설명합니다. 자세한 배경 정보는 [새 사이트 추가](https://technet.microsoft.com/library/cc781496.aspx)를 참조하세요.

1. Active Directory 사이트 및 서비스를 엽니다(**서버 관리자** > **도구** > **Active Directory 사이트 및 서비스**).
2. Azure 가상 네트워크를 만든 지역을 나타내는 사이트를 만듭니다(**사이트** > **작업** > **새 사이트**를 차례로 클릭하고, 새 사이트의 이름을 입력[예: Azure 미국 서부]한 후에 사이트 링크를 선택하고 **확인** 클릭).
3. 서브넷을 만들고 새 사이트와 연결합니다(**사이트**를 두 번 클릭하고 **서브넷** > **새 서브넷**을 마우스 오른쪽 단추로 클릭하고, 가상 네트워크의 IP 주소 범위[예: 시나리오 다이어그램의 10.1.0.0/16]를 입력한 다음에 새 Azure 사이트 선택하고 **확인** 클릭).

## <a name="create-an-azure-virtual-network"></a>Azure 가상 네트워크 만들기
Azure 가상 네트워크를 만들고 사이트 간 VPN을 설정하려면 [사이트 간 연결 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 문서에 포함된 단계를 수행합니다. 

가상 네트워크 게이트웨이를 구성하여 보안 사이트 간 VPN 연결을 만들 수도 있습니다. 새 가상 네트워크와 온-프레미스 VPN 장치 간에 사이트 간 VPN 연결을 만듭니다. 지침은 [Virtual Network 게이트웨이 구성](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md)을.참조하세요.

## <a name="create-azure-vms-for-the-dc-roles"></a>DC 역할에 대한 Azure VM 만들기
DC 역할을 호스트할 VM을 만들려면 필요에 따라 [Azure Portal을 사용하여 Windows 가상 컴퓨터 만들기](../virtual-machines/windows/quick-create-portal.md)의 단계를 반복합니다. 내결함성과 중복성을 제공하려면 가상 DC를 두 개 이상 배포하세요. Azure 가상 네트워크에 비슷하게 구성된 DC가 두 개 이상 있는 경우 내결함성을 향상시키기 위해 해당 DC를 실행하는 VM을 가용성 집합에 배치할 수 있습니다.

Azure Portal 대신 Windows PowerShell을 사용하여 VM을 만들려면 [Azure PowerShell을 사용하여 Windows 기반 Virtual Machines 만들기 및 미리 구성하기](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

DC 역할을 실행할 VM에 대해 고정 IP 주소를 예약합니다. 고정 IP 주소를 예약하려면 Microsoft 웹 플랫폼 설치 관리자를 다운로드하고 [Azure PowerShell을 설치](/powershell/azure/overview) 한 다음 Set-AzureStaticVNetIP cmdlet을 실행합니다. 예:

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
고정 IP 주소 설정에 대한 자세한 내용은 [VM의 고정 내부 IP 주소 구성](../virtual-network/virtual-networks-reserved-private-ip.md)을 참조하세요.

## <a name="install-ad-ds-on-azure-vms"></a>Azure VM에 AD DS 설치
VM에 로그인한 다음 사이트 간 VPN 또는 ExpressRoute 연결을 통해 온-프레미스 네트워크의 리소스에 연결되었는지 확인합니다. Azure VM에 AD DS를 설치합니다. 온-프레미스 네트워크에 추가 DC(UI, Windows PowerShell 또는 응답 파일)를 설치할 때 사용하는 것과 동일한 프로세스를 사용할 수 있습니다. AD DS를 설치할 때 AD 데이터베이스, 로그 및 SYSVOL의 위치에 대해 새 볼륨을 지정해야 합니다. AD DS 설치에 리프레셔가 필요한 경우 [Active Directory Domain Services 설치](https://technet.microsoft.com/library/hh472162.aspx)(수준 100) 또는 [기존 도메인에 복제본 Windows Server 2012 도메인 컨트롤러 설치(수준 200)](https://technet.microsoft.com/library/jj574134.aspx)를 참조하세요.

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Azure 가상 네트워크에 대한 DNS 서버 다시 구성
1. 가상 네트워크 이름 목록을 얻으려면 [Azure Portal](https://portal.azure.com)에서 *가상 네트워크*를 검색한 다음 **가상 네트워크**를 선택하여 목록을 확인합니다. 
2. 관리할 가상 네트워크를 연 다음 [가상 네트워크에 대한 DNS 서버 IP 주소를 다시 구성](../virtual-network/virtual-network-manage-network.md#dns-servers)하여 온-프레미스 DNS 서버의 IP 주소가 아닌 복제본 DC에 할당된 정적 IP 주소를 사용합니다.
3. 가상 네트워크의 모든 복제본 DC VM이 가상 네트워크의 DNS 서버를 사용하도록 구성되어 있는지 확인하려면 다음을 수행하십시오.
  1. **Virtual Machines**를 선택합니다.
  2. VM을 선택한 다음 **다시 시작**을 선택합니다. 
  3. VM이 다시 **실행**될 때까지 기다린 다음 로그인합니다.

## <a name="create-vms-for-application-servers"></a>응용 프로그램 서버에 대한 VM 만들기

응용프로그램 서버 역할을 호스트할 VM을 만들려면 필요에 따라 [Azure Portal을 사용하여 Windows 가상 컴퓨터 만들기](../virtual-machines/windows/quick-create-portal.md)의 단계를 반복합니다. Azure Portal 대신 Microsoft PowerShell을 사용하여 VM을 만들려면 [Azure PowerShell을 사용하여 Windows 기반 Virtual Machines 만들기 및 구성](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요. 다음 테이블에는 제안된 설정이 있습니다.

| 설정 | 값 |
| --- | --- |
|  **이미지 선택** | Windows Server 2012 R2 Datacenter |
|  **가상 컴퓨터 구성** |<p>가상 컴퓨터 이름: 단일 레이블 이름(예: AppServer1)을 입력합니다.</p><p>새 사용자 이름: 사용자 이름을 입력합니다. 이 사용자는 VM에서 로컬 관리자 그룹의 구성원이 됩니다. VM에 처음 로그인하려면 이 이름이 필요합니다. Administrator라는 기본 제공 계정은 사용할 수 없습니다.</p><p>새 암호/확인: 암호를 입력합니다.</p> |
|  **가상 컴퓨터 구성** |<p>클라우드 서비스: 첫 번째 VM에 대해 **새 클라우드 서비스 만들기**를 선택한 다음 응용 프로그램을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</p><p>클라우드 서비스 DNS 이름: 전역적으로 고유한 이름을 지정합니다.</p><p>하위 지역/선호도 그룹/Virtual Network: 가상 네트워크(예: WestUSVNet)를 지정합니다.</p><p>Storage 계정: 첫 번째 VM에 대해 **자동으로 생성된 Storage 계정 사용**을 선택한 다음 응용 프로그램을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</p><p>가용성 집합: **가용성 집합 만들기**를 선택합니다.</p><p>가용성 집합 이름: 첫 번째 VM을 만들 때 가용성 집합의 이름을 입력한 다음 추가 VM을 만들 때는 동일한 이름을 선택합니다.</p> |
|  **가상 컴퓨터 구성** |<p><b>VM 에이전트 설치</b> 및 필요한 다른 확장을 선택합니다.</p> |
  
각 VM이 프로비전된 후 로그인하고 VM을 도메인에 가입합니다. 
1. **서버 관리자** &gt; **로컬 서버** &gt; **작업 그룹** &gt; **변경…**에서 **도메인**을 선택합니다.
2. 온-프레미스 도메인의 이름을 입력합니다. 
3. 도메인 사용자의 자격 증명을 제공합니다.
4. VM을 다시 시작합니다.

## <a name="additional-resources"></a>추가 리소스

* Windows PowerShell 사용에 대한 자세한 내용은 [Azure Cmdlets 시작하기](/powershell/azure/overview) 및 [Azure Cmdlet 참조](/powershell/azure/get-started-azureps)를 확인하세요.
* [Azure Virtual Machines에 Windows Server Active Directory를 배포하기 위한 지침](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Azure PowerShell을 사용하여 기존 온-프레미스 Hyper-V 도메인 컨트롤러를 Azure에 업로드하는 방법](http://support.microsoft.com/kb/2904015)
* [Azure 가상 네트워크에 새 Active Directory 포리스트 설치](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IT Pro IaaS: (01) 가상 컴퓨터 기본 사항(영문)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) Virtual Network 및 프레미스 간 연결 만들기(영문)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure 관리 Cmdlet](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
