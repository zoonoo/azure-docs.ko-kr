---
title: "Azure에서 온-프레미스로 VMware VM 장애 복구 | Microsoft Docs"
description: "VMware VM 및 물리적 서버를 Azure로 장애 조치한 후 온-프레미스 사이트로 장애 복구에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 5a47337f-89a9-43e8-8fdc-7da373c0fb0f
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 03/27/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 3bd182a775377f912914c0c7a63fe41811146e1a
ms.lasthandoff: 04/27/2017


---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>온-프레미스 사이트로 VMWare 가상 컴퓨터 및 물리적 서버 장애 복구
> [!div class="op_single_selector"]
> * [Azure의 VMware/물리적 컴퓨터](site-recovery-how-to-failback-azure-to-vmware.md)
> * [Azure의 Hyper-V VM](site-recovery-failback-from-azure-to-hyper-v.md)

이 문서에서는 Azure 가상 컴퓨터를 Azure에서 온-프레미스 사이트로 장애 복구하는 방법에 대해 설명합니다. 이 [참조](site-recovery-how-to-reprotect.md)를 사용하여 컴퓨터를 다시 보호한 후에는 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 장애 복구할 준비가 될 때 여기에 나온 지침을 따릅니다.

>[!NOTE]
>클래식 Azure Portal을 사용하는 경우 VMware에서 Azure로 아키텍처를 강화하기 위해서는 [여기](site-recovery-failback-azure-to-vmware-classic.md)의 지침을 따르고, 기존 아키텍처를 사용하려면 [여기](site-recovery-failback-azure-to-vmware-classic-legacy.md)를 참조하세요.

## <a name="overview"></a>개요
이 섹션의 다이어그램에서는 이 시나리오의 장애 복구 아키텍처를 보여 줍니다.

프로세스 서버가 온-프레미스이고 ExpressRoute 연결을 사용하는 경우 이 아키텍처를 사용합니다.

