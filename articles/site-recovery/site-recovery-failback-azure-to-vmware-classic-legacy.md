---
title: "클래식 레거시 포털에서 Azure의 VMware VM 장애 복구 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 Azure에 복제된 VMware 가상 컴퓨터를 장애 복구하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: a63524bf-990c-42ee-8554-e017e6e67e68
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 6e6d0539c35d403c24ddd5e0b5d49f4d6116eead
ms.openlocfilehash: 86f7e0565fe30af613f06764a494cbb996abcabe
ms.lasthandoff: 01/30/2017


---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>Azure Site Recovery를 사용하여 Azure의 VMware 가상 컴퓨터 및 물리적 서버를 VMware로 장애 복구(failback)(레거시)
> [!div class="op_single_selector"]
> * [Azure 포털](site-recovery-failback-azure-to-vmware.md)
> * [Azure 클래식 포털](site-recovery-failback-azure-to-vmware-classic.md)
> * [Azure 클래식 포털(레거시)](site-recovery-failback-azure-to-vmware-classic-legacy.md)
>
>

이 문서에서는 [Azure Site Recovery란?](site-recovery-overview.md)을 사용하여 VMware 가상 컴퓨터 및 Windows/Linux 물리적 서버를 온-프레미스 사이트에서 Azure로 복제한 후 Azure에서 온-프레미스 사이트로 장애 복구하는 방법에 대해 설명합니다.

여기서는 이전 구성을 설명하며 새 자격 증명 모음에 사용하면 안 됩니다.

## <a name="architecture"></a>아키텍처
이 다이어그램은 장애 조치 및 장애 복구 시나리오를 나타냅니다. 파란색 선은 장애 조치 중에 사용되는 연결입니다. 빨간색 선은 장애 조치 중에 사용되는 연결입니다. 화살표가 있는 선이 인터넷을 통해 이동합니다.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>시작하기 전에
* VMware VM 또는 물리적 서버를 통해 실패해야 하며 Azure에서 실행해야 합니다.
* Azure의 VMware 가상 컴퓨터 및 Windows/Linux 물리적 서버를 온-프레미스 기본 사이트의 VMware 가상 컴퓨터로 장애 복구할 수 있습니다.  물리적 컴퓨터를 장애 복구하는 경우 Azure로 장애 조치는 이를 Azure VM으로 변환시키고 VMware로 장애 복구는 이를 VMware VM으로 변환시킵니다.

장애 복구를 설정하는 방법은 다음과 같습니다.

1. **장애 복구 구성 요소 설정**: 온-프레미스에 vContinuum 서버를 설정하고 Azure의 구성 서버 VM을 가리키도록 해야 합니다. 또한 온-프레미스 마스터 대상 서버에 데이터를 다시 보내려면 프로세스 서버를 Azure VM으로 설정합니다. 장애 조치를 처리하는 구성 서버와 프로세스 서버를 등록합니다. 온-프레미스 마스터 대상 서버를 설치합니다. Windows 마스터 대상 서버가 필요한 경우 vContinuum을 설치하면 자동으로 설정됩니다. Linux가 필요한 경우 별도 서버에서 수동으로 설정해야 합니다.
2. **보호 및 장애 복구 사용**: 구성 요소를 설정한 후 vContinuum에서 Azure VM에 대한 장애 조치를 위해 보호를 사용해야 합니다. VM에 대한 준비 상태 확인을 실행하고 Azure에서 온-프레미스 사이트로 장애 조치를 실행합니다. 장애 복구가 완료된 후 Azure에 복제를 시작하도록 온-프레미스 컴퓨터를 다시 보호합니다.

## <a name="step-1-install-vcontinuum-on-premises"></a>1단계: 온-프레미스에 vContinuum 설치
온-프레미스에 vContinuum 서버를 설치하고 구성 서버를 가리키도록 해야 합니다.

