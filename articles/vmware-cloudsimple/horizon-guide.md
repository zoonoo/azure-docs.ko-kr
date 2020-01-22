---
title: CloudSimple 별 Azure VMware 솔루션-사설 클라우드 사이트를 사용 하 여 VMware 수평을 통해 가상 데스크톱 인프라 호스팅
description: CloudSimple 사설 클라우드 사이트를 사용 하 여 VMware 수평을 통해 가상 데스크톱 인프라를 호스트 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314711"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>CloudSimple 사설 클라우드 사이트를 사용 하 여 VMware 수평으로 가상 데스크톱 인프라 호스팅

CloudSimple 사설 클라우드 사이트를 사용 하 여 VMware 수평 4.x를 사용 하 여 VDI (가상 데스크톱 인프라)를 호스트할 수 있습니다. 다음 그림은 VDI의 논리적 솔루션 아키텍처를 보여 줍니다.

![수평 배포](media/horizon-deployment.png)

이 솔루션을 사용 하면 수평 보기 관리자와 앱 볼륨을 완벽 하 게 제어할 수 있습니다. 친숙 한 UI, API 및 CLI 인터페이스를 사용 하면 기존 스크립트 및 도구를 사용할 수 있습니다.

CloudSimple 솔루션을 사용 하려면 다음을 수행 해야 합니다.

* 사설 클라우드에서 VMware 수평 4.x를 설치, 구성 및 관리 합니다.
* 사용자 고유의 가로 라이선스를 제공 합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

다음 섹션에서는 사설 클라우드에서 수평을 사용 하 여 VDI 솔루션을 배포 하는 방법을 설명 합니다.

