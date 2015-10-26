<properties 
   pageTitle="Azure에서 VMware로 장애 복구 단계" 
   description="이 문서는 Azure Site Recovery 및 vContinuum 도구를 사용하여 가상 컴퓨터를 VMware로 다시 장애 복구할 수 있는 방법을 설명합니다." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="10/07/2015"
   ms.author="ruturajd@microsoft.com"/>

# Azure에서 VMware로 장애 복구 단계

이 문서는 Azure에서 VMware 사이트로 장애 복구하는 데 필요한 단계를 차례대로 안내합니다. [VMware에서 Azure로 보호 및 복구](site-recovery-vmware-to-azure.md)에 대해 이미 자습서에 설명한 단계를 따랐을 것입니다.

Azure에 성공적인 장애 조치 후에는 가상 컴퓨터 탭에 가상 컴퓨터가 나타납니다. 장애 복구를 결정한 경우 다음 단계를 따라야 합니다.

Azure에서 VMware 사이트로 장애 복구하는 경우 가상 컴퓨터로만 복구할 수 있음을 기억하십시오. VMware에서 초기 원본이 물리적 컴퓨터였던 경우에도 VMware로의 장애 복구 후에 뒤따르는 Azure로의 장애 복구는 가상 컴퓨터로 전환됩니다.

## 개요

1.  VContinuum 서버 온-프레미스 설치

    a. CS를 가리키도록 구성합니다.

2.  Azure에 PS를 배포합니다.

3.  MT 온-프레미스를 설치합니다.

4.  장애 복구한 VM을 다시 온-프레미스로 보호하는 단계

    a. 구성 고려 사항

5.  온-프레미스로 다시 VM의 보호를 모니터링

6.  온-프레미스로 다시 VM 장애 조치

다음은 아래 단계를 통해 달성하는 설정 개요입니다. 설정의 일부가 장애 조치 중에 이미 완료되었습니다.

-   파란색 선은 장애 조치 중에 사용되는 연결입니다.

-   빨간색 선은 장애 조치 중에 사용되는 연결입니다.

-   화살표가 있는 선이 인터넷을 통해 이동합니다.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## 온-프레미스에 vContinuum 설치

vContinuum 설정은 [다운로드 위치](http://go.microsoft.com/fwlink/?linkid=526305)에서 볼 수 있습니다. 또한 [다운로드 위치](http://go.microsoft.com/fwlink/?LinkID=533813)에서 제공되는 vConinuum 패치를 설치합니다.

1.  VContinuum의 설치를 시작하려면 설치 프로그램을 시작합니다. **다음**을 클릭합니다. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
2.  CX 서버 IP 주소 및 CX 서버 포트를 지정합니다. HTTPS를 선택합니다.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

3.  CX IP 주소를 검색하려면 Azure의 CS 배포로 이동하고 해당 대시보드를 봅니다.

	![](./media/site-recovery-failback-azure-to-vmware/image4.png)

4.  CX 공용 포트를 검색하려면 VM 페이지에서 끝점 탭으로 이동하고 HTTPs 끝점 공용 포트를 식별합니다.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

5.  CS 암호를 지정합니다. CS 등록 중에 암호를 기록해두어야 합니다. MT 및 PS 배포 중에도 암호를 사용했을 것입니다. 암호를 기억하지 못하는 경우 Azure에서 CS 서버로 이동하여 C:\\Program Files (x86) \\InMage Systems\\private\\connection.passphrase 아래에 저장된 암호를 찾을 수 있습니다.

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

6.  vContinuum 서버를 설치할 위치를 지정하고 설치를 시작합니다.

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

7.  설치가 완료되면 vContinuum을 시작하여 작동하는지 확인할 수 있습니다. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Azure에서 PS 서버를 설치합니다.

프로세스 서버를 Azure에 설치해야 Azure 내의 VM이 데이터를 온-프레미스 MT에 다시 보낼 수 있습니다. 구성 서버와 동일한 네트워크에서 Azure에 PS를 배포해야 합니다.

1.  Azure의 **구성 서버** 페이지에서 선택하여 새 프로세스 서버를 추가합니다.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  프로세스 서버 이름을 지정하고 이름 및 암호를 입력하여 관리자로 가상 컴퓨터에 연결합니다. 프로세스 서버를 등록하는 구성 서버를 선택합니다. 이 서버는 가상 컴퓨터를 보호하고 장애 조치(failover)하는 데 사용하는 서버와 동일해야 합니다. 프로세스 서버가 배포되어야 하는 Azure 네트워크를 지정합니다. 구성 서버와 동일한 네트워크여야 합니다. 선택한 서브넷에서 고유한 IP 주소를 지정하고 배포를 시작합니다.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)


