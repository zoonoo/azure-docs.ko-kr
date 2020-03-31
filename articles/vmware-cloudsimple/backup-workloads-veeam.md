---
title: 클라우드에 의한 Azure VMware 솔루션 - Veeam을 사용하여 사설 클라우드에서 워크로드 가상 머신 백업
description: Veeam B&R 9.5를 사용하여 Azure 기반 CloudSimple 프라이빗 클라우드에서 실행 중인 가상 컴퓨터를 백업하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025132"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Veeam B&R을 사용하여 CloudSimple 프라이빗 클라우드에서 워크로드 VM 백업

이 가이드에서는 Veeam B&R 9.5를 사용하여 Azure 기반 CloudSimple 프라이빗 클라우드에서 실행 중인 가상 컴퓨터를 백업하는 방법에 대해 설명합니다.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Veeam 백업 및 복구 솔루션 소개

Veeam 솔루션에는 다음 구성 요소가 포함됩니다.

**백업 서버**

백업 서버는 Veeam의 제어 센터 역할을 하며 다음 기능을 수행하는 Windows 서버(VM)입니다. 

* 백업, 복제, 복구 확인 및 복원 작업을 조정합니다.
* 작업 일정 및 리소스 할당 제어
* 백업 인프라 구성 요소를 설정 및 관리하고 백업 인프라에 대한 전역 설정을 지정할 수 있습니다.

**프록시 서버**

프록시 서버는 백업 서버와 백업 인프라의 다른 구성 요소 사이에 설치됩니다. 다음 기능을 관리합니다.

* 프로덕션 저장소에서 VM 데이터 검색
* 압축
* 중복 제거
* 암호화
* 백업 리포지토리로 데이터 전송

**백업 리포지토리**

백업 리포지토리는 Veeam이 복제된 VM에 대한 백업 파일, VM 복사본 및 메타데이터를 보관하는 저장소 위치입니다.  리포지토리는 로컬 디스크(또는 탑재된 NFS/SMB)가 있는 Windows 또는 Linux 서버또는 하드웨어 저장소 중복 제거 어플라이언스가 될 수 있습니다.

### <a name="veeam-deployment-scenarios"></a>Veeam 배포 시나리오
Azure를 활용하여 장기 백업 및 보관을 위한 백업 리포지토리 및 저장소 대상을 제공할 수 있습니다. 프라이빗 클라우드의 VM과 Azure의 백업 리포지토리 간의 모든 백업 네트워크 트래픽은 높은 대역폭의 낮은 대기 시간 링크를 통해 이동합니다. 지역 간 복제 트래픽은 내부 Azure 백플레인 네트워크를 통해 이동하므로 사용자의 대역폭 비용이 줄어듭니다.

**기본 배포**

백업할 TB가 30TB 미만인 환경의 경우 CloudSimple은 다음 구성을 권장합니다.

* Veeam 백업 서버 및 프록시 서버는 프라이빗 클라우드의 동일한 VM에 설치됩니다.
* 백업 작업의 대상으로 구성된 Azure의 Linux 기반 기본 백업 리포지토리입니다.
* `azcopy`기본 백업 리포지토리에서 다른 지역으로 복제되는 Azure Blob 컨테이너로 데이터를 복사하는 데 사용됩니다.

![기본 배포 시나리오](media/veeam-basicdeployment.png)

**고급 배포**

백업할 TB가 30TB를 초과하는 환경의 경우 CloudSimple은 다음 구성을 권장합니다.

* Veeam에서 권장하는 대로 vSAN 클러스터의 노드당 하나의 프록시 서버입니다.
* 프라이빗 클라우드의 Windows 기반 기본 백업 리포지토리는 빠른 복원을 위해 5일간의 데이터를 캐시합니다.
* 더 긴 기간 보존을 위한 백업 복사 작업의 대상으로 Azure의 Linux 백업 리포지토리입니다. 이 리포지토리는 확장 백업 리포지토리로 구성되어야 합니다.
* `azcopy`기본 백업 리포지토리에서 다른 지역으로 복제되는 Azure Blob 컨테이너로 데이터를 복사하는 데 사용됩니다.

![기본 배포 시나리오](media/veeam-advanceddeployment.png)

이전 그림에서 백업 프록시는 vSAN 데이터 스토어의 워크로드 VM 디스크에 대한 핫 추가 액세스 권한이 있는 VM입니다. Veeam은 vSAN에 가상 어플라이언스 백업 프록시 전송 모드를 사용합니다.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>클라우드심플의 Veeam 솔루션에 대한 요구 사항

Veeam 솔루션에서는 다음을 수행해야 합니다.

