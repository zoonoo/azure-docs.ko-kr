---
title: "Azure에서 온-프레미스로 장애 조치하기 위해 Linux 마스터 대상 서버를 설치하는 방법 | Microsoft Docs"
description: "Linux VM을 다시 보호하려면 먼저 Linux 마스터 대상 서버가 필요합니다. 설치 방법에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 8ac18526e6781f189444233a191aa1d6c5b863ff
ms.lasthandoff: 02/22/2017


---
# <a name="how-to-install-linux-master-target-server"></a>Linux 마스터 대상 서버를 설치하는 방법
가상 컴퓨터를 장애 조치하면 VM을 온-프레미스로 다시 장애 복구할 수 있습니다. 장애 복구하려면 먼저 가상 컴퓨터를 Azure에서 온-프레미스로 다시 보호하여 가상 컴퓨터를 보호된 상태로 만들어야 합니다. 이를 위해 온-프레미스에서 트래픽을 수신하기 위해 마스터 대상 서버가 필요합니다. 보호된 가상 컴퓨터가 Windows VM이면 Windows 마스터 대상이 필요합니다. Linux VM의 경우 다시 보호할 Linux 마스터 대상이 필요합니다. 아래 단계를 참조하여 Linux 마스터 대상을 만들고 설치하는 방법에 대해 알아봅니다.

## <a name="overview"></a>개요
이 문서에서는 Linux 마스터 대상을 설치하기 위한 정보와 지침을 제공합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="pre-requisites"></a>필수 조건

* MT(마스터 대상)를 배포해야 하는 호스트를 올바르게 선택하려면 기존 온-프레미스 VM 또는 온-프레미스 VM 삭제에 따른 새 VM으로 장애 복구를 수행할지 결정합니다. 
    * 기존 VM의 경우 MT의 호스트는 VM의 데이터 저장소에 액세스해야 합니다.
    * VM이 온-프레미스에 없으면 MT와 동일한 호스트에 장애 복구 VM을 만듭니다. MT를 설치할 ESXi 호스트를 선택할 수 있습니다.
* MT는 프로세스 서버 및 구성 서버와 통신할 수 있는 네트워크에 있어야 합니다.
* MT 버전은 프로세스 서버 및 구성 서버보다 작거나 같아야 합니다. 예를 들어 CS가 9.4에 있으면 MT는 9.5가 아니라 9.4와 9.3에 있을 수 있습니다.
* MT는 물리적 VM이 아닌 VMware VM만 될 수 있습니다.


## <a name="steps-to-deploy-the-master-target-server"></a>마스터 대상 서버를 배포하는 단계

### <a name="install-centos-66-minimal"></a>CentOS 6.6 minimal 설치

CentOS 6.6 - 64bit 운영 체제를 설치하려면 아래에서 설명하는 단계를 수행합니다.

