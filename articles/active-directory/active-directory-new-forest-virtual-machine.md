---
title: "Azure 가상 네트워크에 Active Directory 포리스트 설치 | Microsoft Docs"
description: "Azure 가상 네트워크의 VM(가상 컴퓨터)에서 새 Active Directory 포리스트를 만드는 방법에 대해 설명하는 자습서입니다."
services: active-directory, virtual-network
keywords: "active directory 가상 컴퓨터, active directory 포리스트 설치, azure active directory 비디오  "
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
tags: 
ms.assetid: eb7170d0-266a-4caa-adce-1855589d65d1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 46e248112985b2e8f58f90e15cf885839d3cfcc8
ms.lasthandoff: 03/25/2017


---
# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Azure 가상 네트워크에 새 Active Directory 포리스트 설치
이 토픽에서는 Azure 가상 네트워크 또는 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)의 VM(가상 컴퓨터)에 새 Windows Server Active Directory 환경을 만드는 방법을 보여 줍니다. 이 경우 Azure 가상 네트워크는 온-프레미스 네트워크에 연결되지 않습니다.

다음 관련 토픽을 참조할 수도 있습니다.

* 이러한 단계를 보여 주는 동영상을 보려면 [Azure 가상 네트워크에 새 Active Directory 포리스트를 설치하는 방법](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* 필요한 경우 [사이트 간 VPN을 구성](../vpn-gateway/vpn-gateway-site-to-site-create.md) 한 다음 새 포리스트를 설치하거나 온-프레미스 포리스트를 Azure 가상 네트워크로 확장할 수 있습니다. 이러한 단계는 [Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치](active-directory-install-replica-active-directory-domain-controller.md)를 참조하세요.
* Azure 가상 네트워크에 AD DS(Active Directory 도메인 서비스)를 설치하는 방법에 대한 개념 지침은 [Azure 가상 컴퓨터에 Windows Server Active Directory 배포에 대한 지침](https://msdn.microsoft.com/library/azure/jj156090.aspx)을 참조하세요.

## <a name="scenario-diagram"></a>시나리오 다이어그램
이 시나리오에서는 외부 사용자가 도메인에 가입된 서버에서 실행되는 응용 프로그램에 액세스해야 합니다. 응용 프로그램 서버를 실행하는 VM과 도메인 컨트롤러를 실행하는 VM이 Azure 가상 네트워크에서 자신의 클라우드 서비스에 설치됩니다. 또한 내결함성 향상을 위해 가용성 집합 내에도 포함됩니다.

![Azure Virtual Network의 가상 컴퓨터에서 Active Directory 포리스트][1] 7

## <a name="how-does-this-differ-from-on-premises"></a>온-프레미스와의 차이점
Azure에 도메인 컨트롤러를 설치할 때와 온-프레미스에 설치할 때의 차이점은 크지 않습니다. 주요 차이점은 다음 표에 나와 있습니다.

| 구성 | 온-프레미스 | Azure 가상 네트워크 |
| --- | --- | --- |
| **도메인 컨트롤러의 IP 주소** |네트워크 어댑터 속성에 고정 IP 주소 할당 |Set-AzureStaticVNetIP cmdlet을 실행하여 고정 IP 주소 할당 |
| **DNS 클라이언트 확인 프로그램** |도메인 구성원의 네트워크 어댑터 속성에 기본 및 대체 DNS 서버 주소 설정 |가상 네트워크 속성에 DNS 서버 주소 설정 |
| **Active Directory 데이터베이스 저장소** |선택적으로 기본 저장소 위치를 C:\에서 변경 |기본 저장소 위치를 C:\에서 변경해야 함 |

## <a name="create-an-azure-virtual-network"></a>Azure 가상 네트워크 만들기
1. Azure 클래식 포털에 로그인합니다.
2. 가상 네트워크를 만듭니다. **네트워크** > **가상 네트워크 만들기**를 클릭합니다. 다음 표의 값을 사용하여 마법사를 완료합니다.

   | 마법사 페이지 | 값 지정 |
   | --- | --- |
   |  **가상 네트워크 세부 정보** |<p>이름: 가상 네트워크의 이름 입력</p><p>지역: 가장 가까운 지역 선택</p> |
   |  **DNS 및 VPN** |<p>DNS 서버는 비워 둠</p><p>VPN 옵션 선택 안 함</p> |
   |  **가상 네트워크 주소 공간** |<p>서브넷 이름: 서브넷의 이름 입력</p><p>시작 IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p> |

## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>도메인 컨트롤러 및 DNS 서버 역할을 실행할 VM 만들기
필요에 따라 다음 단계를 반복하여 DC 역할을 호스트할 VM을 만듭니다. 내결함성과 중복성을 제공하려면 가상 DC를 두 개 이상 배포해야 합니다. Azure 가상 네트워크에 구성된 것과 유사한 DC가 둘 이상 있는 경우(즉, 둘 다 GC이고, DNS 서버를 실행하며, FSMO 역할을 수행하지 않는 등) 이러한 DC를 실행하는 VM을 가용성 집합에 배치하여 내결함성을 개선합니다.

UI 대신 Windows PowerShell을 사용하여 VM을 만들려면 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성하기](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

1. 클래식 포털에서 **새로 만들기** > **계산** > **가상 컴퓨터** > **갤러리에서**를 클릭합니다. 다음 값을 사용하여 마법사를 완료합니다. 다른 값이 제안되거나 필요하지 않은 한 설정의 기본값을 적용합니다.

   | 마법사 페이지 | 값 지정 |
   | --- | --- |
   |  **이미지 선택** |Windows Server 2012 R2 Datacenter |
   |  **가상 컴퓨터 구성** |<p>가상 컴퓨터 이름: 단일 레이블 이름(예: AzureDC1)을 입력합니다.</p><p>새 사용자 이름: 사용자 이름을 입력합니다. 이 사용자는 VM에서 로컬 관리자 그룹의 구성원이 됩니다. VM에 처음 로그인하려면 이 이름이 필요합니다. Administrator라는 기본 제공 계정은 사용할 수 없습니다.</p><p>새 암호/확인: 암호를 입력합니다.</p> |
   |  **가상 컴퓨터 구성** |<p>클라우드 서비스: 첫 번째 VM에 대해 <b>새 클라우드 서비스 만들기</b>를 선택한 다음 DC 역할을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</p><p>클라우드 서비스 DNS 이름: 전역적으로 고유한 이름을 지정합니다.</p><p>하위 지역/선호도 그룹/가상 네트워크: 가상 네트워크(예: WestUSVNet)를 지정합니다.</p><p>저장소 계정: 첫 번째 VM에 대해 <b>자동으로 생성된 저장소 계정 사용</b>을 선택한 다음 DC 역할을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</p><p>가용성 집합: <b>가용성 집합 만들기</b>를 선택합니다.</p><p>가용성 집합 이름: 첫 번째 VM을 만들 때 가용성 집합의 이름을 입력한 다음 추가 VM을 만들 때는 동일한 이름을 선택합니다.</p> |
   |  **가상 컴퓨터 구성** |<p><b>VM 에이전트 설치</b> 및 필요한 다른 확장을 선택합니다.</p> |
2. DC 서버 역할을 실행할 각 VM에 디스크를 연결합니다. AD 데이터베이스, 로그 및 SYSVOL을 저장하려면 추가 디스크가 필요합니다. 디스크 크기(예: 10GB)를 지정하고 **호스트 캐시 기본 설정**을 **없음**으로 설정된 채로 둡니다. 단계를 보려면 [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.
3. VM에 처음 로그인한 후 **서버 관리자** > **파일 및 저장소 서비스**를 열어 NTFS를 사용하여 이 디스크에 볼륨을 만듭니다.
4. DC 역할을 실행할 VM에 대해 고정 IP 주소를 예약합니다. 고정 IP 주소를 예약하려면 Microsoft 웹 플랫폼 설치 관리자를 다운로드하고 [Azure PowerShell을 설치](/powershell/azureps-cmdlets-docs) 한 다음 Set-AzureStaticVNetIP cmdlet을 실행합니다. 예:

    `Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM`

고정 IP 주소 설정에 대한 자세한 내용은 [VM의 고정 내부 IP 주소 구성](../virtual-network/virtual-networks-reserved-private-ip.md)을 참조하세요.

## <a name="install-windows-server-active-directory"></a>Windows Server Active Directory 설치
온-프레미스와 동일한 루틴을 사용하여 [AD DS를 설치](https://technet.microsoft.com/library/jj574166.aspx) 합니다(즉, UI, 응답 파일 또는 Windows PowerShell을 사용할 수 있음). 새 포리스트를 설치하려면 관리자 자격 증명을 제공해야 합니다. Active Directory 데이터베이스, 로그 및 SYSVOL에 대한 위치를 지정하려면 기본 저장소 위치를 운영 체제 드라이브에서 VM에 연결한 추가 데이터 디스크로 변경합니다.

DC 설치가 완료된 후 VM에 다시 연결하고 DC에 로그온합니다. 도메인 자격 증명을 지정해야 합니다.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Azure 가상 네트워크에 대한 DNS 서버 다시 설정
1. 새 DC/DNS 서버에서 DNS 전달자 설정 다시 지정
   1. 서버 관리자에서 **도구** > **DNS**를 클릭합니다.
   2. **DNS 관리자**에서 DNS 서버 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
   3. **전달자** 탭에서 전달자의 IP 주소를 클릭한 후 **편집**을 클릭합니다.  IP 주소를 선택하고 **삭제**를 클릭합니다.
   4. **확인**을 클릭하여 편집기를 닫고 **확인**을 다시 클릭하여 DNS 서버 속성을 닫습니다.
2. 가상 네트워크에 대한 DNS 서버 설정 업데이트
   1. **가상 네트워크**를 클릭하고, 만든 가상 네트워크를 두 번 클릭한 후 **구성** > **DNS 서버**를 클릭하고, DC/DNS 서버 역할을 실행하는 VM 중 하나의 이름 및 DIP를 입력한 다음 **저장**을 클릭합니다.
   2. VM을 선택하고 **다시 시작** 을 클릭하여 새 DNS 서버의 IP 주소로 DNS 확인 프로그램 설정을 구성하도록 VM을 트리거합니다.

## <a name="create-vms-for-domain-members"></a>도메인 구성원에 대한 VM 만들기
1. 다음 단계를 반복하여 응용 프로그램 서버로 실행할 VM을 만듭니다. 다른 값이 제안되거나 필요하지 않은 한 설정의 기본값을 적용합니다.

   | 마법사 페이지 | 값 지정 |
   | --- | --- |
   |  **이미지 선택** |Windows Server 2012 R2 Datacenter |
   |  **가상 컴퓨터 구성** |<p>가상 컴퓨터 이름: 단일 레이블 이름(예: AppServer1)을 입력합니다.</p><p>새 사용자 이름: 사용자 이름을 입력합니다. 이 사용자는 VM에서 로컬 관리자 그룹의 구성원이 됩니다. VM에 처음 로그인하려면 이 이름이 필요합니다. Administrator라는 기본 제공 계정은 사용할 수 없습니다.</p><p>새 암호/확인: 암호를 입력합니다.</p> |
   |  **가상 컴퓨터 구성** |<p>클라우드 서비스: 첫 번째 VM에 대해 **새 클라우드 서비스 만들기**를 선택한 다음 응용 프로그램을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</p><p>클라우드 서비스 DNS 이름: 전역적으로 고유한 이름을 지정합니다.</p><p>하위 지역/선호도 그룹/가상 네트워크: 가상 네트워크(예: WestUSVNet)를 지정합니다.</p><p>저장소 계정: 첫 번째 VM에 대해 **자동으로 생성된 저장소 계정 사용**을 선택한 다음 응용 프로그램을 호스트할 추가 VM을 만들 때는 동일한 클라우드 서비스 이름을 선택합니다.</p><p>가용성 집합: **가용성 집합 만들기**를 선택합니다.</p><p>가용성 집합 이름: 첫 번째 VM을 만들 때 가용성 집합의 이름을 입력한 다음 추가 VM을 만들 때는 동일한 이름을 선택합니다.</p> |
   |  **가상 컴퓨터 구성** |<p><b>VM 에이전트 설치</b> 및 필요한 다른 확장을 선택합니다.</p> |
2. 각 VM이 프로비전된 후 로그인하고 VM을 도메인에 가입합니다. **서버 관리자**에서 **로컬 서버** > **작업 그룹** > **변경...**을 클릭한 다음 **도메인**을 선택하고 온-프레미스 도메인의 이름을 입력합니다. 도메인 사용자의 자격 증명을 제공한 다음 VM을 다시 시작하여 도메인 가입을 완료합니다.

UI 대신 Windows PowerShell을 사용하여 VM을 만들려면 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성하기](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

Windows PowerShell 사용에 대한 자세한 내용은 [Azure Cmdlets 시작하기](https://msdn.microsoft.com/library/azure/jj554332.aspx) 및 [Azure Cmdlet 참조](https://msdn.microsoft.com/library/azure/jj554330.aspx)를 확인하세요.

## <a name="see-also"></a>참고 항목
* [Azure 가상 네트워크에 새 Active Directory 포리스트를 설치하는 방법](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* [Azure 가상 컴퓨터에 Windows Server Active Directory를 배포하기 위한 지침](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [사이트 간 VPN 구성](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Azure 가상 네트워크에 복제 Active Directory 도메인 컨트롤러 설치](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IT Pro IaaS: (01) 가상 컴퓨터 기본 사항(영문)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) 가상 네트워크 및 프레미스 간 연결 만들기(영문)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)
* [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)
* [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
* [Azure Cmdlet 참조](https://msdn.microsoft.com/library/azure/jj554330.aspx)
* [Azure VM 고정 IP 주소 설정](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
* [Azure VM에 고정 IP를 할당하는 방법](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
* [새 Active Directory 포리스트 설치](https://technet.microsoft.com/library/jj574166.aspx)
* [AD DS(Active Directory 도메인 서비스) 가상화(수준 100) 소개](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png