* 자신의 Veeam 라이센스를 제공합니다.
* Veeam을 배포하고 관리하여 CloudSimple 프라이빗 클라우드에서 실행되는 워크로드를 백업합니다.

이 솔루션은 Veeam 백업 도구를 완전히 제어할 수 있으며 기본 Veeam 인터페이스 또는 Veeam vCenter 플러그인을 사용하여 VM 백업 작업을 관리할 수 있는 선택권을 제공합니다.

기존 Veeam 사용자인 경우 Veeam 솔루션 구성 요소의 섹션을 건너뛰고 [Veeam 배포 시나리오로](#veeam-deployment-scenarios)직접 진행할 수 있습니다.

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>클라우드심플 프라이빗 클라우드에서 Veeam 백업 설치 및 구성

다음 섹션에서는 CloudSimple 프라이빗 클라우드를 위한 Veeam 백업 솔루션을 설치하고 구성하는 방법을 설명합니다.

배포 프로세스는 다음 단계로 구성됩니다.

1. [vCenter UI: 프라이빗 클라우드에서 인프라 서비스 설정](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [클라우드 심플 포털: Veeam을 위한 프라이빗 클라우드 네트워킹 설정](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [클라우드 단순 포털: 권한 에스컬레이션](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure 포털: 가상 네트워크를 프라이빗 클라우드에 연결](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure 포털: Azure에서 백업 리포지토리 만들기](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure 포털: 장기 데이터 보존을 위해 Azure Blob 저장소 구성](#configure-azure-blob-storage-for-long-term-data-retention)
7. [v센터 프라이빗 클라우드 의 UI: Veeam B&설치](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam 콘솔: Veeam 백업 & 복구 소프트웨어 구성](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple 포털: Veeam 액세스 설정 및 에스컬레이션 해제 권한](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>시작하기 전에

Veeam 배포를 시작하기 전에 다음이 필요합니다.

* 사용자 소유의 Azure 구독
* 미리 만들어진 Azure 리소스 그룹
* 구독의 Azure 가상 네트워크
* Azure Storage 계정
* CloudSimple 포털을 사용하여 만든 [프라이빗 클라우드입니다.](create-private-cloud.md)  

구현 단계에서는 다음 항목이 필요합니다.

* Veeam을 설치하는 Windows용 VMware 템플릿(예: Windows Server 2012 R2 - 64비트 이미지)
* 백업 네트워크에 대해 식별된 사용 가능한 VLAN 중 하나
* 백업 네트워크에 할당할 서브넷의 CIDR
* 비암 9.5 u3 설치 가능 미디어(ISO) 사설 클라우드의 vSAN 데이터스토어에 업로드

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI: 프라이빗 클라우드에서 인프라 서비스 설정

워크로드 및 도구를 쉽게 관리할 수 있도록 프라이빗 클라우드에서 인프라 서비스를 구성합니다.

* 다음 중 하나로 써서 [Active Directory를 사용하도록 vCenter ID 원본 설정에](set-vcenter-identity.md) 설명된 대로 외부 ID 공급자를 추가할 수 있습니다.

  * 프라이빗 클라우드에서 온-프레미스 Active Directory(AD)에서 사용자를 식별하려고 합니다.
  * 모든 사용자에 대해 프라이빗 클라우드에 AD를 설정하려고 합니다.
  * Azure AD를 사용하려고 합니다.
* 프라이빗 클라우드에서 워크로드에 대한 IP 주소 조회, IP 주소 관리 및 이름 확인 서비스를 제공하려면 CloudSimple 프라이빗 클라우드에서 DNS 및 DHCP 응용 프로그램 및 워크로드 설정에 설명된 대로 [DHCP 및 DNS 서버를 설정합니다.](dns-dhcp-setup.md)

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>클라우드심플 프라이빗 클라우드: Veeam을 위한 프라이빗 클라우드 네트워킹 설정

CloudSimple 포털에 액세스하여 Veeam 솔루션에 대한 프라이빗 클라우드 네트워킹을 설정합니다.

백업 네트워크에 대한 VLAN을 만들고 서브넷 CIDR을 할당합니다. 지침은 [VLAN/서브넷 만들기 및 관리를](create-vlan-subnet.md)참조하십시오.

관리 서브넷과 백업 네트워크 간에 방화벽 규칙을 만들어 Veeam에서 사용하는 포트의 네트워크 트래픽을 허용합니다. Veeam 주제 [중고 포트를](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)참조하십시오. 방화벽 규칙 만들기에 대한 지침은 [방화벽 테이블 및 규칙 설정을](firewall.md)참조하십시오.

다음 표에서는 포트 목록을 제공합니다.

| 아이콘 | 설명 | 아이콘 | 설명 |
| ------------ | ------------- | ------------ | ------------- |
| 백업 서버  | vCenter  | HTTPS / TCP  | 443 |
| 백업 서버 <br> *Veeam 백업 & 복제 구성 요소를 배포하는 데 필요합니다.* | 백업 프록시  | TCP/UDP  | 135, 137 ~ 139 및 445 |
    | 백업 서버   | DNS  | UDP  | 53  | 
    | 백업 서버   | Veeam 업데이트 알림 서버  | TCP  | 80  | 
    | 백업 서버   | Veeam 라이센스 업데이트 서버  | TCP  | 443  | 
    | 백업 프록시   | vCenter |   |   | 
    | 백업 프록시  | 리눅스 백업 저장소   | TCP  | 22  | 
    | 백업 프록시  | 윈도우 백업 리포지토리  | TCP  | 49152 - 65535   | 
    | 백업 리포지토리  | 백업 프록시  | TCP  | 2500 -5000  | 
    | 원본 백업 리포지토리<br> *백업 복사 작업에 사용*  | 대상 백업 리포지토리  | TCP  | 2500 - 5000  | 

[방화벽 테이블 및 규칙 설정에](firewall.md)설명된 대로 워크로드 서브넷과 백업 네트워크 간에 방화벽 규칙을 만듭니다.  응용 프로그램 인식 백업 및 복원을 위해 특정 응용 프로그램을 호스트하는 워크로드 VM에서 [추가 포트를](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) 열어야 합니다.

기본적으로 CloudSimple은 1Gbps 익스프레스루트 링크를 제공합니다. 더 큰 환경 크기의 경우 더 높은 대역폭 링크가 필요할 수 있습니다. 더 높은 대역폭 링크에 대한 자세한 내용은 Azure 지원에 문의하십시오.

설정을 계속하려면 권한 부여 키 및 피어 회로 URI와 Azure 구독에 대한 액세스가 필요합니다.  이 정보는 CloudSimple 포털의 가상 네트워크 연결 페이지에서 확인할 수 있습니다. 지침은 Azure [가상 네트워크에 대한 피어링 정보 얻기에서 CloudSimple 연결을](virtual-network-connection.md)참조하십시오. 정보를 얻는 데 문제가 있는 경우 [지원 팀에 문의하십시오.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>클라우드단순 프라이빗 클라우드: 클라우드 소유자를 위한 권한 에스컬레이션

기본 '클라우드 소유자' 사용자는 VEEAM을 설치하기 위해 프라이빗 클라우드 vCenter에 충분한 권한이 없으므로 사용자의 vCenter 권한을 에스컬레이션해야 합니다. 자세한 내용은 [권한 에스컬레이션 을](escalate-private-cloud-privileges.md)참조하십시오.

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure 포털: 가상 네트워크를 프라이빗 클라우드에 연결

[ExpressRoute를 사용하여 Azure 가상 네트워크 연결의](azure-expressroute-connection.md)지침에 따라 가상 네트워크를 프라이빗 클라우드에 연결합니다.

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure 포털: 백업 리포지토리 VM 만들기

1. 표준 D2 v3 VM(2vCPU 및 8GB 메모리)을 만듭니다.
2. CentOS 7.4 기반 이미지를 선택합니다.
3. VM에 대한 NSG(네트워크 보안 그룹)를 구성합니다. VM에 공용 IP 주소가 없고 공용 인터넷에서 연결할 수 없는지 확인합니다.
4. 새 VM에 대한 사용자 이름 및 암호 기반 사용자 계정을 만듭니다. 지침은 Azure [포털에서 Linux 가상 컴퓨터 만들기를](../virtual-machines/linux/quick-create-portal.md)참조하십시오.
5. 1x512 GiB 표준 HDD를 만들고 리포지토리 VM에 연결합니다.  지침은 Azure [포털의 Windows VM에 관리되는 데이터 디스크를 연결하는 방법을](../virtual-machines/windows/attach-managed-disk-portal.md)참조하세요.
6. [관리 디스크에 XFS 볼륨을 만듭니다.](https://www.digitalocean.com/docs/volumes/how-to/) 앞에서 언급한 자격 증명을 사용하여 VM에 로그인합니다. 다음 스크립트를 실행하여 논리적 볼륨을 만들고, 디스크를 추가하고, XFS 파일 시스템 [파티션을](https://www.digitalocean.com/docs/volumes/how-to/partition/) 만들고, /backup1 경로 아래에 파티션을 [탑재합니다.](https://www.digitalocean.com/docs/volumes/how-to/mount/)

    스크립트 예:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. /backup1을 사설 클라우드에서 실행 중인 Veeam 백업 서버에 NFS 탑재 지점으로 노출합니다. 지침은 [CentOS 6에서 NFS 마운트를 설정하는 방법](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6)디지털 오션 문서를 참조하십시오. Veeam 백업 서버에서 백업 리포지토리를 구성할 때 이 NFS 공유 이름을 사용합니다.

8. 백업 리포지토리 VM에 대한 NSG필터링 규칙을 구성하여 VM을 통해 모든 네트워크 트래픽을 명시적으로 허용합니다.

> [!NOTE]
> Veeam 백업 & 복제는 SSH 프로토콜을 사용하여 Linux 백업 리포지토리와 통신하고 Linux 리포지토리의 SCP 유틸리티가 필요합니다. SSH 데몬이 제대로 구성되었는지, Linux 호스트에서 SCP를 사용할 수 있는지 확인합니다.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>장기 데이터 보존을 위해 Azure Blob 저장소 구성

1. [Azure 저장소](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)시작에 대해 설명한 대로 표준 형식의 범용 저장소 계정(GPv2)과 Blob 컨테이너를 만듭니다.
2. 컨테이너 만들기 참조에 설명된 대로 Azure 저장소 컨테이너를 [만듭니다.](https://docs.microsoft.com/rest/api/storageservices/create-container)
2. 마이크로 `azcopy` 소프트에서 리눅스에 대한 명령 줄 유틸리티를 다운로드합니다. CentOS 7.5의 bash 셸에서 다음 명령을 사용할 수 있습니다.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. `azcopy` 명령을 사용하여 Blob 컨테이너에서 백업 파일을 복사합니다.  자세한 명령은 [리눅스에서 AzCopy와 데이터 전송을](../storage/common/storage-use-azcopy-linux.md) 참조하십시오.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>v중앙 클라우드의 센터 콘솔: Veeam B&설치

프라이빗 클라우드에서 vCenter에 액세스하여 Veeam 서비스 계정을 만들고, Veeam B&R 9.5를 설치하고, 서비스 계정을 사용하여 Veeam을 구성합니다.

1. 'Veeam 백업 역할'이라는 새 역할을 만들고 Veeam에서 권장하는 대로 필요한 권한을 할당합니다. 자세한 내용은 Veeam 주제 [필수 권한을](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)참조하십시오.
2. vCenter에서 새 'Veeam 사용자 그룹' 그룹을 만들고 'Veeam 백업 역할'을 할당합니다.
3. 새 'Veeam 서비스 계정' 사용자를 만들고 'Veeam 사용자 그룹'에 추가합니다.

    ![Veeam 서비스 계정 만들기](media/veeam-vcenter01.png)

4. 백업 네트워크 VLAN을 사용하여 vCenter에서 분산 포트 그룹을 만듭니다. 자세한 내용은 [vSphere 웹 클라이언트에서 분산 포트 그룹 만들기 VMware](https://www.youtube.com/watch?v=wpCd5ZbPOpA)비디오를 참조하십시오.
5. Veeam [시스템 요구 사항에](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)따라 vCenter에서 Veeam 백업 및 프록시 서버에 대한 VM을 만듭니다. 당신은 윈도우2012 R2 또는 리눅스를 사용할 수 있습니다. 자세한 내용은 [Linux 백업 리포지토리 사용에 대한 요구 사항을](https://www.veeam.com/kb2216)참조하십시오.
6. Veeam 백업 서버 VM에 설치 가능한 Veeam ISO를 CDROM 장치로 탑재합니다.
7. Windows 2012 R2 컴퓨터(Veeam 설치 대상)에 RDP 세션을 사용하여 Windows 2012 R2 VM에 [Veeam B&R 9.5u3을 설치합니다.](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
8. Veeam 백업 서버 VM의 내부 IP 주소를 찾아 DHCP 서버에서 정적IP 주소를 구성합니다. 이 작업을 수행하는 데 필요한 정확한 단계는 DHCP 서버에 따라 다릅니다. 예를 들어 Netgate 아티클 <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">정적 DHCP 매핑에서는</a> pfSense 라우터를 사용하여 DHCP 서버를 구성하는 방법을 설명합니다.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam 콘솔: Veeam 백업 및 복구 소프트웨어 설치

Veeam 콘솔을 사용하여 Veeam 백업 및 복구 소프트웨어를 구성합니다. 자세한 내용은 [Veeam 백업 & 복제 v9 - 설치 및 배포](https://www.youtube.com/watch?v=b4BqC_WXARk)를 참조하십시오.

1. VMware vSphere를 관리되는 서버 환경으로 추가합니다. 메시지가 표시되면 비암 서비스 계정의 자격 증명을 제공 합니다 [&.](#vcenter-console-of-private-cloud-install-veeam-br)

    * 부하 제어 및 기본 고급 설정에 대한 기본 설정을 사용합니다.
    * 탑재 서버 위치를 백업 서버로 설정합니다.
    * Veeam 서버의 구성 백업 위치를 원격 리포지토리로 변경합니다.

2. Azure에서 Linux 서버를 백업 리포지토리로 추가합니다.

    * 부하 제어 및 고급 설정에 대한 기본 설정을 사용합니다. 
    * 탑재 서버 위치를 백업 서버로 설정합니다.
    * Veeam 서버의 구성 백업 위치를 원격 리포지토리로 변경합니다.

3. 홈> 구성 백업 설정을 사용하여 구성 **백업의 암호화를**활성화합니다.

4. Windows 서버 VM을 VM웨어 환경의 프록시 서버로 추가합니다. 프록시에 '트래픽 규칙'을 사용하여 와이어를 통해 백업 데이터를 암호화합니다.

5. 백업 작업을 구성합니다.
    * 백업 작업을 구성하려면 백업 [작업 만들기의](https://www.youtube.com/watch?v=YHxcUFEss4M)지침을 따릅니다.
    * 고급 설정 > **저장소에서**백업 파일의 암호화를 활성화합니다.

6. 백업 복사 작업을 구성합니다.

    * 백업 복사 작업을 구성하려면 비디오의 지침에 따라 [백업 복사 작업 만들기.](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)
    * 고급 설정 > **저장소에서**백업 파일의 암호화를 활성화합니다.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple 포털: Veeam 액세스 설정 및 에스컬레이션 해제 권한
Veeam 백업 및 복구 서버에 대한 공용 IP 주소를 만듭니다. 지침은 공용 [IP 주소 할당을](public-ips.md)참조하십시오.

Veeam 백업 서버가 TCP 포트 80에서 업데이트/패치를 다운로드하기 위해 Veeam 웹 사이트에 아웃바운드 연결을 만들 수 있도록 하는 방화벽 규칙을 만듭니다. 지침은 방화벽 [테이블 및 규칙 설정을](firewall.md)참조하십시오.

권한 에스컬레이션 해제하려면 에스컬레이션 [해제 권한 입니다.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="references"></a>참조

### <a name="cloudsimple-references"></a>클라우드 단순 참조

* [프라이빗 클라우드 만들기](create-private-cloud.md)
* [VLAN/서브넷 생성 및 관리](create-vlan-subnet.md)
* [v센터 아이덴티티 소스](set-vcenter-identity.md)
* [워크로드 DNS 및 DHCP 설정](dns-dhcp-setup.md)
* [권한 높이기](escalate-privileges.md)
* [방화벽 테이블 및 규칙 설정](firewall.md)
* [프라이빗 클라우드 권한](learn-private-cloud-permissions.md)
* [공용 IP 주소 할당](public-ips.md)

### <a name="veeam-references"></a>비암 레퍼런스

* [사용된 포트](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [필수 권한](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [시스템 요구 사항](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Veeam 백업 & 복제 설치](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Linux용 멀티 OS FLR 및 리포지토리 지원에 필요한 모듈 및 사용 권한](https://www.veeam.com/kb2216)
* [Veeam 백업 & 복제 v9 - 설치 및 배포 - 비디오](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 백업 작업 만들기 - 비디오](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 백업 복사 작업 만들기 - 비디오](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure 참조

* [Azure Portal을 사용하여 ExpressRoute에 대한 가상 네트워크 게이트웨이 구성](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [회로에 VNet 연결 - 다른 구독](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Azure Portal에서 Linux 가상 머신 만들기](../virtual-machines/linux/quick-create-portal.md)
* [Azure Portal에서 Windows VM에 관리되는 데이터 디스크를 연결하는 방법](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Azure 저장소 시작 - 비디오](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [컨테이너 만들기](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Linux에서 AzCopy를 사용하여 데이터 전송](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VM웨어 참조

* [vSphere 웹 클라이언트에서 분산 포트 그룹 만들기 - 비디오](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>기타 참조

* [관리 디스크에서 XFS 볼륨 만들기 - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [CentOS 7에 NFS 마운트를 설정하는 방법 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [DHCP 서버 구성 - 넷게이트](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