1. [VContinuum을 다운로드합니다](http://go.microsoft.com/fwlink/?linkid=526305).
2. 그런 다음 [vContinuum 업데이트](http://go.microsoft.com/fwlink/?LinkID=533813) 버전을 다운로드합니다.
3. 최신 버전의 vContinuum을 설치합니다. **Welcome** 페이지에서 **다음**을 클릭합니다.
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4. 마법사의 첫 번째 페이지에서 CX 서버 IP 주소 및 CX 서버 포트를 지정합니다. **HTTPS 사용**을 선택합니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image3.png)
5. Azure에서 구성 서버 VM의 **대시보드** 탭에서 구성 서버 IP 주소를 찾습니다.
   ![](./media/site-recovery-failback-azure-to-vmware/image4.png)
6. Azure에서 구성 서버 VM의 **끝점** 탭에서 구성 서버 HTTPS 공용 포트를 찾습니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image5.png)
7. **CS 암호 상세 사항** 페이지에서 구성 서버를 등록할 때 적어둔 암호를 지정합니다. 기억하지 못하는 경우 구성 서버 VM의 **C:\\Program Files (x86)\\InMage Systems\\private\\connection.passphrase**에서 확인합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)
8. **대상 위치 선택** 페이지에서 vContinuum 서버를 설치하려는 위치를 지정하고 **설치**를 클릭합니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image7.png)
9. 설치가 완료되면 vContinuum을 시작할 수 있습니다.
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

## <a name="step-2-install-a-process-server-in-azure"></a>2단계: Azure에 프로세스 서버 설치
프로세스 서버를 Azure에 설치해야 Azure 내의 VM이 데이터를 온-프레미스 마스터 대상 서버에 다시 보낼 수 있습니다.

1. Azure의 **구성 서버** 페이지에서 선택하여 새 프로세스 서버를 추가합니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image9.png)
2. 프로세스 서버 이름, 이름 및 암호를 지정하여 관리자로 가상 컴퓨터에 연결합니다. 프로세스 서버를 등록하려는 구성 서버를 선택합니다. 이 서버는 가상 컴퓨터를 보호하고 장애 조치(failover)하는 데 사용하는 서버와 동일해야 합니다.
3. 프로세스 서버가 배포되어야 하는 Azure 네트워크를 지정합니다. 구성 서버와 동일한 네트워크여야 합니다. 선택한 서브넷에서 고유한 IP 주소를 지정하고 배포를 시작합니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image10.png)
4. 프로세스 서버를 배포하기 위한 작업이 트리거됩니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image11.png)
5. 프로세스 서버가 Azure에 배포되면 지정한 자격 증명을 사용하여 서버에 로그인할 수 있습니다. 온-프레미스 프로세스 서버를 등록한 것과 같은 방식으로 프로세스 서버를 등록합니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

> [!NOTE]
> 장애 복구 중에 등록된 서버는 Site Recovery의 VM 속성 아래에 표시되지 않습니다. 등록된 구성 서버의 **서버** 탭 아래에서만 표시됩니다. 프로세스 서버가 탭에 표시될 때까지 약 10-15분 정도 걸릴 수 있습니다.
>
>

## <a name="step-3-install-a-master-target-server-on-premises"></a>3단계: 온-프레미스에 마스터 대상 서버 설치
원본 가상 컴퓨터 운영 체제에 따라 Linux 또는 Windows 마스터 대상 서버를 온-프레미스에 설치해야 합니다.

### <a name="deploy-a-windows-master-target-server"></a>Windows 마스터 대상 서버 배포
Windows 마스터 대상은 이미 vContinuum 설정에 포함되어 있습니다. vContinuum을 설치하면 마스터 대상이 동일한 컴퓨터에 배포되며 구성 서버에 등록됩니다.

1. 배포를 시작하려면 Azure에서 VM을 복구하고자 하는 ESX 호스트에 빈 컴퓨터 온-프레미스를 생성합니다.
2. VM에 최소&2;개의 디스크가 연결되어 있는지 확인하십시오. 하나는 운영 체제에, 다른 것은 보존 드라이브에 사용됩니다.
3. 운영 체제를 설치합니다.
4. 서버에 vContinuum을 설치합니다. 이 또한 마스터 대상 서버 설치를 완료합니다.

