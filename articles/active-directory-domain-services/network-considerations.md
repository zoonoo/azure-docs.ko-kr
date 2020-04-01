---
title: Azure AD 도메인 서비스에 대한 네트워크 계획 및 연결 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스를 실행할 때 연결에 사용되는 몇 가지 가상 네트워크 디자인 고려 사항 및 리소스에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 69f8cd0f78a45c6c5e53368edc5902c4b6695701
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408827"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Azure AD 도메인 서비스에 대한 가상 네트워크 디자인 고려 사항 및 구성 옵션

Azure Active Directory 도메인 서비스(AD DS)가 다른 응용 프로그램 및 워크로드에 인증 및 관리 서비스를 제공하므로 네트워크 연결이 핵심 구성 요소입니다. 가상 네트워크 리소스가 올바르게 구성되지 않으면 응용 프로그램 및 워크로드가 Azure AD DS에서 제공하는 기능을 통신하고 사용할 수 없습니다. Azure AD DS가 필요에 따라 응용 프로그램 및 워크로드에 서비스를 제공할 수 있는지 확인하기 위해 가상 네트워크 요구 사항을 계획합니다.

이 문서에서는 Azure AD DS를 지원하기 위한 Azure 가상 네트워크에 대한 디자인 고려 사항 및 요구 사항을 간략하게 설명합니다.

## <a name="azure-virtual-network-design"></a>Azure 가상 네트워크 디자인

네트워크 연결을 제공하고 응용 프로그램 및 서비스가 Azure AD DS에 대해 인증할 수 있도록 하려면 Azure 가상 네트워크 및 서브넷을 사용합니다. 이상적으로 Azure AD DS는 자체 가상 네트워크에 배포해야 합니다. 동일한 가상 네트워크에 별도의 응용 프로그램 서브넷을 포함하여 관리 VM 또는 가벼운 응용 프로그램 워크로드를 호스팅할 수 있습니다. Azure AD DS 가상 네트워크를 피어로 하는 더 크거나 복잡한 응용 프로그램 워크로드에 대한 별도의 가상 네트워크는 일반적으로 가장 적합한 디자인입니다. 가상 네트워크 및 서브넷에 대한 다음 섹션에 설명된 요구 사항을 충족하는 경우 다른 디자인 선택 사항이 유효합니다.

Azure AD DS에 대한 가상 네트워크를 디자인할 때 다음 고려 사항이 적용됩니다.

* Azure AD DS는 가상 네트워크와 동일한 Azure 지역에 배포되어야 합니다.
    * 현재 Azure AD 테넌트당 하나의 Azure AD DS 관리 도메인만 배포할 수 있습니다. Azure AD DS 관리 도메인이 단일 지역에 배포됩니다. [Azure AD DS를 지원하는 리전에서](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)가상 네트워크를 만들거나 선택해야 합니다.
* 응용 프로그램 워크로드를 호스트하는 다른 Azure 지역 및 가상 네트워크의 근접성을 고려합니다.
    * 대기 시간을 최소화하려면 Azure AD DS 관리 도메인의 가상 네트워크 서브넷에 가까운 또는 동일한 리전에서 핵심 응용 프로그램을 유지합니다. Azure 가상 네트워크 간의 가상 네트워크 피어링 또는 VPN(가상 사설망) 연결을 사용할 수 있습니다. 이러한 연결 옵션은 다음 섹션에서 설명합니다.
* 가상 네트워크는 Azure AD DS에서 제공하는 서비스 이외의 DNS 서비스에 의존할 수 없습니다.
    * Azure AD DS는 자체 DNS 서비스를 제공합니다. 이러한 DNS 서비스 주소를 사용하도록 가상 네트워크를 구성해야 합니다. 조건부 전달자를 사용하여 추가 네임스페이스에 대한 이름 확인을 수행할 수 있습니다.
    * 사용자 지정 DNS 서버 설정을 사용하여 VM을 비롯한 다른 DNS 서버의 쿼리를 지시할 수 없습니다. 가상 네트워크의 리소스는 Azure AD DS에서 제공하는 DNS 서비스를 사용해야 합니다.

