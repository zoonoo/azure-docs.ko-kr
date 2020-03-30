---
title: 클라우드간이별 Azure VMware 솔루션 - VMware 사이트 복구 관리자를 사용하여 사설 클라우드를 재해 복구 사이트로 설정
description: 온-프레미스 VMware 워크로드에 대한 재해 복구 사이트로 CloudSimple 프라이빗 클라우드를 설정하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565930"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>VMware 사이트 복구 관리자를 통해 프라이빗 클라우드를 재해 복구 대상으로 설정

클라우드심플 프라이빗 클라우드를 온-프레미스 VMware 워크로드에 대한 DR(재해 복구) 사이트로 사용할 수 있습니다.

DR 솔루션은 vSphere 복제 및 VMware 사이트 복구 관리자(SRM)를 기반으로 합니다. 유사한 접근 방식을 따라 Private Cloud를 온-프레미스 복구 사이트에서 보호하는 기본 사이트로 사용할 수 있습니다.

클라우드 심플 솔루션:

* DR을 위해 특별히 데이터 센터를 설정할 필요가 없습니다.
* 전 세계 지리적 복원력을 위해 CloudSimple이 배포되는 Azure 위치를 활용할 수 있습니다.
* DR 을 설정하기 위한 배포 비용과 총 소유 비용을 줄일 수 있는 옵션을 제공합니다.

CloudSimple 솔루션에서는 다음을 수행해야 합니다.

* 프라이빗 클라우드에서 vSphere 복제 및 SRM을 설치, 구성 및 관리합니다.
* 프라이빗 클라우드가 보호된 사이트인 경우 SRM에 대한 자체 라이선스를 제공합니다. 복구 사이트로 사용되는 경우 CloudSimple 사이트에 대한 추가 SRM 라이선스가 필요하지 않습니다.

이 솔루션을 사용하면 vSphere 복제 및 SRM을 완전히 제어할 수 있습니다. 익숙한 UI, API 및 CLI 인터페이스를 사용하면 기존 스크립트와 도구를 사용할 수 있습니다.

![사이트 복구 관리자 배포](media/srm-deployment.png)

프라이빗 클라우드 및 온-프레미스 환경과 호환되는 모든 버전의 vRA 및 SRM을 사용할 수 있습니다. 이 가이드의 예제에서는 vRA 6.5 및 SRM 6.5를 사용합니다. 이러한 버전은 CloudSimple에서 지원하는 vSphere 6.5와 호환됩니다.

## <a name="deploy-the-solution"></a>솔루션 배포

다음 섹션에서는 프라이빗 클라우드에서 SRM을 사용하여 DR 솔루션을 배포하는 방법을 설명합니다.