### <a name="deploy-a-linux-master-target-server"></a>Linux 마스터 대상 서버 배포
1. 배포를 시작하려면 Azure에서 VM을 복구하고자 하는 ESX 호스트에 빈 컴퓨터 온-프레미스를 생성합니다.
2. VM에 최소&2;개의 디스크가 연결되어 있는지 확인하십시오. 하나는 운영 체제에, 다른 것은 보존 드라이브에 사용됩니다.
3. Linux 운영 체제를 설치합니다. Linux 마스터 대상 시스템은 루트 또는 보존 저장소 공간에 LVM을 사용하지 않습니다. Linux 마스터 대상 서버는 기본적으로 LVM 파티션/디스크 검색을 방지하도록 구성됩니다.
4. 만들 수 있는 파티션은 다음과 같습니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image13.png)
5. 마스터 대상 설치를 시작하기 전에 아래 사후 설치 단계를 수행합니다.

#### <a name="post-os-installation-steps"></a>사후 OS 설치 단계
Linux 가상 컴퓨터에서 각 SCSI 하드 디스크에 대해 SCSI ID를 가져오려면 다음과 같이 “disk.EnableUUID = TRUE” 매개 변수를 활성화합니다.

1. 가상 컴퓨터를 종료합니다.
2. 왼쪽 패널에서 VM 항목을 마우스 오른쪽 단추로 클릭하고 **설정 편집**을 선택합니다.
3. **옵션** 탭을 클릭합니다. **고급\>일반 항목** > **구성 매개 변수**를 선택합니다. **구성 매개 변수** 옵션은 컴퓨터가 종료될 때만 사용할 수 있습니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)
4. **disk.EnableUUID** 가 있는 행이 존재하는지 확인합니다. 존재하며 **False**로 설정된 경우 **True**로 설정합니다(대/소문자 구분 안 함). 존재하며 true로 설정되어 있는 경우 **취소** 를 클릭하고 부팅된 후 게스트 운영 체제 내에서SCSI 명령을 테스트합니다. 존재하지 않는 경우 **행 추가**를 클릭합니다.
5. **이름** 열에 disk.EnableUUID를 추가합니다. 해당 값을 TRUE로 설정합니다. 위의 값을 큰따옴표와 함께 추가하지 마세요.

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>추가 패키지 다운로드 및 설치
참고: 추가 패키지를 다운로드하고 설치하기 전에 시스템이 인터넷에 연결되어 있는지 확인합니다.

\# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

이 명령은 CentOS 6.6 저장소에서 이러한 15개의 패키지를 다운로드 및 설치:

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

원본 컴퓨터가 루트 또는 부팅 장치에 Reiser 또는 XFS 파일 시스템을 사용하는 경우 보호하기 전에 다음 패키지를 Linux 마스터 대상에 다운로드하고 설치해야 합니다.

\# cd /usr/local

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

\# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### <a name="apply-custom-configuration-changes"></a>사용자 지정 구성 변경 내용 적용
이러한 변경 내용을 적용하기 전에 이전 섹션을 완료했는지 확인한 후 다음 단계를 수행합니다.

1. RHEL 6-64 통합 에이전트 바이너리를 새롭게 생성한 OS에 복사합니다.
2. **tar -zxvf \<파일 이름\>** 명령을 실행하여 바이너리를 untar합니다.
3. \# **chmod 755 ./ApplyCustomChanges.sh** 명령을 실행하여 사용 권한을 부여합니다.
4. **\# ./ApplyCustomChanges.sh** 스크립트를 실행합니다. 서버에서 스크립트를 한 번만 실행합니다. 스크립트가 실행된 후 서버를 다시 시작합니다.