1. [VMware 제품 버전이 호환 되는지 확인 합니다.](#verify-that-vmware-product-versions-are-compatible)
2. [데스크톱 환경의 크기 예측](#estimate-the-size-of-your-desktop-environment)
3. [사용자 환경에 맞는 사설 클라우드 만들기](#create-a-private-cloud-for-your-environment)
4. [사설 클라우드에 VMware 수평 설치](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware 제품 버전이 호환 되는지 확인 합니다.

* 현재 및 계획 된 버전의 수평, 앱 볼륨, 통합 액세스 게이트웨이 및 사용자 환경 관리자가 사설 클라우드의 vCenter 및 PSC와 호환 되는지 확인 합니다. 호환성 정보는 [수평 7.5에 대 한 VMware 호환성 매트릭스](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)를 참조 하세요.
* 사설 클라우드에서 vCenter 및 PSC의 현재 버전을 확인 하려면 [Cloudsimple 포털](access-cloudsimple-portal.md)의 **리소스로** 이동 하 여 사설 클라우드를 선택 하 고 **vsphere 관리 네트워크** 탭을 클릭 합니다.

![vCenter 및 PSC 버전](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>데스크톱 환경의 크기 예측

* 식별 된 구성이 VMware 작동 제한 내에 있는지 확인 합니다.
* 모든 데스크톱 및 수평 관리 구성 요소에 필요한 리소스를 예측 합니다.

### <a name="create-a-private-cloud-for-your-environment"></a>사용자 환경에 맞는 사설 클라우드 만들기

1. [사설 클라우드 환경 구성](quickstart-create-private-cloud.md)의 지침에 따라 cloudsimple 포털에서 사설 클라우드를 만듭니다.  CloudSimple은 새로 만든 모든 사설 클라우드에서 ' cloudowner ' 라는 기본 vCenter 사용자를 만듭니다. 기본 사설 클라우드 사용자 및 사용 권한 모델에 대 한 자세한 내용은 [사설 클라우드 권한 모델 알아보기](learn-private-cloud-permissions.md)를 참조 하세요.
2. 사설 클라우드에서 관리 평면에 대 한 VLAN을 만들어 서브넷 CIDR에 할당 합니다. 자세한 내용은 [vlan/서브넷 만들기 및 관리](create-vlan-subnet.md)를 참조 하세요. 모든 솔루션 구성 요소 (통합 액세스 게이트웨이, 연결 서버, 앱 볼륨 서버 및 사용자 환경 관리자 서버)가 설치 되는 네트워크입니다.
3. 사설 클라우드 vCenter에서 외부 id 공급자를 사용할 것인지 결정 합니다. 그렇다면 다음 옵션 중 하나를 선택 합니다.
    * 온-프레미스 Active Directory를 외부 id 공급자로 사용 합니다. 지침은 [VCenter Id 원본](set-vcenter-identity.md)을 참조 하세요.
    * 외부 id 공급자로 사용할 규모 관리 평면 VLAN의 사설 클라우드에서 Active Directory 서버를 설정 합니다. 지침은 [VCenter Id 원본](set-vcenter-identity.md)을 참조 하세요.
    * 사설 클라우드의 수평 관리 평면 VLAN에 DHCP 및 DNS 서버를 설정 합니다. 지침은 [CloudSimple 사설 클라우드에서 DNS 및 DHCP 응용 프로그램 및 작업 설정](dns-dhcp-setup.md)을 참조 하세요.
4. 사설 클라우드에 설치 된 DNS 서버에서 DNS 전달을 구성 합니다. 자세한 내용은 [조건부 전달자 만들기](on-premises-dns-setup.md#create-a-conditional-forwarder)를 참조 하세요.

### <a name="install-vmware-horizon-in-your-private-cloud"></a>사설 클라우드에 VMware 수평 설치

다음 배포 다이어그램은 사설 클라우드에 배포 된 수평 솔루션을 보여 줍니다. 통합 액세스 게이트웨이, AD/DC, 보기 및 앱 볼륨 서버는 사용자가 만든 VLAN 234에 설치 됩니다. 통합 액세스 게이트웨이에 인터넷에서 연결할 수 있는 할당 된 공용 IP 주소가 있습니다. 수평 데스크톱 풀 Vm은 추가 격리 및 보안을 제공 하기 위해 VLAN 235에 배포 됩니다.

![사설 클라우드의 수평 배포](media/horizon-private-cloud.png)

다음 섹션에서는 그림에 표시 된 것과 유사한 배포를 설정 하는 지침을 간략하게 설명 합니다. 시작 하기 전에 다음이 있는지 확인 합니다.

* 데스크톱 풀을 실행 하기에 충분 한 용량이 있는 CloudSimple 포털을 사용 하 여 만든 사설 클라우드.
* 데스크톱에 대 한 네트워크 트래픽을 지원 하기 위해 온-프레미스 환경과 사설 클라우드 환경 간의 대역폭이 충분 합니다.
* 온-프레미스 데이터 센터와 사설 클라우드 사이에서 사이트 간 VPN 터널을 설정 합니다.
* 온-프레미스 환경의 최종 사용자 서브넷에서 CloudSimple 사설 클라우드 서브넷으로의 IP 연결입니다.
* 사설 클라우드에 대해 설치 된 AD/DHCP/DNS.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple 포털: 데스크톱 풀에 대 한 전용 VLAN/서브넷 만들기

수평 데스크톱 풀에 대해 VLAN을 만들고 서브넷 CIDR을 할당 합니다. 자세한 내용은 [vlan/서브넷 만들기 및 관리](create-vlan-subnet.md)를 참조 하세요. 모든 데스크톱 가상 컴퓨터가 실행 되는 네트워크입니다.

표준 보안 모범 사례에 따라 수평 배포를 보호 합니다.

* 데스크톱 Vm에 대 한 데스크톱 RDP 트래픽/s s p 트래픽만 허용 합니다.
* 수평 관리 평면 VLAN과 데스크톱 풀 VLAN 간의 관리 트래픽만 허용 합니다.
* 온-프레미스 네트워크의 관리 트래픽만 허용 합니다.

CloudSimple 포털에서 [방화벽 규칙](firewall.md) 을 구성 하 여 이러한 모범 사례를 적용할 수 있습니다.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple 포털: 보안 수평 관리 평면에 대 한 방화벽 규칙 구성

CloudSimple 포털에서 다음 규칙을 설정 합니다. 지침은 [방화벽 테이블 및 규칙 설정](firewall.md)을 참조 하세요.

1. VMware 문서 [수평 포트 목록](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) 에 나열 된 네트워크 포트만 허용 되도록 Cloudsimple N-S 방화벽에서 방화벽 규칙을 구성 하 여 온-프레미스 서브넷과 수평 관리 VLAN 간의 통신을 허용 합니다.

2. 사설 클라우드의 수평 관리 VLAN과 데스크톱 풀 VLAN 간에 E-W 방화벽 규칙을 만듭니다.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple 포털: 통합 액세스 게이트웨이에 대 한 공용 IP 주소 만들기

통합 액세스 게이트웨이 어플라이언스에 대 한 공용 IP 주소를 만들어 인터넷에서 데스크톱 클라이언트 연결을 사용 하도록 설정 합니다. 자세한 내용은 [공용 IP 주소 할당](public-ips.md)을 참조 하세요.

설치가 완료 되 면 공용 IP 주소가 할당 되 고 공용 IP 페이지에 나열 됩니다.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple 포털: 권한 상승

기본 ' cloudowner ' 사용자에 게는 설치를 위한 사설 클라우드 vCenter의 충분 한 권한이 없어 사용자의 vCenter 권한을 에스컬레이션 해야 합니다. 자세한 내용은 [에스컬레이션 권한](escalate-private-cloud-privileges.md)을 참조 하세요.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI: 수평 설치를 위한 사설 클라우드에서 사용자 만들기

1. ' Cloudowner ' 사용자 자격 증명을 사용 하 여 vCenter에 로그인 합니다.
2. VCenter에서 새 사용자 ' 수평-고 n-관리자 '를 만들고 vCenter의 administrators 그룹에 사용자를 추가 합니다.
3. ' Cloudowner ' 사용자로 vCenter에서 로그 아웃 하 고 ' ' 사용자로 로그인 합니다.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: VMware 수평 설치

이전 논리적 아키텍처 섹션에서 설명한 것 처럼 수평 솔루션에는 다음과 같은 구성 요소가 있습니다.

* VMware 수평 보기
* VMware 통합 액세스 게이트웨이
* VMware 앱 볼륨 관리자
* VMware 사용자 환경 관리자

다음과 같이 구성 요소를 설치 합니다.

1. Vmware 문서 [Vmware 통합 액세스 게이트웨이 배포 및 구성](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)에 제공 된 지침에 따라 통합 액세스 게이트웨이를 설치 하 고 구성 합니다.

2. [설치 가이드 보기](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)의 지침에 따라 사설 클라우드에 수평 보기를 설치 합니다.

3. [VMware 앱 볼륨 설치 및 구성](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)의 지침에 따라 앱 볼륨 관리자를 설치 합니다.

4. [VMware 사용자 환경 관리자 설치](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)및 구성 정보의 지침에 따라 사용자 환경 관리자를 설치 하 고 구성 합니다.

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>VMware 수평 사전 패키지 앱 볼륨을 업로드 하기 위한 지원 요청 파일

설치 프로세스의 일부로, 앱 볼륨 관리자는 미리 패키지 된 볼륨을 사용 하 여 앱 스택과 쓰기 가능한 볼륨을 프로 비전 합니다. 이러한 볼륨은 앱 스택과 쓰기 가능한 볼륨의 템플릿으로 사용 됩니다.

사설 클라우드 데이터 저장소에 볼륨을 업로드 하려면 ESXi 루트 암호가 필요 합니다. 지원을 받으려면 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출 하세요. CloudSimple 지원 담당자가 개인 클라우드 환경에 템플릿을 업로드할 수 있도록 AppVolumes installer 번들을 연결 합니다.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple 포털: 에스컬레이션 권한

이제 ' cloudowner ' 사용자의 [권한을 에스컬레이션](escalate-private-cloud-privileges.md#de-escalate-privileges) 해제할 수 있습니다.

## <a name="ongoing-management-of-your-horizon-solution"></a>수평 솔루션의 지속적인 관리

사설 클라우드 환경에서 수평 및 앱 볼륨 관리자 소프트웨어를 완전히 제어할 수 있으며 필요한 소프트웨어 수명 주기 관리를 수행할 것으로 예상 됩니다. 수평 또는 앱 볼륨을 업데이트 하거나 업그레이드 하기 전에 새 버전의 소프트웨어가 사설 클라우드 vCenter 및 PSC와 호환 되는지 확인 합니다.
