---
title: CloudSimple의 Azure VMware 솔루션-VMware Site Recovery Manager를 사용 하 여 사설 클라우드를 재해 복구 사이트로 설정
description: CloudSimple 사설 클라우드를 온-프레미스 VMware 워크 로드에 대 한 재해 복구 사이트로 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77565930"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>VMware Site Recovery Manager를 사용 하 여 사설 클라우드를 재해 복구 대상으로 설정

CloudSimple 사설 클라우드를 온-프레미스 VMware 워크 로드를 위한 DR (재해 복구) 사이트로 사용할 수 있습니다.

DR 솔루션은 vSphere 복제 및 SRM (VMware Site Recovery Manager)을 기반으로 합니다. 온-프레미스 복구 사이트에서 보호 하는 기본 사이트로 사설 클라우드를 사용 하도록 설정 하기 위해 유사한 접근 방식을 사용할 수 있습니다.

CloudSimple 솔루션:

* DR에 대 한 데이터 센터를 설정 하지 않아도 됩니다.
* 전 세계 지리적 복원 력을 위해 CloudSimple이 배포 되는 Azure 위치를 활용할 수 있습니다.
* 에서는 배포 비용과 DR 설정에 대 한 총 소유 비용을 줄일 수 있는 옵션을 제공 합니다.

CloudSimple 솔루션을 사용 하려면 다음을 수행 해야 합니다.

* 사설 클라우드에서 vSphere 복제 및 SRM을 설치, 구성 및 관리 합니다.
* 사설 클라우드가 보호 된 사이트인 경우 SRM에 대 한 사용자 고유의 라이선스를 제공 합니다. CloudSimple 사이트를 복구 사이트로 사용 하는 경우 추가 SRM 라이선스가 필요 하지 않습니다.

이 솔루션을 사용 하면 vSphere 복제 및 SRM을 완벽 하 게 제어할 수 있습니다. 친숙 한 UI, API 및 CLI 인터페이스를 사용 하면 기존 스크립트 및 도구를 사용할 수 있습니다.

![Site Recovery Manager 배포](media/srm-deployment.png)

사설 클라우드 및 온-프레미스 환경과 호환 되는 모든 버전의 vRA 및 SRM을 사용할 수 있습니다. 이 가이드의 예제에서는 vRA 6.5 및 SRM 6.5을 사용 합니다. 이러한 버전은 CloudSimple에서 지원 되는 vSphere 6.5와 호환 됩니다.

## <a name="deploy-the-solution"></a>솔루션 배포

다음 섹션에서는 개인 클라우드에서 SRM을 사용 하 여 DR 솔루션을 배포 하는 방법을 설명 합니다.