### <a name="install-the-linux-server"></a>Linux 서버 설치
1. [다운로드](http://go.microsoft.com/fwlink/?LinkID=529757) 합니다.
2. 선택한 sftp 클라이언트 유틸리티를 사용하여 파일을 Linux 마스터 대상 가상 컴퓨터에 복사합니다. 또는 Linux 마스터 대상 가상 컴퓨터에 로그인 하여 wget을 사용하여 제공된 링크에서 설치 패키지를 다운로드할 수 있습니다.
3. 선택한 ssh 클라이언트를 사용하여 Linux 마스터 대상 서버 가상 컴퓨터에 로그인합니다.
4. VPN 연결을 통해 Linux 마스터 대상 서버를 배포한 Azure 네트워크에 연결하는 경우 가상 컴퓨터 **대시보드** 탭 및 포트 22에서 찾을 수 있는 서버에 대한 내부 IP 주소를 사용하여 Secure Shell을 통해 Linux 마스터 대상 서버에 연결합니다.
5. 공용 인터넷 연결을 통해 Linux 마스터 대상 서버에 연결하는 경우 Linux 마스터 대상 서버의 공용 가상 IP 주소(가상 컴퓨터 **대시보드** 탭에서) 및 ssh용으로 생성된 공용 끝점을 사용하여 Linux 서버에 로그인합니다.
6. 설치 관리자 파일을 포함하는 디렉터리에서 *“tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64\”*를 실행하여 GZip 압축된 Linux 마스터 대상 서버 설치 프로그램 tar 보관 파일에서 파일을 추출합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)
7. 설치 프로그램 파일을 다른 디렉터리로 추출했다면 tar 압축 파일의 내용이 추출된 디렉터리로 변경합니다. 이 디렉터리 경로에서 “sudo ./install.sh”를 실행합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)
8. 주 역할을 선택하라는 메시지가 나타나면 **2(마스터 대상)**를 선택합니다. 다른 대화형 설치 옵션은 기본값으로 그대로 둡니다.
9. 설치가 계속되고 호스트 구성 인터페이스가 나타날 때까지 기다립니다. Linux 마스터 대상 서버에 대한 호스트 구성 유틸리티는 명령줄 유틸리티입니다. ssh 클라이언트 유틸리티 창의 크기를 조정하지 마십시오. 화살표 키를 사용하여 **전역** 옵션을 선택하고 키보드에서 ENTER 키를 누릅니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)
10. **IP** 필드에 구성 서버의 내부 IP 주소를 입력한 다음(구성 서버 VM의 **대시보드** 탭에서 찾을 수 있음) ENTER 키를 누릅니다. **포트**에 **22**를 입력하고 ENTER 키를 누릅니다.
11. **HTTPS 사용**을 **예**로 두고 ENTER 키를 누릅니다.
12. 구성 서버에서 생성된 암호를 입력합니다. Windows 컴퓨터에서 PUTTY 클라이언트를 사용하여 Linux 가상 컴퓨터에 ssh를 실행하는 경우 Shift + Insert를 사용하여 클립보드의 내용을 붙여넣을 수 있습니다. Ctrl + C를 사용하여 로컬 클립보드에 암호를 복사하고 Shift + Insert를 사용하여 붙여넣습니다. ENTER 키를 누릅니다.
13. 오른쪽 화살표 키를 사용하여 종료를 찾아가서 ENTER 키를 누릅니다. 설치가 완료될 때까지 기다립니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

어떠한 이유로 Linux 마스터 대상 서버를 구성 서버에 등록하지 못했다면 /usr/local/ASR/Vx/bin/hostconfigcli로부터 호스트 구성 유틸리티를 실행하여 다시 수행할 수 있습니다(먼저 chmod를 슈퍼 사용자로 실행하여 이 디렉터리에 대한 액세스 권한을 설정해야 함).

#### <a name="validate-master-target-registration"></a>마스터 대상 등록의 유효성 검사
Azure Site Recovery 자격 증명 모음 > **구성 서버** > **서버 세부 정보**에서 마스터 대상 서버가 구성 서버로 성공적으로 등록되었는지 확인할 수 있습니다.

> [!NOTE]
> 마스터 대상 서버를 등록한 후 Azure에서 가상 컴퓨터가 삭제되었을 수 있거나 끝점이 제대로 구성되지 않았다는 구성 오류가 발생하는 경우 마스터 대상이 Azure에 배포될 때 Azure 끝점에서 마스터 대상 구성이 감지되어도 온-프레미스 마스터 대상 서버 온-프레미스에 대해 true가 아닙니다. 장애 복구에 영향을 주지 않으며 이러한 오류는 무시할 수 있습니다.
>
>

## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>4단계: 온-프레미스 사이트에 가상 컴퓨터 보호
장애 복구 전에 온-프레미스 사이트에 VM을 보호해야 합니다.

### <a name="before-you-begin"></a>시작하기 전에
VM이 Azure로 장애 조치되면 페이지 파일에 임시 드라이브가 추가됩니다. 이것은 이미 페이지 파일 전용 드라이브를 가지고 있을 수도 있으므로 일반적으로 장애 조치된 VM에서 필요로 하지 않는 추가 드라이브입니다. 가상 컴퓨터의 역방향 보호를 시작하기 전에 보호되지 않도록 드라이브가 오프라인으로 가져와지도록 해야 합니다. 다음과 같이 수행합니다.

1. 컴퓨터 관리를 열고 저장소 관리를 선택하여 온라인 디스크와 컴퓨터에 연결된 디스크를 나열시킵니다.
2. 컴퓨터에 연결된 임시 디스크를 선택하고 오프라인으로 전환을 선택합니다.

### <a name="protect-the-vms"></a>VM 보호
1. Azure 포털에서 가상 컴퓨터의 상태를 확인하여 장애 조치되었는지 확인합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)
2. 컴퓨터에 vContinuum을 시작합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)
3. **새 보호** 를 클릭하고 작업 시스템 유형을 선택합니다.
4. 열리는 새 창에서 장애 복구하려는 VM에 대해 **OS 유형** > **세부 정보**를 선택합니다. **주 서버 세부 정보**에서 보호하려는 가상 컴퓨터를 식별하고 선택합니다. VM이 장애 조치 전에 있었던 vCenter 호스트 이름 아래에 나열됩니다.
5. 가상 컴퓨터를 선택하여 보호하면(이미 Azure에 장애 조치된 경우) 팝업 창이 가상 컴퓨터에 대한 두 가지 항목을 제공합니다. 이것은 구성 서버가 등록된 가상 컴퓨터의 인스턴스&2;개를 발견했기 때문입니다. 올바른 VM을 보호할 수 있도록 온-프레미스 VM에 대한 항목을 제거해야 합니다. 여기에서 올바른 Azure VM 항목을 식별하려면 Azure VM에 로그인하여 C:\Program Files (x86)\Microsoft Azure Site Recovery\Application Data\etc로 이동합니다. 파일 drscout.conf에서 호스트 ID를 식별합니다. vContinuum 대화 상자에서 VM에서 호스트 ID를 발견한 항목을 유지합니다. 다른 모든 항목을 삭제합니다. 올바른 VM을 선택하기 위해 해당 IP 주소를 참조할 수 있습니다. IP 주소 범위 온-프레미스는 온-프레미스 VM이 됩니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

   ![](./media/site-recovery-failback-azure-to-vmware/image23.png)
6. VCenter 서버에서 가상 컴퓨터를 중지합니다. 또한 온-프레미스에서 VM을 삭제할 수 있습니다.
7. 그런 다음 VM을 보호하고자 하는 온-프레미스 MT 서버를 지정합니다. 이를 위해 장애 복구하려는 vCenter 서버에 연결합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)
8. VM을 복구하려는 호스트를 기반으로 마스터 대상 서버를 선택합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)
9. 각 가상 컴퓨터에 대해 복제 옵션을 제공합니다. 이를 위해 VM이 복구되는 복구 쪽 데이터 저장소를 선택해야 합니다. 표에서 각 VM에 대해 제공해야 하는 다양한 옵션을 요약합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

   | **옵션** | **옵션이 권장하는 값** |
   | --- | --- |
   |  프로세스 서버 IP 주소 |Azure에 배포된 프로세스 서버를 선택합니다. |
   |  보존 크기(MB) | |
   |  보존 값 |1 |
   |  날짜/시간 |일 |
   |  일관성 간격 |1 |
   |  대상 데이터 저장소를 선택합니다. |복구 쪽에서 사용할 수 있는 데이터 저장소입니다. 이 데이터 저장소는 공간이 충분해야 하며 가상 컴퓨터를 복원하려는 ESX 호스트에서 제공되어야 합니다. |