프로세스 서버를 배포하기 위한 작업이 트리거됩니다.

![](./media/site-recovery-failback-azure-to-vmware/image11.png)

프로세스 서버가 Azure에 배포되면 지정한 자격 증명을 사용하여 서버에 로그인할 수 있습니다. 앞 방향의 보호 중에 사용한 단계를 사용하여 PS를 등록합니다.

![](./media/site-recovery-failback-azure-to-vmware/image12.png)

장애 복구(failback) 중에 등록된 서버는 VM 속성 아래에 표시되지 않습니다. 등록된 구성 서버 아래에 있는 서버 탭 아래에서만 표시됩니다. PS가 CS 아래에 나열될 때까지는 10-15분이 걸릴 수 있습니다.

## MT 서버 온-프레미스 설치

원본 측 가상 컴퓨터에 따라 Linux 또는 Windows 마스터 대상 서버를 온-프레미스에 설치해야 합니다.

### Windows MT 배포

Windows MT는 이미 vContinuum 설정에 포함되어 있습니다. vContinuum을 설치하면 MT가 동일한 컴퓨터에 배포되며 구성 서버에 등록됩니다.

1.  배포를 시작하려면 Azure에서 VM을 복구하고자 하는 ESX 호스트에 빈 컴퓨터 온-프레미스를 생성하십시오.

2.  VM에 최소 2개의 디스크가 연결되어 있는지 확인하십시오. 하나는 OS에, 두 번째는 보존 드라이브에 사용됩니다.

3.  운영 체제를 설치합니다.

4.  서버에 vContinuum을 설치합니다. 그러면 MT의 설치가 완료됩니다.

### Linux MT 배포

1.  배포를 시작하려면 Azure에서 VM을 복구하고자 하는 ESX 호스트에 빈 컴퓨터 온-프레미스를 생성하십시오.

2.  VM에 최소 2개의 디스크가 연결되어 있는지 확인하십시오. 하나는 OS에, 두 번째는 보존 드라이브에 사용됩니다.

3.  Linux 운영 체제를 설치합니다. Linux MT(마스터 대상) 시스템은 루트 또는 보존 저장소 공간에 LVM을 사용하지 않습니다. Linux MT는 기본적으로 LVM 파티션/디스크 검색을 방지하도록 구성됩니다.
4.  만들 수 있는 파티션은 다음과 같습니다.

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  MT 설치를 시작하기 전에 아래 사후 설치 단계를 수행하십시오.


#### 사후 OS 설치 단계

Linux 가상 컴퓨터에서 각 SCSI 하드 디스크에 대해 SCSI ID를 가져오려면 “disk.EnableUUID = TRUE” 매개 변수를 활성화해야 합니다. 이 매개 변수를 사용하려면 아래 지정된 단계를 따르십시오.

1. 가상 컴퓨터를 종료합니다.
2. 왼쪽 패널에서 VM의 항목을 마우스 오른쪽 단추로 클릭하고 **설정 편집**을 선택합니다.
3. **옵션** 탭을 클릭합니다. 왼쪽에서 **고급>일반 항목**을 선택하고 오른쪽에 표시되는 **구성 매개 변수**를 클릭합니다. “컴퓨터가 실행 중일 때 "구성 매개 변수" 옵션이 비활성화 상태가 됩니다." 이 탭을 활성화시키려면 컴퓨터를 종료합니다.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. **disk.EnableUUID**가 있는 행이 이미 존재하는지 확인합니다. 존재하며 값이 False로 설정되어 있는 경우 값을 True로 덮어씁니다(True와 False 값은 대소문자를 구분). 존재하며 True로 설정되어 있는 경우 취소를 클릭하고 부팅한 후 게스트 운영 체제 내에서SCSI 명령을 테스트합니다. 존재하지 않는 경우 **행 추가**를 클릭합니다.
5. 이름 열에 disk.EnableUUID를 추가합니다. 해당 값을 TRUE로 설정합니다. 위의 값을 큰따옴표와 함께 추가하지 마세요.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### 추가 패키지 다운로드 및 설치