> [!IMPORTANT]
> 서비스를 활성화한 후에는 Azure AD DS를 다른 가상 네트워크로 이동할 수 없습니다.

Azure AD DS 관리 도메인은 Azure 가상 네트워크의 서브넷에 연결합니다. Azure AD DS에 대해 다음과 같은 사항을 고려하여 이 서브넷을 디자인합니다.

* Azure AD DS는 자체 서브넷에 배포해야 합니다. 기존 서브넷 이나 게이트웨이 서브넷을 사용 하지 마십시오.
* 네트워크 보안 그룹은 Azure AD DS 관리 도메인을 배포하는 동안 만들어집니다. 이 네트워크 보안 그룹에는 올바른 서비스 통신에 필요한 규칙이 포함되어 있습니다.
    * 사용자 지정 규칙을 사용하여 기존 네트워크 보안 그룹을 만들거나 사용하지 마십시오.
* Azure AD DS에는 3-5개의 IP 주소가 필요합니다. 서브넷 IP 주소 범위가 이 수의 주소를 제공할 수 있는지 확인합니다.
    * 사용 가능한 IP 주소를 제한하면 Azure AD 도메인 서비스가 두 개의 도메인 컨트롤러를 유지 관리하지 못할 수 있습니다.

다음 예제 다이어그램에서는 Azure AD DS에 자체 서브넷이 있고, 외부 연결을 위한 게이트웨이 서브넷이 있고, 응용 프로그램 워크로드가 가상 네트워크 내의 연결된 서브넷에 있는 유효한 디자인을 간략하게 설명합니다.