1. [VMware 제품 버전이 호환되는지 확인](#verify-that-vmware-product-versions-are-compatible)
2. [DR 환경의 크기 예측](#estimate-the-size-of-your-dr-environment)
3. [사용자 환경을 위한 프라이빗 클라우드 만들기](#create-a-private-cloud-for-your-environment)
4. [SRM 솔루션을 위한 프라이빗 클라우드 네트워킹 설정](#set-up-private-cloud-networking-for-the-srm-solution)
5. [온-프레미스 네트워크와 프라이빗 클라우드 간 사이트 간 VPN 연결을 설정하고 필요한 포트를 엽니다.](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [프라이빗 클라우드에서 인프라 서비스 설정](#set-up-infrastructure-services-in-your-private-cloud)
7. [온-프레미스 환경에 vSphere 복제 어플라이언스 설치](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [프라이빗 클라우드 환경에 vSphere 복제 어플라이언스 설치](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [온-프레미스 환경에 SRM 서버 설치](#install-srm-server-in-your-on-premises-environment)
10. [프라이빗 클라우드에 SRM 서버 설치](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware 제품 버전이 호환되는지 확인

이 가이드의 구성에는 다음과 같은 호환성 요구 사항이 적용됩니다.

* 동일한 버전의 SRM을 프라이빗 클라우드 및 온-프레미스 환경에 배포해야 합니다.
* 동일한 버전의 vSphere 복제는 프라이빗 클라우드 및 온-프레미스 환경에 배포해야 합니다.
* 프라이빗 클라우드 및 온-프레미스 환경의 PSC(플랫폼 서비스 컨트롤러) 버전은 호환되어야 합니다.
* 프라이빗 클라우드및 온-프레미스 환경의 vCenter 버전은 호환되어야 합니다.
* SRM 및 vSphere 복제 버전은 서로 호환되며 PSC 및 vCenter 버전과 호환되어야 합니다.

관련 VMware 설명서 및 호환성 정보에 대한 링크는 [VMware 사이트 복구 관리자](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) 설명서로 이동하십시오.

프라이빗 클라우드에서 vCenter 및 PSC 버전을 확인하려면 CloudSimple 포털을 엽니다. **리소스로**이동하여 프라이빗 클라우드를 선택하고 **vSphere 관리 네트워크** 탭을 클릭합니다.

![프라이빗 클라우드의 & PSC 버전 센터](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>DR 환경의 크기 예측

1. 식별된 온-프레미스 구성이 지원되는 제한 내에 있는지 확인합니다. SRM 6.5의 경우 [사이트 복구 관리자 6.5의 운영 제한에 대한](https://kb.vmware.com/s/article/2147110)VMware 기술 자료 문서에 제한이 설명되어 있습니다.
2. 워크로드 크기 및 RPO 요구 사항을 충족하기에 충분한 네트워크 대역폭이 있는지 확인합니다. [vSphere 복제에 대한 대역폭 요구 사항 계산에 대한](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) VMware 기술 자료 문서에서는 대역폭 제한에 대한 지침을 제공합니다.
3. CloudSimple 사이저 도구를 사용하여 온-프레미스 환경을 보호하기 위해 DR 사이트에 필요한 리소스를 추정합니다.

### <a name="create-a-private-cloud-for-your-environment"></a>사용자 환경을 위한 프라이빗 클라우드 만들기

사설 클라우드 만들기의 지침및 크기 조정 권장 사항에 따라 CloudSimple 포털에서 프라이빗 [클라우드를](create-private-cloud.md)만듭니다.

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>SRM 솔루션을 위한 프라이빗 클라우드 네트워킹 설정

CloudSimple 포털에 액세스하여 SRM 솔루션에 대한 프라이빗 클라우드 네트워킹을 설정합니다.

SRM 솔루션 네트워크에 대한 VLAN을 만들고 서브넷 CIDR을 할당합니다. 지침은 [VLAN/서브넷 만들기 및 관리를](create-vlan-subnet.md)참조하십시오.

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>온-프레미스 네트워크와 프라이빗 클라우드 간 사이트 간 VPN 연결을 설정하고 필요한 포트를 엽니다.

온-프레미스 네트워크와 프라이빗 클라우드 간의 사이트 간 연결을 설정합니다. 지침은 [CloudSimple 프라이빗 클라우드에 대한 VPN 연결 구성을](set-up-vpn.md)참조하십시오.

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>프라이빗 클라우드에서 인프라 서비스 설정

워크로드 및 도구를 쉽게 관리할 수 있도록 프라이빗 클라우드에서 인프라 서비스를 구성합니다.

다음 중 한 가지 를 수행하려는 경우 [CloudSimple 프라이빗 클라우드의 vCenter용 ID 공급자로 Azure AD 사용에](azure-ad.md) 설명된 대로 외부 ID 공급자를 추가할 수 있습니다.

* 프라이빗 클라우드에서 온-프레미스 Active Directory(AD)에서 사용자를 식별합니다.
* 모든 사용자에 대해 프라이빗 클라우드에서 AD를 설정합니다.
* Azure AD를 사용합니다.

프라이빗 클라우드에서 워크로드에 대한 IP 주소 조회, IP 주소 관리 및 이름 확인 서비스를 제공하려면 CloudSimple 프라이빗 클라우드에서 DNS 및 DHCP 응용 프로그램 및 워크로드 설정에 설명된 대로 [DHCP 및 DNS 서버를 설정합니다.](dns-dhcp-setup.md)

*.cloudsimple.io 도메인은 프라이빗 클라우드의 관리 VM 및 호스트에서 사용됩니다. 이 도메인에 대한 요청을 해결하려면 [조건부 전달자 만들기에](on-premises-dns-setup.md#create-a-conditional-forwarder)설명된 대로 DNS 서버에서 DNS 전달을 구성합니다.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>온-프레미스 환경에 vSphere 복제 어플라이언스 설치

VMware 설명서에 따라 온-프레미스 환경에 vSphere 복제 어플라이언스(vRA)를 설치합니다. 설치는 다음과 같은 고급 단계로 구성됩니다.

1. vRA 설치를 위해 온-프레미스 환경을 준비합니다.

2. vmware.com VR ISO의 OVF를 사용하여 온-프레미스 환경에 vRA를 배포합니다. vRA 6.5의 경우 [이 VMware 블로그에는](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) 관련 정보가 있습니다.

3. 온-프레미스 사이트에서 vCenter 단일 사인온으로 온-프레미스 vRA를 등록합니다. vSphere 복제 6.5에 대한 자세한 지침은 VMware 문서 [VMware vSphere 복제 6.5 설치 및 구성을](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)참조하십시오.

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>프라이빗 클라우드 환경에 vSphere 복제 어플라이언스 설치

시작하기 전에 다음이 있는지 확인합니다.

* 온-프레미스 환경의 서브넷에서 프라이빗 클라우드의 관리 서브넷에 이르는 IP 도달 가능성
* 온-프레미스 환경의 복제 서브넷에서 프라이빗 클라우드의 SRM 솔루션 서브넷에 이르기까지 IP 도달 가능성

지침은 [CloudSimple 프라이빗 클라우드에 대한 VPN 연결 구성을](set-up-vpn.md)참조하십시오. 단계는 온-프레미스 설치에 대 한 것과 비슷합니다.

CloudSimple은 vRA 및 SRM 설치 중에 IP 주소 대신 FQDNs를 사용하는 것이 좋습니다. 프라이빗 클라우드에서 vCenter 및 PSC의 FQDN을 찾으려면 CloudSimple 포털을 엽니다. **리소스로**이동하여 프라이빗 클라우드를 선택하고 **vSphere 관리 네트워크** 탭을 클릭합니다.

![프라이빗 클라우드에서 vCenter/PSC의 FQDN 찾기](media/srm-resources.png)

CloudSimple은 기본 '클라우드 소유자' 사용자를 사용하여 vRA 및 SRM을 설치하지 않고 대신 새 사용자를 만들어야 합니다. 이는 프라이빗 클라우드 vCenter 환경에 대한 높은 가동 시간 및 가용성을 보장하기 위한 것입니다. 그러나 프라이빗 클라우드 vCenter의 기본 클라우드 소유자 사용자는 관리 권한이 있는 새 사용자를 만들기에 충분한 권한이 없습니다.

vRA 및 SRM을 설치하기 전에 클라우드 소유자 사용자의 vCenter 권한을 에스컬레이션한 다음 vCenter SSO 도메인에서 관리 권한이 있는 사용자를 만들어야 합니다. 기본 프라이빗 클라우드 사용자 및 권한 모델에 대한 자세한 내용은 [사설 클라우드 권한 모델 알아보기를](learn-private-cloud-permissions.md)참조하십시오.

설치는 다음과 같은 고급 단계로 구성됩니다.

1. [권한 에스컬레이션.](escalate-private-cloud-privileges.md)
2. vSphere 복제 및 SRM 설치를 위해 프라이빗 클라우드에서 사용자를 만듭니다. [vCenter UI에서 아래에 설명: vRA & SRM 설치에 대 한 개인 클라우드에서 사용자를 만듭니다.](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)
3. vRA 설치를 위해 프라이빗 클라우드 환경을 준비합니다.
4. vmware.com VR ISO의 OVF를 사용하여 프라이빗 클라우드에 vRA를 배포합니다. vRA 6.5의 경우 [이 VMware 블로그에는](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) 관련 정보가 있습니다.
5. vRA에 대한 방화벽 규칙을 구성합니다. [CloudSimple 포털에서 아래에 설명: vRA에 대한 방화벽 규칙을 구성합니다.](#cloudsimple-portal-configure-firewall-rules-for-vra)
6. 프라이빗 클라우드 사이트에서 vCenter 단일 사인온으로 프라이빗 클라우드 vRA를 등록합니다.
7. 두 어플라이언스 간에 vSphere 복제 연결을 구성합니다. 필요한 포트가 방화벽 전체에서 열리는지 확인합니다. vSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 [이 VMware 기술 자료 문서를](https://kb.vmware.com/s/article/2087769) 참조하십시오.

vSphere 복제 6.5에 대한 자세한 설치 지침은 VMware 문서 [VMware vSphere 복제 6.5 설치 및 구성을](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)참조하십시오.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI: vRA 및 SRM 설치를 위해 프라이빗 클라우드에서 사용자 만들기

CloudSimple 포털에서 권한을 에스컬레이션한 후 클라우드 소유자 사용자 자격 증명을 사용하여 vCenter에 로그인합니다.

vCenter에서 새 `srm-soln-admin`사용자를 만들고 vCenter의 관리자 그룹에 추가합니다.
vCenter에서 클라우드 소유자 사용자로 로그아웃하고 *srm-soln-admin* 사용자로 로그인합니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple 포털: vRA에 대한 방화벽 규칙 구성

방화벽 테이블 및 [규칙 설정에](firewall.md) 설명된 대로 방화벽 규칙을 구성하여 포트를 열어 다음 간의 통신을 활성화합니다.

* SRM 솔루션 네트워크및 vCenter 및 ESXi 호스트의 vRA를 관리 네트워크의 호스트로 합니다.
* 두 사이트에서 vRA 어플라이언스.

vSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 이 [VMware 기술 자료 문서를](https://kb.vmware.com/s/article/2087769) 참조하십시오.

### <a name="install-srm-server-in-your-on-premises-environment"></a>온-프레미스 환경에 SRM 서버 설치

시작하기 전에 다음을 확인합니다.

* vSphere 복제 어플라이언스는 온-프레미스 및 프라이빗 클라우드 환경에 설치됩니다.
* 두 사이트의 vSphere 복제 어플라이언스가 서로 연결되어 있습니다.
* 전제 조건 및 모범 사례에 대한 VMware 정보를 검토했습니다. SRM 6.5의 경우 [SRM 6.5에 대한](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)VMware 문서 필수 구성 조건 및 모범 사례를 참조할 수 있습니다.

VMware 설명서를 따라 이 [VMWare 문서에](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)설명된 대로 배포 모델 '플랫폼 서비스 컨트롤러당 하나의 vCenter 인스턴스가 있는 2사이트 토폴로지'에서 SRM 서버 설치를 수행합니다. SRM 6.5에 대한 설치 지침은 VMware 문서 [설치 사이트 복구 관리자에서](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)확인할 수 있습니다.

### <a name="install-srm-server-in-your-private-cloud"></a>프라이빗 클라우드에 SRM 서버 설치

시작하기 전에 다음을 확인합니다.

* vSphere 복제 어플라이언스는 온-프레미스 및 프라이빗 클라우드 환경에 설치됩니다.
* 두 사이트의 vSphere 복제 어플라이언스가 서로 연결되어 있습니다.
* 전제 조건 및 모범 사례에 대한 VMware 정보를 검토했습니다. SRM 6.5의 경우 [사이트 복구 관리자 6.5 서버 설치에 대한 필수 구성 프로그램 및 모범 사례를](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)참조할 수 있습니다.

다음 단계는 프라이빗 클라우드 SRM 설치에 대해 설명합니다.

1. [vCenter UI: SRM 설치](#vcenter-ui-install-srm)
2. [CloudSimple 포털: SRM에 대한 방화벽 규칙 구성](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: SRM 구성](#vcenter-ui-configure-srm)
4. [CloudSimple 포털: 에스컬레이션 해제 권한](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI: SRM 설치

srm-soln-admin 자격 증명을 사용하여 vCenter에 로그온한 후 VMware 설명서를 따라 이 [VMWare 문서에](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)설명된 대로 배포 모델 '플랫폼 서비스 컨트롤러당 하나의 vCenter 인스턴스가 있는 2사이트 토폴로지'에서 SRM 서버 설치를 수행합니다. SRM 6.5에 대한 설치 지침은 VMware 문서 [설치 사이트 복구 관리자에서](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)확인할 수 있습니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple 포털: SRM에 대한 방화벽 규칙 구성

방화벽 테이블 및 규칙 [설정에](firewall.md) 설명된 대로 방화벽 규칙을 구성하여 다음 간의 통신을 허용합니다.

프라이빗 클라우드의 SRM 서버 및 vCenter / PSC입니다.
두 사이트의 SRM 서버

vSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 [이 VMware 기술 자료 문서를](https://kb.vmware.com/s/article/2087769) 참조하십시오.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: SRM 구성

사설 클라우드에 SRM을 설치한 후 VMware 사이트 복구 관리자 설치 및 구성 가이드의 섹션에 설명된 대로 다음 작업을 수행합니다. SRM 6.5의 경우 VMware 문서 [설치 사이트 복구 관리자에서](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)지침을 사용할 수 있습니다.

1. 보호된 복구 사이트에 사이트 복구 관리자 서버 인스턴스를 연결합니다.
2. 원격 사이트 복구 관리자 서버 인스턴스에 대한 클라이언트 연결을 설정합니다.
3. 사이트 복구 관리자 라이센스 키를 설치합니다.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 포털: 에스컬레이션 해제 권한

권한 에스컬레이션 해제하려면 에스컬레이션 [해제 권한 입니다.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="ongoing-management-of-your-srm-solution"></a>SRM 솔루션의 지속적인 관리

프라이빗 클라우드 환경에서 vSphere 복제 및 SRM 소프트웨어를 완전히 제어할 수 있으며 필요한 소프트웨어 수명 주기 관리를 수행해야 합니다. vSphere 복제 또는 SRM을 업데이트하거나 업그레이드하기 전에 새 버전의 소프트웨어가 프라이빗 클라우드 vCenter 및 PSC와 호환되는지 확인합니다.

> [!NOTE]
> CloudSimple은 현재 관리형 DR 서비스를 제공하기 위한 옵션을 모색하고 있습니다. 

## <a name="multiple-replication-configuration"></a>다중 복제 구성

 [배열 기반 복제 및 vSphere 복제 기술을 SRM과](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) 동시에 사용할 수 있습니다. 그러나 별도의 VM 집합에 적용해야 합니다(지정된 VM은 배열 기반 복제 또는 vSphere 복제에 의해 보호될 수 있지만 둘 다 는 아님). 또한 CloudSimple 사이트는 여러 보호된 사이트의 복구 사이트로 구성할 수 있습니다. 다중 사이트 구성에 대한 자세한 내용은 [SRM 다중 사이트 옵션을](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) 참조하십시오.

## <a name="references"></a>참조

* [VMware 사이트 복구 관리자 설명서](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [사이트 복구 관리자의 운영 제한 6.5](https://kb.vmware.com/s/article/2147110)
* [vSphere 복제에 대한 대역폭 요구 사항 계산](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [vSphere 복제 6.5를 배포할 때 OVF 선택 사항](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere 복제 6.5 설치 및 구성](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5에 대한 전제 조건 및 모범 사례](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [플랫폼 서비스 컨트롤러당 하나의 vCenter 서버 인스턴스가 있는 2사이트 토폴로지의 사이트 복구 관리자](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware 사이트 복구 관리자 6.5 설치 및 구성 가이드](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [배열 기반 복제 와 vSphere 복제를 가진 SRM에 VMware 블로그](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [SRM 멀티 사이트 옵션에 대한 VMware 블로그](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [vSphere 복제 5.8.x, 6.x 및 8에 대해 열려 있어야 하는 포트 번호](https://kb.vmware.com/s/article/2147112)
