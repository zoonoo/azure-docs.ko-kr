---
title: Azure Virtual Machines에 Windows Server Active Directory를 배포하기 위한 지침 | Microsoft Docs
description: 온-프레미스에 AD 도메인 서비스 및 AD 페더레이션 서비스를 배포하는 방법을 아는 경우 Azure 가상 머신에서 작동하는 방법을 학습합니다.
services: active-directory
documentationcenter: ''
author: femila
manager: mtillman
editor: ''
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2018
ms.author: femila
ms.openlocfilehash: c2d58e056cdb285be51d259492e11e6ae37b253e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Azure 가상 머신에 Windows Server Active Directory를 배포하기 위한 지침
이 문서에서는 Windows Server AD DS(Active Directory Domain Services) 및 AD FS(Active Directory Federation Services) 온-프레미스 배포와 Microsoft Azure 가상 머신에 배포 간의 중요한 차이점을 설명합니다.

## <a name="scope-and-audience"></a>범위 및 대상
이 문서는 Active Directory 온-프레미스 배포에 경험이 있는 사용자를 위한 것입니다. Microsoft Azure 가상 머신/Azure 가상 네트워크에 Active Directory 배포와 기존 온-프레미스 Active Directory 배포 간의 차이점을 설명합니다. Azure 가상 머신과 Azure 가상 네트워크는 클라우드에서 컴퓨팅 리소스를 활용하도록 조직에 제공하는 IaaS(Infrastructure-as-a-Service)의 일부입니다.