![권장되는 서브넷 디자인](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Azure AD DS 가상 네트워크에 대한 연결

이전 섹션에서 설명한 것처럼 Azure의 단일 가상 네트워크에서 Azure AD 도메인 서비스 관리 도메인만 만들 수 있으며 Azure AD 테넌트당 하나의 관리되는 도메인만 만들 수 있습니다. 이 아키텍처를 기반으로 응용 프로그램 워크로드를 호스팅하는 하나 이상의 가상 네트워크를 Azure AD DS 가상 네트워크에 연결해야 할 수 있습니다.

다음 방법 중 하나를 사용하여 다른 Azure 가상 네트워크에서 호스팅되는 응용 프로그램 워크로드를 연결할 수 있습니다.

* 가상 네트워크 피어링
* 가상 사설망(VPN)

### <a name="virtual-network-peering"></a>가상 네트워크 피어링

가상 네트워크 피어링은 Azure 백본 네트워크를 통해 동일한 지역에 있는 두 개의 가상 네트워크를 연결하는 메커니즘입니다. 글로벌 가상 네트워크 피어링은 Azure 리전에서 가상 네트워크를 연결할 수 있습니다. 피어싱되면 두 가상 네트워크는 VM과 같은 리소스가 개인 IP 주소를 사용하여 서로 직접 통신할 수 있도록 합니다. 가상 네트워크 피어링을 사용하면 다른 가상 네트워크에 배포된 응용 프로그램 워크로드를 사용하여 Azure AD DS 관리 도메인을 배포할 수 있습니다.

![피어링을 사용하여 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/vnet-peering.png)

자세한 내용은 [Azure 가상 네트워크 피어링 개요를](../virtual-network/virtual-network-peering-overview.md)참조하십시오.

### <a name="virtual-private-networking-vpn"></a>VPN(가상 사설망)

가상 네트워크를 온-프레미스 사이트 위치에 가상 네트워크를 구성할 수 있는 것과 같은 방식으로 다른 가상 네트워크(VNet-to-VNet)에 가상 네트워크를 연결할 수 있습니다. 두 연결 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 사용하여 보안 터널을 만듭니다. 이 연결 모델을 사용하면 Azure AD DS를 Azure 가상 네트워크에 배포한 다음 온-프레미스 위치 또는 기타 클라우드를 연결할 수 있습니다.

![VPN 게이트웨이를 사용한 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

가상 사설망 사용에 대한 자세한 내용은 [Azure 포털을 사용하여 VNet-VNet VPN 게이트웨이 연결 구성을](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)참조하십시오.

## <a name="name-resolution-when-connecting-virtual-networks"></a>가상 네트워크를 연결할 때 이름 확인

Azure AD 도메인 서비스 가상 네트워크에 연결된 가상 네트워크에는 일반적으로 자체 DNS 설정이 있습니다. 가상 네트워크를 연결하면 Azure AD DS 관리 도메인에서 제공하는 서비스를 해결하기 위해 연결 가상 네트워크에 대한 이름 확인이 자동으로 구성되지 않습니다. 응용 프로그램 워크로드가 Azure AD 도메인 서비스를 찾을 수 있도록 연결 가상 네트워크의 이름 확인을 구성해야 합니다.

연결 가상 네트워크를 지원하는 DNS 서버의 조건부 DNS 전달자를 사용하거나 Azure AD 도메인 서비스 가상 네트워크의 동일한 DNS IP 주소를 사용하여 이름 확인을 활성화할 수 있습니다.

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS에서 사용하는 네트워크 리소스

Azure AD DS 관리 도메인은 배포 하는 동안 일부 네트워킹 리소스를 만듭니다. 이러한 리소스는 Azure AD DS 관리 도메인의 성공적인 운영 및 관리에 필요하며 수동으로 구성해서는 안 됩니다.

| Azure 리소스                          | Description |
|:----------------------------------------|:---|
| 네트워크 인터페이스 카드                  | Azure AD DS는 Windows 서버에서 Azure VM으로 실행되는 두 개의 도메인 컨트롤러(DC)에서 관리되는 도메인을 호스팅합니다. 각 VM에는 가상 네트워크 서브넷에 연결하는 가상 네트워크 인터페이스가 있습니다. |
| 동적 표준 공용 IP 주소      | Azure AD DS는 표준 SKU 공용 IP 주소를 사용하여 동기화 및 관리 서비스와 통신합니다. 공용 IP 주소에 대한 자세한 내용은 Azure 의 [IP 주소 유형 및 할당 메서드를](../virtual-network/virtual-network-ip-addresses-overview-arm.md)참조하십시오. |
| Azure 표준 로드 밸러터            | Azure AD DS는 NAT(네트워크 주소 변환) 및 로드 분산(보안 LDAP와 함께 사용되는 경우)에 표준 SKU 로드 밸런서를 사용합니다. Azure 로드 밸로드밸해시에 대한 자세한 내용은 [Azure 로드 밸로드밸해시(Azure Load Balancer)란 무엇입니까?](../load-balancer/load-balancer-overview.md) |
| 네트워크 주소 변환(NAT) 규칙 | Azure AD DS는 로드 밸러저에 대한 세 가지 NAT 규칙을 만들고 사용합니다. |
| 부하 분산 장치 규칙                     | TCP 포트 636에서 안전한 LDAP를 위해 Azure AD DS 관리 도메인이 구성되면 로드 밸런서에서 세 가지 규칙이 만들어지고 트래픽을 분산하는 데 사용됩니다. |

> [!WARNING]
> Azure AD DS에서 만든 네트워크 리소스(예: 로드 밸러블러 또는 규칙)를 삭제하거나 수정하지 마십시오. 네트워크 리소스를 삭제하거나 수정하면 Azure AD DS 서비스 중단이 발생할 수 있습니다.

## <a name="network-security-groups-and-required-ports"></a>네트워크 보안 그룹 및 필수 포트

[NSG(네트워크 보안 그룹)에는](../virtual-network/virtual-networks-nsg.md) Azure 가상 네트워크의 트래픽에 대한 네트워크 트래픽을 허용하거나 거부하는 규칙 목록이 포함되어 있습니다. 네트워크 보안 그룹은 서비스가 인증 및 관리 기능을 제공할 수 있도록 하는 규칙 집합을 포함하는 Azure AD DS를 배포할 때 만들어집니다. 이 기본 네트워크 보안 그룹은 Azure AD DS 관리 도메인이 배포되는 가상 네트워크 서브넷과 연결됩니다.

Azure AD DS가 인증 및 관리 서비스를 제공하려면 다음 네트워크 보안 그룹 규칙이 필요합니다. Azure AD DS 관리 도메인이 배포되는 가상 네트워크 서브넷에 대해 이러한 네트워크 보안 그룹 규칙을 편집하거나 삭제하지 마십시오.

| 포트 번호 | 프로토콜 | 원본                             | 대상 | 작업 | 필수 | 목적 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActive 디렉터리도메인 서비스 | 모두         | Allow  | 예      | Azure AD 테넌트와 동기화합니다. |
| 3389        | TCP      | 코프넷소                         | 모두         | Allow  | 예      | 도메인 관리. |
| 5986        | TCP      | AzureActive 디렉터리도메인 서비스 | 모두         | Allow  | 예      | 도메인 관리. |
| 636         | TCP      | 모두                                | 모두         | Allow  | 예       | 보안 LDAP(LDAPS)를 구성할 때만 사용하도록 설정됩니다. |

> [!WARNING]
> 이러한 네트워크 리소스 및 구성을 수동으로 편집하지 마십시오. 잘못 구성된 네트워크 보안 그룹 또는 사용자 정의 경로 테이블을 Azure AD DS가 배포되는 서브넷과 연결하면 Microsoft의 도메인 서비스 및 관리 기능이 중단될 수 있습니다. Azure AD 테넌트와 Azure AD DS 관리 도메인 간의 동기화도 중단됩니다.
>
> *AllowVnetInBound,* *AllowAzureLoadand,* *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*및 *DenyAllOutBound에* 대한 기본 규칙은 네트워크 보안 그룹에 대해서도 존재합니다. 이러한 기본 규칙을 편집하거나 삭제하지 마십시오.
>
> Azure SLA는 Azure AD DS가 도메인을 업데이트하고 관리하지 못하도록 차단하는 잘못 구성된 네트워크 보안 그룹 및/또는 사용자 정의 경로 테이블이 적용된 배포에는 적용되지 않습니다.

### <a name="port-443---synchronization-with-azure-ad"></a>포트 443 - Azure AD와 동기화

* Azure AD 테넌트를 Azure AD DS 관리 도메인과 동기화하는 데 사용됩니다.
* 이 포트에 액세스하지 않으면 Azure AD DS 관리 도메인이 Azure AD 테넌트와 동기화할 수 없습니다. 사용자는 Azure AD DS 관리 도메인에 암호 변경 내용이 동기화되지 않기 때문에 로그인하지 못할 수 있습니다.
* 이 포트에서 IP 주소에 대한 인바운드 액세스는 기본적으로 **AzureActiveDirectoryDomainServices** 서비스 태그를 사용하여 제한됩니다.
* 이 포트에서 아웃바운드 액세스를 제한하지 마십시오.

### <a name="port-3389---management-using-remote-desktop"></a>포트 3389 - 원격 데스크톱을 사용한 관리

* Azure AD DS 관리 도메인의 도메인 컨트롤러에 대한 원격 데스크톱 연결에 사용됩니다.
* 기본 네트워크 보안 그룹 규칙은 *CorpNetSaw* 서비스 태그를 사용하여 트래픽을 추가로 제한합니다.
    * 이 서비스 태그를 사용하면 Microsoft 회사 네트워크의 보안 액세스 워크스테이션만 Azure AD DS 관리 도메인에 원격 데스크톱을 사용할 수 있습니다.
    * 액세스는 관리 또는 문제 해결 시나리오와 같은 비즈니스 타당성을 통해서만 허용됩니다.
* 이 규칙은 *Deny로*설정할 수 있으며 필요한 경우에만 *허용하도록* 설정할 수 있습니다. 대부분의 관리 및 모니터링 작업은 PowerShell 원격을 사용하여 수행됩니다. RDP는 Microsoft가 고급 문제 해결을 위해 관리되는 도메인에 원격으로 연결해야 하는 드문 경우에만 사용됩니다.

> [!NOTE]
> 이 네트워크 보안 그룹 규칙을 편집하려고 하면 포털에서 *CorpNetSaw* 서비스 태그를 수동으로 선택할 수 없습니다. *CorpNetSaw* 서비스 태그를 사용하는 규칙을 수동으로 구성하려면 Azure PowerShell 또는 Azure CLI를 사용해야 합니다.

### <a name="port-5986---management-using-powershell-remoting"></a>포트 5986 - PowerShell 원격을 사용한 관리

* Azure AD DS 관리 도메인에서 PowerShell 원격을 사용하여 관리 작업을 수행하는 데 사용됩니다.
* 이 포트에 액세스하지 않으면 Azure AD DS 관리 도메인을 업데이트, 구성, 백업 또는 모니터링할 수 없습니다.
* 리소스 관리자 기반 가상 네트워크를 사용하는 Azure AD DS 관리 도메인의 경우 이 포트에 대한 인바운드 액세스를 *AzureActiveDirectoryDomainServices* 서비스 태그로 제한할 수 있습니다.
    * 클래식 기반 가상 네트워크를 사용하여 레거시 Azure AD DS 관리 도메인의 경우 이 포트에 대한 인바운드 액세스를 다음 소스 IP 주소로 *23.101.0.70*제한할 수 *있습니다.* *13.74.249.156,* *52.187.117.83,* *52.161.13.95,* *104.40.156.18*및 *104.40.87.209*. *52.225.184.198* *52.179.126.223*

    > [!NOTE]
    > 2017년에 Azure AD 도메인 서비스를 Azure 리소스 관리자 네트워크에서 호스트할 수 있게 되었습니다. 그 이후로 Azure Resource Manager의 최신 기능을 사용하여 보다 안전한 서비스를 빌드할 수 있었습니다. Azure 리소스 관리자 배포는 클래식 배포를 완전히 대체하므로 Azure AD DS 클래식 가상 네트워크 배포는 2023년 3월 1일에 사용 중지됩니다.
    >
    > 자세한 내용은 공식 [사용 중단 공지를](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/) 참조하십시오.

## <a name="user-defined-routes"></a>사용자 정의 경로

사용자 정의 경로는 기본적으로 만들어지지 않으며 Azure AD DS가 올바르게 작동하기 위해 필요하지 않습니다. 경로 테이블을 사용해야 하는 경우 *0.0.0.0* 경로를 변경하지 마십시오. 이 경로의 변경으로 Azure AD 도메인 서비스가 중단되고 관리되는 도메인이 지원되지 않는 상태로 표시됩니다.

또한 각 Azure 서비스 태그에 포함된 IP 주소에서 Azure AD 도메인 서비스 서브넷으로 인바운드 트래픽을 라우팅해야 합니다. 서비스 태그 및 관련 IP 주소에 대한 자세한 내용은 [Azure IP 범위 및 서비스 태그 - 퍼블릭 클라우드를](https://www.microsoft.com/en-us/download/details.aspx?id=56519)참조하십시오.

> [!CAUTION]
> 이러한 Azure 데이터 센터 IP 범위는 예고 없이 변경될 수 있습니다. 최신 IP 주소가 있는지 확인하는 프로세스가 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS에서 사용하는 일부 네트워크 리소스 및 연결 옵션에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [Azure 가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 네트워크 보안 그룹](../virtual-network/security-overview.md)
