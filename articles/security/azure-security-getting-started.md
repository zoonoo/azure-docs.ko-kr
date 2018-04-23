---
title: Microsoft Azure 보안 시작 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure 보안 기능 및 클라우드 공급자에 자산을 마이그레이션하는 조직에 대한 일반적인 고려 사항에 개요를 제공합니다.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 7d3fab20ec238bff0664fc98c2067c919e97a7c2
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="getting-started-with-microsoft-azure-security"></a>Microsoft Azure 보안 시작
IT 자산을 작성하거나 클라우드 공급자에 마이그레이션하는 경우 해당 조직이 제공하는 서비스와 컨트롤을 통해 응용 프로그램 및 데이터를 보호할 수 있는 해당 조직의 역량에 의존하여 클라우드 기반 자산의 보안을 관리합니다.

Azure의 인프라는 수백만 명의 고객을 동시에 호스팅하기 위한 시설에서 응용 프로그램에 이르는 인프라를 설계하며 비즈니스의 보안 요구 사항을 충족하는 신뢰할 수 있는 기반을 제공합니다. 또한 Azure는 다양하게 구성 가능한 보안 옵션 및 제어 능력을 제공하므로 배포에 대한 고유한 요구 사항에 맞게 보안을 사용자 지정할 수 있습니다.

Azure 보안에 있는 이 개요 문서에서 다음 사항을 살펴봅니다.

* Azure 내에서 서비스와 데이터를 보호 하기 위해 사용할 수 있는 Azure 서비스 및 기능.
* Microsoft가 Azure 인프라를 보호하여 데이터 및 응용 프로그램을 보호하는 방법.

## <a name="identity-and-access-management"></a>ID 및 액세스 관리
IT 인프라, 데이터 및 응용 프로그램에 대한 액세스를 제어하는 것이 중요합니다. Microsoft Azure는 Azure AD(Azure Active Directory), Azure Storage, 수많은 표준 및 API에 대한 지원과 같은 서비스를 통해 이러한 기능을 제공합니다.