1. [VMware 제품 버전이 호환 되는지 확인 합니다.](#verify-that-vmware-product-versions-are-compatible)
2. [DR 환경의 크기 예측](#estimate-the-size-of-your-dr-environment)
3. [사용자 환경에 맞는 사설 클라우드 만들기](#create-a-private-cloud-for-your-environment)
4. [SRM 솔루션에 대 한 사설 클라우드 네트워킹 설정](#set-up-private-cloud-networking-for-the-srm-solution)
5. [온-프레미스 네트워크와 사설 클라우드 간에 사이트 간 VPN 연결을 설정 하 고 필요한 포트를 엽니다.](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [사설 클라우드에서 인프라 서비스 설정](#set-up-infrastructure-services-in-your-private-cloud)
7. [온-프레미스 환경에서 vSphere 복제 어플라이언스 설치](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [사설 클라우드 환경에 vSphere 복제 어플라이언스 설치](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [온-프레미스 환경에 SRM 서버 설치](#install-srm-server-in-your-on-premises-environment)
10. [개인 클라우드에 SRM 서버 설치](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware 제품 버전이 호환 되는지 확인 합니다.

이 가이드의 구성에는 다음과 같은 호환성 요구 사항이 적용 됩니다.

* 동일한 버전의 SRM이 사설 클라우드 및 온-프레미스 환경에 배포 되어야 합니다.
* 동일한 버전의 vSphere 복제를 사설 클라우드 및 온-프레미스 환경에 배포 해야 합니다.
* 사설 클라우드와 온-프레미스 환경의 플랫폼 서비스 컨트롤러 (PSC) 버전이 호환 되어야 합니다.
* 사설 클라우드와 온-프레미스 환경의 vCenter 버전이 호환 되어야 합니다.
* SRM 및 vSphere 대 한 복제의 버전은 PSC 및 vCenter 버전과 호환 되어야 합니다.

관련 VMware 설명서 및 호환성 정보에 대 한 링크를 보려면 [VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) 설명서로 이동 하세요.

사설 클라우드에서 vCenter 및 PSC의 버전을 확인 하려면 CloudSimple 포털을 엽니다. **리소스**로 이동 하 여 사설 클라우드를 선택 하 고 **Vsphere 관리 네트워크** 탭을 클릭 합니다.

![사설 클라우드의 vCenter & PSC 버전](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>DR 환경의 크기 예측

1. 식별 된 온-프레미스 구성이 지원 되는 제한 범위 내에 있는지 확인 합니다. SRM 6.5에 대 한 제한은 VMware 기술 자료 문서 [Site Recovery Manager 6.5에 대 한 운영 제한](https://kb.vmware.com/s/article/2147110)사항에 설명 되어 있습니다.
2. 작업 크기 및 RPO 요구 사항을 충족 하기에 충분 한 네트워크 대역폭이 있는지 확인 합니다. [VSphere 복제에 대 한 대역폭 요구 사항을 계산](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) 하는 VMware 기술 자료 문서는 대역폭 제한에 대 한 지침을 제공 합니다.
3. CloudSimple sizer 도구를 사용 하 여 온-프레미스 환경을 보호 하기 위해 DR 사이트에서 필요한 리소스를 예상할 수 있습니다.

### <a name="create-a-private-cloud-for-your-environment"></a>사용자 환경에 맞는 사설 클라우드 만들기

[사설 클라우드 만들기](create-private-cloud.md)의 지침 및 크기 조정 권장 사항에 따라 cloudsimple 포털에서 사설 클라우드를 만듭니다.

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>SRM 솔루션에 대 한 사설 클라우드 네트워킹 설정

CloudSimple 포털에 액세스 하 여 SRM 솔루션에 대 한 사설 클라우드 네트워킹을 설정 합니다.

SRM 솔루션 네트워크에 대 한 VLAN을 만들고 서브넷 CIDR에 할당 합니다. 자세한 내용은 [vlan/서브넷 만들기 및 관리](create-vlan-subnet.md)를 참조 하세요.

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>온-프레미스 네트워크와 사설 클라우드 간에 사이트 간 VPN 연결을 설정 하 고 필요한 포트를 엽니다.

온-프레미스 네트워크와 사설 클라우드 사이에서 사이트 간 연결을 설정 합니다. 지침은 [CloudSimple 사설 클라우드에 대 한 VPN 연결 구성](set-up-vpn.md)을 참조 하세요.

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>사설 클라우드에서 인프라 서비스 설정

워크 로드 및 도구를 쉽게 관리할 수 있도록 사설 클라우드에서 인프라 서비스를 구성 합니다.

다음 중 하나를 수행 하려는 경우 [AZURE AD를 CloudSimple 사설 클라우드에서 vCenter 용 id 공급자로 사용](azure-ad.md) 에 설명 된 대로 외부 id 공급자를 추가할 수 있습니다.

* 사설 클라우드의 온-프레미스 AD (Active Directory)에서 사용자를 식별 합니다.
* 모든 사용자에 대해 사설 클라우드에서 AD를 설정 합니다.
* Azure AD를 사용 합니다.

사설 클라우드의 워크 로드에 대 한 IP 주소 조회, IP 주소 관리 및 이름 확인 서비스를 제공 하려면 [CloudSimple 사설 클라우드에서 DNS 및 dhcp 응용 프로그램 및 워크 로드 설정](dns-dhcp-setup.md)에 설명 된 대로 DHCP 및 dns 서버를 설정 합니다.

*. Cloudsimple.io 도메인은 사설 클라우드의 관리 Vm 및 호스트에서 사용 됩니다. 이 도메인에 대 한 요청을 해결 하려면 [조건부 전달자 만들기](on-premises-dns-setup.md#create-a-conditional-forwarder)에 설명 된 대로 dns 서버에서 dns 전달을 구성 합니다.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>온-프레미스 환경에서 vSphere 복제 어플라이언스 설치

VMware 설명서에 따라 온-프레미스 환경에 vSphere 복제 어플라이언스 (Vsphere)를 설치 합니다. 설치는 다음과 같은 개략적인 단계로 구성 됩니다.

1. VRA 설치를 위한 온-프레미스 환경을 준비 합니다.

2. Vmware.com에서 VR ISO의를 사용 하 여 온-프레미스 환경에서 vRA를 배포 합니다. VRA 6.5의 경우 [이 VMware 블로그에서](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) 관련 정보가 있습니다.

3. 온-프레미스 사이트에서 vCenter Single Sign-on을 사용 하 여 온-프레미스 vRA를 등록 합니다. VSphere 복제 6.5에 대 한 자세한 지침은 VMware 문서 [VMware vSphere replication 6.5 설치 및 구성](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)을 참조 하세요.

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>사설 클라우드 환경에 vSphere 복제 어플라이언스 설치

시작 하기 전에 다음이 있는지 확인 합니다.

* 온-프레미스 환경의 서브넷에서 사설 클라우드의 관리 서브넷으로의 IP 연결
* 온-프레미스 vSphere 환경에 있는 복제 서브넷에서 사설 클라우드의 SRM 솔루션 서브넷으로의 IP 연결

지침은 [CloudSimple 사설 클라우드에 대 한 VPN 연결 구성](set-up-vpn.md)을 참조 하세요. 이러한 단계는 온-프레미스 설치와 비슷합니다.

CloudSimple은 vRA 및 SRM을 설치 하는 동안 IP 주소 대신 Fqdn을 사용 하는 것이 좋습니다. 사설 클라우드에서 vCenter 및 PSC의 FQDN을 확인 하려면 CloudSimple 포털을 엽니다. **리소스**로 이동 하 여 사설 클라우드를 선택 하 고 **Vsphere 관리 네트워크** 탭을 클릭 합니다.

![사설 클라우드에서 vCenter/PSC의 FQDN 찾기](media/srm-resources.png)

CloudSimple을 사용 하려면 기본 ' cloudowner ' 사용자를 사용 하 여 vRA 및 SRM을 설치 하지 말고 새 사용자를 만들어야 합니다. 사설 클라우드 vCenter 환경의 가동 시간 및 가용성을 보장 하기 위해 수행 됩니다. 그러나 사설 클라우드 vCenter does't의 기본 cloudowner 사용자에 게는 관리자 권한으로 새 사용자를 만들 수 있는 충분 한 권한이 있습니다.

VRA 및 SRM을 설치 하기 전에 cloudowner 사용자의 vCenter 권한을 에스컬레이션 하 고 vCenter SSO 도메인에서 관리 권한이 있는 사용자를 만들어야 합니다. 기본 사설 클라우드 사용자 및 사용 권한 모델에 대 한 자세한 내용은 [사설 클라우드 권한 모델 알아보기](learn-private-cloud-permissions.md)를 참조 하세요.

설치는 다음과 같은 개략적인 단계로 구성 됩니다.

1. [권한](escalate-private-cloud-privileges.md)상승
2. 사설 클라우드에서 vSphere 복제 및 SRM 설치를 위한 사용자를 만듭니다. [VCENTER UI: vRA & SRM 설치를 위한 사설 클라우드에서 사용자 만들기](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)에 설명 되어 있습니다.
3. VRA 설치를 위한 사설 클라우드 환경을 준비 합니다.
4. Vmware.com에서 VR ISO의를 사용 하 여 사설 클라우드에 vRA를 배포 합니다. VRA 6.5의 경우 [이 VMware 블로그](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) 는 관련 정보를 포함 합니다.
5. VRA에 대 한 방화벽 규칙을 구성 합니다. [Cloudsimple 포털: vRA에 대 한 방화벽 규칙 구성](#cloudsimple-portal-configure-firewall-rules-for-vra)에서 아래에 설명 되어 있습니다.
6. 사설 클라우드 사이트에서 vCenter Single Sign-on을 사용 하 여 사설 클라우드 vRA를 등록 합니다.
7. 두 어플라이언스 간의 vSphere 복제 연결을 구성 합니다. 필요한 포트가 방화벽에서 열려 있는지 확인 합니다. VSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 [VMware 기술 자료 문서](https://kb.vmware.com/s/article/2087769) 를 참조 하세요.

VSphere 복제 6.5에 대 한 자세한 설치 지침은 VMware 문서 [VMware vSphere replication 6.5 설치 및 구성](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)을 참조 하세요.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI: vRA 및 SRM 설치를 위한 사설 클라우드에서 사용자 만들기

CloudSimple 포털에서 권한을 에스컬레이션 한 후 cloudowner 사용자 자격 증명을 사용 하 여 vCenter에 로그인 합니다.

VCenter에서 새 사용자를 만들고 `srm-soln-admin` vcenter의 administrators 그룹에 추가 합니다.
Cloudowner 사용자로 vCenter에서 로그 아웃 하 고 *srm-* 사용자로 로그인 합니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple 포털: vRA에 대 한 방화벽 규칙 구성

[방화벽 테이블 설정 및](firewall.md) 포트를 여는 규칙 설정에서 설명한 대로 방화벽 규칙을 구성 하 여 다음 사이에서 통신을 사용 하도록 설정 합니다.

* 관리 네트워크의 SRM 솔루션 네트워크 및 vCenter 및 ESXi 호스트에서 vRA.
* 두 사이트의 vRA 어플라이언스.

VSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 [VMware 기술 자료 문서](https://kb.vmware.com/s/article/2087769) 를 참조 하세요.

### <a name="install-srm-server-in-your-on-premises-environment"></a>온-프레미스 환경에 SRM 서버 설치

시작 하기 전에 다음을 확인 합니다.

* vSphere 복제 어플라이언스는 온-프레미스 및 사설 클라우드 환경에 설치 됩니다.
* 두 사이트의 vSphere 복제 어플라이언스는 서로 연결 되어 있습니다.
* 필수 조건 및 모범 사례에 대 한 VMware 정보를 검토 했습니다. SRM 6.5의 경우, [srm 6.5에 대 한 VMware 문서 필수 조건 및 모범 사례](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)를 참조할 수 있습니다.

Vmware 설명서에 따라이 [vmware 문서](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)에 설명 된 대로 배포 모델의 두 사이트 토폴로지 (플랫폼 서비스 컨트롤러 당 하나의 vCenter 인스턴스 포함)에서 SRM 서버 설치를 수행 합니다. SRM 6.5에 대 한 설치 지침은 [Site Recovery Manager를 설치](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)하는 VMware 문서에서 사용할 수 있습니다.

### <a name="install-srm-server-in-your-private-cloud"></a>개인 클라우드에 SRM 서버 설치

시작 하기 전에 다음을 확인 합니다.

* vSphere 복제 어플라이언스는 온-프레미스 및 사설 클라우드 환경에 설치 됩니다.
* 두 사이트의 vSphere 복제 어플라이언스는 서로 연결 되어 있습니다.
* 필수 조건 및 모범 사례에 대 한 VMware 정보를 검토 했습니다. SRM 6.5의 경우 [Site Recovery Manager 6.5 서버 설치에 대 한 필수 구성 요소 및 모범 사례](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)를 참조할 수 있습니다.

다음 단계에서는 사설 클라우드 SRM 설치에 대해 설명 합니다.

1. [vCenter UI: SRM 설치](#vcenter-ui-install-srm)
2. [CloudSimple 포털: SRM에 대 한 방화벽 규칙 구성](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: SRM 구성](#vcenter-ui-configure-srm)
4. [CloudSimple 포털: 에스컬레이션 권한](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI: SRM 설치

Srm-고-관리자 자격 증명을 사용 하 여 vCenter에 로그인 한 후 VMware [문서](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)에 설명 된 대로 vmware 설명서에 따라 배포 모델 ' 플랫폼 서비스 컨트롤러 당 하나의 vCenter 인스턴스를 사용 하는 2 사이트 토폴로지 '의 srm 서버 설치를 수행 합니다. SRM 6.5에 대 한 설치 지침은 [Site Recovery Manager를 설치](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)하는 VMware 문서에서 사용할 수 있습니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple 포털: SRM에 대 한 방화벽 규칙 구성

다음 간의 통신을 허용 하도록 [방화벽 테이블 및 규칙 설정](firewall.md) 에 설명 된 대로 방화벽 규칙을 구성 합니다.

사설 클라우드의 SRM 서버 및 vCenter/PSC
두 사이트의 SRM 서버

VSphere 복제 6.5에 대해 열려 있어야 하는 포트 번호 목록은 [VMware 기술 자료 문서](https://kb.vmware.com/s/article/2087769) 를 참조 하세요.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: SRM 구성

SRM이 사설 클라우드에 설치 된 후 VMware Site Recovery Manager 설치 및 구성 가이드의 섹션에 설명 된 대로 다음 작업을 수행 합니다. SRM 6.5의 경우 [Site Recovery Manager를 설치](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)하는 VMware 문서에서 지침을 사용할 수 있습니다.

1. 보호 된 사이트와 복구 사이트에서 Site Recovery Manager 서버 인스턴스를 연결 합니다.
2. 원격 Site Recovery 관리자 서버 인스턴스에 대 한 클라이언트 연결을 설정 합니다.
3. Site Recovery Manager 라이선스 키를 설치 합니다.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 포털: 에스컬레이션 권한

권한을 에스컬레이션 하려면 [권한 승격](escalate-private-cloud-privileges.md#de-escalate-privileges)을 참조 하세요.

## <a name="ongoing-management-of-your-srm-solution"></a>SRM 솔루션의 지속적인 관리

사설 클라우드 환경에서 vSphere 복제 및 SRM 소프트웨어를 완전히 제어할 수 있으며 필요한 소프트웨어 수명 주기 관리를 수행할 것으로 예상 됩니다. VSphere 복제 또는 SRM을 업데이트 하거나 업그레이드 하기 전에 새 버전의 소프트웨어가 사설 클라우드 vCenter 및 PSC와 호환 되는지 확인 합니다.

> [!NOTE]
> CloudSimple은 현재 관리 되는 DR 서비스를 제공 하기 위한 옵션을 탐색 합니다. 

## <a name="multiple-replication-configuration"></a>다중 복제 구성

 [배열 기반 복제와 vSphere 복제 기술은 동시에 SRM과 함께 사용할 수 있습니다](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) . 그러나 별도의 Vm 집합에 적용 해야 합니다. 지정 된 VM은 배열 기반 복제 또는 vSphere 복제를 통해 보호 될 수 있지만 둘 다로 보호할 수는 없습니다. 또한 CloudSimple 사이트는 여러 보호 된 사이트에 대 한 복구 사이트로 구성 될 수 있습니다. 다중 사이트 구성에 대 한 자세한 내용은 [SRM 다중 사이트 옵션](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) 을 참조 하세요.

## <a name="references"></a>참조

* [VMware Site Recovery Manager 설명서](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Site Recovery Manager 6.5에 대 한 운영 제한](https://kb.vmware.com/s/article/2147110)
* [VSphere 복제에 대 한 대역폭 요구 사항 계산](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [VSphere 복제 6.5을 배포할 때의을 선택 합니다.](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere 복제 6.5 설치 및 구성](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [SRM 6.5에 대 한 사전 요구 사항 및 모범 사례](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [두 사이트 토폴로지의 Site Recovery Manager (플랫폼 서비스 컨트롤러 당 하나의 vCenter Server 인스턴스 포함)](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 설치 및 구성 가이드](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [배열 기반 복제와 vSphere 복제 하는 SRM의 VMware 블로그](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [SRM 다중 사이트 옵션에 대 한 VMware 블로그](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [VSphere 복제 5.8 x, 6.x 및 8에 대해 열려 있어야 하는 포트 번호](https://kb.vmware.com/s/article/2147112)