참고: 추가 패키지를 다운로드하고 설치하기 전에 시스템이 인터넷에 연결되어 있는지 확인합니다.

# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

위 명령을 입력하면 CentOS 6.6 저장소에서 아래에 언급된 15개의 패키지를 다운로드하고 설치합니다.

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

참고: 원본 컴퓨터가 루트 또는 부팅 장치에 Reiser 또는 XFS 파일 시스템을 사용하는 경우 보호 전에 다음 패키지를 Linux 마스터 대상에 다운로드하고 설치해야 합니다.

# cd /usr/local

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### 사용자 지정 구성 변경 내용 적용

사용자 지정 구성 변경 내용을 적용하기 전에 사후 설치 단계를 완료했는지 확인하십시오.

사용자 지정 구성 변경 내용을 적용하려면 아래 언급된 단계를 따릅니다.

1. RHEL 6-64 통합 에이전트 바이너리를 새롭게 생성한 OS에 복사합니다.

2. 아래 명령을 실행하여 바이너리를 untar합니다.

**tar -zxvf <파일 이름>**

3. 아래의 명령을 실행하여 권한을 부여합니다.

# **chmod 755 ./ApplyCustomChanges.sh**

4. 아래 명령을 실행하여 스크립트를 실행합니다.

**# ./ApplyCustomChanges.sh**

참고: 서버에서 한 번만 스크립트를 실행 합니다. **재부팅**: 위 스크립트를 성공적으로 실행한 후에 서버를 재부팅합니다.

### MT 설치 시작

