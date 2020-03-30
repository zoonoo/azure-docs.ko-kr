---
title: 클라우드간이별 Azure VMware 솔루션 - 프라이빗 클라우드 사이트를 사용하여 VMware Horizon을 사용하여 가상 데스크톱 인프라를 호스팅합니다.
description: 클라우드Simple 프라이빗 클라우드 사이트를 사용하여 VMware Horizon을 사용하여 가상 데스크톱 인프라를 호스팅하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025251"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>클라우드심플 프라이빗 클라우드 사이트를 사용하여 VMware Horizon을 사용하여 가상 데스크톱 인프라 호스팅

클라우드심플 프라이빗 클라우드 사이트를 사용하여 VMware Horizon 7.x를 사용하여 가상 데스크톱 인프라(VDI)를 호스팅할 수 있습니다. 다음 그림은 VDI에 대한 논리적 솔루션 아키텍처를 보여 주며 있습니다.

![호라이즌 배포](media/horizon-deployment.png)

이 솔루션을 사용하면 Horizon View 관리자 및 앱 볼륨을 완전히 제어할 수 있습니다. 익숙한 UI, API 및 CLI 인터페이스를 사용하면 기존 스크립트와 도구를 사용할 수 있습니다.

CloudSimple 솔루션에서는 다음을 수행해야 합니다.

* 프라이빗 클라우드에서 VMware Horizon 7.x를 설치, 구성 및 관리합니다.
* 고유한 Horizon 라이선스를 제공합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

다음 섹션에서는 프라이빗 클라우드에서 Horizon을 사용하여 VDI 솔루션을 배포하는 방법을 설명합니다.