1. 다음 링크에서 가장 가까운 미러를 선택하여 CentOS 6.6 minimal 64비트 ISO를 다운로드합니다.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    DVD 드라이브에 CentOS 6.6 minimal 64비트 ISO를 유지하고 시스템을 부팅합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. **건너뛰기**를 선택하여 미디어 테스트 프로세스를 무시합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. 이제 설치 시작 화면을 볼 수 있습니다. 여기서 **다음** 단추를 클릭합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. 원하는 언어로 **영어**를 선택하고 **다음**을 클릭하여 계속합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. 키보드 레이아웃으로 **영어(미국)**을 선택합니다. **다음**을 클릭하여 설치를 계속합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. 설치할 장치 유형을 선택합니다. **기본 저장소 장치**를 선택합니다. **다음**을 클릭하여 설치를 계속합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. 하드 드라이브의 기존 데이터를 삭제한다는 경고 메시지가 나타납니다. 하드 드라이브에 중요한 데이터가 없는지 확인한 다음 **예, 데이터 모두 무시**를 클릭합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. **호스트 이름 텍스트 상자**에서 서버의 호스트 이름을 입력합니다. **네트워크 구성**을 클릭하고, **네트워크 연결** 창에서 네트워크 인터페이스를 선택합니다. **편집** 단추를 클릭하여 [IPV4 설정]을 구성합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. 다음과 같은 **eth0 시스템 편집** 창이 표시됩니다. **자동으로 연결** 확인란을 선택합니다. "IPv4 설정" 탭에서 방법을 **수동**으로 선택하고 **추가** 단추를 클릭합니다. 고정 IP, 네트워크 마스크, 게이트웨이 및 DNS 서버 세부 정보를 제공합니다. **적용**을 클릭하여 세부 정보를 저장합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. 콤보 상자에서 표준 시간대를 선택하고 **다음**을 클릭하여 계속합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. **루트 암호**를 입력하고 암호를 확인한 후 **다음**을 클릭하여 계속합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. 파티션 모드로 **사용자 지정 레이아웃 만들기**를 선택하고 **다음**을 클릭하여 계속합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. **무료** 파티션을 선택하고 **만들기**를 클릭하여 파일 시스템 형식이 **ext4**인 **/**, **/var/crash** 및 **/home** 파티션을 만듭니다. 파일 시스템 형식이 **swap**인 **스왑 파티션**을 만듭니다. 파티션 크기를 할당하려면 아래 표에서 설명한 대로 크기 할당 수식을 따릅니다.

    > [!NOTE]
    > Linux MT 시스템은 루트 또는 보존 저장소 공간에 LVM을 사용하지 않습니다. Linux MT는 기본적으로 LVM 파티션/디스크 검색을 방지하도록 구성됩니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. 파티션을 만든 후 **다음**을 클릭하여 계속합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. 기존 장치가 있으면 포맷에 대한 경고 메시지가 나타납니다. **포맷**을 클릭하여 하드 드라이브를 최신 파티션 테이블로 포맷합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. **디스크에 변경 내용 쓰기**를 클릭하여 디스크의 파티션 변경 내용을 적용합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. **부팅 로더 설치** 옵션을 선택하고 **다음**을 클릭하여 루트 파티션에 부팅 로더를 설치합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. 설치 프로세스가 시작됩니다. 설치 진행률을 모니터링할 수 있습니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. 설치가 성공적으로 완료되면 다음 화면이 표시됩니다. **다시 부팅**을 클릭합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>사후 설치 단계
다음으로 마스터 대상 서버로 구성할 컴퓨터를 준비합니다.

Linux 가상 컴퓨터에서 각 SCSI 하드 디스크에 대해 SCSI ID를 가져오려면 “disk.EnableUUID = TRUE” 매개 변수를 활성화해야 합니다.

이 매개 변수를 사용하려면 아래 지정된 단계를 따르십시오.

a. 가상 컴퓨터를 종료합니다.

b. 왼쪽 패널에서 VM의 항목을 마우스 오른쪽 단추로 클릭하고 **설정 편집**을 선택합니다.

c. **옵션** 탭을 클릭합니다.

d. 왼쪽에서 **고급&gt;일반 항목**을 차례로 선택하고 오른쪽에 표시되는 **구성 매개 변수**를 클릭합니다.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

“컴퓨터가 실행 중일 때 "구성 매개 변수" 옵션이 비활성화 상태가 됩니다." 이 탭을 활성화하려면 컴퓨터를 종료합니다.

e. **disk.EnableUUID** 가 있는 행이 이미 존재하는지 확인합니다.

f. 값이 존재하고 False로 설정되어 있으면 값을 True로 덮어씁니다(True와 False 값은 대소문자를 구분함).

g. 값이 존재하고 True로 설정되어 있으면 [취소]를 클릭합니다.
    
h. 값이 존재하지 않는 경우 **행 추가**를 클릭합니다.

i. [이름] 열에서 disk.EnableUUID를 추가하고 값을 TRUE로 설정합니다.

   * 존재하며 값이 False로 설정되어 있는 경우 값을 True로 덮어씁니다(True와 False 값은 대소문자를 구분).

   * 존재하며 True로 설정되어 있는 경우 취소를 클릭하고 부팅한 후 게스트 운영 체제 내에서SCSI 명령을 테스트합니다.