![ExpressRoute 아키텍처 다이어그램](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

프로세스 서버가 Azure에 있고 VPN 또는 ExpressRoute 연결을 사용하는 경우 이 아키텍처를 사용합니다.

![VPN 아키텍처 다이어그램](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

포트 목록 전체 및 장애 복구 아키텍처 다이어그램을 보려면 다음 이미지를 참조하세요.

![모든 포트의 장애 조치-장애 복구 목록](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Azure로 장애 조치한 후 다음 세 단계로 온-프레미스 사이트에 장애 복구합니다.

* **1단계**: Azure VM을 다시 보호하여 온-프레미스 사이트에서 실행 중인 VMware VM으로 복제를 다시 시작합니다.
* **2단계**: Azure VM이 온-프레미스 사이트에 복제된 후 Azure에서 장애 복구하도록 장애 조치를 실행합니다.
* **3단계**: 데이터가 장애 복구된 후 Azure로 복제가 시작하도록 장애 복구한 온-프레미스 VM을 다시 보호합니다.

### <a name="fail-back-to-the-original-location-or-an-alternate-location"></a>원래 위치 또는 다른 위치로 장애 복구
VMware VM을 장애 조치한 후에도 동일한 원본 VM이 여전히 온-프레미스에 있는 경우 이 원본 VM으로 장애 복구할 수 있습니다. 이 시나리오에서는 델타만 장애 복구합니다.

물리적 서버를 장애 조치한 경우 장애 복구는 항상 새 VMware VM으로 수행됩니다. 물리적 컴퓨터를 장애 복구하기 전에 다음 사항에 유의하세요.
* 보호된 물리적 컴퓨터가 Azure에서 VMware로 다시 장애 조치된 경우 가상 컴퓨터로 다시 돌아옵니다. Windows Server 2008 R2 SP1 물리적 컴퓨터가 보호되고 Azure로 장애 조치된 경우 장애 복구될 수 없습니다. 온-프레미스 가상 컴퓨터로 시작된 Windows Server 2008 R2 SP1 컴퓨터는 장애 복구할 수 있습니다.
* 장애 복구에 필요한 필수 ESX/ESXi 호스트와 함께 하나 이상의 마스터 대상 서버를 검색해야 합니다.

원래 VM으로 장애 복구하는 경우 다음이 필요합니다.

* VM이 vCenter 서버에서 관리되는 경우 마스터 대상의 ESX 호스트에서 VM 데이터 저장소에 액세스할 수 있어야 합니다.
* VM이 ESX 호스트에 있지만 vCenter에서 관리되지 않는 경우 VM의 하드 디스크는 MT의 호스트에서 액세스할 수 있는 데이터 저장소에 있어야 합니다.
* VM이 ESX 호스트에 있고 vCenter를 사용하지 않는 경우 다시 보호하기 전에 MT의 ESX 호스트 검색을 완료해야 합니다. 물리적 서버도 장애 복구하는 경우 적용됩니다.
* 다른 옵션(온-프레미스 VM이 있는 경우)은 장애 복구를 수행하기 전에 삭제하는 것입니다. 그런 다음 장애 복구를 수행하면 마스터 대상 ESX 호스트와 동일한 호스트에 새 VM을 만듭니다.

대체 위치로 장애 복구하는 경우 온-프레미스 마스터 대상 서버에서 사용한 것과 동일한 데이터 저장소와 동일한 ESX 호스트에 데이터를 복구합니다.

## <a name="prerequisites"></a>필수 조건
* VMware VM 및 물리적 서버를 장애 복구하려면 VMware 환경이 필요합니다. 물리적 서버로 장애 복구는 지원되지 않습니다.
* 장애 복구하려면 보호를 처음 설정할 때 Azure 네트워크를 만들어야 합니다. 장애 복구는 온-프레미스 사이트에 있는 Azure VM이 있는 Azure 네트워크에서 VPN 또는 Express 경로 연결이 필요합니다.
* 장애 복구하려는 VM을 vCenter Server에서 관리하는 경우 vCenter 서버에서 VM을 검색하는 데 필요한 권한이 있는지 확인합니다. 자세한 내용은 [Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)를 참조하세요.
* 스냅숏이 VM에 있는 경우 다시 보호에 실패합니다. 스냅숏 또는 디스크를 삭제할 수 있습니다.
* 장애 복구하기 전에 다음 구성 요소를 만듭니다.
  * **Azure에 프로세스 서버 만들기**: 이 구성 요소는 장애 복구 중에 만들고 계속 실행하는 Azure VM입니다. 장애 복구가 완료되면 VM을 삭제할 수 있습니다.
  * **마스터 대상 서버 만들기**: 마스터 대상 서버는 장애 복구 데이터를 송수신합니다. 온-프레미스를 만든 관리 서버에는 기본적으로 설치된 마스터 대상 서버가 있습니다. 그러나 장애 복구된 트래픽의 양에 따라 장애 복구를 위한 별도의 마스터 대상 서버를 만들어야 할 수도 있습니다.
  * Linux에서 실행되는 추가 마스터 대상 서버를 만들려면 나중에 설명하는 대로 마스터 대상 서버를 설치하기 전에 Linux VM을 설정합니다.
* 장애 복구를 수행할 때 구성 서버가 온-프레미스에 있어야 합니다. 장애 복구하는 동안 가상 컴퓨터가 구성 서버 데이터베이스에 있어야 합니다. 구성 서버 데이터베이스에 VM이 없으면 장애 복구가 성공할 수 없습니다. 따라서 서버의 예약된 정기 백업을 수행해야 합니다. 재난이 발생하면 장애 복구가 작동할 수 있도록 동일한 IP 주소로 복원해야 합니다.
* VMware의 마스터 대상 VM **구성 매개 변수**에서 disk.enableUUID=true로 설정합니다. 이 행이 존재하지 않는 경우 추가합니다. 이 설정은 가상 컴퓨터 디스크(VMDK) 파일에 일관된 UUID(Universally Unique Identifier)를 제공하여 올바르게 탑재되도록 하는데 필요합니다.
* 장애 복구 실패가 발생할 수 있는 "마스터 대상 서버는 vMotioned 저장소가 될 수 없음" 조건을 알고 있어야 합니다. 디스크를 사용할 수 없으므로 VM을 시작할 수 없습니다.
* 마스터 대상 서버에 보존 드라이브라고 하는 드라이브를 추가합니다. 디스크를 추가하고 드라이브를 포맷합니다.

## <a name="failback-policy"></a>장애 복구 정책
온-프레미스로 다시 복제하려면 장애 복구 정책이 필요합니다. 정책은 정방향 정책을 만들 때 자동으로 만들어지고, 구성 서버와 자동으로 연결되며, 편집할 수는 없습니다. 정책의 복제 설정은 다음과 같습니다.

* RPO 임계값 = 15분
* 복구 지점 보존 기간 = 24시간
* 앱 일치 스냅숏 빈도 = 60분

 ![장애 복구 정책의 복제 설정](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-a-process-server-in-azure"></a>Azure에서 프로세스 서버 설정
Azure VM에서 데이터를 온-프레미스 마스터 대상 서버로 다시 보낼 수 있도록 Azure에 프로세스 서버를 설치합니다.

클래식 리소스로서 컴퓨터를 보호한 경우(Azure에서 복구되는 VM이 클래식 배포 모델을 사용하여 만들어진 VM이기 때문) Azure에 프로세스 서버가 필요합니다. 배포 유형으로 Azure Resource Manager를 사용하여 VM을 복구한 경우 프로세스 서버에는 배포 유형으로 Resource Manager가 있어야 합니다. 프로세스 서버를 배포하는 Azure 가상 네트워크에서 배포 유형을 선택합니다.

1. **자격 증명 모음** > **설정** > **Site Recovery 인프라**(**관리** 아래) > **구성 서버**(**VMware 및 물리적 컴퓨터**)에서 구성 서버를 선택 합니다.
2. **프로세스 서버**를 클릭합니다.

  ![프로세스 서버 단추](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
3. 프로세스 서버 배포를 위해 **Azure에서 장애 복구 프로세스 서버 배포**를 선택합니다.
4. VM을 복구한 구독을 선택합니다.
5. VM을 복구한 Azure 네트워크를 선택합니다. 복구된 VM과 프로세스 서버가 통신할 수 있도록 프로세스 서버는 동일한 네트워크에 있어야 합니다.
6. *클래식 배포 모델* 네트워크를 선택한 경우 Azure Marketplace를 통해 VM을 만든 다음 그 안에 프로세스 서버를 설치합니다.

 !["프로세스 서버 추가" 창](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)

 프로세스 서버를 만들 때 다음 사항에 주의하세요.
 * 이미지 이름은 *Microsoft Azure Site Recovery 프로세스 서버 V2*입니다. 배포 모델로 **클래식**을 선택합니다.

       ![Select "Classic" as the Process Server deployment model](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
 * [Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)의 지침에 따라 프로세스 서버를 설치합니다.
7. *Resource Manager* Azure 네트워크를 선택하는 경우 다음 정보를 제공하여 프로세스 서버를 배포합니다.

  * 서버를 배포하려는 리소스 그룹 이름
  * 서버 이름
  * 서버에 로그인하기 위한 사용자 이름과 암호
  * 서버를 배포하려는 저장소 계정
  * 연결하려는 서브넷 및 네트워크 인터페이스
   >[!NOTE]
   >프로세스 서버를 배포하는 중에 사용자 고유의 [NIC(네트워크 인터페이스)](../virtual-network/virtual-networks-multiple-nics.md)를 만들고 선택해야 합니다.

    !["프로세스 서버 추가" 대화 상자에 정보 입력](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)

8. **확인**을 클릭합니다. 이 작업은 프로세스 서버 설치 중에 Resource Manager 배포 유형 가상 컴퓨터를 만드는 작업을 트리거합니다. 서버를 구성 서버에 등록하려면 [Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)의 지침에 따라 VM 내에서 설정을 실행합니다. 프로세스 서버를 배포하는 작업도 트리거합니다.

  프로세스 서버가 **구성 서버** > **연결된 서버** > **프로세스 서버** 탭에 나열됩니다.

    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    > [!NOTE]
    > 프로세스 서버는 **VM 속성** 아래에 표시되지 않으며, 등록된 관리 서버의 **서버** 탭에서만 볼 수 있습니다. 프로세스 서버가 나타날 때까지 10-15분이 걸릴 수 있습니다.


## <a name="set-up-the-master-target-server-on-premises"></a>마스터 대상 서버 온-프레미스 설정
마스터 대상 서버는 장애 복구 데이터를 받습니다. 서버가 온-프레미스 관리 서버에 자동으로 설치되지만 너무 많은 데이터를 장애 복구하는 경우 추가 마스터 대상 서버를 설정해야 할 수도 있습니다. 온-프레미스에서 마스터 대상 서버를 설정하려면 다음을 수행합니다.

> [!NOTE]
> Linux에서 마스터 대상 서버를 설정하려면 다음 절차로 건너뜁니다. CentOS 6.6 minimal 운영 체제만 마스터 대상 OS로 사용합니다.

1. Windows에서 마스터 대상 서버를 설정하는 경우 마스터 대상 서버를 설치하는 VM에서 빠른 시작 페이지를 엽니다.
2. Azure Site Recovery 통합 설치 마법사의 설치 파일을 다운로드합니다.
3. 설치 마법사를 실행하고 **시작하기 전에**에서 **배포 규모 확장을 위해 추가 프로세스 서버 추가**를 선택합니다.
4. [관리 서버를 설정](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)했을 때 수행한 것과 동일한 방식으로 마법사를 완료합니다. **구성 서버 세부 정보** 페이지에서 마스터 대상 서버의 IP 주소를 지정하고 VM에 액세스하기 위한 암호를 입력합니다.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>마스터 대상 서버로 Linux VM 설정
마스터 대상 서버를 실행하는 관리 서버를 Linux VM으로 설정하려면 CentOS 6.6 minimal 운영 체제를 설치합니다. 다음으로 각 SCSI 하드 디스크의 SCSI ID를 검색하고 몇 가지 추가 패키지를 설치한 다음 일부 사용자 지정 변경 내용을 적용합니다.

#### <a name="install-centos-66"></a>CentOS 6.6 설치

1. 관리 서버 VM에 CentOS 6.6 최소 운영 체제를 설치합니다. DVD 드라이브에 ISO를 보관하고 시스템을 부팅합니다. 미디어 테스트를 건너뜁니다. 언어로 **영어(미국)**를 선택하고, **기본 저장소 장치**를 선택하고, 하드 드라이브에 중요한 데이터가 없는지 확인한 다음 **예**를 클릭하고, 모든 데이터를 삭제합니다. 관리 서버의 호스트 이름을 입력하고 서버 네트워크 어댑터를 선택합니다.  **시스템 편집** 대화 상자에서 **자동으로 연결**을 선택한 다음 고정 IP 주소, 네트워크 및 DNS 설정을 추가합니다. 표준 시간대를 지정합니다. 관리 서버에 액세스하려면 루트 암호를 입력합니다.
2. 원하는 설치 유형을 묻는 경우 파티션으로 **사용자 지정 레이아웃 만들기**를 선택합니다. **다음**을 클릭합니다. **무료**를 선택하고 **만들기**를 클릭합니다. **FS 유형:** **ext4**를 사용하여 **/**,  **/var/crash** 및 **/home partitions**를 만듭니다. **FS 형식: 스왑**으로 스왑 파티션을 만듭니다.
3. 기존 장치가 발견되면 경고 메시지가 표시됩니다. **포맷** 을 클릭하여 파티션 설정으로 드라이브를 포맷합니다. **디스크에 변경 내용 쓰기** 를 클릭하여 파티션 변경 내용을 적용합니다.
4. **부팅 로더 설치** > **다음**을 선택하여 루트 파티션에 부팅 로더를 설치합니다.
5. 설치가 완료되면 **다시 부팅**을 클릭합니다.

#### <a name="retrieve-the-scsi-ids"></a>SCSI ID 검색

1. 설치 후 VM의 각 SCSI 하드 디스크에 대한 SCSI ID를 검색합니다. 이렇게 하려면 관리 서버 VM을 종료합니다. VMware의 VM 속성에서 VM 항목 > **설정 편집** > **옵션**을 마우스 오른쪽 단추로 클릭합니다.
2. **고급** > **일반 항목**을 선택한 다음 **구성 매개 변수**를 클릭합니다. 컴퓨터가 실행 중일 때는 이 옵션을 사용할 수 없습니다. 옵션을 사용하려면 시스템을 종료해야 합니다.
3. 다음 중 하나를 수행합니다.
 * **disk.EnableUUID** 행이 있는 경우 값이 **True**(대/소문자 구분)로 설정되었는지 확인합니다. 값이 이미 True로 설정되어 있으면 게스트 운영 체제가 부팅된 후 SCSI 명령을 취소하고 테스트할 수 있습니다.
 * **disk.EnableUUID** 행이 없는 경우 **행 추가**를 클릭한 다음 **True** 값을 추가합니다. 큰따옴표는 사용하지 마세요.

#### <a name="install-additional-packages"></a>추가 패키지 설치
추가 패키지를 다운로드하여 설치합니다.

1. 마스터 대상 서버가 인터넷에 연결되어 있는지 확인합니다.
2. CentOS 리포지토리에서 15개의 패키지를 다운로드하여 설치하려면 `# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools` 명령을 실행합니다.
3. 보호하려는 원본 컴퓨터에서 루트 또는 부팅 장치에 대해 Reiser 또는 XFS 파일 시스템으로 Linux를 실행 중인 경우 다음과 같이 추가 패키지를 다운로드하여 설치합니다.

   * \# cd /usr/local
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * \# rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * \# wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * \# rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm
   * \# yum install device-mapper-multipath(마스터 대상 서버에서 다중 경로 패키지를 사용하도록 설정하는 데 필요함)

#### <a name="apply-custom-changes"></a>사용자 지정 변경 내용 적용
설치 후 단계를 완료하고 패키지를 설치한 후에는 다음을 수행하여 사용자 지정 변경 내용을 적용합니다.

1. RHEL 6-64 통합 에이전트 바이너리를 VM에 복사합니다. 바이너리를 untar하려면 `tar –zxvf <file name>` 명령을 실행합니다.
2. 권한을 부여하려면 `# chmod 755 ./ApplyCustomChanges.sh` 명령을 실행합니다.
3. `# ./ApplyCustomChanges.sh` 스크립트를 실행합니다. 한 번만 실행합니다. 스크립트가 성공적으로 실행되면 서버를 다시 부팅합니다.

## <a name="run-the-failback"></a>장애 복구 실행
### <a name="reprotect-the-azure-vms"></a>Azure VM 다시 보호
1. **자격 증명 모음**의 **복제된 항목**에서 장애 조치된 VM을 마우스 오른쪽 단추로 클릭한 다음 **다시 보호**를 선택합니다.
2. 블레이드에서 보호 방향으로 **Azure - 온-프레미스**가 이미 선택되어 있음을 볼 수 있습니다.
3. **마스터 대상 서버** 및 **프로세스 서버**에서 온-프레미스 마스터 대상 서버 및 Azure VM 프로세스 서버를 선택합니다.
4. 온-프레미스 디스크를 복구하려는 데이터 저장소를 선택합니다. 온-프레미스 VM이 삭제되고 새 디스크를 만들어야 하는 경우에 이 옵션을 사용합니다. 디스크가 이미 있으면 이 옵션을 무시합니다. 다만 값은 지정해야 합니다.
5. 보존 드라이브를 사용하여 VM을 온-프레미스로 다시 복제할 때 특정 시점을 중지합니다. 여기서는 보존 드라이브에 대한 몇 가지 기준을 나열합니다. 이러한 기준이 없으면 드라이브가 마스터 대상 서버에 나열되지 않습니다.

  * 볼륨은 다른 용도(복제의 대상 등)로 사용되지 않아야 합니다.
  * 볼륨은 잠금 모드가 아니어야 합니다.
  * 볼륨은 캐시 볼륨이 아니어야 합니다. (마스터 대상 설치는 해당 볼륨에 없어야 합니다. 프로세스 서버와 마스터 대상 사용자 지정 설치 볼륨은 보존 볼륨에 적합하지 않습니다. 여기서는 설치된 프로세스 서버와 마스터 대상 볼륨이 마스터 대상의 캐시 볼륨입니다.)
  * 볼륨 파일 시스템 유형은 FAT 및 FAT32가 아니어야 합니다.
  * 볼륨 용량은 0이 아닌 값이어야 합니다.
  * Windows의 기본 보존 볼륨은 R 볼륨입니다.
  * Linux의 기본 보존 볼륨은 /mnt/retention입니다.

6. 장애 복구 정책이 자동으로 선택됩니다.
7. **확인**을 클릭하여 다시 보호를 시작하면 Azure에서 온-프레미스 사이트로 VM을 복제하는 작업을 시작합니다. **작업** 탭에서 진행률을 추적할 수 있습니다.

대체 위치로 복구하려는 경우 마스터 대상 서버에 대해 구성된 보존 드라이브 및 데이터 저장소를 선택합니다. 온-프레미스 사이트로 장애 복구할 때 장애 복구 보호 계획의 VMware VM은 마스터 대상 서버와 동일한 데이터 저장소를 사용합니다. 복제본 Azure VM을 동일한 온-프레미스 VM에 복구하려는 경우 온-프레미스 VM은 마스터 대상 서버와 동일한 데이터 저장소에 있어야 합니다. 온-프레미스에 VM이 없는 경우 다시 보호하는 동안 새 VM이 만들어집니다.

![드롭다운 메뉴에서 "Azure - 온-프레미스"를 선택합니다.](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

복구 계획 수준에서 다시 보호할 수도 있습니다. 복제 그룹이 있는 경우 복구 계획을 통해서만 이 복제 그룹을 다시 보호할 수 있습니다. 복구 계획을 사용하여 다시 보호하는 경우 모든 보호 대상 컴퓨터에 대해 이전 값을 사용합니다.

> [!NOTE]
> 동일한 마스터 대상 서버를 사용하여 복제 그룹을 다시 보호해야 합니다. 다른 마스터 대상 서버를 통해 복제 그룹을 다시 보호하는 경우 이 복제 그룹에 공통된 특정 시점을 결정할 수 없습니다.

### <a name="run-a-failover-to-the-on-premises-site"></a>온-프레미스 사이트로 장애 조치 실행
VM을 다시 보호한 후에는 Azure에서 온-프레미스로 장애 조치를 시작할 수 있습니다.

1. 복제된 항목 페이지에서 가상 컴퓨터를 마우스 오른쪽 단추로 클릭한 다음 **계획되지 않은 장애 조치**를 선택합니다.
2. **장애 조치(failover) 확인** 에서 장애 조치 방향(Azure에서)을 확인한 후 장애 조치에 사용할 복구 지점(최신 시점 또는 최신 응용 프로그램 일치 복구 지점)을 선택합니다. 최신 시점 이전에 최신 응용 프로그램 일치 복구 지점이 발생하고 일부 데이터가 손실됩니다.
3. 장애 조치 중에 Site Recovery에서 Azure VM을 종료합니다. 장애 복구가 예상대로 완료되었는지 확인한 후에 Azure VM이 예상대로 종료되었는지 확인할 수 있습니다.

### <a name="reprotect-the-on-premises-site"></a>온-프레미스 사이트 다시 보호
장애 복구가 완료된 후 가상 컴퓨터를 커밋하여 Azure에서 복구된 VM이 삭제되었는지 확인해야 합니다. 이렇게 하려면 보호된 항목을 마우스 오른쪽 단추로 클릭한 다음 **커밋**을 클릭합니다. 이 작업은 Azure에서 이전에 복구된 가상 컴퓨터를 제거하는 작업을 트리거합니다.

커밋이 완료되면 데이터가 온-프레미스 사이트에 다시 있어야 하지만 보호되지는 않습니다. Azure로 다시 복제하려면 다음을 수행합니다.

1. **자격 증명 모음**의 **설정** > **복제된 항목**에서 장애 복구한 VM을 선택한 다음 **다시 보호**를 클릭합니다.
2. Azure로 데이터를 다시 전송하는 데 사용해야 하는 프로세스 서버의 값을 제공합니다.
3. **확인**을 클릭합니다.

다시 보호가 완료되면 VM이 Azure로 다시 복제하고 장애 조치를 수행할 수 있습니다.

### <a name="resolve-common-failback-issues"></a>일반적인 장애 복구 문제 해결
* 읽기 전용 사용자 vCenter 검색을 수행하고 가상 컴퓨터를 보호하면 작업에 성공하고 장애 조치가 작동합니다. 다시 보호 중에는 데이터 저장소를 검색할 수 없기 때문에 장애 조치가 실패합니다. 하나의 증상으로, 다시 보호하는 동안 나열된 데이터 저장소가 표시되지 않습니다. 이 문제를 해결하려면 vCenter 자격 증명을 권한이 있는 적절한 계정으로 업데이트하고 작업을 다시 시도하면 됩니다. 자세한 내용은 [Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)를 참조하세요.
* Linux VM을 장애 복구하고 이 VM을 온-프레미스에서 실행하면 네트워크 관리자 패키지가 컴퓨터에서 제거되었음을 알 수 있습니다. 이 제거는 Azure에서 VM을 복구할 때 네트워크 관리자 패키지가 제거되었기 때문에 발생합니다.
* VM을 고정 IP 주소로 구성하고 Azure로 장애 조치하면 DHCP를 통해 IP 주소를 가져옵니다. 온-프레미스로 다시 장애 조치하면 VM에서 DHCP를 사용하여 IP 주소를 계속 가져옵니다. 컴퓨터에 수동으로 로그인하고, 필요한 경우 IP 주소를 고정 주소로 다시 설정합니다.
* ESXi 5.5 무료 버전 또는 vSphere 6 하이퍼바이저 무료 버전을 사용하는 경우 장애 조치는 성공하지만 장애 복구가 실패합니다. 장애 복구를 사용하도록 설정하려면 평가판 라이선스로 업그레이드합니다.
* 프로세스 서버에서 구성 서버에 연결할 수 없는 경우 443 포트의 구성 서버 컴퓨터에 텔넷하여 구성 서버에 대한 연결성을 확인합니다. 프로세스 서버 컴퓨터에서 구성 서버에 대한 ping을 시도할 수도 있습니다. 또한 프로세스 서버는 구성 서버에 연결될 때 하트비트가 있어야 합니다.
* 대체 vCenter로 장애 복구하려고 시도하는 경우 새 vCenter가 검색되고 마스터 대상 서버도 검색되는지 확인해야 합니다. 일반적인 증상으로 **다시 보호** 대화 상자에서 데이터 저장소에 액세스할 수 없거나 데이터 저장소를 볼 수 없습니다.
* 물리적 온-프레미스 컴퓨터로 보호되는 WS2008R2SP1 컴퓨터는 Azure에서 온-프레미스로 장애 복구할 수 없습니다.

## <a name="fail-back-with-expressroute"></a>ExpressRoute로 장애 복구
VPN 연결 또는 Azure ExpressRoute 연결을 통해 장애 복구할 수 있습니다. ExpressRoute 연결을 사용하려면 다음에 유의합니다.

* ExpressRoute 연결은 원본 컴퓨터에서 장애 조치하고 장애 조치가 발생한 후 Azure VM이 있는 Azure 가상 네트워크에 설정되어 있어야 합니다.
* 데이터는 공용 끝점의 Azure 저장소 계정에 복제됩니다. ExpressRoute 연결을 사용하려면 ExpressRoute에서 Site Recovery 복제를 위한 대상 데이터 센터로 공용 피어링을 설정합니다.