1. [VMware 제품 버전이 호환되는지 확인](#verify-that-vmware-product-versions-are-compatible)
2. [데스크톱 환경의 크기 예측](#estimate-the-size-of-your-desktop-environment)
3. [사용자 환경을 위한 프라이빗 클라우드 만들기](#create-a-private-cloud-for-your-environment)
4. [프라이빗 클라우드에 VMware 호라이즌 설치](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware 제품 버전이 호환되는지 확인

* 현재 및 계획된 버전의 Horizon, 앱 볼륨, 통합 액세스 게이트웨이 및 사용자 환경 관리자가 서로 호환되는지, 프라이빗 클라우드의 vCenter 및 PSC와 호환되는지 확인합니다. 호환성 정보는 [Horizon 7.5에 대한 VMware 호환성 매트릭스를](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)참조하십시오.
* 프라이빗 클라우드에서 vCenter 및 PSC의 현재 버전을 확인하려면 [CloudSimple 포털의](access-cloudsimple-portal.md) **리소스로** 이동하여 프라이빗 클라우드를 선택하고 **vSphere 관리 네트워크** 탭을 클릭합니다.

![v센터 및 PSC 버전](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>데스크톱 환경의 크기 예측

* 식별된 구성이 VMware 작동 한계 내에 있는지 확인합니다.
* 모든 데스크톱및 Horizon 관리 구성 요소에 필요한 리소스를 예측합니다.

### <a name="create-a-private-cloud-for-your-environment"></a>사용자 환경을 위한 프라이빗 클라우드 만들기

1. 프라이빗 클라우드 환경 구성의 지침에 따라 CloudSimple 포털에서 [프라이빗 클라우드를](quickstart-create-private-cloud.md)만듭니다.  CloudSimple은 새로 만든 모든 프라이빗 클라우드에서 '클라우드 소유자'라는 기본 vCenter 사용자를 만듭니다. 기본 프라이빗 클라우드 사용자 및 권한 모델에 대한 자세한 내용은 [사설 클라우드 권한 모델 알아보기를](learn-private-cloud-permissions.md)참조하십시오.
2. 프라이빗 클라우드에서 Horizon 관리 평면에 VLAN을 만들고 서브넷 CIDR을 할당합니다. 지침은 [VLAN/서브넷 만들기 및 관리를](create-vlan-subnet.md)참조하십시오. 이 네트워크는 모든 솔루션 구성 요소(통합 액세스 게이트웨이, 연결 서버, 앱 볼륨 서버 및 사용자 환경 관리자 서버)가 설치되는 네트워크입니다.
3. 프라이빗 클라우드 vCenter에서 외부 ID 공급자를 사용할지 여부를 결정합니다. 그렇다면 다음 옵션 중 하나를 선택합니다.
    * 온-프레미스 Active Directory를 외부 ID 공급자로 사용합니다. 지침은 [vCenter ID 소스를](set-vcenter-identity.md)참조하십시오.
    * Horizon 관리 평면 VLAN의 프라이빗 클라우드에 Active Directory 서버를 설정하여 외부 ID 공급자로 사용합니다. 지침은 [vCenter ID 소스를](set-vcenter-identity.md)참조하십시오.
    * 프라이빗 클라우드의 호라이즌 관리 평면 VLAN에 DHCP 및 DNS 서버를 설정합니다. 지침은 [CloudSimple 프라이빗 클라우드의 DNS 및 DHCP 애플리케이션 및 워크로드 설정을](dns-dhcp-setup.md)참조하십시오.
4. 프라이빗 클라우드에 설치된 DNS 서버에서 DNS 전달을 구성합니다. 지침은 [조건부 전달자 만들기](on-premises-dns-setup.md#create-a-conditional-forwarder)를 참조하십시오.

### <a name="install-vmware-horizon-in-your-private-cloud"></a>프라이빗 클라우드에 VMware 호라이즌 설치

다음 배포 다이어그램은 프라이빗 클라우드에 배포된 Horizon 솔루션을 설명합니다. 통합 액세스 게이트웨이, AD/DC, 보기 및 앱 볼륨 서버는 사용자가 만든 VLAN 234에 설치됩니다. 통합 액세스 게이트웨이에는 인터넷에서 연결할 수 있는 할당된 공용 IP 주소가 있습니다. Horizon 데스크톱 풀 VM은 VLAN 235에 배포되어 추가 격리 및 보안을 제공합니다.

![프라이빗 클라우드의 수평선 배포](media/horizon-private-cloud.png)

다음 섹션에서는 그림에 설명된 것과 유사한 배포를 설정하는 지침을 간략하게 설명합니다. 시작하기 전에 다음이 있는지 확인합니다.

* 데스크톱 풀을 실행하기에 충분한 용량의 CloudSimple 포털을 사용하여 만든 사설 클라우드입니다.
* 데스크톱의 네트워크 트래픽을 지원하기 위해 온-프레미스 환경과 프라이빗 클라우드 환경 간의 충분한 대역폭.
* 온-프레미스 데이터 센터와 프라이빗 클라우드 사이에 설정된 사이트 간 VPN 터널입니다.
* 온-프레미스 환경의 최종 사용자 서브넷에서 CloudSimple 프라이빗 클라우드 서브넷에 이르는 IP 도달 가능성.
* 프라이빗 클라우드용으로 설치된 AD/DHCP/DNS.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple 포털: 데스크톱 풀전용 전용 VLAN/서브넷 만들기

Horizon 데스크톱 풀에 대한 VLAN을 만들고 서브넷 CIDR을 할당합니다. 지침은 [VLAN/서브넷 만들기 및 관리를](create-vlan-subnet.md)참조하십시오. 모든 데스크톱 가상 시스템이 실행되는 네트워크입니다.

표준 보안 모범 사례를 따라 Horizon 배포를 보호합니다.

* 데스크톱 RDP 트래픽/SSH 트래픽만 데스크톱 VM에 허용합니다.
* 호라이즌 관리 평면 VLAN과 데스크톱 풀 VLAN 간의 관리 트래픽만 허용합니다.
* 온-프레미스 네트워크의 관리 트래픽만 허용합니다.

CloudSimple 포털에서 [방화벽 규칙을](firewall.md) 구성하여 이러한 모범 사례를 적용할 수 있습니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple 포털: 호라이즌 관리 평면을 보호하기 위해 방화벽 규칙을 구성합니다.

CloudSimple 포털에서 다음 규칙을 설정합니다. 지침은 방화벽 [테이블 및 규칙 설정을](firewall.md)참조하십시오.

1. 클라우드심플 N-S 방화벽의 방화벽 규칙을 구성하여 온-프레미스 서브넷과 Horizon 관리 VLAN 간의 통신을 허용하여 VMware 문서 [Horizon 포트 목록에](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) 나열된 네트워크 포트만 허용합니다.

2. 프라이빗 클라우드에서 Horizon 관리 VLAN과 데스크톱 풀 VLAN 간에 E-W 방화벽 규칙을 만듭니다.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>클라우드 심플 포털: 통합 액세스 게이트웨이를 위한 공용 IP 주소 만들기

통합 액세스 게이트웨이 어플라이언스에 대한 공용 IP 주소를 만들어 인터넷에서 데스크톱 클라이언트 연결을 활성화합니다. 지침은 공용 [IP 주소 할당을](public-ips.md)참조하십시오.

설정이 완료되면 공용 IP 주소가 할당되고 공용 IP 페이지에 나열됩니다.

#### <a name="cloudsimple-portal-escalate-privileges"></a>클라우드 심플 포털: 권한 에스컬레이션

기본 '클라우드 소유자' 사용자는 Horizon을 설치하기 위해 프라이빗 클라우드 vCenter에 충분한 권한이 없으므로 사용자의 vCenter 권한을 에스컬레이션해야 합니다. 자세한 내용은 [권한 에스컬레이션 을](escalate-private-cloud-privileges.md)참조하십시오.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI: 수평선 설치를 위한 프라이빗 클라우드에서 사용자 만들기

1. '클라우드 소유자' 사용자 자격 증명을 사용하여 vCenter에 로그인합니다.
2. vCenter에서 새 사용자인 'horizon-soln-admin'을 만들고 vCenter의 관리자 그룹에 사용자를 추가합니다.
3. vCenter에서 '클라우드 소유자' 사용자로 로그아웃하고 'horizon-soln-admin' 사용자로 로그인합니다.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: VM웨어 호라이즌 설치

이전 논리 아키텍처 섹션에서 설명한 대로 Horizon 솔루션에는 다음과 같은 구성 요소가 있습니다.

* VMware 호라이즌 뷰
* VMware 통합 액세스 게이트웨이
* VMware 앱 볼륨 관리자
* VMware 사용자 환경 관리자

다음과 같이 구성 요소를 설치합니다.

1. VMware 문서 배포 및 구성 VMware 통합 액세스 게이트웨이에 제공된 지침에 따라 통합 [액세스 게이트웨이를](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)설치하고 구성합니다.

2. [보기 설치 가이드의](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)지침에 따라 프라이빗 클라우드에서 수평선 보기를 설치합니다.

3. [설치 및 VMware 앱 볼륨 구성의](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)지침에 따라 앱 볼륨 관리자를 설치합니다.

4. [VMware 사용자](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)환경 관리자 의 설치 및 구성 에 대한 지침에 따라 사용자 환경 관리자를 설치하고 구성합니다.

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>VMware Horizon 사전 패키지 앱 볼륨 업로드 지원 요청 제출

설치 프로세스의 일부로 앱 볼륨 관리자는 미리 패키지된 볼륨을 사용하여 앱 스택 및 쓰기 가능한 볼륨을 프로비저닝합니다. 이러한 볼륨은 앱 스택 및 쓰기 볼륨에 대한 템플릿역할을 합니다.

볼륨을 프라이빗 클라우드 데이터 스토어에 업로드하려면 ESXi 루트 암호가 필요합니다. 도움이 필요하면 [지원 요청을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)제출하십시오. CloudSimple 지원 담당자가 템플릿을 프라이빗 클라우드 환경에 업로드할 수 있도록 AppVolumes 설치 관리자 번들을 연결합니다.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 포털: 에스컬레이션 해제 권한

이제 '클라우드 소유자' 사용자의 [권한을 해제할](escalate-private-cloud-privileges.md#de-escalate-privileges) 수 있습니다.

## <a name="ongoing-management-of-your-horizon-solution"></a>Horizon 솔루션의 지속적인 관리

프라이빗 클라우드 환경에서 Horizon 및 App Volume Manager 소프트웨어를 완전히 제어할 수 있으며 필요한 소프트웨어 수명 주기 관리를 수행해야 합니다. Horizon 또는 앱 볼륨을 업데이트하거나 업그레이드하기 전에 새 버전의 소프트웨어가 프라이빗 클라우드 vCenter 및 PSC와 호환되는지 확인합니다.