10. 온-프레미스 사이트로 장애 조치 후 가상 컴퓨터가 입수하는 속성을 구성할 수 있습니다. 속성은 다음 표에 요약되어 있습니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    | **속성** | **세부 정보** |
    | --- | --- |
    | 네트워크 구성 |검색된 각 네트워크 어댑터에 대해 선택하고 **변경** 을 클릭하여 가상 컴퓨터에 대한 장애 복구 IP 주소를 구성합니다. |
    | 하드웨어 구성 |VM에 대한 CPU 및 메모리를 지정합니다. 설정은 보호하고자 하는 모든 VM에 적용할 수 있습니다. CPU 및 메모리에 대해 올바른 값을 식별하려면 IAAS Vm 역할 크기를 참조하고 할당된 메모리 및 코어 수를 확인할 수 있습니다. |
    | 표시 이름 |온-프레미스로 장애 복구 후 vCenter 인벤토리에 표시될 가상 컴퓨터의 이름을 변경할 수 있습니다. 기본 이름은 가상 컴퓨터 호스트 이름입니다. VM 이름을 식별하려면 보호 그룹 내 VM 목록을 참조할 수 있습니다. |
    | NAT 구성 |아래에 자세히 설명합니다. |

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>NAT 설정 구성
1. 가상 컴퓨터의 보호를 활성화하려면 통신 채널&2;개를 구축해야 합니다. 첫 번째 채널은 가상 컴퓨터와 프로세스 서버 사이입니다. 이 채널은 VM에서 데이터를 수집하고 마스터 대상 서버에 데이터를 전송하는 프로세스 서버에 보냅니다. 프로세스 서버와 보호할 가상 컴퓨터가 동일한 Azure 가상 네트워크에 있다면 NAT 설정을 사용하지 않아도 됩니다. 그렇지 않으면 NAT 설정을 지정합니다. Azure에서 프로세스 서버의 공용 IP 주소를 봅니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
2. 두 번째 채널은 프로세스 서버와 마스터 대상 서버 사이입니다. NAT을 사용할 것인지 여부는 VPN 기반 연결을 사용하는지 또는 인터넷을 통해 통신하는지 여부에 따라 다릅니다. VPN을 사용하는 경우 NAt를 선택하지 마십시오. 인터넷 연결을 사용하는 경우만 선택합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)
3. 지정한 대로 온-프레미스 가상 컴퓨터를 삭제하지 않았으며 장애 복구를 실행하는 데이터 저장소에 여전히 기존의 VMDK가 포함되어 있다면 장애 복구 VM이 역시 새로운 위치에 생성되도록 해야 합니다. 이를 위해 **고급** 설정을 선택하고 **폴더 이름 설정**에서 복원할 대체 폴더를 지정합니다. 다른 옵션은 기본 설정을 그대로 둡니다. 모든 서버에 폴더 이름을 설정을 적용합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)
4. 가상 컴퓨터가 준비 검사를 실행하여 온-프레미스로 다시 보호될 준비가 되었는지 확인합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)
5. 완료할 때까지 기다립니다. 모든 VM에서 완료되면 보호 계획에 대한 이름을 지정할 수 있습니다. 그런 다음 **보호** 를 클릭하여 시작합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)
6. vContinuum에서 진행률을 모니터링할 수 있습니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)
7. **보호 계획 활성화** 단계가 완료된 후 사이트 복구 포털에서 VM 보호를 모니터링할 수 있습니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)
8. VM을 클릭하고 진행률을 모니터링하여 정확한 상태를 볼 수 있습니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>장애 복구 계획 준비
응용프로그램이 언제든지 온-프레미스 사이트로 장애 복구될 수 있도록 vContinuum을 사용하여 장애 복구 계획을 준비할 수 있습니다. 이러한 복구 계획은 사이트 복구의 복구 계획과 매우 유사합니다.