AD 배포에 익숙하지 않은 사용자는 [AD DS 배포 가이드](https://technet.microsoft.com/library/cc753963) 또는 [AD FS 배포 계획](https://technet.microsoft.com/library/dn151324.aspx)을 적절하게 참조하세요.

이 문서에서는 독자가 다음 개념을 알고 있다고 가정합니다.

* Windows Server AD DS 배포 및 관리
* Windows Server AD DS 인프라를 지원하도록 DNS 배포 및 구성
* Windows Server AD FS 배포 및 관리
* Windows Server AD FS 토큰을 사용할 수 있는 신뢰 당사자 응용 프로그램(웹 사이트 및 웹 서비스) 배포, 구성 및 관리
* 가상 머신, 가상 디스크 및 가상 네트워크를 구성하는 방법과 같은 일반적인 가상 머신 개념

이 문서는 Windows Server AD DS 또는 AD FS가 온-프레미스로 부분적으로 배포되며 Azure 가상 머신에 부분적으로 배포되는 하이브리드 배포 시나리오에 대한 요구 사항을 강조 표시합니다. 문서는 먼저 Azure 가상 머신과 온-프레미스에서 Windows Server AD DS 및 AD FS 실행 간의 중요한 차이점과 디자인 및 배포에 영향을 주는 중요한 결정 사항을 다룹니다. 문서의 나머지 부분에서는 각각의 의사 결정 지점에 대한 구체적 지침 및 다양한 배포 시나리오에 지침을 적용하는 방법을 설명합니다.

이 문서는 클라우드 응용 프로그램에 대한 ID 관리 및 액세스 제어 기능을 제공하는 REST 기반 서비스인 [Azure Active Directory](http://azure.microsoft.com/services/active-directory/)의 구성을 설명하지 않습니다. 하지만 Azure AD(Azure Active Directory) 및 Windows Server AD DS는 오늘날의 하이브리드 IT 환경 및 최신 응용 프로그램에 대한 ID 및 액세스 관리 솔루션을 제공하기 위해 함께 작동하도록 설계되었습니다. Windows Server AD DS와 Azure AD 간의 차이점 및 관계를 이해하려면 다음을 고려하세요.

1. Azure를 사용하여 온-프레미스 데이터 센터를 클라우드로 확장하는 경우 Azure 가상 머신의 클라우드에서 Windows Server AD DS를 실행할 수 있습니다.
2. Azure AD를 사용하여 사용자가 SaaS(Software-as-a-Service) 응용 프로그램에서 Single Sign-On을 사용하도록 제공할 수 있습니다. 이 기술은 Microsoft Office 365에서 사용되며, Azure 또는 기타 클라우드 플랫폼에서 실행하는 응용 프로그램에서도 이 기술을 사용할 수 있습니다.
3. Azure AD(해당 Access Control Service)를 사용하여 Facebook, Google, Microsoft 및 다른 ID 공급자에게서 받은 ID를 사용하여 사용자가 클라우드 또는 온-프레미스에 호스팅된 응용 프로그램에 로그인할 수 있게 할 수 있습니다.

이러한 차이점에 대한 자세한 내용은 [Azure ID](fundamentals-identity.md)를 참조하세요.

## <a name="related-resources"></a>관련 리소스
[Azure Virtual Machine 준비 평가](https://www.microsoft.com/download/details.aspx?id=40898)를 다운로드하고 실행할 수 있습니다. 평가는 환경을 Azure로 마이그레이션할 수 있도록 이 항목의 지침에 따라 자동으로 온-프레미스 환경을 검사하고 사용자 지정된 보고서를 생성합니다.

먼저 다음 항목을 설명하는 자습서, 가이드 및 비디오를 검토하는 것이 좋습니다.

* [Azure Portal에서 클라우드 전용 Virtual Network 구성](../virtual-network/quick-create-portal.md)
* [Azure Portal에서 사이트 간 VPN 구성](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Azure 가상 네트워크에 새 Active Directory 포리스트 설치](active-directory-new-forest-virtual-machine.md)
* [Azure에서 복제 Active Directory 도메인 컨트롤러 설치](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IT Pro IaaS: (01) Virtual Machine 기본 사항](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) Virtual Network 및 프레미스 간 연결 만들기(영문)](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>소개
Azure 가상 머신에서 Windows Server Active Directory를 배포하기 위한 기본 요구 사항은 온-프레미스 가상 머신(및 어느 정도까지는 물리적 컴퓨터)에서 배포하는 경우와 아주 조금 다릅니다. 예를 들어 Windows Server AD DS의 경우 Azure 가상 머신에 배포하는 DC(도메인 컨트롤러)는 기존 온-프레미스 회사 도메인/포리스트에서 복제본이며 Azure 배포는 다른 모든 추가 Windows Server Active Directory 사이트를 처리하는 것과 대부분 동일한 방식으로 처리될 수 있습니다. 즉, 서브넷은 만들어진 사이트, 해당 사이트에 연결된 서브넷, Windows Server AD DS에서 정의되어야 하며 적절한 사이트 링크를 사용하여 다른 사이트에 연결되어야 합니다. 그러나 모든 Azure 배포에 공통적인 몇 가지 차이점과 특정 배포 시나리오에 따라 다른 일부가 있습니다. 두 가지 근본적인 차이점은 다음과 같습니다.

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Azure 가상 머신을 온-프레미스 회사 네트워크에 연결해야 합니다.
Azure 가상 머신을 온-프레미스 회사 네트워크에 다시 연결하려면 Azure 가상 머신 및 온-프레미스 컴퓨터를 원활하게 연결할 수 있는 사이트 간 또는 사이트와 지점 간 VPN(가상 사설망) 구성 요소를 포함하는 Azure 가상 네트워크가 필요합니다. 이 VPN 구성 요소를 통해 온-프레미스 도메인 구성원 컴퓨터는 해당 도메인 컨트롤러가 Azure 가상 머신에 독점적으로 호스팅되는 Windows Server Active Directory 도메인에 액세스할 수도 있습니다. 한편 VPN에 실패하면 Windows Server Active Directory에 의존하는 인증 및 다른 작업도 또한 실패하는 것을 고려해야 합니다. 사용자는 기존 캐시된 자격 증명을 사용하여 로그인할 수 있는 반면 티켓이 발생되어야 하거나 유효하지 않게 된 모든 피어-투-피어 또는 클라이언트-서버 간 인증 시도는 실패합니다.

[Azure Portal에서 사이트 간 VPN 구성](../vpn-gateway/vpn-gateway-site-to-site-create.md)을 포함하는 단계별 자습서의 데모 비디오 및 목록은 [Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/)를 참조하세요.

> [!NOTE]
> 또한 온-프레미스 네트워크와 연결되지 않은 Azure 가상 네트워크에 Windows Server Active Directory를 배포할 수도 있습니다. 그러나 이 항목의 지침은 Azure 가상 네트워크가 Windows Server에 필수적인 IP 주소 지정 기능을 제공하기 때문에 사용된다고 가정합니다.
> 
> 

### <a name="static-ip-addresses-can-be-configured-with-azure-powershell"></a>고정 IP 주소는 Azure PowerShell을 통해 구성할 수 있습니다.
기본적으로 동적 주소가 할당되지만, 고정 IP 주소를 할당하려면 Set-AzureStaticVNetIP cmdlet을 사용하면 됩니다. 이 cmdlet은 서비스 복구 및 VM 종료/다시 시작을 통해 유지되는 고정 IP 주소를 설정합니다. 자세한 내용은 [가상 머신에 대한 고정 내부 IP 주소](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)를 참조하세요. 아래와 같이 Azure Portal에서 VM을 만드는 동안 고정 IP 주소를 구성할 수도 있습니다. 자세한 내용은 [Azure Portal을 사용하여 고정 공용 IP 주소를 사용하는 VM 만들기](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md)를 참조하세요.

![VM을 만들 때 고정 IP 주소를 추가하는 단계의 스크린샷](media/active-directory-deploying-ws-ad-guidelines/static-ip.png)

## <a name="BKMK_Glossary"></a>용어 및 정의
다음은 이 문서에서 참조되는 다양한 Azure 기술에 대한 용어의 전체 목록이 아닙니다.

* **Azure 가상 머신**: 고객이 거의 모든 기존 온-프레미스 서버 작업을 실행하는 VM을 배포할 수 있도록 허용하는 Azure에서 제공되는 IaaS.
* **Azure 가상 네트워크**: 고객이 VPN(가상 사설망)을 사용하여 Azure에서 가상 네트워크를 만들고 관리하고 온-프레미스 네트워킹 인프라에 안전하게 연결할 수 있도록 하는 Azure의 네트워킹 서비스.
* **가상 IP 주소**: 특정 컴퓨터 또는 네트워크 인터페이스 카드에 바인딩되지 않은 인터넷 연결 IP 주소. 클라우드 서비스는 Azure VM으로 리디렉션되는 네트워크 트래픽을 받기 위한 가상 IP 주소를 할당합니다. 가상 IP 주소는 하나 이상의 Azure 가상 머신을 포함할 수 있는 클라우드 서비스의 속성입니다. 또한 Azure 가상 네트워크는 하나 이상의 클라우드 서비스를 포함할 수 있습니다. 가상 IP 주소는 기본 부하 분산 기능을 제공합니다.
* **동적 IP 주소**: 내부용으로만 사용되는 IP 주소입니다. DC/DNS 서버 역할을 호스팅하는 VM에 대한 고정 IP 주소(Set-AzureStaticVNetIP cmdlet을 사용하여)로 구성되어야 합니다.
* **서비스 복구**: Azure에서 서비스가 실패했음을 감지한 후 서비스를 실행 상태로 자동으로 반환하는 프로세스. 서비스 복구는 가용성과 복원력을 지원하는 Azure의 측면 중 하나입니다. 가능성이 드물긴 하지만 VM에서 실행되는 DC에 대한 서비스 복구 인시던트에 따른 결과는 계획되지 않은 재부팅과 유사하지만 몇 가지 파생 효과가 있습니다.
  
  * VM의 가상 네트워크 어댑터가 변경됩니다.
  * 가상 네트워크 어댑터의 MAC 주소가 변경됩니다.
  * VM의 프로세서/CPU ID가 변경됩니다.
  * 가상 네트워크 어댑터의 IP 구성은 VM이 가상 네트워크에 연결되어 있고 VM의 IP 주소가 정적인 한 변경되지 않습니다.
  
  MAC 주소 또는 프로세서/CPU ID에 종속성이 없으므로 이러한 동작은 Windows Server Active Directory에 영향을 주지 않으며 Azure에서 모든 Windows Server Active Directory 배포는 위에서 설명한 대로 Azure 가상 네트워크에서 실행되는 것이 좋습니다.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Windows Server Active Directory 도메인 컨트롤러 가상화에 안전합니까?
Azure 가상 머신에 Windows Server Active Directory DC 배포는 가상 머신에서 온-프레미스로 DC를 실행하는 경우와 동일한 지침이 적용됩니다. DC 백업 및 복원을 위한 지침을 따르는 한 가상화된 DC 실행은 안전한 사례입니다. 가상화된 DC를 실행하기 위한 제약 조건 및 지침에 대한 자세한 내용은 [Hyper-V에서 도메인 컨트롤러 실행](https://technet.microsoft.com/library/dd363553)을 참조하세요.

하이퍼바이저는 Windows Server Active Directory를 비롯한 대부분의 분산된 시스템에 문제가 발생할 수 있는 기술을 제공 또는 하찮아 보이게 합니다. 예를 들어 물리적 서버에서 디스크를 복제하거나 지원되는 메서드를 사용하여 SAN 등의 사용을 비롯한 서버의 상태를 롤백할 수 있지만 물리적 서버에서 이렇게 하는 것은 하이퍼바이저에서 가상 머신 스냅숏을 복사하는 것보다 훨씬 어렵습니다. Azure는 동일한 바람직하지 않은 조건을 가져올 수 있는 기능을 제공합니다. 예를 들어 복원하면 스냅숏 복원 기능을 사용하는 경우 비슷한 상황이 발생할 수 있으므로 정기적인 백업을 수행하는 대신 DC의 VHD 파일을 복사하지 않아야 합니다.

이러한 롤백은 DC 간에 영구적으로 달라지는 상태를 일으킬 수 있는 USN 거품을 설명합니다. 다음과 같은 문제가 발생할 수 있습니다.

* 느린 개체
* 암호 불일치
* 특성 값 불일치
* 스키마 마스터가 롤백되는 경우 스키마 불일치

DC에 영향을 주는 방법에 대한 자세한 내용은 [USN 및 USN 롤백](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback)을 참조하세요.

Windows Server 2012부터는 [추가 세이프가드가 AD DS에 기본 제공됩니다](https://technet.microsoft.com/library/hh831734.aspx). 이러한 세이프가드 도움은 기본 하이퍼바이저 플랫폼이 VM-GenerationID를 지원하는 한 앞서 언급한 문제에 대해서 가상화된 도메인 컨트롤러를 보호합니다. Azure는 Azure 가상 머신에서 Windows Server 2012 이상을 실행하는 도메인 컨트롤러에 추가 세이프가드가 있음을 의미하는 VM-GenerationID를 지원합니다.

> [!NOTE]
> Azure Portal에서 **종료** 옵션을 사용하는 대신 게스트 운영 체제 내의 Azure에서 도메인 컨트롤러 역할을 실행하는 VM을 종료하고 다시 시작해야 합니다. 오늘날 포털을 사용하여 VM을 종료하면 VM 할당이 취소됩니다. 할당 취소된 VM은 비용이 발생하지 않는다는 장점이 있지만 DC에 바람직하지 않은 VM-GenerationID를 다시 설정합니다. VM-GenerationID를 다시 설정할 때 AD DS 데이터베이스의 invocationID 또한 다시 설정되며 RID 풀이 삭제되고 SYSVOL이 권한이 없는 것으로 표시됩니다. 자세한 내용은 [AD DS(Active Directory Domain Services) 가상화 소개](https://technet.microsoft.com/library/hh831734.aspx) 및 [안전하게 DFSR 가상화](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx)를 참조하세요.
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Azure Virtual Machines에 Windows Server AD DS를 배포하는 이유는 무엇입니까?
많은 Windows Server AD DS 배포 시나리오는 Azure에서 VM으로 배포하기에 적합합니다. 예를 들어 아시아의 원격 위치에서 사용자를 인증해야 하는 회사가 유럽에 있다고 가정합니다. 회사는 배포하는 비용 및 배포 후에 서버를 관리하는 데 제한된 전문 지식으로 인해 아시아에 Windows Server Active Directory DC를 이전에 배포하지 않았습니다. 결과적으로, 아시아의 인증 요청은 최적이 아닌 결과로 유럽의 DC에서 지원됩니다. 이 경우 아시아의 Azure 데이터 센터 내에서 실행되어야 한다고 지정한 VM에 DC를 배포할 수 있습니다. 해당 DC를 원격 위치에 직접 연결된 Azure 가상 네트워크에 연결하면 인증 성능이 향상됩니다.

Azure는 비용이 드는 재해 복구(DR) 사이트에 대한 대체로 적합합니다. Azure에서 적은 수의 도메인 컨트롤러 및 단일 가상 네트워크 호스팅의 상대적으로 낮은 비용은 매력적인 대안을 나타냅니다.

마지막으로 Azure에서 SharePoint와 같은 Windows Server Active Directory가 필요하지만 온-프레미스 네트워크 또는 회사 Windows Server Active Directory에 의존하지 않는 네트워크 응용 프로그램을 배포할 수도 있습니다. 이 경우 SharePoint 서버의 요구 사항을 충족하기 위해 Azure에서 분리된 포리스트를 배포하는 것이 최적입니다. 다시 온-프레미스 네트워크와 회사 Active Directory에 대한 연결이 필요한 네트워크 응용 프로그램 배포도 지원됩니다.

> [!NOTE]
> 계층-3 연결을 제공하므로 Azure 가상 네트워크와 온-프레미스 네트워크 간의 연결을 제공하는 VPN 구성 요소는 온-프레미스를 실행하는 구성원 서버가 Azure 가상 네트워크에서 Azure 가상 머신으로 실행하는 DC를 활용할 수 있도록 할 수도 있습니다. 그러나 VPN을 사용할 수 없는 경우 온-프레미스 컴퓨터와 Azure 기반 도메인 컨트롤러 간의 통신은 작동하지 않으며 인증 및 다양한 다른 오류가 발생합니다.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Azure Virtual Machines와 온-프레미스에 Windows Server Active Directory 도메인 컨트롤러 배포 간의 비교
* 단일 VM을 두 개 이상 포함하는 모든 Windows Server Active Directory 배포 시나리오의 경우 IP 주소 일관성을 위해 Azure 가상 네트워크를 사용해야 합니다. 이 가이드에서는 DC가 Azure 가상 네트워크에서 실행 중임을 가정합니다.
* 온-프레미스 DC와 마찬가지로 고정 IP 주소를 사용하는 것이 좋습니다. 고정 IP 주소는 Azure PowerShell을 사용하여 구성할 수 있습니다. 자세한 내용은 [VM에 대한 고정 내부 IP 주소](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) 를 참조하세요. 모니터링 시스템 또는 게스트 운영 체제 내에서 고정 IP 주소 구성을 확인하는 다른 솔루션이 있는 경우 VM의 네트워크 어댑터 속성에 동일한 고정 IP 주소를 할당할 수 있습니다. 하지만 VM이 서비스 복구를 받거나 포털에서 종료되고 해당 주소가 할당 취소되는 경우 네트워크 어댑터가 삭제됩니다. 이 경우 게스트 내의 고정 IP 주소를 다시 설정해야 합니다.
* 가상 네트워크에 VM 배포는 온-프레미스 네트워크에 다시 연결하는 것을 의미(요구)하지 않습니다. 가상 네트워크는 단순히 해당 가능성을 활성화합니다. Azure와 온-프레미스 네트워크 간의 개인 통신에 대한 가상 네트워크를 만들어야 합니다. 온-프레미스 네트워크에 VPN 끝점을 배포해야 합니다. VPN은 Azure에서 온-프레미스 네트워크로 열립니다. 자세한 내용은 [Virtual Network 개요](../virtual-network/virtual-networks-overview.md) 및 [Azure Portal에서 사이트 간 VPN 구성](../vpn-gateway/vpn-gateway-site-to-site-create.md)을 참조하세요.

> [!NOTE]
> [지점 및 사이트 간 VPN 만들기](../vpn-gateway/vpn-gateway-point-to-site-create.md) 에 대한 옵션은 개별 Windows 기반 컴퓨터를 Azure 가상 네트워크에 직접 연결할 수 있습니다.
> 
> 

* 가상 네트워크를 만드는지 여부에 관계 없이 Azure는 송신 트래픽에 대한 요금을 청구하지만 하지만 수신 트래픽에 대한 요금을 청구하지 않습니다. 다양한 Windows Server Active Directory 디자인 선택은 배포에서 생성되는 송신 트래픽의 양에 영향을 줄 수 있습니다. 예를 들어 읽기 전용 도메인 컨트롤러(RODC) 배포는 아웃바운드를 복제하지 않으므로 송신 트래픽을 제한합니다. 그러나 RODC를 배포하는 결정은 DC 및 사이트의 응용 프로그램 및 서비스가 RODC와 함께 가진 [호환성](https://technet.microsoft.com/library/cc755190) 에 대해 쓰기 작업을 수행할 필요성에 대한 고려가 필요합니다. 트래픽 요금에 대한 자세한 내용은 [Azure 가격 개요](http://azure.microsoft.com/pricing/)를 참조하세요.
* Azure에서 RAM, 디스크 크기 등과 같은 VM 온-프레미스에 사용할 서버 리소스를 완전히 제어할 수 있지만 미리 구성된 서버 크기의 목록에서 선택해야 합니다. DC의 경우 Windows Server Active Directory 데이터베이스를 저장하기 위해 운영 체제 디스크 외에도 데이터 디스크가 필요합니다.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Azure 가상 머신에 Windows Server AD FS를 배포할 수 있습니까?
예, Azure 가상 머신에 Windows Server AD FS를 배포할 수 있으며 온-프레미스에서 [AD FS 배포에 대한 모범 사례](https://technet.microsoft.com/library/dn151324.aspx) 는 Azure에서 AD FS 배포에 동일하게 적용합니다. 하지만 부하 분산 및 고가용성 기능과 같은 몇 가지 모범 사례는 AD FS가 제공하는 이상의 기술이 필요합니다. 기본 인프라에서 제공되어야 합니다. 몇 가지 모범 사례를 검토하고 Azure VM 및 Azure 가상 네트워크를 사용하여 실현할 수 있는 방법을 살펴 보겠습니다.

1. **STS(보안 토큰 서비스) 서버를 인터넷에 직접 노출하지 마세요.**
   
    STS는 보안 토큰을 발급하기 때문에 이것은 중요합니다. 결과적으로 AD FS 서버와 같은 STS 서버는 도메인 컨트롤러로 동일한 수준의 보호로 처리되어야 합니다. STS가 손상되면 악의적인 사용자가 신뢰 당사자 응용 프로그램과 신뢰 조직의 다른 STS 서버를 선택할 권리를 잠재적으로 포함하는 액세스 토큰을 발급할 수 있습니다.
2. **AD FS 서버와 동일한 네트워크의 모든 사용자 도메인에 대한 Active Directory 도메인 컨트롤러를 배포합니다.**
   
    AD FS 서버는 Active Directory Domain Services를 사용하여 사용자를 인증합니다. AD FS 서버와 동일한 네트워크에서 도메인 컨트롤러를 배포하는 것이 좋습니다. Azure 네트워크와 온-프레미스 네트워크 간의 링크가 중단된 경우 비즈니스 연속성을 제공하고 로그인에 대한 낮은 대기 시간 및 향상된 성능을 가능하게 합니다.
3. **고가용성 및 부하 분산에 대한 여러 AD FS 노드를 배포합니다.**
   
    대부분의 경우에서 보안 토큰이 필요한 응용 프로그램이 종종 중요 업무용이기 때문에 AD FS가 활성화하는 응용 프로그램의 오류는 허용할 수 없습니다. 결과적으로 AD FS는 이제 중요 업무용 응용 프로그램에 액세스하는 요주의 경로에 있기 때문에 AD FS 서비스는 여러 AD FS 프록시 및 AD FS 서버를 통해 항상 사용 가능해야 합니다. 요청의 배포를 위해 부하 분산 장치는 일반적으로 AD FS 프록시 및 AD FS 서버 앞에 배포됩니다.
4. **인터넷 액세스에 대한 하나 이상의 웹 응용 프로그램 프록시 노드를 배포합니다.**
   
    사용자가 AD FS 서비스에 의해 보호되는 응용 프로그램에 액세스해야 하는 경우 AD FS 서비스는 인터넷을 통해 사용할 수 있어야 합니다. 이는 웹 응용 프로그램 프록시 서비스를 배포하여 수행됩니다. 고가용성 및 부하 분산을 위해 둘 이상의 노드를 배포하는 것이 좋습니다.
5. **웹 응용 프로그램 프록시 노드에서 내부 네트워크 리소스에 액세스를 제한합니다.**
   
    외부 사용자가 인터넷에서 AD FS에 액세스할 수 있도록 허용하려면 웹 응용 프로그램 프록시 노드(또는 Windows Server의 이전 버전에서 AD FS 프록시)를 배포해야 합니다. 웹 응용 프로그램 프록시 노드는 인터넷에 직접 노출됩니다. 도메인에 가입할 필요가 없으며 TCP 포트 443 및 80을 통해 AD FS 서버에 대한 액세스 권한만 있으면 됩니다. 다른 모든 컴퓨터(특히 도메인 컨트롤러)에 대한 해당 통신은 차단되는 것이 좋습니다.
   
    이는 일반적으로 DMZ를 통해 온-프레미스에서 수행됩니다. 방화벽은 작업의 허용 목록 모드를 사용하여 온-프레미스 네트워크에 대한 DMZ의 트래픽을 제한합니다.(즉, 지정된 IP 주소 및 지정된 포트를 통한 트래픽은 허용되고 다른 모든 트래픽은 차단됩니다.)

다음 다이어그램은 기존의 온-프레미스 AD FS 배포를 보여 줍니다.

![기존의 온-프레미스 Active Directory Federation Services 배포 다이어그램](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

그러나 Azure는 기본으로 완전한 기능의 방화벽 기능을 제공하지 않으므로 트래픽을 제한하는 데 다른 옵션을 사용해야 합니다. 다음 표는 각 옵션 및 해당 장점 및 단점을 보여 줍니다.

| 옵션 | 장점 | 단점 |
| --- | --- | --- |
| [Azure 네트워크 ACL](../virtual-network/virtual-networks-acl.md) |보다 저렴하고 간단한 초기 구성 |새 VM이 배포에 추가된 경우 필요한 추가 네트워크 ACL 구성 |
| [Barracuda NG 방화벽](https://www.barracuda.com/products/ngfirewall) |작업의 허용 목록 모드 및 네트워크 ACL 구성이 필요하지 않음 |초기 설치에 대한 증가된 비용 및 복잡성 |

이 경우 AD FS를 배포하는 대략적인 단계는 다음과 같습니다.

1. VPN 또는 [ExpressRoute](http://azure.microsoft.com/services/expressroute/)를 사용하여 크로스-프레미스 연결로 가상 네트워크를 만듭니다.
2. 가상 네트워크에 도메인 컨트롤러를 배포합니다. 이 단계는 선택 사항이지만 권장됩니다.
3. 가상 네트워크에 도메인에 가입된 AD FS 서버를 배포합니다.
4. AD FS 서버를 포함하고 가상 네트워크(동적 IP 주소) 내에서 새로운 개인 IP 주소를 사용하는 [내부 부하 분산 집합](http://azure.microsoft.com/blog/internal-load-balancing/) 을 만듭니다.
   
   1. DNS를 업데이트하여 내부 부하 분산 집합의 개인(동적) IP 주소를 가리키는 FQDN을 만듭니다.
5. 웹 응용 프로그램 프록시 노드에 대한 클라우드 서비스(또는 별도 가상 네트워크)를 만듭니다.
6. 클라우드 서비스 또는 가상 네트워크에 웹 응용 프로그램 프록시 노드를 배포합니다.
   
   1. 웹 응용 프로그램 프록시 노드를 포함하는 외부 부하 분산 집합을 만듭니다.
   2. 외부 DNS 이름(FQDN)을 업데이트하여 클라우드 서비스 공용 IP 주소(가상 IP 주소)를 가리킵니다.
   3. AD FS 프록시를 구성하여 AD FS 서버에 대한 내부 부하 분산 집합에 해당하는 FQDN을 사용합니다.
   4. 클레임 기반 웹 사이트를 업데이트하여 클레임 공급자에 대한 외부 FQDN을 사용합니다.
7. AD FS 가상 네트워크의 모든 컴퓨터에 웹 응용 프로그램 프록시 간의 액세스를 제한합니다.

트래픽을 제한하려면 Azure 내부 부하 분산 장치에 대한 부하 분산 집합은 TCP 포트 80 및 443에 대한 트래픽만을 위해 구성되어야 하고 부하 분산 집합의 내부 동적 IP 주소에 대한 다른 모든 트래픽은 삭제됩니다.

![TCP 443 + 80 허용으로 ADFS 네트워크 ACL의 다이어그램.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

AD FS 서버에 대한 트래픽은 다음 원본을 통해서만 허용됩니다.

* Azure 내부 부하 분산 장치.
* 온-프레미스 네트워크에서 관리자의 IP 주소.

> [!WARNING]
> 디자인은 웹 응용 프로그램 프록시 노드가 Azure 가상 네트워크 또는 온-프레미스 네트워크의 모든 위치에 있는 다른 모든 VM에 연결되지 못하도록 해야 합니다. Express 경로 연결용 온-프레미스 어플라이언스 또는 사이트 간 VPN 연결에 대한 VPN 장치에서 방화벽 규칙을 구성하여 수행할 수 있습니다.
> 
> 

이 옵션의 단점은 내부 부하 분산 장치, AD FS 서버 및 가상 네트워크에 추가하는 기타 서버를 포함하여 여러 장치에 대해 네트워크 ACL을 구성해야 한다는 점입니다. 트래픽을 제한하도록 네트워크 ACL을 구성하지 않고 장치가 배포에 추가된 경우 전체 배포는 위험에 노출될 수 있습니다. 웹 응용 프로그램 프록시 노드의 IP 주소를 변경하는 경우 네트워크 ACL을 다시 설정해야 합니다(즉, 프록시는 [정적 동적 IP 주소](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)를 사용하도록 구성되어야 함).

![네트워크 ACLS를 사용한 Azure의 ADFS.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

다른 옵션은 AD FS 프록시 서버와 AD FS 서버 간의 트래픽을 제어하는 [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) 어플라이언스를 사용하는 것입니다. 이 옵션은 보안 및 고가용성을 위해 모범 사례를 준수하며 Barracuda NG Firewall 어플라이언스는 방화벽 관리의 허용 목록 모드를 제공하고 Azure 가상 네트워크에 직접 설치할 수 있으므로 초기 설치 후 적은 관리가 필요합니다. 새 서버가 배포에 추가될 때마다 네트워크 ACL을 구성하지 않아도 됩니다. 하지만 이 옵션은 초기 배포의 복잡성과 비용이 있습니다.

이 경우 두 개의 가상 네트워크가 하나 대신 배포됩니다. 이를 VNet1 및 VNet2라고 합니다. VNet1은 프록시를 포함하고 VNet2는 STS와 회사 네트워크로의 네트워크 연결을 포함합니다. 따라서 VNet1은 차례로 VNet2와 회사 네트워크에서 물리적으로(비록 가상일지라도) 분리됩니다. VNet1은 TINA(전송 독립 네트워크 아키텍처)라는 특수 터널링 기술을 사용하여 VNet2에 연결됩니다. TINA 터널은 가상 네트워크 각각에서 하나의 Barracuda로 Barracuda NG 방화벽을 사용하여 가상 네트워크 각각에 연결됩니다.  고가용성을 위해 각 가상 네트워크에 하나는 활성으로 하나는 수동으로 두 개의 Barracudas를 배포하는 것이 좋습니다. Azure에서 기존 온-프레미스 DMZ의 작업을 모방할 수 있는 매우 효율적인 방화벽 기능을 제공합니다.

![방화벽을 사용한 Azure의 ADFS.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

자세한 내용은 [AD FS: 인터넷으로 클레임 인식 온-프레미스 프런트 엔드 응용 프로그램 확장](#BKMK_CloudOnlyFed)을 참조하세요.

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>목표가 Office 365 SSO만인 경우 AD FS 배포에 대한 대안
목표가 오직 Office 365에 대한 로그인을 활성화하는 것인 경우 AD FS를 함께 배포하는 다른 대안이 있습니다. 해당 경우 이 방법은 AD FS 또는 Azure가 필요하지 않으므로 단순히 암호 동기화 온-프레미스를 사용하여 DirSync를 배포하고 최소 배포 복잡성으로 동일한 최종 결과를 얻을 수 있습니다.

다음 표는 AD FS 배포로 및 AD FS 배포 없이 로그인 프로세스 작동 방법을 비교합니다.

| AD FS 및 DirSync를 사용하여 Office 365 Single Sign-On | DirSync + 암호 동기화를 사용하여 Office 365 동일한 로그온 |
| --- | --- |
| 1. 사용자는 회사 네트워크에 로그온하고 Windows Server Active Directory에 인증됩니다. |1. 사용자는 회사 네트워크에 로그온하고 Windows Server Active Directory에 인증됩니다. |
| 2. 사용자(@contoso.com)는 Office 365에 액세스하려고 시도합니다. |2. 사용자(@contoso.com)는 Office 365에 액세스하려고 시도합니다. |
| 3. Office 365는 Azure AD에 사용자를 리디렉션합니다. |3. Office 365는 Azure AD에 사용자를 리디렉션합니다. |
| 4. Azure AD는 사용자를 인증할 수 없으므로 AD FS 온-프레미스와 트러스트가 있음을 이해하고 사용자를 AD FS로 리디렉션합니다. |4. Azure AD는 Kerberos 티켓을 직접 수락할 수 없고 트러스트 관계가 없으므로 사용자가 자격 증명을 입력하도록 요청합니다. |
| 5. 사용자는 AD FS STS에 Kerberos 티켓을 보냅니다. |5. 사용자는 동일한 온-프레미스 암호를 입력하고 Azure AD는 DirSync에 의해 동기화된 사용자 이름 및 암호를 확인합니다. |
| 6. AD FS는 Kerberos 티켓을 필요한 토큰 형식/클레임으로 변환하고 사용자를 Azure AD로 리디렉션합니다. |6. Azure AD는 Office 365에 사용자를 리디렉션합니다. |
| 7. 사용자는 Azure AD에 인증됩니다(또 다른 변환이 발생). |7. 사용자는 Azure AD 토큰을 사용하여 Office 365 및 OWA에 로그인할 수 있습니다. |
| 8. Azure AD는 Office 365에 사용자를 리디렉션합니다. | |
| 9. 사용자는 Office 365에 자동으로 로그인됩니다. | |

암호 동기화 시나리오(AD FS 없음)로 DirSync를 사용하는 Office 365에서 Single Sign-On은 “동일한 로그온"으로 대체됩니다. 여기서 "동일한"은 사용자가 Office 365에 액세스할 때 동일한 온-프레미스 자격 증명을 다시 입력해야 함을 의미합니다. 이 데이터는 후속 프롬프트를 줄일 수 있도록 사용자의 브라우저에서 기억될 수 있습니다.

### <a name="additional-food-for-thought"></a>추가 고려 사항
* Azure 가상 머신에서 AD FS 프록시를 배포하는 경우 AD FS 서버에 대한 연결이 필요합니다. 온-프레미스인 경우 AD FS 서버와 통신하는 웹 응용 프로그램 프록시 노드를 허용하도록 가상 네트워크에서 제공하는 사이트 간 VPN 연결을 활용하는 것이 좋습니다.
* Azure 가상 머신에서 AD FS 서버를 배포하는 경우 Windows Server Active Directory 도메인 컨트롤러, 특성 저장소 및 구성 데이터베이스에 대한 연결이 필요하며 Azure 가상 네트워크와 온-프레미스 네트워크 간에 ExpressRoute 또는 사이트 간 VPN 연결도 필요할 수 있습니다.
* Azure 가상 머신(송신 트래픽)에서 모든 트래픽에 요금이 부과됩니다. 비용이 주요 요인인 경우 Azure에 AD FS 서버 온-프레미스를 활용하는 웹 응용 프로그램 프록시 노드를 배포하는 것이 좋습니다. Azure 가상 머신에 AD FS 서버를 배포하는 경우 온-프레미스 사용자를 인증하는 데 추가 비용이 발생합니다. 송신 트래픽은 ExpressRoute 또는 VPN 사이트 간 연결을 순회 중인지 여부에 관계 없이 비용을 발생합니다.
* AD FS 서버의 고가용성을 위해 Azure의 기본 서버 부하 분산 기능을 사용하려는 경우 부하 분산은 클라우드 서비스 내에서 가상 머신의 상태를 확인하는 데 사용되는 프로브를 제공합니다. Azure 가상 머신의 경우(웹 또는 작업자 역할이 아니라) 기본 프로브에 응답하는 에이전트는 Azure 가상 머신에 존재하지 않으므로 사용자 지정 프로브를 사용해야 합니다. 간단히 하기 위해 사용자 지정 TCP 프로브를 사용할 수 있습니다. 가상 머신 상태를 확인하도록 TCP 연결(TCP SYN ACK 세그먼트로 TCP SYN 세그먼트를 보내고 응답)이 성공적으로 설정되어야 합니다. 사용자 지정 프로브를 구성하여 가상 머신이 활성 상태로 수신 대기하는 TCP 포트를 사용할 수 있습니다.

> [!NOTE]
> 동일한 포트 집합을 인터넷(예: 포트 80 및 443)에 직접 노출해야 하는 컴퓨터는 동일한 클라우드 서비스를 공유할 수 없습니다. 따라서 응용 프로그램 및 Windows Server AD FS에 대한 포트 요구 사항 간의 잠재적인 겹침을 방지하기 위해 Windows Server AD FS 서버에 대한 전용 클라우드 서비스를 만드는 것이 좋습니다.
> 
> 

## <a name="deployment-scenarios"></a>배포 시나리오
다음 섹션에서는 반드시 고려해야 하는 중요한 고려 사항을 살펴보도록 일반적인 배포 시나리오를 설명합니다. 각 시나리오에는 고려할 결정 사항 및 요인에 대한 자세한 내용의 링크가 있습니다.

1. [AD DS: 회사 네트워크 연결에 대한 요구 사항 없이 AD DS 인식 응용 프로그램 배포](#BKMK_CloudOnly)
   
    예를 들어 인터넷 연결 SharePoint 서비스는 Azure 가상 머신에 배포됩니다. 응용 프로그램은 회사 네트워크 리소스에 의존하지 않습니다. 응용 프로그램은 Windows Server AD DS가 필요하지만 회사 Windows Server AD DS는 필요하지 않습니다.
2. [AD FS: 인터넷으로 클레임 인식 온-프레미스 프런트 엔드 응용 프로그램 확장](#BKMK_CloudOnlyFed)
   
    예를 들어 온-프레미스에 성공적으로 배포되고 회사 사용자가 사용하는 클레임 인식 응용 프로그램을 인터넷에서 액세스할 수 있게 해야 합니다. 응용 프로그램은 자체 회사 ID를 사용하여 비즈니스 파트너와 기존 회사 사용자가 인터넷을 통해 직접 액세스할 수 있어야 합니다.
3. [AD DS: 회사 네트워크에 연결해야 하는 Windows Server AD DS 인식 응용 프로그램 배포](#BKMK_HybridExt)
   
    예를 들어 Windows 통합 인증을 지원하고 구성 및 사용자 프로필 데이터에 대한 리포지토리로 Windows Server AD DS를 사용하는 LDAP 인식 응용 프로그램은 Azure 가상 머신에 배포됩니다. 응용 프로그램은 기존의 회사 Windows Server AD DS를 활용하고 Single Sign-On을 제공하는 것이 좋습니다. 응용 프로그램은 클레임 인식되지 않습니다.

### <a name="BKMK_CloudOnly"></a>1. AD DS: 회사 네트워크 연결에 대한 요구 사항 없이 AD DS 인식 응용 프로그램 배포
![클라우드 전용 AD DS 배포](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**그림 1**

#### <a name="description"></a>설명
SharePoint가 Azure 가상 머신에 배포되고 응용 프로그램은 회사 네트워크 리소스에 의존하지 않습니다. 응용 프로그램은 Windows Server AD DS가 필요하지만 회사 Windows Server AD DS는 필요하지 *않습니다* . 사용자는 응용 프로그램을 통해 Azure 가상 머신의 클라우드에서 호스팅되는 Windows Server AD DS 도메인으로 자체 프로비전되기 때문에 Kerberos 또는 페더레이션된 트러스트는 필요하지 않습니다.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>시나리오 고려 사항 및 시나리오에 대한 기술 영역 적용 방법
* [네트워크 토폴로지](#BKMK_NetworkTopology): 크로스-프레미스 연결(사이트 간 연결이라고도 함) 없이 Azure 가상 네트워크를 만듭니다.
* [DC 배포 구성](#BKMK_DeploymentConfig): 새로운 단일 도메인 Windows Server Active Directory 포리스트에 새 도메인 컨트롤러를 배포합니다. Windows DNS 서버와 함께 배포되어야 합니다.
* [Windows Server Active Directory 사이트 토폴로지](#BKMK_ADSiteTopology): 기본 Windows Server Active Directory 사이트를 사용합니다(모든 컴퓨터는 Default-First-Site-Name에 있음).
* [IP 주소 지정 및 DNS](#BKMK_IPAddressDNS):
  
  * Set-AzureStaticVNetIP Azure PowerShell cmdlet을 사용하여 DC에 대한 고정 IP 주소를 설정합니다.
  * Azure의 도메인 컨트롤러에 Windows Server DNS를 설치 및 구성합니다.
  * DC 및 DNS 서버 역할을 호스팅하는 VM의 이름 및 IP 주소로 가상 네트워크 속성을 구성합니다.
* [글로벌 카탈로그](#BKMK_GC): 포리스트의 첫 번째 DC는 글로벌 카탈로그 서버여야 합니다. 단일 도메인 포리스트에서 글로벌 카탈로그는 DC에서 추가 작업이 필요하지 않으므로 추가 DC는 GC로 구성되어야 합니다.
* [Windows Server AD DS 데이터베이스 및 SYSVOL의 배치](#BKMK_PlaceDB): Windows Server Active Directory 데이터베이스, 로그 및 SYSVOL을 저장하기 위해 Azure VM으로 실행되는 DC에 데이터 디스크를 추가합니다.
* [Backup 및 복원](#BKMK_BUR): 시스템 상태 백업을 저장할 위치를 결정합니다. 필요한 경우 백업을 저장하도록 DC VM에 다른 데이터 디스크를 추가합니다.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: 인터넷으로 클레임 인식 온-프레미스 프런트 엔드 응용 프로그램 확장
![크로스-프레미스 연결로 페더레이션](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**그림 2**

#### <a name="description"></a>설명
온-프레미스에 성공적으로 배포되고 회사 사용자가 사용하는 클레임 인식 응용 프로그램을 인터넷에서 직접 액세스할 수 있게 해야 합니다. 응용 프로그램은 데이터를 저장하는 SQL 데이터베이스에 웹 프런트 엔드 역할을 합니다. 응용 프로그램에서 사용하는 SQL Server도 회사 네트워크에 있습니다. 두 개의 Windows Server AD FS STS 및 부하 분산 장치는 회사 사용자에 게 액세스를 제공하도록 온-프레미스에 배포되었습니다. 이제 응용 프로그램은 자체 회사 ID를 사용하여 비즈니스 파트너와 기존 회사 사용자가 인터넷을 통해 추가로 직접 액세스할 수 있어야 합니다.

이 새로운 요구 사항의 배포 및 구성 요구를 단순화하고 충족시키기 위해 두 개의 추가 웹 프런트 엔드 및 두 개의 Windows Server AD FS 프록시 서버가 Azure 가상 머신에 설치되도록 결정되었습니다. 모든 4개의 VM은 인터넷에 직접 노출되고 Azure Virtual Network의 사이트 간 VPN 기능을 사용하여 온-프레미스 네트워크에 대한 연결이 제공됩니다.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>시나리오 고려 사항 및 시나리오에 대한 기술 영역 적용 방법
* [네트워크 토폴로지](#BKMK_NetworkTopology): Azure 가상 네트워크를 만들고 [크로스-프레미스 연결을 구성합니다](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > 각각의 Windows Server AD FS 인증서의 경우 Azure에서 실행되는 Windows Server AD FS 인스턴스에서 인증서 템플릿 및 생성된 인증서 내에 정의된 URL에 연결할 수 있는지 확인합니다. PKI 인프라의 일부에 대한 크로스-프레미스 연결이 필요할 수도 있습니다. 예를 들어 CRL의 끝점이 LDAP 기반이고 단독으로 온-프레미스에서 호스팅되는 경우 크로스-프레미스 연결이 필요합니다. 필요 없는 경우 인터넷을 통해 해당 CRL을 액세스할 수 있는 CA에서 발급한 인증서를 사용해야 할 수 있습니다.
  > 
  > 
* [클라우드 서비스 구성](#BKMK_CloudSvcConfig): 두 개의 부하 분산된 가상 IP 주소를 제공하기 위해 두 개의 클라우드 서비스가 있는지 확인합니다. 첫 번째 클라우드 서비스의 가상 IP 주소는 포트 80 및 443에서 두 개의 Windows Server AD FS 프록시 VM으로 전달됩니다. Windows Server AD FS 프록시 VM은 Windows Server AD FS STS를 향하는 온-프레미스 부하 분산 장치의 IP 주소를 가리키도록 구성됩니다. 두 번째 클라우드 서비스의 가상 IP 주소는 포트 80 및 443에서 웹 프런트 엔드를 다시 실행하는 두 VM으로 전달됩니다. 부하 분산 장치가 Windows Server AD FS 프록시와 웹 프런트 엔드 VM 작동에 대한 트래픽을 전송하도록 사용자 지정 프로브를 구성합니다.
* [페더레이션 서버 구성](#BKMK_FedSrvConfig): Windows Server AD FS를 STS(페더레이션 서버)로 구성하여 클라우드에서 만들어진 Windows Server Active Directory 포리스트에 대한 보안 토큰을 생성합니다. ID를 허용하려는 다른 파트너와 클레임 공급자 트러스트 관계를 설정하고 토큰을 생성하려는 다른 응용 프로그램과 신뢰 당사자 트러스트 관계를 구성합니다.
  
    대부분의 시나리오에서 Windows Server AD FS 프록시 서버는 보안용으로 인터넷 연결 용량에 배포되는 반면 해당 Windows Server AD FS 페더레이션 항목은 직접 인터넷 연결에서 격리됩니다. 배포 시나리오에 관계 없이 공개적으로 노출된 IP 주소와 두 개의 Windows Server AD FS STS 인스턴스 또는 프록시 인스턴스 간에 부하를 분산할 수 있는 포트를 제공하는 가상 IP 주소로 클라우드 서비스를 구성해야 합니다.
* [Windows Server AD FS 고가용성 구성](#BKMK_ADFSHighAvail): 장애 조치 및 부하 분산을 위해 최소 두 개 이상의 서버로 Windows Server AD FS 팜을 배포하는 것이 좋습니다. Windows Server AD FS 구성 데이터에 대해 WID(Windows 내부 데이터베이스) 사용을 고려할 수도 있으며 Azure의 내부 부하 분산 기능을 사용하여 들어오는 요청을 팜의 서버에 걸쳐 분산시킬 수 있습니다.

자세한 내용은 [AD DS 배포 가이드](https://technet.microsoft.com/library/cc753963)를 참조하세요.

### <a name="BKMK_HybridExt"></a>3. AD DS: 회사 네트워크에 연결해야 하는 Windows Server AD DS 인식 응용 프로그램 배포
![크로스-프레미스 AD DS 배포](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**그림 3**

#### <a name="description"></a>설명
LDAP 인식 응용 프로그램이 Azure 가상 머신에 배포됩니다. Windows 통합 인증을 지원하고 구성 및 사용자 프로필 데이터에 대한 리포지토리로 Windows Server AD DS를 사용합니다. 응용 프로그램에 대한 목표는 기존의 회사 Windows Server AD DS를 활용하고 Single Sign-On을 제공하는 것입니다. 응용 프로그램은 클레임 인식되지 않습니다. 또한 사용자는 인터넷에서 직접 응용 프로그램에 액세스해야 합니다. 성능 및 비용을 최적화하기 위해 Azure에서 응용 프로그램과 함께 회사 도메인의 일부인 두 개의 추가 도메인 컨트롤러를 배포하도록 결정했습니다.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>시나리오 고려 사항 및 시나리오에 대한 기술 영역 적용 방법
* [네트워크 토폴로지](#BKMK_NetworkTopology): [크로스-프레미스 연결](../vpn-gateway/vpn-gateway-site-to-site-create.md)로 Azure 가상 네트워크를 만듭니다.
* [설치 방법](#BKMK_InstallMethod): 회사 Windows Server Active Directory 도메인의 복제본 DC를 배포합니다. 복제본 DC의 경우 VM에 Windows Server AD DS를 설치할 수 있으며 필요에 따라 IFM(미디어에서 설치) 기능을 사용하여 설치하는 동안 새로운 DC에 복제해야 하는 데이터의 양을 줄일 수 있습니다. 자습서는 [Azure에서 복제 Active Directory 도메인 컨트롤러 설치](active-directory-install-replica-active-directory-domain-controller.md)를 참조하세요. IFM을 사용하는 경우라도 설치하는 동안 Windows Server AD DS를 복제하는 대신 가상 DC 온-프레미스를 빌드하고 전체 VHD(가상 하드 디스크)를 클라우드로 이동하는 것이 더욱 효율적일 수 있습니다. 보안을 위해 Azure로 복사된 후 온-프레미스 네트워크에서 VHD를 삭제하는 것이 좋습니다.
* [Windows Server Active Directory 사이트 토폴로지](#BKMK_ADSiteTopology): Active Directory 사이트 및 서비스에서 새로운 Azure 사이트를 만듭니다. Azure 가상 네트워크를 나타내는 Windows Server Active Directory 서브넷 개체를 만들고 사이트에 서브넷을 추가합니다. Azure 간 Windows Server Active Directory 트래픽을 제어하고 최적화하기 위해 새 Azure 사이트와 Azure 가상 네트워크 VPN 끝점이 위치한 사이트를 포함하는 새 사이트 링크를 만듭니다.
* [IP 주소 지정 및 DNS](#BKMK_IPAddressDNS):
  
  * Set-AzureStaticVNetIP Azure PowerShell cmdlet을 사용하여 DC에 대한 고정 IP 주소를 설정합니다.
  * Azure의 도메인 컨트롤러에 Windows Server DNS를 설치 및 구성합니다.
  * DC 및 DNS 서버 역할을 호스팅하는 VM의 이름 및 IP 주소로 가상 네트워크 속성을 구성합니다.
* [지리적으로 분산된 DC](#BKMK_DistributedDCs): 필요에 따라 추가 가상 네트워크를 구성합니다. Active Directory 사이트 토폴로지에서 다른 Azure 지역에 해당하는 지역에 DC가 필요한 경우 Active Directory 사이트를 적절하게 만들려고 합니다.
* [읽기 전용 DC](#BKMK_RODC): DC에 대한 쓰기 작업 수행을 위한 요구 사항 및 RODC를 사용한 사이트의 응용 프로그램 및 서비스의 호환성에 따라 Azure 사이트에서 RODC를 배포할 수 있습니다. 응용 프로그램 호환성에 대한 자세한 내용은 [읽기 전용 도메인 컨트롤러 응용 프로그램 호환성 가이드](https://technet.microsoft.com/library/cc755190)를 참조하세요.
* [글로벌 카탈로그](#BKMK_GC): GC는 다중 도메인 포리스트에서 로그온 요청을 처리하는 데 필요합니다. Azure 사이트에서 GC를 배포하지 않는 경우 인증 요청이 다른 사이트에서 쿼리 GC를 발생시키므로 송신 트래픽 비용이 발생합니다. 해당 트래픽을 최소화하기 위해 Active Directory 사이트 및 서비스에서 Azure 사이트에 대한 유니버설 그룹 멤버 자격 캐싱을 사용할 수 있습니다.
  
    GC를 배포하는 경우 Azure 사이트의 GC가 동일한 부분 도메인 파티션을 복제해야 하는 다른 GC에서 원본 DC로 선호되지 않도록 사이트 링크 및 사이트 링크 비용을 구성합니다.
* [Windows Server AD DS 데이터베이스 및 SYSVOL의 배치](#BKMK_PlaceDB): Windows Server Active Directory 데이터베이스, 로그 및 SYSVOL을 저장하기 위해 Azure VM에서 실행되는 DC에 데이터 디스크를 추가합니다.
* [Backup 및 복원](#BKMK_BUR): 시스템 상태 백업을 저장할 위치를 결정합니다. 필요한 경우 백업을 저장하도록 DC VM에 다른 데이터 디스크를 추가합니다.

## <a name="deployment-decisions-and-factors"></a>배포 결정 및 요인
이 표에서 아래의 자세한 내용에 대한 링크와 함께 이전 시나리오 및 고려해야 할 해당 결정에 영향을 받는 Windows Server Active Directory 기술 영역을 요약합니다. 일부 기술 영역은 모든 배포 시나리오에 적용되지 않을 수 있으며 일부 기술 영역은 다른 기술 영역보다 배포 시나리오에 더 중요할 수 있습니다.

예를 들어 가상 네트워크에서 복제본 DC를 배포하고 포리스트가 단일 도메인을 가진 경우 글로벌 카탈로그 서버를 배포하도록 선택하는 것은 추가 복제 요구 사항을 만들지 않으므로 해당 경우에서 배포 시나리오에 중요하지 않습니다. 반대로 포리스트에 여러 도메인이 있는 경우 가상 네트워크에 글로벌 카탈로그를 배포하는 결정은 사용 가능한 대역폭, 성능, 인증, 디렉터리 조회 등에 영향을 줄 수 있습니다.

| Windows Server Active Directory 기술 영역 | 의사 결정 | 요소 |
| --- | --- | --- |
| [네트워크 토폴로지](#BKMK_NetworkTopology) |가상 네트워크를 만듭니까? |<li>회사 리소스 액세스 요구 사항</li> <li>인증</li> <li>계정 관리</li> |
| [DC 배포 구성](#BKMK_DeploymentConfig) |<li>트러스트 없이 별도 포리스트를 배포합니까?</li> <li>페더레이션이 포함된 새 포리스트를 배포합니까?</li> <li>Windows Server Active Directory 포리스트 트러스트 또는 Kerberos가 포함된 새 포리스트를 배포합니까?</li> <li>복제본 DC를 배포하여 회사 포리스트를 확장합니까?</li> <li>새 자식 도메인 또는 도메인 트리를 배포하여 회사 포리스트를 확장합니까?</li> |<li>보안</li> <li>규정 준수</li> <li>비용</li> <li>복원력 및 내결함성</li> <li>응용 프로그램 호환성</li> |
| [Windows Server Active Directory 사이트 토폴로지](#BKMK_ADSiteTopology) |트래픽을 최적화하고 비용을 최소화하기 위해 Azure Virtual Network로 서브넷, 사이트 및 사이트 링크를 어떻게 구성합니까? |<li>서브넷 및 사이트 정의</li> <li>사이트 링크 속성 및 변경 알림</li> <li>복제 압축</li> |
| [IP 주소 지정 및 DNS](#BKMK_IPAddressDNS) |IP 주소 및 이름 확인을 구성하는 방법은 무엇입니까? |<li>Set-AzureStaticVNetIP cmdlet 사용을 사용하여 고정 IP 주소 할당</li> <li>Windows Server DNS 서버를 설치하고 DC 및 DNS 서버 역할을 호스팅하는 VM의 이름 및 IP 주소로 가상 네트워크 속성을 구성합니다.</li> |
| [지리적으로 분산된 DC](#BKMK_DistributedDCs) |별도 가상 네트워크의 DC에 복제하는 방법은 무엇입니까? |Active Directory 사이트 토폴로지에서 다른 Azure 지역에 해당하는 지역에 DC가 필요한 경우 Active Directory 사이트를 적절하게 만들려고 합니다. [가상 네트워크 연결에 가상 네트워크를 구성](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) 하여 별도 가상 네트워크의 도메인 컨트롤러 간에 복제할 수 있습니다. |
| [읽기 전용 DC](#BKMK_RODC) |읽기 전용 또는 쓰기 가능 DC를 사용합니까? |<li>HBI/PII 특성 필터링</li> <li>암호 필터링</li> <li>아웃바운드 트래픽 제한</li> |
| [글로벌 카탈로그](#BKMK_GC) |글로벌 카탈로그를 설치합니까? |<li>단일 도메인 포리스트의 경우 모든 DC GC를 만듭니다.</li> <li>다중 도메인 포리스트의 경우 인증에 GC가 필요합니다.</li> |
| [설치 방법](#BKMK_InstallMethod) |Azure에서 DC를 설치하는 방법은 무엇입니까? |다음 중 한 방법으로 찾을 수 있습니다. <li>Windows PowerShell 또는 Dcpromo를 사용하여 AD DS 설치</li> <li>온-프레미스 가상 DC의 VHD 이동</li> |
| [Windows Server AD DS 데이터베이스 및 SYSVOL의 배치](#BKMK_PlaceDB) |Windows Server AD DS 데이터베이스, 로그 및 SYSVOL을 저장하는 위치는 어디입니까? |Dcpromo.exe 기본값을 변경합니다. 이러한 중요한 Active Directory 파일은 쓰기 캐싱을 구현하는 운영 체제 디스크 대신 Azure Data Disks에 배치 *되어야* 합니다. |
| [Backup 및 복원](#BKMK_BUR) |데이터를 보호하고 복구하는 방법은 무엇입니까? |시스템 상태 백업 만들기 |
| [페더레이션 서버 구성](#BKMK_FedSrvConfig) |<li>클라우드에 페더레이션이 포함된 새 포리스트를 배포합니까?</li> <li>AD FS 온-프레미스를 배포하고 클라우드에서 프록시를 노출합니까?</li> |<li>보안</li> <li>규정 준수</li> <li>비용</li> <li>비즈니스 파트너가 응용 프로그램에 액세스</li> |
| [클라우드 서비스 구성](#BKMK_CloudSvcConfig) |클라우드 서비스는 처음 가상 머신을 만들 때 암시적으로 배포됩니다. 추가 클라우드 서비스를 배포해야 하나요? |<li>VM에 인터넷에 대한 직접 노출이 필요합니까?</li> <li> 서비스에 부하 분산이 필요합니까?</li> |
| [공용 및 개인 IP 주소 지정(동적 IP 및 가상 IP)에 대한 페더레이션 서버 요구 사항](#BKMK_FedReqVIPDIP) |<li>Windows Server AD FS 인스턴스가 인터넷에서 직접 연결되어야 합니까?</li> <li>고유한 인터넷 연결 IP 주소 및 포트가 클라우드에서 배포되는 응용 프로그램에 필요합니까?</li> |배포에 필요한 각 가상 IP 주소에 대한 단일 클라우드 서비스 만들기 |
| [Windows Server AD FS 고가용성 구성](#BKMK_ADFSHighAvail) |<li>Windows Server AD FS 서버 팜에 있는 노드는 몇 개입니까?</li> <li>Windows Server AD FS 프록시 팜에 배포할 노드는 몇 개입니까?</li> |복원력 및 내결함성 |

### <a name="BKMK_NetworkTopology"></a>네트워크 토폴로지
IP 주소 일관성과 Windows Server AD DS의 DNS 요구 사항을 충족하기 위해 먼저 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)를 만들고 가상 머신을 연결해야 합니다. 만드는 동안 필요에 따라 투명하게 Azure 가상 머신을 온-프레미스 컴퓨터에 연결하는 온-프레미스 회사 네트워크에 연결을 확장할지 여부를 결정해야 합니다. 이는 기존 VPN 기술을 사용하여 이루어지고 회사 네트워크의 가장자리에 VPN 끝점을 노출해야 합니다. 즉, VPN은 Azure에서 회사 네트워크로 시작되며 반대의 경우는 아닙니다.

각 VM에 적용되는 표준 요금 외 온-프레미스 네트워크에 가상 네트워크를 확장하는 경우 추가 요금이 적용됩니다. 특히 Azure Virtual Network 게이트웨이의 CPU 시간 및 VPN을 통해 온-프레미스 컴퓨터와 통신하는 각 VM에서 생성되는 송신 트래픽에 대한 요금이 있습니다. 네트워크 트래픽 요금에 대한 자세한 내용은 [한눈에 보는 Azure 가격 책정](http://azure.microsoft.com/pricing/)을 참조하세요.

### <a name="BKMK_DeploymentConfig"></a>DC 배포 구성
DC를 구성하는 방법은 Azure에서 실행하려는 서비스에 대한 요구 사항에 따라 달라집니다. 예를 들어 개념 증명 테스트용으로 고유한 회사 포리스트로부터 격리된 새 포리스트, 새 응용 프로그램 또는 내부 회사 리소스에 대한 특정 액세스가 아닌 디렉터리 서비스가 필요한 일부 다른 단기 프로젝트를 배포할 수 있습니다.

이점으로 분리된 포리스트 DC는 직접 비용을 절감하는 시스템 자체에서 생성된 적은 아웃바운드 네트워크 트래픽을 발생하여 온-프레미스 DC와 복제하지 않습니다. 네트워크 트래픽 요금에 대한 자세한 내용은 [한눈에 보는 Azure 가격 책정](http://azure.microsoft.com/pricing/)을 참조하세요.

또 다른 예로 서비스에 대한 개인 정보 보호 요구 사항이 있지만 서비스가 내부 Windows Server Active Directory에 대한 액세스에 따라 달라진다고 가정합니다. 클라우드의 서비스에 대한 데이터를 호스팅할 수 있으면 Azure에서 내부 포리스트에 대해 새 자식 도메인을 배포할 수 있습니다. 이 경우 새 자식 도메인에 대한 DC를 배포할 수 있습니다(개인 정보 보호 문제를 해결하기 위해 글로벌 카탈로그 없이). 이 시나리오에서는 복제본 DC 배포와 함께 온-프레미스 DC와 연결하기 위한 가상 네트워크가 필요합니다.

새 포리스트를 만드는 경우 [Active Directory 트러스트](https://technet.microsoft.com/library/cc771397) 또는 [페더레이션 트러스트](https://technet.microsoft.com/library/dd807036)의 사용 여부를 선택합니다. 호환성, 보안, 규정 준수, 비용 및 복원 기능으로 지정된 요구 사항의 균형을 조정합니다. 예를 들어 [선택적 인증](https://technet.microsoft.com/library/cc755844) 을 활용하기 위해 Azure의 새 포리스트를 배포하고 온-프레미스 포리스트 및 클라우드 포리스트 간에 Windows Server Active Directory 트러스트를 만들도록 선택할 수 있습니다. 그러나 응용 프로그램이 클레임 인식인 경우 Active Directory 포리스트 트러스트 대신 페더레이션 트러스트를 배포할 수 있습니다. 또 다른 요소는 온-프레미스 Windows Server Active Directory를 클라우드로 확장하여 더 많은 데이터를 복제하거나 인증 및 쿼리 로드의 결과로 더 많은 아웃바운드 트래픽을 생성하는 비용입니다.

가용성 및 내결함성에 대한 요구 사항은 선택에도 영향을 줍니다. 예를 들어 링크가 중단되는 경우 Kerberos 트러스트 또는 페더레이션 트러스트를 활용하는 응용 프로그램은 Azure에서 충분한 인프라를 배포하지 않은 한 모두 완전히 작동하지 않을 가능성이 있습니다. 복제본 DC와 같은 대체 배포 구성(쓰기 가능 또는 RODC)은 링크 중단을 허용할 수 있는 가능성을 높입니다.

### <a name="BKMK_ADSiteTopology"></a>Windows Server Active Directory 사이트 토폴로지
트래픽을 최적화하고 비용을 최소화하기 위해 사이트 및 사이트 링크를 올바르게 정의해야 합니다. 사이트, 사이트 링크 및 서브넷은 DC와 인증 트래픽 흐름 간의 복제 토폴로지에 영향을 줍니다. 다음 트래픽 요금을 고려한 다음 배포 시나리오의 요구 사항에 따라 DC를 배포 및 구성합니다.

* 게이트웨이 자체에 대해 시간당 아주 적은 수수료가 있습니다.
  
  * 필요에 따라 시작 및 중지할 수 있습니다.
  * 중지된 경우 Azure VM은 회사 네트워크에서 격리됩니다.
* 인바운드 트래픽은 무료입니다.
* 아웃바운드 트래픽은 [한눈에 보는 Azure 가격 책정](http://azure.microsoft.com/pricing/)에 따라 요금이 청구됩니다. 다음과 같이 온-프레미스 사이트와 클라우드 사이트 간의 사이트 링크 속성을 최적화할 수 있습니다.
  
  * 여러 개의 가상 네트워크를 사용하는 경우 사이트 링크 및 해당 비용을 적절하게 구성하여 Windows Server AD DS가 동일한 수준의 서비스를 무료로 제공할 수 있는 것보다 Azure 사이트에 우선 순위를 두지 못하도록 합니다. BASL(브리지 모든 사이트 링크) 비활성화를 고려할 수도 있습니다(기본으로 활성화됨). 이렇게 하면 직접 연결된 사이트만 서로 복제합니다. 전이적으로 연결된 사이트의 DC는 더 이상 서로 직접 복제할 수는 없지만 공통 사이트 또는 사이트를 통해 복제해야 합니다. 중개 사이트가 어떤 이유로 사용 불가능해진 경우 전이적으로 연결된 사이트의 DC 간 복제는 사이트 간 연결을 사용할 수 있더라도 발생하지 않습니다. 마지막으로 전이적 복제 동작의 섹션이 바람직하게 남아있는 곳에서 온-프레미스, 회사 네트워크 사이트와 같은 적절한 사이트 링크 및 사이트를 포함하는 사이트 링크 브리지를 만듭니다.
  * [사이트 링크 비용을 구성합니다](https://technet.microsoft.com/library/cc794882) . 예를 들어 **다음으로 가장 가까운 사이트 시도** 설정이 활성화된 경우 Azure 사이트를 회사 네트워크에 다시 연결하는 사이트 링크 개체와 관련된 비용을 증가시켜 가상 네트워크 사이트가 다음으로 가장 가까운 사이트가 아닌지 확인합니다.
  * 일관성 요구 사항 및 개체 변경 비율에 따라 사이트 링크 [간격](https://technet.microsoft.com/library/cc794878) 및 [일정](https://technet.microsoft.com/library/cc816906)을 구성합니다. 복제 일정을 대기 시간 허용치에 맞춥니다. DC는 값의 마지막 상태만을 복제하므로 충분한 개체 변경 비율이 있는 경우 복제 간격을 줄이면 비용을 절약할 수 있습니다.
* 비용 최소화가 매우 중요한 경우 복제가 예약되었고 변경 알림이 활성화되지 않았는지 확인합니다. 사이트 간에 복제하는 경우 이것이 기본 구성입니다. RODC는 아웃바운드 변경 내용을 복제하지 않기 때문에 가상 네트워크에서 RODC를 배포하는 경우 중요하지 않습니다. 하지만 쓰기 가능한 DC를 배포하는 경우 사이트 링크가 불필요한 빈도로 업데이트를 복제하도록 구성되지 않았는지 확인해야 합니다. GC(글로벌 카탈로그) 서버를 배포하는 경우 GC를 포함하는 다른 모든 사이트가 Azure 사이트에 있는 GC보다 비용이 낮은 사이트 링크와 연결된 사이트의 원본 DC에서 도메인 파티션을 복제하는지 확인합니다.
* 여전히 복제 압축 알고리즘을 변경하여 사이트 간 복제에 의해 생성된 네트워크 트래픽을 줄일 수 있습니다. 압축 알고리즘은 REG_DWORD 레지스트리 항목 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator compression algorithm으로 제어됩니다. 기본값은 3이며 Xpress 압축 알고리즘과 관련이 있습니다. 값을 2로 변경할 수 있으며 알고리즘을 MSZip으로 변경합니다. 대부분의 경우에서 이는 압축을 높이지만 CPU 사용률의 비용으로 그렇게 수행합니다. 자세한 내용은 [Active Directory 복제 토폴로지 작동 방법](https://technet.microsoft.com/library/cc755994)을 참조하세요.

### <a name="BKMK_IPAddressDNS"></a>IP 주소 지정 및 DNS
Azure 가상 머신은 기본적으로 "DHCP 임대 주소"로 할당됩니다. Azure 가상 네트워크 동적 주소는 가상 머신의 수명 동안 가상 머신과 지속되기 때문에 Windows Server AD DS의 요구 사항은 충족됩니다.

결과적으로 Azure에서 동적 주소를 사용하는 경우 임대 기간 동안 라우팅할 수 있으므로 사실상 고정 IP 주소를 사용하고 있으며 임대 기간은 클라우드 서비스의 수명과 같습니다.

그러나 VM이 종료되는 경우 동적 주소가 할당됩니다. IP 주소를 할당 취소되지 않도록 하려면 [Set-AzureStaticVNetIP를 사용하여 고정 IP 주소를 할당](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx)할 수 있습니다.

이름 확인을 위해 사용자 고유의(또는 기존 것 활용) DNS 서버 인프라를 배포합니다. Azure 제공 DNS는 Windows Server AD DS의 고급 이름 확인 요구 사항을 충족하지 않습니다. 예를 들어 동적 SRV 레코드 등을 지원하지 않습니다. 이름 확인은 DC 및 도메인에 가입된 클라이언트에 대한 중요한 구성 항목입니다. DC는 리소스 레코드를 등록하고 다른 DC의 리소스 레코드를 해결해야 합니다.
내결함성과 성능상의 이유로 Azure에서 실행되는 DC에 Windows Server DNS 서비스를 설치하는 데 적합합니다. 그런 다음 DNS 서버의 이름 및 IP 주소로 Azure 가상 네트워크 속성을 구성합니다. 가상 네트워크에 있는 다른 VM을 시작하는 경우 해당 DNS 클라이언트 확인자 설정은 동적 IP 주소 할당의 일부로 DNS 서버로 구성됩니다.

> [!NOTE]
> 인터넷을 통해 직접 Azure에서 호스팅되는 Windows Server AD DS Active Directory 도메인에 온-프레미스 컴퓨터를 가입할 수 없습니다. Active Directory 및 도메인 가입 작업에 대한 포트 요구 사항은 필요한 포트 및 사실상 전체 DC를 인터넷에 직접 노출하는 데 불가능한 것으로 렌더링합니다.
> 
> 

VM은 시작 시 또는 이름 변경 내용이 있을 때 자동으로 해당 DNS 이름을 등록합니다.

첫 번째 VM을 프로비전하고 AD DS를 설치하는 방법을 보여 주는 이 예제 및 다른 예제에 대한 자세한 내용은 [Microsoft Azure에서 새 Active Directory 포리스트 설치](active-directory-new-forest-virtual-machine.md)를 참조하세요. Windows PowerShell 사용에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azureps-cmdlets-docs) 및 [Azure 관리 Cmdlet](/powershell/module/azurerm.compute/#virtual_machines)을 참조하세요.

### <a name="BKMK_DistributedDCs"></a>지리적으로 분산된 DC
Azure는 서로 다른 가상 네트워크에 여러 DC를 호스팅하는 경우 이점을 제공합니다.

* 다중 사이트 내결함성
* 지사(대기 시간 단축)에 대한 물리적 근접성

가상 네트워크 간의 직접 통신을 구성하는 방법에 대한 정보는 [가상 네트워크 연결에 가상 네트워크 구성](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)을 참조하세요.

### <a name="BKMK_RODC"></a>읽기 전용 DC
읽기 전용 또는 쓰기 가능 DC를 배포할지 여부를 선택해야 합니다. 물리적으로 제어할 수 없지만 RODC는 지사와 같은 해당 물리적 보안이 위험한 위치에 배포되도록 설계되었기 때문에 RODC를 배포할 수도 있습니다.

Azure는 지사의 물리적 보안 위험을 제공하지 않지만 RODC가 제공하는 기능은 매우 다양한 이유로 이러한 환경에 적합하므로 RODC는 여전히 더욱 비용 효율적인 것으로 입증될 수 있습니다. 예를 들어 RODC는 아웃바운드 복제가 없으며 선택적으로 비밀(암호)을 채울 수 있습니다. 단점으로 이러한 암호의 부족은 사용자 또는 컴퓨터 인증으로 유효성을 검사하는 주문형 아웃바운드 트래픽이 필요할 수 있습니다. 그러나 암호는 선택적으로 미리 채워지고 캐시할 수 있습니다.

RODC는 RODC FAS(필터링된 특성 집합)에 대한 중요한 데이터를 포함하는 특성을 추가할 수 있으므로 HBI 및 PII 문제와 관련된 추가적인 이점을 제공합니다. FAS는 RODC에 복제되지 않은 사용자 지정 가능한 특성 집합입니다. 허용되지 않거나 Azure에 PII 또는 HBI를 저장하지 않으려는 경우 FAS를 보호 장치로 사용할 수 있습니다. 자세한 내용은 [RODC 복제가 제한된 특성 집합[(https://technet.microsoft.com/library/cc753459)]을 참조하세요.

응용 프로그램이 사용하려는 RODC와 호환되는지 확인합니다. 많은 Windows Server Active Directory 사용 응용 프로그램은 RODC와 잘 작동하지만 쓰기 가능한 DC에 대한 액세스가 없는 경우 일부 응용 프로그램은 비효율적으로 수행하거나 실패할 수 있습니다. 자세한 내용은 [읽기 전용 DC 응용 프로그램 호환성 가이드](https://technet.microsoft.com/library/cc755190)를 참조하세요.

### <a name="BKMK_GC"></a>글로벌 카탈로그
글로벌 카탈로그(GC) 설치 여부를 선택해야 합니다. 단일 도메인 포리스트에서 모든 DC를 글로벌 카탈로그 서버로 구성해야 합니다. 추가 복제 트래픽이 없기 때문에 비용이 증가하지 않습니다.

다중 도메인 포리스트에서 GC는 인증 프로세스 중에 유니버설 그룹 구성원 자격을 확장해야 합니다. GC를 배포하지 않는 경우 Azure에서 DC에 대해 인증하는 가상 네트워크의 작업은 각 인증 시도 중 온-프레미스에서 GC를 쿼리하도록 아웃바운드 인증 트래픽을 간접적으로 생성합니다.

GC는 모든 도메인(부분에서)을 호스팅하기 때문에 GC와 관련된 비용은 예측하기가 어렵습니다. 작업이 인터넷 서비스를 호스팅하고 Windows Server AD DS에 대한 사용자를 인증하는 경우 비용은 완전히 예측할 수 없습니다. 인증하는 동안 클라우드 사이트 외부의 GC 쿼리를 줄이려면 [유니버설 그룹 구성원 자격 캐싱을 활성화](https://technet.microsoft.com/library/cc816928)할 수 있습니다.

### <a name="BKMK_InstallMethod"></a>설치 방법
가상 네트워크에 DC를 설치하는 방법을 선택해야 합니다.

* 새 DC를 승격합니다. 자세한 내용은 [Azure 가상 네트워크에 새 Active Directory 포리스트 설치](active-directory-new-forest-virtual-machine.md)를 참조하세요.
* 온-프레미스 가상 DC의 VHD를 클라우드로 이동합니다. 이 경우 온-프레미스 가상 DC가 "복사됨" 또는 "복제됨"이 아닌 "이동됨"인지 확인해야 합니다.

DC에 대한 Azure 가상 머신만 사용합니다(Azure "웹" 또는 "작업자" 역할 VM과 달리). 지속성이 있으며 DC에 대한 상태의 내구성이 필요합니다. Azure 가상 머신은 DC와 같은 작업을 위해 설계되었습니다.

SYSPREP를 사용하여 DC를 배포하거나 복제하지 마십시오. DC를 복제하는 기능은 Windows Server 2012부터 사용할 수 있습니다. 복제 기능을 사용하려면 기본 하이퍼바이저에서 VMGenerationID에 대한 지원이 필요합니다. Windows Server 2012 및 Azure 가상 네트워크의 Hyper-V는 모두 타사 가상화 소프트웨어 공급 업체와 마찬가지로 VMGenerationID를 지원합니다.

### <a name="BKMK_PlaceDB"></a>Windows Server AD DS 데이터베이스 및 SYSVOL의 배치
Windows Server AD DS 데이터베이스, 로그 및 SYSVOL을 배치할 위치를 선택합니다. Azure 데이터 디스크에 배포해야 합니다.

> [!NOTE]
> Azure 데이터 디스크는 4TB로 제한됩니다.
> 
> 

데이터 디스크 드라이브는 기본적으로 쓰기를 캐시하지 않습니다. VM에 연결되어 있는 데이터 디스크 드라이브는 write-through 캐싱을 사용합니다. write-through 캐싱을 통해 VM의 운영 체제의 관점에서 트랜잭션이 완료되기 전에 쓰기가 지속형 Azure 저장소에 커밋되었는지 확인합니다. 쓰기 속도가 약간 느려지는 대신 지속성을 제공합니다.

write-behind 캐싱은 DC에서 만든 가정을 무효화하므로 Windows Server AD DS의 경우 중요합니다. Windows Server AD DS에서 쓰기 캐싱을 비활성화하려고 하지만 이를 인식하는 디스크 IO 시스템에 따라 다릅니다. 특정 상황에서 쓰기 캐싱 비활성화에 대한 실패는 느린 개체 및 다른 문제를 발생시키는 USN 롤백을 도입합니다.

가상 DC에 대한 모범 사례로 다음을 수행합니다.

* Azure 데이터 디스크의 호스트 캐시 기본 설정을 없음으로 설정합니다. 이는 AD DS 작업에 대한 쓰기 캐싱을 사용하여 문제를 방지합니다.
* 동일한 데이터 디스크나 별도 데이터 디스크에 데이터베이스, 로그 및 SYSVOL을 저장합니다. 일반적으로 운영 체제 자체에 사용되는 디스크와 별도 디스크입니다. 핵심 내용은 Windows Server AD DS 데이터베이스 및 SYSVOL이 Azure 운영 체제 디스크 유형에 저장되지 않아야 한다는 점입니다. 기본적으로 AD DS 설치 프로세스는 Azure에 권장되지 않는 %systemroot% 폴더에 이러한 구성 요소를 설치합니다.

### <a name="BKMK_BUR"></a>Backup 및 복원
일반적으로 DC 및 보다 구체적으로 VM에서 실행되는 것의 백업 및 복원에 지원되는 것과 지원되지 않는 것을 확인합니다. [가상화된 DC에 대한 Backup 및 복원 고려 사항](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers)을 참조하세요.

Windows Server Backup과 같은 Windows Server AD DS에 대한 백업 요구 사항을 특별히 인식하는 백업 소프트웨어만을 사용하여 시스템 상태 백업을 만듭니다.

정기 백업을 수행하는 대신 DC의 VHD 파일을 복사하거나 복제하지 마십시오. 복원이 필요한 경우 Windows Server 2012 및 지원되는 하이퍼바이저 없이 복제되거나 복사된 VHD를 사용하여 수행하면 USN 거품이 발생합니다.

### <a name="BKMK_FedSrvConfig"></a>페더레이션 서버 구성
Windows Server AD FS 페더레이션 서버(STS)의 구성은 Azure에 배포하려는 응용 프로그램을 온-프레미스 네트워크의 리소스에 액세스해야 하는지 여부에 따라 부분적으로 달라집니다.

응용 프로그램이 다음 조건을 충족하는 경우 온-프레미스 네트워크에서 격리된 상태에서 응용 프로그램을 배포할 수 있습니다.

* SAML 보안 토큰에 동의합니다.
* 인터넷에 노출 가능합니다.
* 온-프레미스 리소스에 액세스하지 않습니다.

이 경우 Windows Server AD FS STS를 다음과 같이 구성합니다.

1. Azure에서 분리된 단일 도메인 포리스트를 구성합니다.
2. Windows Server AD FS 페더레이션 서버 팜을 구성하여 포리스트에 대한 페더레이션된 액세스를 제공합니다.
3. 온-프레미스 포리스트에서 Windows Server AD FS(페더레이션 서버 팜 및 페더레이션 서버 프록시 팜)를 구성합니다.
4. 온-프레미스 및 Windows Server AD FS의 Azure 인스턴스 간에 페더레이션 트러스트 관계를 설정합니다.

반면에 응용 프로그램에 온-프레미스 리소스에 대한 액세스가 필요한 경우 다음과 같이 Azure의 응용 프로그램으로 Windows Server AD FS를 구성할 수 있습니다.

1. 온-프레미스 네트워크와 Azure 간의 연결을 구성합니다.
2. 온-프레미스 포리스트에서 Windows Server AD FS 페더레이션 서버 팜을 구성합니다.
3. Azure에서 Windows Server AD FS 페더레이션 서버 프록시 팜을 구성합니다.

이 구성에는 경계 네트워크의 응용 프로그램으로 Windows Server AD FS를 구성할 때와 마찬가지로 온-프레미스 리소스의 노출을 줄이는 이점이 있습니다.

어느 시나리오에서든 B2B 공동 작업이 필요한 경우 더 많은 ID 공급자로 트러스트 관계를 설정할 수 있습니다.

### <a name="BKMK_CloudSvcConfig"></a>클라우드 서비스 구성
VM을 인터넷에 직접 노출하거나 인터넷 연결 부하 분산된 응용 프로그램을 노출하려는 경우 클라우드 서비스가 필요합니다. 각 클라우드 서비스는 단일 구성 가능한 가상 IP 주소를 제공하기 때문에 이것이 가능합니다.

### <a name="BKMK_FedReqVIPDIP"></a>공용 및 개인 IP 주소 지정(동적 IP 및 가상 IP)에 대한 페더레이션 서버 요구 사항
각 Azure 가상 컴퓨터는 동적 IP 주소를 받습니다. 동적 IP 주소는 Azure 내에서만 액세스할 수 있는 개인 주소입니다. 그러나 대부분의 경우에서 Windows Server AD FS 배포에 대한 가상 IP 주소를 구성해야 합니다. 가상 IP는 Windows Server AD FS 끝점을 인터넷에 노출하는 데 필요하며 인증 및 지속적인 관리를 위해 페더레이션된 파트너와 클라이언트에서 사용됩니다. 가상 IP 주소는 하나 이상의 Azure 가상 머신을 포함하는 클라우드 서비스의 속성입니다. Azure 및 Windows Server AD FS에 배포된 클레임 인식 응용 프로그램은 모두 인터넷에 연결되어 있고 공통 포트를 공유하며 각각은 자체의 가상 IP 주소가 필요하므로 응용 프로그램에 대한 하나의 클라우드 서비스 및 Windows Server AD FS에 대한 두 번째를 만드는 데 필요합니다.

가상 IP 주소 및 동적 IP 주소의 용어 정의는 [용어 및 정의](#BKMK_Glossary)를 참조하세요.

### <a name="BKMK_ADFSHighAvail"></a>Windows Server AD FS 고가용성 구성
독립 실행형 Windows Server AD FS 페더레이션 서비스를 배포할 수 있지만 AD FS STS와 프로덕션 환경에 대한 프록시에 대한 두 개 이상의 노드가 포함된 팜을 배포하는 것이 좋습니다.

[AD FS 2.0 디자인 가이드](https://technet.microsoft.com/library/dd807036)의 [AD FS 2.0 배포 토폴로지 고려 사항](https://technet.microsoft.com/library/gg982489)을 참조하여 특정 필요에 가장 잘 맞는 배포 구성 옵션을 결정합니다.

> [!NOTE]
> Azure에서 Windows Server AD FS 끝점에 대해 부하 분산하려면 동일한 클라우드 서비스에서 Windows Server AD FS 팜의 모든 구성원을 구성하고 HTTP(기본값 80) 및 HTTPS 포트(기본값 443)에 대한 Azure의 부하 분산 기능을 사용합니다. 자세한 내용은 [Azure 부하 분산 장치 프로브](https://msdn.microsoft.com/library/azure/jj151530)를 참조하세요.
> Windows Server NLB(네트워크 부하 분산)는 Azure에서 지원되지 않습니다.
> 
> 