Linux 마스터 대상 서버 설치 파일을 [다운로드합니다.](http://go.microsoft.com/fwlink/?LinkID=529757)

선택한 sftp 클라이언트 유틸리티를 사용하여 다운로드한 Linux 마스터 대상 서버 설치 프로그램을 Linux 마스터 대상 가상 컴퓨터에 복사합니다. 또는 Linux 마스터 대상 가상 컴퓨터에 로그인 하여 wget을 사용하여 제공된 링크에서 설치 패키지를 다운로드할 수 있습니다.

선택한 ssh 클라이언트를 사용하여 Linux 마스터 대상 서버 가상 컴퓨터에 로그인합니다.

VPN 연결을 통해 Linux 마스터 대상 서버에 배포한 Azure 네트워크에 연결하는 경우 가상 컴퓨터 대시보드 및 포트 22로부터 입수한 Linux 마스터 대상 서버에 대한 내부 IP 주소를 사용하여 Secure Shell을 통해 Linux 마스터 대상 서버에 연결하십시오.

공용 인터넷 연결을 통해 Linux 마스터 대상 서버에 연결하는 경우 Linux 마스터 대상 서버의 공용 가상 IP 주소(가상 컴퓨터 대시보드 페이지에서) 및 ssh용으로 생성된 공용 끝점을 사용하여 Linux 마스터 대상 서버에 로그인합니다.

다음을 실행하여 gzip된 Linux 마스터 대상 서버 설치 프로그램 tar 압축 파일로부터 파일을 추출합니다.

*“tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64*”*(Linux 마스터 대상 서버 설치 프로그램을 복사한 디렉터리에서)

![](./media/site-recovery-failback-azure-to-vmware/image16.png)

설치 프로그램 파일을 다른 디렉터리로 추출했다면 디렉터리를 tar 압축 파일의 내용이 추출된 디렉터리로 변경합니다. 이 디렉터리 경로에서 "sudo. / install.sh"를 실행합니다.

![](./media/site-recovery-failback-azure-to-vmware/image17.png)

이 에이전트의 기본 역할을 선택할 것이 요청되면 2(마스터 대상)를 선택합니다.

다른 대화형 설치 옵션은 기본값으로 그대로 둡니다.

나머지 설치가 진행되고 호스트 구성 인터페이스가 나타날 때까지 기다립니다. Linux 마스터 대상 서버에 대한 호스트 구성 유틸리티는 명령줄 유틸리티입니다. ssh 클라이언트 유틸리티 창의 크기를 조정하지 마십시오. 화살표 키를 사용하여 전역 옵션을 선택하고 키보드에서 enter 키를 누릅니다.

![](./media/site-recovery-failback-azure-to-vmware/image18.png)

![](./media/site-recovery-failback-azure-to-vmware/image19.png)

1.  필드 IP에 가상 컴퓨터 대시보드에서 가져온 구성 서버의 내부 IP 주소를 입력하고 enter 키를 누릅니다.

2.  필드 포트에 22를 입력하고 enter 키를 누릅니다.

3.  https 사용:을 ‘예’로 둡니다. Enter 키를 한 번 더 누릅니다.

4.  구성 서버에서 생성된 암호를 입력합니다. Windows 컴퓨터에서 PuTTY 클라이언트를 사용하여 Linux 마스터 대상 서버 가상 컴퓨터에 ssh를 실행하는 경우 Shift + Insert를 사용하여 클립보드의 내용을 붙여넣을 수 있습니다. Ctrl + C를 사용하여 로컬 클립보드에 암호를 복사하고 Shift + Insert를 사용하여 붙여넣습니다. Enter 키를 누릅니다.

5.  오른쪽 화살표 키를 사용하여 종료를 찾아가서 enter 키를 누릅니다.

설치가 완료될 때까지 기다립니다.

![](./media/site-recovery-failback-azure-to-vmware/image20.png)

어떠한 이유로 Linux 마스터 대상 서버를 구성 서버에 등록하지 못했다면 /usr/local/ASR/Vx/bin/hostconfigcli로부터 호스트 구성 유틸리티를 실행하여 다시 수행할 수 있습니다(먼저 chmod를 슈퍼 사용자로 실행하여 이 디렉터리에 대한 액세스 권한을 설정해야 함).


#### 구성 서버에 대한 마스터 대상 서버 등록의 유효성을 검사합니다.

Azure Site Recovery 자격 증명 모음에 구성 서버 페이지 아래에 있는 서버 세부 정보 페이지를 방문하여 마스터 대상 서버가 구성 서버와 함께 성공적으로 등록되었는지 확인할 수 있습니다.

참고: Mt를 등록한 후 MT에 예상 원인으로 인한 구성 오류가 있다는 것을 발견할 수 있습니다. - 이 경우 가상 컴퓨터를 Azure에서 삭제하거나 끝점이 잘못 구성되었을 수 있습니다. 이는 MT가 Azure에서 배포될 경우 MT 구성이 Azure 끝점에 의해 감지되었기 때문입니다. 그러나 온-프레미스 MT에서는 이렇게 되지 않고 오류가 무시될 수 있습니다. 장애 복구에는 이로 인한 문제가 없습니다.


## 온-프레미스에 다시 가상 컴퓨터 보호를 시작합니다.

온-프레미스에 다시 VM을 장애 복구하기 전에 먼저 가상 컴퓨터를 온-프레미스에 다시 보호해야 합니다. 아래 단계를 따라 응용프로그램의 VM을 보호합니다.

### 임시 드라이브에 메모

VM이 Azure로 장애 조치되면 페이지 파일에 임시 드라이브가 추가됩니다. 이것은 이미 페이지 파일 전용 드라이브를 가지고 있을 수도 있으므로 일반적으로 장애 조치된 VM에서 필요로 하지 않는 추가 드라이브입니다.

가상 컴퓨터의 역방향 보호를 시작하기 전에 보호되지 않도록 드라이브가 오프라인으로 가져와지도록 해야 합니다.

그렇게 하려면

1.  컴퓨터 관리를 엽니다(제어판 관리 도구를 통해 또는 탐색기 창에서 이 PC를 마우스 오른쪽 단추로 클릭하고 관리를 선택).

2.  저장소 관리를 선택하여 온라인 디스크와 컴퓨터에 연결된 디스크를 나열시킵니다.

3.  컴퓨터에 연결된 임시 디스크를 선택하고 오프라인으로 전환을 선택합니다.

4.  오프라인 전환에 성공하면 반대 방향으로 가상 컴퓨터 보호를 진행할 수 있습니다.

### VM 보호 계획

Azure 포털에서 가상 컴퓨터의 상태를 확인하 고 장애 조치되었는지 확인합니다.

![](./media/site-recovery-failback-azure-to-vmware/image21.png)

참고: 다시 Azure로부터 온-프레미스로 장애 조치 중에 Azure VM은 실제 VM과 유사하게 간주됩니다. 온-프레미스로의 장애 조치는 가상 컴퓨터에 대한 것입니다.

1.  컴퓨터에 vContinuum을 시작합니다.

![](./media/site-recovery-failback-azure-to-vmware/image8.png)

2.  **응용프로그램 선택** 설정에서 **P2V**를 선택합니다.

3.  **새 보호** 옵션을 클릭하여 시작합니다.

4.  열리는 새 창에서 가상 컴퓨터를 다시 온-프레미스로 보호할 수 있습니다.

    a. 장애 조치하려는 VM에 따라 **OS 유형**과 **세부 정보**를 클릭합니다.

    b. **주 버서 세부 정보**에서 보호하고자 하는 가상 컴퓨터를 식별해야 합니다.

    c. 가상 컴퓨터는 vCenter 또는 Azure에 표시되는 순서가 아니라 해당 컴퓨터의 호스트 이름으로 나열됩니다.

    d. 가상 컴퓨터들은 장애 조치 전에 가상 컴퓨터가 있었던 vCenter 호스트 이름 아래에 나열됩니다.

    e. 보호하려는 VM을 식별하면 하나씩 선택합니다.

5.  가상 컴퓨터를 선택하여 보호하면(이미 Azure에 장애 조치된 경우) 가상 컴퓨터에 대한 두 가지 항목을 제공하는 팝업 창이 나타납니다. 이것은 CS가 등록된 가상 컴퓨터의 인스턴스 2개를 발견했기 때문입니다. 올바른 VM을 보호할 수 있도록 온-프레미스 VM에 대한 항목을 제거해야 합니다. 해당 컴퓨터의 호스트 이름에 의한 항목들이 표시됨을 유의하십시오. 여기에서 올바른 Azure VM 항목을 식별하려면 Azure VM에 로그인하여 C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc로 이동합니다. 파일 drscout.conf에서 호스트 ID를 식별합니다. VContinuum 대화 상자에서 VM에서 hostID가 발견된 항목을 유지합니다. 다른 모든 항목을 삭제합니다.

![](./media/site-recovery-failback-azure-to-vmware/image22.png)

6.  올바른 VM을 선택하기 위해 해당 IP 주소를 참조할 수 있습니다. IP 주소 범위 온-프레미스는 온-프레미스 VM이 됩니다.
7.  **제거**를 클릭하여 항목을 삭제합니다.

![](./media/site-recovery-failback-azure-to-vmware/image23.png)

8.  vCenter로 이동하고 vCenter의 가상 컴퓨터를 중지합니다.
9.  그런 다음 가상 컴퓨터 온-프레미스도 삭제할 수 있습니다.
10.  그 다음에는 VM을 보호하고자 하는 온-프레미스 MT 서버를 지정해야 합니다.
11.  이를 위해 장애 복구(failback)하려는 vCenter에 연결합니다.

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

12.  가상 컴퓨터를 복구하고자 하는 호스트를 기반으로 MT 서버를 선택합니다.

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

13.  그 다음 각 가상 컴퓨터에 대해 복제 옵션을 제공합니다.

![](./media/site-recovery-failback-azure-to-vmware/image25.png)

14.  이를 위해 복구 쪽 **데이터 저장소**를 선택해야 합니다. 이는 VM이 복구되는 데이터 저장소를 의미합니다.

VM별로 제공해야 하는 다양한 옵션은 다음과 같습니다.

**옵션** | **옵션이 권장하는 값**
---|---
프로세스 서버 IP | Azure에 배포한 PS를 선택 합니다.
보존 크기(MB)| 
보존 값 | 1
날짜/시간 | 일
일관성 간격 | 1
대상 데이터 저장소를 선택합니다. | 복구 쪽에서 사용할 수 있는 데이터 저장소 이 데이터 저장소는 공간이 충분해야 하며 가상 컴퓨터를 실현시키고자 하는 ESX 호스트에서 제공되어야 합니다.
15.  그런 다음 온-프레미스 사이트로 장애 조치 후 가상 컴퓨터가 입수하는 속성을 구성할 수 있습니다. 구성할 수 있는 다른 속성은 다음과 같습니다.

![](./media/site-recovery-failback-azure-to-vmware/image26.png)


**속성** | **구성 방법**
---|---
네트워크 구성|감지된 각 NIC에 대해 가상 컴퓨터에 대해 장애 복구 IP 주소를 구성합니다. NIC를 선택하고 **변경**을 클릭하여 IP 주소 세부 정보를 지정합니다.
하드웨어 구성|VM에 대한 CPU 및 메모리 값을 지정할 수 있습니다. 이 설정은 보호하고자 하는 모든 VM에 적용할 수 있습니다.
표시 이름|CPU 및 메모리에 대해 올바른 값을 식별하려면 IAAS Vm 역할 크기를 참조하고 할당된 메모리 및 코어 수를 확인할 수 있습니다.
표시 이름|온-프레미스에 다시 장애 조치 후, vCenter 재고에 표시되는 가상 컴퓨터의 이름을 변경하도록 선택할 수 있습니다. 여기에 표시되는 기본 값이 가상 컴퓨터 호스트 이름입니다. VM 이름을 식별하려면 보호 그룹 내 VM 목록을 참조할 수 있습니다.
NAT 구성|아래에 자세히 설명합니다.

![](./media/site-recovery-failback-azure-to-vmware/image27.png)



> **PS NAT 설정 선택**
>
> 가상 컴퓨터의 보호를 활성화하려면 통신 채널 2개를 구축해야 합니다.
>
> 첫 번째 채널은 가상 컴퓨터와 프로세스 서버 사이입니다. 이 채널은 VM으로부터 데이터를 수집하고 PS로 보냅니다. 그 다음 PS가 이 데이터를 마스터 대상으로 보냅니다. 프로세스 서버와 보호할 가상 컴퓨터가 동일한 Azure vNet에 있다면 NAT 설정을 사용하지 않아도 됩니다. PS와 보호할 가상 컴퓨터가 2개의 다른 vNet에 있다면 해당 PS에 대한 NAT 설정을 지정하고 첫 번째 옵션을 확인해야 합니다.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
>
> 프로세서 서버 공용 IP를 식별하려면 Azure에서 PS 배포로 이동하고 해당 공용 IP 주소를 확인하십시오.
>
> 두 번째 채널은 프로세스 서버와 마스터 대상 사이입니다. NAT을 사용할 것인지 여부는 MT와 PS 사이에 VPN 기반 연결을 사용하는지 또는 인터넷을 통해 보호하는지 여부에 따라 다릅니다. PS가 VPN을 통해 MT와 통신하는 경우에는 이 옵션을 선택해서는 안 됩니다. 마스터 대상이 인터넷을 통해 프로세스 서버와 통신해야 한다면 PS에 대해 NAT 설정을 지정하십시오.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image29.png)
>
> 프로세서 서버 공용 IP를 식별하려면 Azure에서 PS 배포로 이동하고 해당 공용 IP 주소를 확인하십시오.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