1. vContinuum을 시작하고 **계획 관리** > **복구**를 선택합니다. 장애 조치 VM에 사용된 모든 계획의 목록을 볼 수 있습니다. 복구를 위해 동일한 계획을 사용할 수 있습니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image37.png)
2. 보호 계획 및 그 안에서 복구하려는 모든 VM을 선택합니다. 각 VM을 선택하면 대상 ESX 서버와 원본 VM 디스크를 포함한 자세한 정보를 볼 수 있습니다. **다음** 을 클릭하여 복구 마법사를 시작하고 복구하려는 VM을 선택합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)
3. 여러 옵션에 따라 복구할 수는 있지만 **최신 태그**를 사용하고 **모든 VM에 적용**을 선택하여 가상 컴퓨터에서 최신 데이터를 사용하는지 확인하는 것이 좋습니다.
4. **준비 검사**를 실행합니다. 이 올바른 매개 변수가 VM 복구를 활성화하도록 구성되는지 확인합니다. **다음**을 클릭하여 모든 검사가 성공적으로 수행되는지 확인합니다. 그렇지 않은 경우 로그를 확인하고 오류를 해결합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)
5. **VM 구성** 에서 복구 설정이 올바르게 설정되었는지 확인합니다. 필요한 경우 VM 설정을 변경할 수 있습니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image40.png)
6. 복구될 가상 컴퓨터의 목록을 검토하고 복구 순서를 지정합니다. 컴퓨터 호스트 이름을 사용하여 VM이 나열됩니다. 컴퓨터 호스트 이름을 가상 컴퓨터에 매핑하기 어려울 수도 있습니다.
   이름을 매핑하려면 Azure의 가상 컴퓨터 **대시보드** 로 이동하고 VM 호스트 이름을 확인합니다.

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)
7. 계획 이름을 지정하고 **나중에 복구**를 선택합니다. 초기 보호가 불완전할 수 있으므로 나중에 복구하는 것이 좋습니다.
8. **복구** 를 클릭하여 계획을 저장하거나 나중이 아닌 지금 복구하도록 선택한 경우 복구를 트리거합니다. 계획이 저장되었는지 복구 상태를 확인할 수 있습니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

   ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>가상 컴퓨터 복구
계획을 만든 후에 가상 컴퓨터를 복구할 수 있습니다. 실행하기 전에 가상 컴퓨터가 동기화를 완료했는지 확인합니다. 복제 상태에서 OK를 표시하는 경우 보호가 완료되었으며 RPO 임계값을 충족하는 것입니다. VM 속성에서 상태를 확인할 수 있습니다.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

복구를 시작하기 전에 Azure 가상 컴퓨터를 끕니다. 이렇게 하면 스플릿 브레인이 없으며 사용자가 응용 프로그램의 사본 하나에 액세스합니다.

1. 저장된 계획을 시작합니다. vContinuum에서 계획 **모니터링** 을 선택합니다. 실행된 모든 계획을 나열합니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image45.png)
2. **복구**에서 계획을 선택하고 **시작**을 클릭합니다. 복구를 모니터링할 수 있습니다. VM이 켜진 후 vcenter에 연결할 수 있습니다.

   ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>장애 복구 후 Azure로 다시 보호
장애 복구가 완료된 후 가상 컴퓨터를 다시 보호하는 것이 좋습니다. 다음과 같이 수행합니다.

1. 가상 컴퓨터 온-프레미스가 작동되는지, 응용프로그램이 도달할 수 있는지 확인합니다.
2. Azure Site Recovery 포털에서 가상 컴퓨터를 선택하고 삭제합니다. 가상 컴퓨터의 보호를 사용하지 않도록 설정하려면 선택합니다. 이렇게 하면 VM이 더 이상 보호되지 않습니다.
3. Azure에서 장애 조치된 Azure 가상 컴퓨터 삭제
4. VSpehere에서 오래된 가상 컴퓨터를 삭제합니다. 이들은 이전에 Azure로 장애 조치된 VM입니다.
5. 사이트 복구 포털에서 최근에 장애 조치된 VM을 보호합니다. 보호된 후 복구 계획에 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [알아봅니다](site-recovery-vmware-to-azure-classic.md) .

