---
title: 온-프레미스 사이트로 VMWare 가상 컴퓨터 및 물리적 서버 장애 복구 | Microsoft Docs
description: VMware VM 및 물리적 서버를 Azure로 장애 조치한 후 온-프레미스 사이트로 장애 복구에 대해 알아봅니다.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: mkjain
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 10/05/2016
ms.author: ruturajd

---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>온-프레미스 사이트로 VMWare 가상 컴퓨터 및 물리적 서버 장애 복구
> [!div class="op_single_selector"]
> * [Azure 포털](site-recovery-failback-azure-to-vmware.md)
> * [Azure 클래식 포털](site-recovery-failback-azure-to-vmware-classic.md)
> * [Azure 클래식 포털(레거시)](site-recovery-failback-azure-to-vmware-classic-legacy.md)
> 
> 

이 문서에서는 Azure의 Azure 가상 컴퓨터를 온-프레미스 사이트로 장애 복구하는 방법을 설명합니다. 이 [자습서](site-recovery-vmware-to-azure-classic.md)를 사용하여 온-프레미스 사이트에서 Azure로 장애 조치한 후 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버 장애 복구가 준비된 경우 이 문서의 지침에 따릅니다.

## <a name="overview"></a>개요
이 다이어그램은 이 시나리오에 대한 장애 복구 아키텍처를 보여 줍니다.

프로세스 서버가 온-프레미스이고 Express 경로를 사용할 경우 이 아키텍처를 사용합니다.