1.  5\.d 단계에 지정한 대로 온-프레미스 가상 컴퓨터를 삭제하지 않았으며 7.a 단계에서 선택한 것처럼 장애 복구를 실행하는 데이터 저장소에 여전히 기존의 VMDK가 포함되어 있다면 장애 복구 VM이 역시 새로운 위치에 생성되도록 해야 합니다. 이를 위해 고급 설정을 선택하고 고급 설정의 **폴더 이름 설정** 섹션에서 복구할 대체 폴더를 지정할 수 있습니다.

![](./media/site-recovery-failback-azure-to-vmware/image31.png)

고급 설정의 다른 옵션은 기본값으로 남겨둬도 됩니다. 모든 서버에 폴더 이름을 설정을 적용하도록 하십시오.

2.  그 다음 보호의 최종 단계로 이동합니다. 여기서 가상 컴퓨터가 준비 검사를 실행하여 온-프레미스로 다시 보호될 준비가 되었는지 확인해야 합니다.

![](./media/site-recovery-failback-azure-to-vmware/image32.png)

	a.  Click on the readiness check and wait for it to complete.

	b.  The Readiness Report tab will give the information if all the
    virtual machines are ready.

	c.  If the Readiness Report is successful on all the virtual machines it
    will allow you to specify a name to the Protection plan