[Azure AD](../active-directory/active-directory-whatis.md)는 조직의 사용자, 그룹 및 개체에 대한 인증, 권한 부여 및 액세스 제어를 제공하는 ID 리포지토리 및 엔진입니다. 또한 Azure AD는 응용 프로그램에서 ID 관리를 통합할 수 있는 효과적인 방법을 개발자에게 제공합니다. [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx) 및 [OpenID 연결](http://openid.net/connect/)과 같은 산업 표준 프로토콜을 통해 .NET, Java, Node.js 및 PHP와 같은 플랫폼에서 로그인 기능을 설정할 수 있습니다.

REST 기반 Graph API를 통해 개발자가 어떠한 플랫폼에서든 디렉터리에 대한 읽기 및 쓰기를 수행할 수 있습니다. [OAuth 2.0](http://oauth.net/2/) 지원을 통해 개발자는 Microsoft 및 타사 웹 API와 통합되는 모바일 및 웹 응용 프로그램을 빌드하고 사용자 고유의 보안 웹 API를 빌드할 수 있습니다. 개발 중인 추가 라이브러리와 함께 오픈 소스 클라이언트 라이브러리를 .Net, Windows Store, iOS 및 Android에서 사용할 수 있습니다.

### <a name="how-azure-enables-identity-and-access-management"></a>Azure가 ID 및 액세스 관리를 사용하는 방법
Azure AD는 조직의 독립 실행형 클라우드 디렉터리 또는 기존 온-프레미스 Active Directory와 통합된 솔루션으로 사용될 수 있습니다. 일부 통합 기능은 디렉터리 동기화 및 Single Sign-On(SSO)을 포함합니다. 기존 온-프레미스 ID의 도달 범위를 클라우드로 확장하고 관리자 및 사용자 환경을 개선합니다.

ID 및 액세스 관리에 대한 기타 기능은 다음과 같습니다.

* Azure AD는 호스팅되는 위치에 관계 없이 SaaS 응용 프로그램에 [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)를 사용합니다. 응용 프로그램 일부는 Azure AD를 사용하여 페더레이션되고 나머지는 암호 SSO를 사용합니다. 또한 페더레이션된 응용 프로그램은 사용자 프로비전 및 암호 보관을 지원할 수 있습니다.
* [Azure Storage](https://azure.microsoft.com/services/storage/)의 데이터 액세스는 인증을 통해 제어됩니다. 각 저장소 계정에는 기본 키([저장소 계정 키](https://msdn.microsoft.com/library/azure/ee460785.aspx) 또는 SAK) 및 보조 비밀 키(공유 액세스 서명 또는 SAS)가 있습니다.
* Azure AD는 온-프레미스 디렉터리와 함께 [Active Directory Federation Services](../active-directory/fundamentals-identity.md), 동기화 및 복제를 사용하여 페더레이션을 통해 IaaS(Identity as a Service)를 제공합니다.
* [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)은 사용자에게 모바일 앱, 전화 통화 또는 문자 메시지를 사용하여 로그인을 검증하도록 요구하는 다단계 인증 서비스입니다. Azure AD와 함께 사용할 수 있으며 Azure Multi-Factor Authentication 서버를 통해 온-프레미스 리소스의 보안을 유지하도록 도와주며, SDK를 사용하여 사용자 지정 응용 프로그램 및 디렉터리와도 사용될 수 있습니다.
* [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/)는 도메인 컨트롤러를 배포하지 않고 Azure Virtual Machines를 도메인에 가입할 수 있도록 합니다. 이러한 가상 컴퓨터에 회사 Active Directory 자격 증명을 사용하여 로그인하고 모든 Azure Virtual Machines에 보안 기준을 적용하도록 그룹 정책을 사용하여 도메인에 가입된 가상 컴퓨터를 관리할 수 있습니다.
* [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)는 수억 개의 ID로 확장하는 소비자 지향 응용 프로그램에 항상 사용 가능한 전역적인 ID 관리 서비스를 제공합니다. 이 서비스는 모바일 및 웹 플랫폼에 통합될 수 있습니다. 고객은 사용자 지정 가능한 환경을 통해 기존 소셜 계정을 사용하거나 새 자격 증명을 만들어 모든 응용 프로그램에 로그인할 수 있습니다.

## <a name="data-access-control-and-encryption"></a>데이터 액세스 제어 및 암호화
Microsoft는 Azure 운영 전반에 걸쳐 의무 분리 및 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 원칙을 적용합니다. Azure 지원 담당자가 데이터에 액세스하면 명시적 사용 권한이 필요하며 기록되고 감사받는 "just-in-time" 기준이 부여된 다음 서비스 계약이 완료된 후에 해지됩니다.

Azure는 전송 중인 데이터와 미사용 데이터 보호를 위한 다수의 기능도 제공합니다. 여기에는 데이터, 파일, 응용 프로그램, 서비스, 통신, 장치에 대한 암호화가 포함됩니다. 정보를 Azure에 배치하기 전에 암호화할 수 있고 온-프레미스 데이터 센터에 키를 저장할 수도 있습니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Azure 암호화 기술
[Azure AD 보고](../active-directory/active-directory-reporting-audit-events.md)를 사용하여 구독 환경에 대한 관리 액세스 세부 정보를 수집할 수 있습니다. Azure의 중요한 정보를 포함하는 VHD에서 [BitLocker 드라이브 암호화](https://technet.microsoft.com/library/cc732774.aspx)를 구성할 수 있습니다.

데이터 보안을 유지하는 데 도움이 되는 Azure의 기타 기능은 다음과 같습니다.

* 응용 프로그램 개발자는 Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) 및.NET Framework를 사용하여 Azure에 배포할 응용 프로그램에 암호화를 작성할 수 있습니다.
* Azure Blob Storage에 대한 클라이언트측 암호화를 사용하여 키를 완전히 제어할 수 있습니다. 저장소 서비스는 키를 표시하지 못하고 데이터를 해독할 수 없습니다.
* [Azure RMS(Azure Rights Management)](https://technet.microsoft.com/library/jj585026.aspx)([RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx) 포함)는 정책 기반 액세스 관리를 통해 파일 및 데이터 수준 암호화와 데이터 누수 방지를 제공합니다.
* Azure는 SQL Server 가상 머신에서 [테이블 수준 및 열 수준 암호화(TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx)를 지원하고 데이터 센터에서 타사 온-프레미스 키 관리 서버를 지원합니다.
* Storage 계정 키, 공유 액세스 서명, 관리 인증서 및 다른 키는 각각 Azure 테넌트에 대해 고유합니다.
* Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) 하이브리드 저장소는 데이터를 Azure Storage에 업로드하기 전에 128비트 공개/개인 키 쌍을 통해 암호화합니다.
* Azure는 데이터 형식, 컨테이너 및 전송에 따라 SSL/TLS, IPsec 및 AES를 포함하는 다양한 암호화 메커니즘을 지원하고 사용합니다.

## <a name="virtualization"></a>가상화
Azure 플랫폼은 가상화된 환경을 사용합니다. 사용자 인스턴스는 실제 호스트 서버에 대한 액세스 권한이 없는 독립 실행형 가상 머신으로 작동하고 이러한 격리는 실제 [프로세서(ring-0/ring-3) 권한 수준](https://en.wikipedia.org/wiki/Protection_ring)으로 적용됩니다.

링 0는 사용 권한이 가장 많고 3은 가장 적습니다. 게스트 OS는 권한이 낮은 링 1에서 실행되고 응용 프로그램은 권한이 가장 낮은 링 3에서 실행합니다. 실제 리소스의 가상화는 게스트 OS와 하이퍼바이저 간에 명확한 분리를 이끌어 내고 둘 사이에 추가 보안 분리가 발생합니다.

Azure 하이퍼바이저는 마이크로 커널처럼 작동하고 VMBus라는 공유 메모리 인터페이스를 사용하여 게스트 가상 머신에서 호스트에 모든 하드웨어 액세스 요청을 처리하도록 전달합니다. 이렇게 하면 사용자가 시스템에 원시 읽기/쓰기/실행 액세스를 가져오는 것을 방지하고 시스템 리소스를 공유할 위험을 완화합니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Azure가 가상화를 구현하는 방법
Azure는 하이퍼바이저에서 구현되고 패브릭 컨트롤러 에이전트에서 구성된 하이퍼바이저 방화벽(패킷 필터)를 사용합니다. 이렇게 하면 무단 액세스로부터 테넌트를 보호할 수 있습니다. 기본적으로 모든 트래픽은 가상 머신이 만들어질 때 차단된 다음 패브릭 컨트롤러 에이전트에서 권한이 부여된 트래픽을 허용하기 위해 패킷 필터를 구성하여 *규칙과 예외*를 추가합니다.

프로그래밍하는 규칙에는 다음과 같은 두 가지 범주가 있습니다.

* **컴퓨터 구성 또는 인프라 규칙**: 기본적으로 모든 통신이 차단됩니다. 가상 머신이 DHCP 및 DNS 트래픽을 보내고 받을 수 있는 데 예외 사항이 있습니다. 또한 가상 머신은 "공용" 인터넷에 트래픽을 전송하고 클러스터 및 OS 정품 인증 서버 내에서 다른 가상 머신에도 트래픽을 전송할 수 있습니다. 가상 머신의 허용된 송신 대상 목록에는 Azure 라우터 서브넷, Azure 관리 백 엔드 및 기타 Microsoft 속성이 포함되지 않습니다.
* **역할 구성 파일**: 테넌트의 서비스 모델에 기반하여 인바운드 ACL(Access Control 목록)을 정의합니다. 예를 들어 테넌트에 있는 특정 가상 머신의 포트 80에 웹 프런트 엔드가 있을 때 [Azure 클래식 배포 모델](../azure-resource-manager/resource-manager-deployment-model.md)에서 끝점을 구성하면 Azure는 모든 IP에 TCP 포트 80을 엽니다. 가상 머신에서 백 엔드 또는 작업자 역할을 실행 중이면 동일한 테넌트 내의 가상 머신에만 작업자 역할을 엽니다.

## <a name="isolation"></a>격리
또 다른 중요한 클라우드 보안 요구 사항은 공유 다중 테넌트 아키텍처의 배포 간에 정보의 무단 전송 또는 의도치 않은 전송을 막기 위한 분리입니다.

Azure는 VLAN 격리, ACL, 부하 분산 장치 및 IP 필터를 통해 [네트워크 액세스 제어](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) 및 분리를 구현합니다. 정의하는 가상 머신의 프로토콜 및 포트에 인바운드되는 외부 트래픽을 제한합니다. Azure는 스푸핑된 트래픽을 방지하고 들어오고 나가는 트래픽을 신뢰할 수 있는 플랫폼 구성 요소로 제한하기 위해 네트워크 필터링을 구현합니다. 트래픽 흐름 정책은 기본적으로 트래픽을 거부하는 경계 보호 장치에서 구현됩니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-getting-started/sec-azgsfig3.PNG)

네트워크 주소 변환(NAT)은 외부 트래픽에서 내부 네트워크 트래픽을 분리하는 데 사용됩니다. 내부 트래픽은 외부에서 라우팅할 수 없습니다. 외부에서 라우팅할 수 있는 [가상 IP 주소](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx)는 Azure 내에서만 라우팅할 수 있는 [내부 동적 IP](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) 주소로 변환됩니다.

Azure 가상 머신에 대한 외부 트래픽은 라우터, 부하 분산 장치 및 3계층 스위치에서 ACL을 통해 방화벽이 적용됩니다. 알려진 특정 프로토콜에만 허용됩니다. ACL은 관리를 위해 게스트 가상 머신에서 발생하여 다른 VLAN으로 이동하는 트래픽을 제한하기 위해 존재합니다. 이외에도 호스트 OS에서 IP 필터를 통해 필터링된 트래픽이 데이터 링크 및 네트워크 계층 모두에서 트래픽을 추가로 제한합니다.

### <a name="how-azure-implements-isolation"></a>Azure가 격리를 구현하는 방법
Azure 패브릭 컨트롤러는 테넌트 워크로드에 대한 인프라 리소스를 할당하는 일을 담당하고 호스트에서 가상 머신에 단방향 통신을 관리합니다. Azure 하이퍼바이저는 가상 머신 간의 메모리 및 프로세스 분리를 적용하고 네트워크 트래픽을 게스트 OS 테넌트로 안전하게 라우팅합니다. 또한 Azure는 테넌트, 저장소 및 가상 네트워크에 대한 격리를 구현합니다.

* 각 Azure AD 테넌트는 보안 경계를 사용하여 논리적으로 분리됩니다.
* Azure Storage 계정은 각 구독에 대해 고유하고 액세스는 저장소 계정 키를 사용하여 인증되어야 합니다.
* 가상 네트워크는 고유한 개인 IP 주소, 방화벽 및 IP ACL의 조합을 통해 논리적으로 격리됩니다. 부하 분산 장치는 끝점 정의에 따라 적절한 테넌트에 트래픽을 라우팅합니다.

## <a name="virtual-networks-and-firewalls"></a>가상 네트워크 및 방화벽
Azure 도움말에서 [분산된 가상 네트워크](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx)는 개인 네트워크 트래픽이 다른 Azure Virtual Network의 트래픽에서 논리적으로 분리되도록 합니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-getting-started/sec-azgsfig4.PNG)

구독에는 다수의 격리된 개인 네트워크가 포함될 수 있습니다(방화벽, 부하 분산 및 네트워크 주소 변환 포함).

Azure는 각 Azure 클러스터에서 세 가지 기본 수준의 네트워크 분리를 제공하여 트래픽을 논리적으로 분리합니다. [Virtual Local Area Network](https://azure.microsoft.com/services/virtual-network/)(VLAN)는 Azure 네트워크의 나머지 부분에서 고객 트래픽을 분리하는 데 사용됩니다. 부하 분산 장치를 통해 클러스터 외부에서 Azure 네트워크에 액세스가 제한됩니다.

가상 머신 간의 네트워크 트래픽은 하이퍼바이저 가상 스위치를 통해 전달해야 합니다. 루트 OS의 IP 필터 구성 요소는 게스트 가상 머신에서 루트 가상 머신을 격리하고 다른 게스트 가상 머신을 서로 격리합니다. 테넌트의 노드와 공용 인터넷(고객의 서비스 구성에 따라) 간의 통신을 제한하는 트래픽 필터링을 수행하며 이는 다른 테넌트에서 노드를 분리합니다.

IP 필터는 게스트 가상 머신에 다음과 같은 사항을 방지합니다.

* 스푸핑된 트래픽 생성.
* 지정되지 않은 트래픽 수신.
* 보호된 인프라 끝점에 트래픽 보내기.
* 부적절한 브로드캐스트 트래픽 송신 및 수신.

가상 머신을 [Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/)에 배치할 수 있습니다. 이러한 가상 네트워크는 온-프레미스 환경에서 구성한 네트워크와 유사하며 일반적으로 가상 스위치에 연결됩니다. 동일한 가상 네트워크에 연결된 가상 머신은 추가 구성 없이 서로 통신할 수 있습니다. 가상 네트워크 내에 다른 서브넷을 구성할 수도 있습니다.

다음과 같은 Azure Virtual Network 기술을 사용하여 가상 네트워크에서 안전한 통신을 도울 수 있습니다.

* [**NSG(네트워크 보안 그룹)**](../virtual-network/virtual-networks-nsg.md). NSG를 사용하여 가상 네트워크에 있는 하나 이상의 가상 머신 인스턴스에 대한 트래픽을 제어할 수 있습니다. NSG에는 트래픽 방향, 프로토콜, 원본 주소 및 포트, 대상 주소 및 포트에 따라 트래픽을 허용하거나 거부하는 액세스 제어 규칙이 포함되어 있습니다.
* [**사용자 정의 라우팅**](../virtual-network/virtual-networks-udr-overview.md). 특정 서브넷으로 흐르는 패킷이 가상 네트워크 보안 어플라이언스로 가도록 홉을 지정하는 사용자 정의 경로를 생성하여 가상 어플라이언스를 통해 패킷의 라우팅을 제어할 수 있습니다.
* [**IP 전달**](../virtual-network/virtual-networks-udr-overview.md). 가상 네트워크 보안 어플라이언스는 주소가 자신으로 지정되지 않은 들어오는 트래픽을 받을 수 있어야 합니다. 가상 머신이 다른 대상으로 주소가 지정된 트래픽을 받을 수 있도록 하려면 해당 가상 머신에서 IP 전달을 사용하도록 설정합니다.
* [**강제 터널링**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). 강제 터널링을 사용하면 검사 및 감사에 대한 사이트 간 VPN 터널을 통해 가상 네트워크의 가상 머신에서 생성된 모든 인터넷 바인딩된 트래픽을 온-프레미스 위치에 다시 리디렉션하거나 "강제"할 수 있습니다.
* [**끝점 ACL**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 끝점 ACL을 정의하여 어떤 컴퓨터가 인터넷에서 가상 네트워크의 가상 머신에 인바운드 연결을 허용하는지 제어할 수 있습니다.
* [**파트너 네트워크 보안 솔루션**](https://azure.microsoft.com/marketplace/). Azure Marketplace에서 액세스할 수 있는 파트너 네트워크 보안 솔루션은 많습니다.

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Azure가 가상 네트워크 및 방화벽을 구현하는 방법
Azure에서는 기본적으로 모든 호스트 및 게스트 가상 머신에 패킷 필터링 방화벽을 구현합니다. 또한 Azure Marketplace에서 Windows OS 이미지는 Windows 기본적으로 방화벽을 사용할 수 있습니다. Azure의 공용 네트워크 경계에서 부하 분산 장치는 고객 관리자에 의해 관리되는 IP ACL에 기반한 통신을 제어합니다.

Azure가 정상적인 작업의 일부로 서 또는 재해 발생 중에 고객의 데이터를 이동하면 개인의 암호화된 통신 채널을 통해 수행합니다. 가상 네트워크 및 방화벽에서 사용하기 위해 Azure에 적용되는 다른 기능은 다음과 같습니다.

* **네이티브 호스트 방화벽**: Azure Service Fabric 및 Azure Storage는 하이퍼바이저가 없는 네이티브 OS에서 실행됩니다. 따라서 Windows 방화벽이 이전 규칙 집합 두 개를 사용하여 구성됩니다. 저장소는 네이티브를 실행하여 성능을 최적화합니다.
* **호스트 방화벽** - 호스트 방화벽은 하이퍼바이저를 실행하는 호스트 운영 체제를 보호합니다. 규칙은 Service Fabric 컨트롤러 및 점프 상자만이 특정 포트에서 호스트 OS와 통신하도록 프로그래밍됩니다. 다른 예외는 DHCP 응답 및 DNS 응답을 허용하는 것입니다. Azure는 호스트 OS에 대한 방화벽 규칙의 템플릿이 있는 컴퓨터 구성 파일을 사용합니다. 호스트 자체는 알려지고 인증된 원본에서 통신을 허용하도록 구성된 Windows 방화벽에 의해 외부의 공격으로부터 보호됩니다.
* **게스트 방화벽** - 가상 머신 스위치 패킷 필터에서 규칙을 복제하지만 서로 다른 소프트웨어에서 프로그래밍됩니다(예: 게스트 OS의 Windows 방화벽 부분). 통신이 IP 필터 호스트의 구성에서 허용되더라도 게스트 가상 머신 방화벽은 게스트 가상 머신 간에 통신을 제한하도록 구성될 수 있습니다. 예를 들어 게스트 가상 컴퓨터 방화벽을 사용하도록 선택하여 서로 연결되도록 구성된 두 개의 VNet 간의 통신을 제한할 수 있습니다.
* **저장소 방화벽(FW)**: 저장소 프런트 엔드에 방화벽은 트래픽을 필터링하여 포트 80/443 및 기타 필요한 유틸리티 포트에만 존재하도록 합니다. 저장소 백 엔드의 방화벽은 저장소 프런트 엔드 서버에서만 통신을 받도록 제한합니다.
* **가상 네트워크 게이트웨이**: [Azure Virtual Network Gateways](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)는 Azure Virtual Network의 워크로드를 온-프레미스 사이트에 연결하는 크로스 프레미스 게이트웨이로 사용됩니다. [IPsec 사이트 간 VPN 터널](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) 또는 [ExpressRoute](../expressroute/expressroute-introduction.md) 회로를 통해 온-프레미스 사이트에 연결해야 합니다. IPsec/IKE VPN 터널의 경우 게이트웨이는 IKE 핸드셰이크를 수행하고 가상 네트워크와 온-프레미스 사이트 간에 IPsec S2S VPN 터널을 설정합니다. 또한 가상 네트워크 게이트웨이는 [지점 및 사이트 간 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)을 종료합니다.

## <a name="secure-remote-access"></a>안전한 원격 액세스
클라우드에 저장된 데이터는 전송되는 동안 충분한 예방 조치를 통해 공격을 방지하고 기밀성과 무결성을 유지 관리하도록 해야 합니다. 여기에는 조직의 정책 기반, 감사 가능한 ID 및 액세스 관리 메커니즘을 사용하여 연결된 네트워크 제어가 포함됩니다.

기본 제공 암호화 기술을 사용하면 배포 간, Azure 지역 간 및 Azure에서 온-프레미스 데이터 센터에 통신을 암호화할 수 있습니다. [원격 데스크톱 세션](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), [원격 Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx) 및 Azure Portal을 통한 가상 머신에 대한 관리자 액세스는 항상 암호화됩니다.

Azure에서는 온-프레미스 데이터 센터를 클라우드로 안전하게 확장하기 위해 [사이트 간 VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) 및 [지점 및 사이트 간 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) 뿐만 아니라 [ExpressRoute](../expressroute/expressroute-introduction.md) 전용 링크를 모두 제공합니다(VPN을 통한 Azure Virtual Network 연결이 암호화됨).

### <a name="how-azure-implements-secure-remote-access"></a>Azure가 안전한 원격 액세스를 구현하는 방법
Azure Portal에 대한 연결은 항상 인증되어야 하며 SSL/TLS이 필요합니다. 관리 인증서를 구성하여 보안 관리를 사용할 수 있습니다. [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) 및 [IPsec](https://en.wikipedia.org/wiki/IPsec)과 같은 보안 업계 표준 프로토콜은 완벽하게 지원됩니다.

[Azure ExpressRoute](../expressroute/expressroute-introduction.md)를 사용하면 온-프레미스 또는 공동 배치 환경의 인프라와 Azure 데이터 센터 간에 개인 연결을 만들 수 있습니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 일반적인 인터넷 기반 연결보다 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안성을 제공합니다. 경우에 따라 ExpressRoute 연결을 사용하여 온-프레미스 위치와 Azure 간에 데이터를 전송하면 상당한 비용 혜택을 얻을 수 있습니다.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링
Azure는 감사 내역을 생성하는 보안 관련 이벤트의 인증된 로깅을 제공하고 정보 훼손에 강하도록 엔지니어링됩니다. Azure 인프라 가상 머신 및 Azure AD에서 보안 이벤트 로그와 같은 시스템 정보가 포함됩니다. 보안 이벤트 모니터링은 DHCP 또는 DNS 서버 IP 주소와 같은 이벤트를 수집하고 설계에서 차단되는 포트, 프로토콜 또는 IP 주소에 액세스를 시도하며 보안 정책 또는 방화벽 설정, 계정 또는 그룹 만들기, 예기치 않은 프로세스 또는 드라이버 설치를 변경하는 작업을 포함합니다.

![Azure의 Microsoft 맬웨어 방지](./media/azure-security-getting-started/sec-azgsfig5.PNG)

감사는 권한 있는 사용자 액세스 및 활동, 권한이 부여되거나 무단인 액세스 시도, 시스템 예외 및 보안 이벤트가 설정된 시간 동안 보관되는 정보를 기록합니다. 고유의 요구 사항에 로그 수집 및 보존을 구성하기 때문에 로그의 보존은 사용자의 판단에 따릅니다.

### <a name="how-azure-implements-logging-and-monitoring"></a>Azure가 로깅 및 모니터링을 구현하는 방법
Azure는 네이티브 또는 가상인지에 따라 관리에서 각 계산, 저장소, 또는 패브릭 노드에 관리 에이전트(MA) 및 Azure 보안 모니터(ASM) 에이전트를 배포합니다. 각 관리 에이전트는 Azure 인증서 저장소에서 가져온 인증서 및 앞서 미리 구성된 진단과 저장소 계정에 대한 이벤트 데이터를 사용하여 서비스 팀 저장소 계정에 인증하도록 구성됩니다. 이러한 에이전트는 고객의 가상 머신에 배포되지 않습니다.

Azure 관리자는 인증되고 제어된 로그에 대한 액세스를 위해 웹 포털을 통해 로그에 액세스합니다. 관리자는 로그의 구문을 분석하고 필터링하며 상호 연결 및 분석합니다. 로그에 대한 Azure 서비스 팀 저장소 계정은 직접 관리자 액세스로부터 보호되어 로그 훼손을 방지합니다.

Microsoft는 Syslog 프로토콜을 사용하여 네트워크 장치에서 그리고 Microsoft 감사 컬렉션 서비스(ACS)를 사용하여 호스트 서버에서 로그를 수집합니다. 이러한 로그는 로그 데이터베이스에 배치되고 이를 통해 의심스러운 이벤트에 대한 경고가 생성됩니다. 관리자는 이러한 로그에 액세스하고 분석할 수 있습니다.

[Azure 진단](https://msdn.microsoft.com/library/azure/gg433048.aspx)은 Azure에서 실행하는 응용 프로그램으로부터 진단 데이터를 수집할 수 있는 Azure 기능입니다. 디버깅 및 문제 해결, 성능 측정, 리소스 사용 모니터링, 트래픽 분석 및 용량 계획, 감사 등에 사용할 수 있는 진단 데이터입니다. 진단 데이터를 수집한 후에 지속성을 위해 Azure 저장소 계정에 전송될 수 있습니다. 전송은 예약되거나 요청 시에 가능합니다.

## <a name="threat-mitigation"></a>위협 해결 방법
Azure에서는 격리, 암호화 및 필터링 외에도 인프라 및 서비스를 보호하기 위한 다양한 위협 해결 방법 메커니즘 및 프로세스를 사용합니다. DDoS, 권한 에스컬레이션 및 [OWASP 상위 10개](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)와 같은 고급 위협을 탐색하고 해결하는 데 사용하는 내부 컨트롤 및 기술이 포함됩니다.

Microsoft가 해당 클라우드 인프라를 보호하기 위해 마련한 보안 제어 및 위험 관리 프로세스는 보안 문제 발생의 위험을 줄입니다. 인시던트가 발생하는 경우 Microsoft OSSC(Online Security Services and Compliance) 팀 내의 SIM(Security Incident Management) 팀이 언제든 대응할 수 있도록 준비되어 있습니다.

### <a name="how-azure-implements-threat-mitigation"></a>Azure가 위협 해결 방법을 구현하는 방법
Azure에는 위협 해결 방법을 구현하고 고객이 해당 환경에서 잠재적인 위협을 완화하도록 도와주는 보안 제어를 준비합니다. 다음 목록에서는 Azure에서 제공하는 위협 해결 방법 기능을 요약합니다.

* [Azure 맬웨어 방지](azure-security-antimalware.md)는 모든 인프라 서버에서 기본적으로 사용됩니다. 필요에 따라 사용자 고유의 가상 머신 내에서 사용할 수 있습니다.
* Microsoft는 위협 요소를 감지하고 악용을 방지하기 위해 서버, 네트워크 및 응용 프로그램을 모니터링을 지속적으로 유지 관리합니다. 자동화된 경고는 내부 및 외부 위협에 수정 작업을 수행하도록 허용하도록 관리자에게 비정상적인 동작을 알립니다.
* 구독 내에서 [Barracuda](https://techlib.barracuda.com/ng54/deployonazure)의 웹 응용 프로그램 방화벽과 같은 타사 보안 솔루션을 배포할 수 있습니다.
* Microsoft의 침투 테스트 접근 방식에는 "[레드 팀](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)"이 있으며, 실제 환경의 발전된 지속적 위협에 대한 방어를 테스트하기 위해 Azure에서 (비고객) 라이브 프로덕션 시스템을 공격하는 Microsoft 보안 전문가가 관계됩니다.
* 통합된 배포 시스템으로 Azure 플랫폼에 걸쳐 보안 패치의 배포 및 설치를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 보안 센터](https://azure.microsoft.com/support/trust-center/)

[Azure 보안 팀 블로그](http://blogs.msdn.com/b/azuresecurity/)

[Microsoft 보안 대응 센터](https://technet.microsoft.com/library/dn440717.aspx)

[Active Directory 블로그](http://blogs.technet.com/b/ad/)