![Express 경로용 아키텍처 다이어그램](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

프로세스 서버가 Azure에 있고 VPN 또는 Express 경로를 연결할 경우 이 아키텍처를 사용합니다.

![VPN용 아키텍처 다이어그램](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.PNG)

포트 및 장애 복구(failback) 아키텍처 다이어그램의 전체 목록을 보려면 아래 이미지를 참조하세요.

![모든 포트의 장애 조치-장애 복구](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

장애 복구의 작동 방식은 다음과 같습니다.

* Azure로 장애 조치한 후 다음 몇 가지 단계에서 온-프레미스 사이트로 장애 복구합니다.
  * **1단계**: 온-프레미스 사이트에서 실행 중인 VMware VM으로 다시 복제가 시작되도록 Azure VM을 다시 보호합니다. 
  * **2단계**: Azure VM이 온-프레미스 사이트에 복제된 후 Azure에서 장애 복구하도록 장애 조치를 실행합니다.
  * **3단계**: 데이터가 장애 복구된 후 Azure로 복제가 시작하도록 장애 복구한 온-프레미스 VM을 다시 보호합니다.

### <a name="failback-to-the-original-or-alternate-location"></a>원래 또는 대체 위치로 장애 복구
VMware VM을 장애 조치한 경우 온-프레미스에 아직 있는 경우 동일한 원본 VM으로 장애 복구할 수 있습니다. 이 시나리오에서 델타 변경 내용만 장애 복구됩니다. 다음 사항에 유의하세요.

* 실제 서버를 장애 조치한 경우 장애 복구는 항상 새 VMware VM으로 수행됩니다.
  * 물리적 컴퓨터를 장애 복구(failback)하기 전에 다음 사항에 유의하세요.
    * 보호된 물리적 컴퓨터는 Azure에서 VMware로 다시 장애 조치(failover)된 경우 가상 컴퓨터로 복구됩니다.
    * 장애 복구(failback)해야 하는 필요한 ESX/ESXi 호스트와 함께 하나 이상의 마스터 대상 서버를 검색해야 합니다.
* 원래 VM으로 장애 복구하는 경우 다음이 필요합니다.
  
  * VM이 vCenter 서버에 의해 관리되는 경우 마스터 대상의 ESX 호스트에서 VM 데이터 저장소에 액세스할 수 있어야 합니다.
  * VM이 ESX 호스트에 있지만 vCenter에서 관리하지 않는 경우 VM의 하드 디스크는 MT의 호스트에서 액세스할 수 있는 데이터 저장소에 있어야 합니다.
  * VM이 ESX 호스트에 있고 vCenter를 사용하지 않는 경우 다시 보호하기 전에 MT의 ESX 호스트 검색을 완료해야 합니다. 물리적 서버도 장애 복구하는 경우 적용됩니다.
  * 다른 옵션(온-프레미스 VM이 있는 경우)은 장애 복구를 수행하기 전에 삭제하는 것입니다. 그런 다음 장애 복구는 마스터 대상 ESX 호스트와 동일한 호스트에 새 VM을 생성합니다.
* 대체 위치로 장애 복구하는 경우 데이터는 온-프레미스 마스터 대상 서버에서 사용한 것과 동일한 데이터 저장소와 동일한 ESX 호스트로 복구됩니다. 

## <a name="prerequisites"></a>필수 조건
* VMware VM 및 물리적 서버를 장애 복구하려면 VMware 환경이 필요합니다. 물리적 서버로 장애 복구는 지원되지 않습니다.
* 장애 복구하려면 보호를 처음 설정할 때 Azure 네트워크를 만들어야 합니다. 장애 복구는 온-프레미스 사이트에 있는 Azure VM이 있는 Azure 네트워크에서 VPN 또는 Express 경로 연결이 필요합니다.
* 장애 복구하려는 VM이 vCenter 서버를 통해 관리되는 경우 vCenter 서버의 VM 검색에 필요한 권한이 있는지 확인해야 합니다. [자세히 알아보기](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* 스냅숏이 VM에 있는 경우 다시 보호에 실패합니다. 스냅숏 또는 디스크를 삭제할 수 있습니다. 
* 장애 복구하려면 여러 구성 요소를 만들어야 합니다.
  * **Azure에 프로세스 서버를 만듭니다**. 장애 복구 중에 만들고 계속 실행해야 하는 Azure VM입니다. 장애 복구를 완료한 후 컴퓨터를 삭제할 수 있습니다.
  * **마스터 대상 서버 만들기**: 마스터 대상 서버는 장애 복구 데이터를 송수신합니다. 온-프레미스를 만든 관리 서버에는 기본적으로 설치된 마스터 대상 서버가 있습니다. 그러나 장애 복구 트래픽 볼륨에 따라 장애 복구에 대한 별도 마스터 대상 서버를 만들어야 할 수도 있습니다.
  * Linux에서 실행되는 추가 마스터 대상 서버를 만들려는 경우 아래에 설명된 대로 마스터 대상 서버를 설치하기 전에 Linux VM을 설정해야 합니다.
* 장애 복구(failback)를 수행할 때 구성 서버는 온-프레미스여야 합니다. 장애 복구(failback)하는 동안 구성 서버 데이터베이스에 가상 컴퓨터가 존재해야 하며, 그러지 않으면 장애 복구(failback)가 실패하게 됩니다. 따라서 서버의 예정된 정기 백업을 수행해야 합니다. 재해가 발생할 경우 장애 복구(failback)가 작동할 수 있도록 동일한 IP 주소를 사용하여 복원해야 합니다.
* VMware의 마스터 대상 VM 구성 매개 변수에서 disk.enableUUID=true로 설정합니다. 이 행이 존재하지 않는 경우 추가합니다. 올바르게 탑재할 수 있도록 VMDK에 일관성 있는 UUID를 제공해야 합니다.
* **마스터 대상 서버는 저장소 vMotion된 저장소일 수 없습니다**. 이 때문에 장애 복구에 오류가 발생할 수 있습니다. 디스크를 사용할 수 있도록 되지 않기 때문에 VM 상태가 되지 않습니다.

## <a name="failback-policy"></a>장애 복구 정책
온-프레미스로 다시 복제하려면 장애 복구 정책이 필요합니다. 이 정책은 정방향 정책을 만들 때 자동으로 생성됩니다. 다음 사항에 유의하세요. 

1. 이 정책은 만들어지는 동안 구성 서버와 자동으로 연결됩니다.
2. 이 정책은 편집할 수 없습니다.
3. 정책의 설정 값(RPO 임계값 = 15분, 복구 지점 보존 = 24시간, 응용 프로그램 스냅숏 빈도 = 60분) ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-the-process-server-in-azure"></a>Azure에 프로세스 서버 설정
Azure에 프로세스 서버를 설치해야 Azure VM이 데이터를 온-프레미스 마스터 대상 서버에 다시 보낼 수 있습니다. 

클래식 리소스로 컴퓨터를 보호한 경우(즉, Azure에서 복구된 VM이 클래식 VM) Azure에서 클래식 프로세스 서버가 필요합니다. 배포 유형으로 리소스 관리자로서 컴퓨터를 복구한 경우, 리소스 관리자 배포 유형의 프로세스 서버가 필요합니다. 이 형식은 프로세스 서버가 배포된 Azure 가상 네트워크가 선택합니다.

1. 자격 증명 모음 > 설정 > Site Recovery 인프라("관리" 헤드에 있음) > **구성 서버**("VMware 및 실제 컴퓨터" 헤드에 있음)에서 구성 서버를 선택합니다. 아래 예제의 화면 예제에서 노란색으로 강조 표시된 "+서버 프로세스"를 클릭합니다.
   
   ![](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
2. 프로세스 서버 배포를 위해 "Azure에서 장애 복구(failback) 프로세스 서버 배포"를 선택합니다.
3. 컴퓨터를 복원한 곳에서 구독을 선택합니다. 
4. 다음으로 컴퓨터를 복구한 Azure 네트워크를 선택합니다. 복구된 VM과 프로세스 서버가 통신할 수 있도록 프로세스 서버는 동일한 네트워크에 위치해야 합니다.
5. *클래식 배포* 네트워크를 선택하면 Azure 갤러리를 통해 새로운 VM을 만들고 그 안에 프로세스 서버를 설치할 것인지에 대한 질문을 받게 됩니다.
   
    ![](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)
   
   1. 이미지의 이름은 *Microsoft Azure Site Recovery 프로세스 서버 V2*입니다. 배포 모델로 *클래식* 을 선택했는지 확인하도록 합니다.
      
       ![](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
   2. [여기에 주어진](./site-recovery-vmware-to-azure-classicz.md#step-5-install-the-management-server)
6. *Resource Manager* Azure 네트워크를 선택하면 서버 배포를 위해 다음 입력을 제공해야 합니다.
   
   1. 서버 배포를 원하는 리소스 그룹
   2. 서버 이름 제공
   3. 로그인 할 수 있는 사용자 이름과 암호 제공
   4. 서버 배포를 원하는 저장소 계정 선택
   5. 연결할 특정 서브넷과 네트워크 인터페이스를 선택합니다. 참고 - 자신만의 [네트워크 인터페이스](../virtual-network/virtual-networks-multiple-nics.md) (NIC)를 만들고 배포하는 동안 선택해야 합니다.
      
       ![](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)
   6. 확인을 클릭합니다. 이것으로 프로세스 서버 설정과 함께 리소스 관리자 배포 유형 가상 컴퓨터를 생성하는 작업이 트리거됩니다. 서버를 구성 서버에 등록하기 위해 VM 내에서 설치 프로그램을 실행해야 합니다. [다음 단계](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)에 따라 실시할 수 있습니다.
   7. 프로세스 서버를 배포하기 위한 작업이 트리거됩니다.
7. 끝으로 프로세스 서버는 프로세스 서버 탭의 관련 서버 섹션 하에 있는 구성 서버 페이지에 나열돼 있어야 합니다.
    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    >[AZURE.NOTE] **VM 속성**아래에서 서버가 표시되지 않습니다. 등록되는 관리 서버의 **서버** 탭에서만 볼 수 있습니다. 프로세스 서버가 나타날 때까지 약 10-15분이 소요될 수 있습니다.


## <a name="set-up-the-master-target-server-on-premises"></a>마스터 대상 서버 온-프레미스 설정
마스터 대상 서버는 장애 복구 데이터를 받습니다. 마스터 대상 서버는 온-프레미스 관리 서버에 자동으로 설치되지만 많은 데이터를 장애 복구하는 경우 추가 마스터 대상 서버를 설정해야 할 수도 있습니다. 다음과 같이 수행합니다.

> [!NOTE]
> Linux에 마스터 대상 서버를 설치하려는 경우 다음 절차의 지침을 따릅니다.
> 
> 

1. Windows에 마스터 대상 서버를 설치하는 경우 마스터 대상 서버를 설치하는 VM에서 빠른 시작 페이지를 열고 Azure Site Recovery 통합 설치 마법사에 대한 설치 파일을 다운로드합니다.
2. 설치를 실행하고 **시작하기 전에**에서 **배포 규모 확장을 위해 추가 프로세스 서버 추가**를 선택합니다.
3. [관리 서버를 설정](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)했을 때 수행한 것과 동일한 방식으로 마법사를 완료합니다. **구성 서버 세부 정보** 페이지에서 이 마스터 대상 서버의 IP 주소와 VM에 액세스하기 위한 암호를 지정합니다.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>마스터 대상 서버로 Linux VM 설정
Linux VM으로 마스터 대상 서버를 실행하는 관리 서버를 설정하려면 CentOS 6.6 최소 운영 체제를 설치하고 각 SCSI 하드 디스크에 대한 SCSI ID를 검색하고 몇 가지 추가 패키지를 설치하고 일부 사용자 지정 변경 내용을 적용해야 합니다.

#### <a name="install-centos-6.6"></a>CentOS 6.6 설치
1. 관리 서버 VM에 CentOS 6.6 최소 운영 체제를 설치합니다. DVD 드라이브에 ISO를 유지하고 시스템을 부팅합니다. 미디어 테스트를 건너뛰고 언어에서 영어(미국)를 선택하고 **기본 저장소 장치**를 선택하고 하드 드라이브에 중요한 데이터가 없는지 확인하고 **예**를 클릭하고 모든 데이터를 삭제합니다. 관리 서버의 호스트 이름을 입력하고 서버 네트워크 어댑터를 선택합니다.  **편집 시스템** 대화 상자에서 **자동으로 연결**을 선택하고 고정 IP 주소, 네트워크 및 DNS 설정을 추가합니다. 표준 시간대 및 관리 서버에 액세스하기 위한 루트 암호를 지정합니다. 
2. 설치 형식을 묻는 경우 파티션으로 **사용자 지정 레이아웃 만들기** 를 선택합니다.  **다음** select **무료** 를 선택하고 만들기를 클릭합니다. **FS 유형:** **ext4**로 **/**, **/var/crash** 및 **/home partitions**을 만듭니다. **FS 형식: 스왑**으로 스왑 파티션을 만듭니다.
3. 기존 장치가 발견되는 경우 경고 메시지가 표시됩니다. **포맷** 을 클릭하여 파티션 설정으로 드라이브를 포맷합니다. **디스크에 변경 내용 쓰기** 를 클릭하여 파티션 변경 내용을 적용합니다.
4. **부팅 로더 설치** > **다음**을 선택하여 루트 파티션에 부팅 로더를 설치합니다.
5. 설치가 완료되면 **재부팅**을 클릭합니다.

#### <a name="retrieve-the-scsi-ids"></a>SCSI ID 검색
1. 설치 후 VM에서 각 SCSI 하드 디스크에 대한 SCSI ID를 검색합니다. 이를 수행하려면 관리 서버 VM을 종료하고 VMware의 VM 속성에서 VM 항목 > **설정 편집** > **옵션**을 마우스 오른쪽 단추로 클릭합니다.
2. **고급** > **일반 항목**을 선택하고 **구성 매개 변수**를 클릭합니다. 컴퓨터가 실행되는 경우 이 옵션은 비활성화됩니다. 활성화하려면 컴퓨터를 종료해야 합니다.
3. **disk.EnableUUID** 행이 존재하는 경우 값이 **True**(대/소문자 구분)로 설정되었는지 확인합니다. 존재하는 경우 취소하고 부팅된 후 게스트 운영 체제 내에서 SCSI 명령을 테스트할 수 있습니다. 
4. 행이 존재하지 않는 경우 **행 추가**를 클릭하고 **True** 값으로 추가합니다. 큰따옴표를 사용하지 마십시오.

#### <a name="install-additional-packages"></a>추가 패키지 설치
몇 가지 추가 패키지를 다운로드 및 설치해야 합니다. 

1. 마스터 대상 서버가 인터넷에 연결되어 있는지 확인합니다.
2. **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools** 명령을 실행하여 다운로드하고 CentOS 리포지토리에서 15개의 패키지를 설치합니다.
3. 보호하려는 원본 컴퓨터가 루트 또는 부팅 장치에 대해 Linux wit Reiser 또는 XFS 파일 시스템을 실행하는 경우 다음과 같이 추가 패키지를 다운로드하고 설치해야 합니다.
   
   * # <a name="cd-/usr/local"></a>cd /usr/local
   * # <a name="wget-[http://elrepo.org/linux/elrepo/el6/x86_64/rpms/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/rpms/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * # <a name="wget-[http://elrepo.org/linux/elrepo/el6/x86_64/rpms/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/rpms/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * # <a name="rpm-–ivh-kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm-reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm"></a>rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * # <a name="wget-[http://mirror.centos.org/centos/6.6/os/x86_64/packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * # <a name="rpm-–ivh-xfsprogs-3.1.1-16.el6.x86_64.rpm"></a>rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>사용자 지정 변경 내용 적용
설치 후 단계를 완료하고 패키지를 설치한 후 다음을 수행하여 사용자 지정 변경 내용을 적용합니다.

1. RHEL 6-64 통합 에이전트 바이너리를 VM에 복사합니다. **tar –zxvf <file name>** 명령을 실행하여 바이너리를 untar합니다.
2. **# chmod 755 ./ApplyCustomChanges.sh** 명령을 실행하여 사용 권한을 부여합니다.
3. 스크립트: **# ./ApplyCustomChanges.sh**를 실행합니다. 스크립트를 한 번만 실행해야 합니다. 스크립트가 성공적으로 실행된 후 서버를 다시 부팅합니다.

## <a name="run-the-failback"></a>장애 복구 실행
### <a name="reprotect-the-azure-vms"></a>Azure VM 다시 보호
1. 자격 증명 모음 > 복제된 항목에서 장애 조치된 VM을 선택하고 **다시 보호**를 마우스 오른쪽 단추로 클릭합니다. 컴퓨터를 클릭하고 명령 단추에서 다시 보호를 선택할 수도 있습니다.
2. 블레이드에서 보호 “Azure에서 온-프레미스” 보호 방향이 이미 선택되어 있는 것을 볼 수 있습니다.
3. **마스터 대상 서버** 및 **프로세스 서버**에서 온-프레미스 마스터 대상 서버 및 Azure VM 프로세스 서버를 선택합니다.
4. 디스크 온-프레미스를 복구하려는 **데이터 저장소**를 선택합니다. 이 옵션은 온-프레미스 VM이 삭제되고 새 디스크를 생성해야 하는 경우에 사용합니다. 디스크가 이미 존재하면 이 옵션을 무시합니다. 다만 값은 지정해야 합니다. 
5. 보존 드라이브는 VM이 온-프레미스에 다시 복제되는 특정 시점을 중지하는데 사용됩니다. 보존 드라이브의 일부 조건은 아래와 같습니다. 여기에는 마스터 대상 서버에 대한 드라이브가 나열되어 있지 않습니다.
   a. 볼륨은 다른 용도(복제의 대상 등)로는 사용되지 않아야 합니다. b. 볼륨은 잠금 모드가 아니어야 합니다.
   c. 볼륨은 캐시 볼륨이 아니어야 합니다. (MT 설치가 해당 볼륨에 존재하지 않아야 합니다. PS+MT 사용자 지정 설치 볼륨은 보존 볼륨에 적합하지 않습니다. 여기 설치된 PS+MT 볼륨은 MT의 캐시 볼륨입니다.) d. 볼륨 파일 시스템 유형은 FAT 및 FAT32이 아니어야 합니다.
   e. 볼륨 용량은 0이 아닌 값이어야 합니다.
   e. Windows에 대한 기본 보존 볼륨은 R 볼륨입니다. 
   f. Linux에 대한 기본 보존 볼륨은 /mnt/보존입니다.
6. 장애 복구 정책은 자동으로 선택됩니다.
7. **확인** 을 클릭하여 시작하면 다시 보호 작업이 Azure의 VM을 온-프레미스 사이트로 복제하는 작업을 시작합니다. **작업** 탭에서 진행률을 추적할 수 있습니다.

대체 위치로 복구하려는 경우 마스터 대상 서버에 대해 구성된 보존 드라이브 및 데이터 저장소를 선택합니다. 온-프레미스 사이트로 장애 복구할 때 장애 복구 보호 계획의 VMware VM은 마스터 대상 서버와 동일한 데이터 저장소를 사용합니다. 동일한 온-프레미스 VM으로 복제본 Azure VM을 복구하려는 경우 온-프레미스 VM은 마스터 대상 서버와 동일한 데이터 저장소에 있어야 합니다. VM 온-프레미스가 없는 경우 다시 보호하는 동안 새 VM 온-프레미스가 만들어집니다.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

복구 계획 수준에서 다시 보호할 수도 있습니다. 복제 그룹이 있는 경우, 복구 계획을 통해서만 다시 보호될 수 있습니다. 복구 계획을 통해 다시 보호를 하는 동안 보호된 모든 컴퓨터에 위의 값을 제공해야 합니다. 

> [!NOTE]
> 동일한 마스터 대상을 사용하여 복제 그룹을 보호해야 합니다. 다른 마스터 대상 서버를 통해 보호된다면 특정 시간이 지나면 더 이상 이를 수행할 수 없습니다.
> 
> 

### <a name="run-a-failover-to-the-on-premises-site"></a>온-프레미스 사이트로 장애 조치 실행
VM을 다시 보호한 후 Azure에서 온-프레미스로 장애 조치를 시작할 수 있습니다. 

1. 복제된 항목 페이지에서 가상 컴퓨터를 선택하고 마우스 오른쪽 단추를 클릭하여 **계획되지 않은 장애 조치**를 선택합니다.
2. **장애 조치(failover) 확인** 에서 장애 조치(failover) 방향(Azure에서)을 확인하고 장애 조치(failover)(최신 또는 최근 응용 프로그램 일치)에 사용할 복구 지점을 선택합니다. 응용 프로그램 일치 시점이 시간상 더 나중에 있게되며 이로써 데이터 손실이 야기됩니다.
3. 장애 조치 중 사이트 복구는 Azure VM을 종료합니다. 장애 복구가 예상대로 완료된 후 Azure VM이 예상대로 종료되었는지 확인할 수 있습니다.

### <a name="reprotect-the-on-premises-site"></a>온-프레미스 사이트 다시 보호
장애 복구가 완료된 후 가상 컴퓨터를 커밋하여 Azure에서 복구된 VM이 삭제되었는지를 확인해야 합니다.

1. 보호된 항목을 마우스 오른쪽 단추로 클릭하고 커밋을 클릭합니다. 이 작업이 Azure에서 이전에 복구된 가상 컴퓨터의 제거를 트리거합니다.

커밋이 완료된 후 데이터는 온-프레미스 사이트로 다시 이동되지만 보호할 수 없습니다. Azure에 복제를 다시 시작하려면 다음을 수행합니다.

1. 자격 증명 모음 > 설정 > 복제된 항목에서 장애 복구(failback)된 VM을 선택하고 **다시 보호**를 클릭합니다. 
2. 다시 Azure로 데이터를 전송하는데 사용해야 하는 프로세스 서버의 값을 제공합니다.
3. 확인을 클릭하여 다시 보호 작업을 시작합니다.

다시 보호가 완료되면 VM이 다시 Azure로 복제되고 장애 조치(failover)를 수행할 수 있습니다.

### <a name="common-issues-in-failback"></a>장애 복구(failback)의 일반적인 문제
1. 읽기 전용 사용자 vCenter 검색을 수행하고 가상 컴퓨터를 보호한 경우 작업에 성공하고 장애 조치(failover)가 작동합니다. 다시 보호 시점에서는 데이터 저장소를 검색할 수 없으므로 작업에 실패합니다. 하나의 증상으로, 다시 보호하는 동안 나열된 데이터 저장소가 보이지 않습니다. 이 문제를 해결하려면 사용 권한이 있는 적절한 계정을 사용하여 vCenter 자격 증명을 업데이트하고 작업을 다시 시도하면 됩니다. [자세히 알아보기](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Linux VM을 장애 복구(failback)하고 온-프레미스에서 실행하는 경우 네트워크 관리자 패키지가 컴퓨터에서 제거됩니다. 이는 Azure에서 VM이 복구된 경우 네트워크 관리자 패키지가 제거되기 때문입니다.
3. VM이 고정 IP 주소로 구성되고 Azure로 장애 조치(failover)된 경우 IP 주소는 DHCP를 통해 가져옵니다. 온-프레미스로 다시 장애 조치(failover)하면 VM에서 계속 DHCP를 사용하여 IP 주소를 가져옵니다. 컴퓨터에 수동으로 로그인하고 필요한 경우 IP 주소를 고정 주소로 다시 설정해야 합니다.
4. ESXi 5.5 무료 버전 또는 vSphere 6 하이퍼바이저 무료 버전을 사용하는 경우 장애 조치(failover)에는 성공하지만 장애 복구(failback)에는 실패합니다. 장애 복구(failback)를 사용하도록 설정하려면 평가판 라이선스로 업그레이드해야 합니다.

## <a name="failing-back-with-expressroute"></a>Express 경로로 장애 복구
VPN 연결 또는 Azure Express 경로를 통해 장애 복구할 수 있습니다. Express 경로를 사용하려면 다음에 유의합니다.

* Express 경로는 원본 컴퓨터가 장애 조치되고 장애 조치가 발생한 후 Azure VM이 위치하는 Azure 가상 네트워크에 설치되어 있어야 합니다.
* 데이터는 공용 끝점의 Azure 저장소 계정에 복제됩니다. Express 경로를 사용하려면 사이트 복구 복제에 대한 대상 데이터 센터로 Express 경로에 공용 피어링을 설정해야 합니다.

<!---HONumber=Oct16_HO2-->