![](./media/site-recovery-failback-azure-to-vmware/image33.png)

	d.  Give the plan a new Name and begin Protect by clicking the button
    below.


3.  이제 보호가 시작됩니다.

    a. vContinuum에서 보호의 진행률을 볼 수 있습니다.

![](./media/site-recovery-failback-azure-to-vmware/image34.png)

	b.  Once the step **Activating Protection Plan** is completed, you can
    monitor the protection of the virtual machines via the ASR Portal.

	c.  You can also monitor the protection via Azure Site Recovery.

![](./media/site-recovery-failback-azure-to-vmware/image35.png)

또한 가상 컴퓨터를 클릭하고 음량 복제 섹션 아래의 진행률을 모니터링하여 쌍의 정확한 상태를 볼 수도 있습니다.

![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## 장애 복구 계획 준비

응용프로그램이 언제든지 다시 온-프레미스로 장애 조치될 수 있도록 vContinuum를 사용하여 장애 복구 계획을 준비할 수 있습니다. 이러한 복구 계획은 ASR 복구 계획과 매우 유사합니다.

1.  vContinuum를 시작하고 옵션을 선택하여 **계획을 관리합니다**.

2.  사용자 하위 옵션 **복구**를 선택합니다.

![](./media/site-recovery-failback-azure-to-vmware/image37.png)

3.  가상 컴퓨터를 보호하는 데 사용된 모든 계획의 목록을 볼 수 있습니다. 이들은 복구에 사용할 수 있는 동일한 계획입니다.

4.  보호 계획을 선택하고 그 안에서 복구하려는 모든 VM을 선택합니다.

    a. 각 VM 선택 시 원본 VM, VM이 복구될 대상 ESX 서버 및 원본 VM 디스크에 대한 자세한 정보를 볼 수 있습니다.

5.  다음을 클릭하여 **복구 마법사**를 시작합니다.

6.  복구하려는 가상 컴퓨터를 선택합니다.

    a. 복구할 수 있는 모든 가상 컴퓨터의 목록을 참조 하십시오.

![](./media/site-recovery-failback-azure-to-vmware/image38.png)

	b.  You can **recover based on** multiple options however we recommend
    the **Latest Tag.** This will ensure that the latest data from the
    virtual machine will be used.

	c.  Select **Apply for All VMs** to ensure that the latest tag will be
    chosen for all the virtual machines.


7.  **준비 검사**를 실행합니다. 이것은 오른쪽 매개 변수가 가상 컴퓨터의 최신 태그 복구를 활성화하도록 구성되었는지 알려줍니다. 모든 검사가 성공적이면 다음을 클릭하고 그렇지 않으면 로그를 보고 오류를 해결합니다.

![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  마법사의 VM 구성 단계에서 복구 설정이 올바르게 설정되었는지 확인합니다. VM 설정이 필요한 것과 다를 경우 변경을 선택할 수 있습니다. 보호하는 동안 이미 이 작업을 완료했으므로 이번에는 무시할 수도 있습니다.

![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9.  마지막으로 복구하는 가상 컴퓨터의 목록을 검토합니다.

    a. 가상 컴퓨터에 복구 순서를 지정합니다.

참고: 가상 컴퓨터는 컴퓨터 호스트 이름을 사용하여 나열됩니다. 컴퓨터 호스트 이름을 가상 컴퓨터에 매핑하기 어려울 수도 있습니다. 이름을 매핑하기 위해 Azure IAAS에서 가상 컴퓨터 대시보드로 이동하고 가상 컴퓨터의 호스트 이름을 볼 수 있습니다.

![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10.  **복구 계획 이름**을 지정하고 **복구 옵션**에서 **나중에 복구**를 선택합니다.

    a. 지금 바로 복구하려는 경우에는 **복구 옵션**에서 **지금 복구**를 선택할 수 있습니다.

    b. 보호 초기 복제가 완료되지 않았을 수도 있으므로 나중에 복구하는 것이 좋습니다.

    c. 마지막으로 **복구** 단추를 클릭하여 계획을 저장하거나 **복구 옵션**을 기반으로 복구를 트리거합니다.

11.  복구 상태를 볼 수 있으며 해당 계획이 성공적으로 저장되었는지 확인할 수 있습니다.

![](./media/site-recovery-failback-azure-to-vmware/image42.png)

12.  나중에 복구를 선택했다면 계획이 생성되었으며 나중에 복구할 수 있다는 알림이 표시됩니다.

![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## 가상 컴퓨터 복구

계획을 만든 후에 가상 컴퓨터를 복구하도록 선택할 수 있습니다. 필수 구성 요소로 가상 컴퓨터가 동기화를 완료해야 합니다.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

복제 상태에서 OK를 표시하는 경우 보호가 완료되었으며 RPO 임계값을 충족하는 것입니다. 복제 쌍의 상태를 확인하기 위해 가상 컴퓨터 속성으로 이동하고 복제의 상태를 확인할 수 있습니다.

**복구를 시작하기 전에 Azure 가상 컴퓨터를 끕니다. 이렇게 하면 스플릿 브레인이 없으며, 최종 고객이 응용프로그램의 사본 하나에 의해 처리됩니다. Azure VM이 꺼졌는지 성공적으로 확인한 경우에만 아래 단계를 계속 진행하십시오.**

가상 컴퓨터의 복구를 다시 온-프레미스로 시작하려면 저장된 계획을 시작해야 합니다.

1.  vContinuum에서 선택하면 계획이 **모니터링**됩니다.

2.  마법사가 지금까지 실행된 계획을 나열합니다.

![](./media/site-recovery-failback-azure-to-vmware/image45.png)

3.  **복구** 노드를 선택하고 복구 하고자 하는 계획을 선택합니다.

    a. 그러면 계획이 아직 시작되지 않았음을 알려줍니다.

4.  **시작**을 클릭하여 복구를 시작합니다.

5.  가상 컴퓨터의 복구를 모니터링할 수 있습니다.


![](./media/site-recovery-failback-azure-to-vmware/image46.png)

6. VM의 전원이 켜지면 vCenter에 있는 가상 컴퓨터에 연결할 수 있습니다.

## 장애 복구 후 Azure로 다시 보호

장애 복구가 완료되면 가상 컴퓨터를 다시 보호하는 것이 좋습니다. 아래 단계는 보호를 다시 구성하는 데 도움이 됩니다.

1.  가상 컴퓨터 온-프레미스가 작동되는지, 응용프로그램이 최종 고객에게 도달할 수 있는지 확인하십시오.

2.  Azure Site Recovery 포털에서 가상 컴퓨터를 선택하고 삭제합니다.

    a. 가상 컴퓨터의 보호를 사용하지 않도록 설정하려면 선택합니다. 이렇게 하면 VM이 더 이상 보호되지 않습니다.

3.  Azure IAAS 가상 컴퓨터로 이동하고 장애 조치된 VM을 삭제합니다.

4.  VSpehere에서 오래된 VM 삭제 – 이들은 이전에 Azure로 장애 조치된 VM입니다.

5.  ASR 포털에서 최근에 장애 조치된 가상 컴퓨터를 보호하려면 선택합니다.

6.  VM이 보호되면 복구 계획에 추가할 수 있으며 지속적으로 보호됩니다.


 

<!---HONumber=Oct15_HO3-->