f. 존재하지 않는 경우 **행 추가**를 클릭합니다.

  이름 열에 disk.EnableUUID를 추가합니다.

  해당 값을 TRUE로 설정



![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>추가 패키지 다운로드 및 설치

> [!NOTE]
> 추가 패키지를 다운로드하여 설치하기 전에 시스템이 인터넷에 연결되어 있는지 확인합니다. 그렇지 않으면 수동으로 이러한 패키지 RPM을 찾아 설치해야 합니다.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

위 명령을 입력하면 CentOS 6.6 저장소에서 아래에 언급된 15개의 패키지를 다운로드하고 설치합니다. 인터넷에 액세스할 수 없는 경우 다음 RPM을 다운로드해야 합니다.


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


#### <a name="install-additional-packages-for-specific-operating-systems"></a>특정 운영 체제용 추가 패키지 설치

> [!NOTE]
> 보호된 원본 컴퓨터에서 루트 또는 부팅 장치에 Reiser 또는 XFS 파일 시스템을 사용하는 경우 보호하기 전에 다음 추가 패키지를 Linux 마스터 대상에 다운로드하여 설치해야 합니다.
 

***ReiserFS(Suse11SP3에서  사용되지만 Suse11SP3의 기본 파일 시스템이 아닌 경우)***

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS(RHEL, CentOS 7 이상)***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath 
```
이는 MT 서버에서 다중 경로 패키지를 사용하도록 설정하는 데 필요합니다.

### <a name="get-the-installer-for-setup"></a>설치를 위한 설치 프로그램 가져오기

마스터 대상 서버에서 인터넷에 액세스할 수 있는 경우 아래 단계를 통해 설치 프로그램을 다운로드할 수 있습니다. 그렇지 않으면 프로세스 서버에서 설치 프로그램을 복사하여 설치할 수 있습니다.

#### <a name="download-the-mt-installation-packages"></a>MT 설치 패키지 다운로드

[https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc)에서 최신 Linux 마스터 대상 설치 비트를 다운로드합니다.

Linux를 통해 다운로드하려면 다음을 입력합니다. 

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

설치 관리자를 다운로드하고 홈 디렉터리에만 압축을 풉니다. /usr/Local에 압축을 풀면 설치가 실패합니다.


#### <a name="access-the-installer-from-the-process-server"></a>프로세스 서버에서 설치 프로그램 액세스

프로세스 서버로 이동하여 C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository 디렉터리로 이동합니다.

프로세스 서버에서 필요한 설치 프로그램 파일을 복사하고 홈 디렉터리에 latestlinuxmobsvc.tar.gz로 저장합니다.


### <a name="apply-custom-configuration-changes"></a>사용자 지정 구성 변경 내용 적용

사용자 지정 구성 변경 내용을 적용하려면 아래 언급된 단계를 따릅니다.


1. 아래 명령을 실행하여 바이너리를 untar합니다.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
2. 아래의 명령을 실행하여 권한을 부여합니다.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 아래 명령을 실행하여 스크립트를 실행합니다.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> 서버에서 스크립트를 한 번만 실행합니다. 서버를 종료합니다. 다음 단계에서 지정한 대로 디스크를 추가한 후 서버를 다시 부팅합니다.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Linux MT VM에 보존 디스크 추가 

아래에서 설명한 단계에 따라 보존 디스크를 만듭니다.

1. 새 **1TB** 디스크를 Linux MT VM에 연결하고 컴퓨터를 **부팅**합니다.

2. **multipath -ll** 명령을 호출하여 보존 디스크의 다중 경로 ID를 확인합니다.

    ```
    multipath -ll
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. 드라이브를 포맷하고 새 드라이브에 파일 시스템을 만듭니다. 
    
    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. 파일 시스템을 만들었으면 보존 디스크를 탑재합니다.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. 마지막으로 부팅할 때마다 보존 드라이브를 탑재하도록 fstab 항목을 만듭니다.
    ```
    vi /etc/fstab 
    ```
    Insert 키를 눌러 파일 편집을 시작합니다. 새 줄을 만들고 그 안에 다음 텍스트를 삽입합니다. 이전 명령에서 강조 표시된 다중 경로 ID에 따라 디스크 다중 경로 ID를 편집합니다.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Esc 키를 눌러 :wq(쓰기 및 종료)를 입력하고 편집기 창을 닫습니다.

### <a name="install-master-target"></a>마스터 대상 설치

> [!NOTE]
> 마스터 대상 서버 버전은 프로세스 서버 및 구성 서버보다 작거나 같아야 합니다. MT 버전이 더 높으면 다시 보호가 성공하지만 복제는 실패합니다.
 

> [!NOTE]
> 마스터 대상 서버를 설치하기 전에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 VM의 /etc/hosts 파일에 포함되어 있는지 확인합니다.
 
1. 다음 명령을 실행하여 구성 서버의 **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase**에서 암호를 복사한 다음 동일한 로컬 디렉터리에 있는 passphrase.txt에 저장합니다.

    ```
    echo <passphrase> >passphrase.txt
    ```
    예제: echo itUx70I47uxDuUVY >passphrase.txt

2. 다음 단계에서 필요하므로  구성 서버의 IP 주소를 적어둡니다.

3. 다음 명령을 실행하여 마스터 대상 서버를 설치하고 이 서버를 구성 서버에 등록합니다.
    
    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    예제: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    스크립트 실행이 완료될 때까지 기다립니다. 마스터 대상이 성공적으로 등록되면 해당 MT가 포털의 Site Recovery 인프라 페이지에 나열되어 있음을 알 수 있습니다.

#### <a name="installing-master-target-using-interactive-install"></a>대화식 설치를 사용하여 마스터 대상 설치

1. 다음 명령을 실행하여 마스터 대상을 설치합니다. 에이전트 역할을 "마스터 대상"으로 선택합니다.

    ```
    ./install
    ```

2. 설치를 위한 기본 위치를 선택합니다. Enter 키를 눌러 계속합니다.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. 구성할 전역 설정을 선택합니다.

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. CS 서버 IP 주소를 지정합니다.

5. CS 서버 포트를 443으로 지정합니다.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. 구성 서버의 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase에서 CS 암호를 복사하고 암호 상자에서 해당 암호를 지정합니다. 복사하여 붙여넣은 후에도 비어 있는 것으로 표시됩니다.

7. 메뉴에서 [종료]로 이동합니다.

8. 설치 및 등록을 완료하도록 합니다.

### <a name="install-vmware-tools-on-the-master-target-server"></a>마스터 대상 서버에 VMware 도구 설치

VMware 도구는 데이터 저장소를 찾을 수 있도록 MT에 설치해야 합니다. 도구가 설치되지 않으면 다시 보호 화면에서 데이터 저장소를 나열하지 않습니다. VMware 도구를 설치한 후에 다시 부팅해야 합니다.

## <a name="next-steps"></a>다음 단계
마스터 대상의 설치 및 등록이 완료되면 구성 서버 개요 아래에 있는 Site Recovery 인프라의 마스터 대상 섹션에서 해당 MT를 확인할 수 있습니다.

이제 [다시 보호](site-recovery-how-to-reprotect.md)와 장애 복구를 계속 진행할 수 있습니다.

## <a name="common-issues"></a>일반적인 문제

* MT와 같은 관리 구성 요소에서 Storage vMotion을 설정하지 않아야 합니다. MT가 성공적으로 보호된 후에 이동하면 VMDK를 분리할 수 없으며 장애 복구가 실패합니다.
* MT 컴퓨터에는 가상 컴퓨터에 대한 스냅숏이 없어야 합니다. 스냅숏이 있으면 장애 복구가 실패합니다.
* 일부 고객의 일부 사용자 지정 NIC 구성으로 인해 부팅 시에 네트워크 인터페이스가 비활성화되고 MT 에이전트가 초기화될 수 없습니다. 다음 속성이 올바르게 설정되어 있는지 확인합니다.
    * /etc/sysconfig/network-scripts/ifcfg-eth* 이더넷 카드 파일에서 다음 두 속성을 확인합니다.
        * BOOTPROTO=dhcp 
        * ONBOOT=yes


