---
title: "Azure에서 온-프레미스로 장애 조치하기 위해 Linux 마스터 대상 서버를 설치하는 방법 | Microsoft Docs"
description: "Linux 가상 컴퓨터를 다시 보호하려면 Linux 마스터 대상 서버가 필요합니다. 설치 방법에 대해 알아봅니다."
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
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 22a86001fe93dcb11e180dbdd75045b49b85b58f
ms.lasthandoff: 04/25/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Linux 마스터 대상 서버 설치 방법
가상 컴퓨터를 장애 조치(failover)한 후 가상 컴퓨터를 다시 온-프레미스 사이트에 장애 복구할 수 있습니다. 장애 복구하려면 가상 컴퓨터를 Azure에서 온-프레미스 사이트로 다시 보호해야 합니다. 이 프로세스를 수행하려면 트래픽을 수신할 온-프레미스 마스터 대상 서버가 필요합니다. 보호된 가상 컴퓨터가 Windows 가상 컴퓨터인 경우 Windows 마스터 대상이 필요합니다. Linux 가상 컴퓨터인 경우 Linux 마스터 대상이 필요합니다. 다음 단계를 읽고 Linux 마스터 대상을 만들고 설치하는 방법에 대해 알아보세요.

## <a name="overview"></a>개요
이 문서에서는 Linux 마스터 대상을 설치하기 위한 정보와 지침을 제공합니다.

이 문서의 마지막 부분 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 의견이나 질문을 게시할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 마스터 대상을 배포해야 하는 호스트를 올바르게 선택하려면 기존 온-프레미스 가상 컴퓨터에 장애 복구를 수행할 것인지 아니면 온-프레미스 가상 컴퓨터가 삭제되어 새 가상 컴퓨터에 장애 복구를 수행할 것인지 결정해야 합니다.
    * 기존 가상 컴퓨터에 수행하는 경우 마스터 대상의 호스트가 가상 컴퓨터의 데이터 저장소에 액세스할 수 있어야 합니다.
    * 온-프레미스 가상 컴퓨터가 없는 경우 마스터 대상과 동일한 호스트에 장애 복구 가상 컴퓨터가 생성됩니다. 아무 ESXi 호스트를 선택하여 마스터 대상을 설치할 수 있습니다.
* 마스터 대상은 프로세스 서버 및 구성 서버와 통신할 수 있는 네트워크에 있어야 합니다.
* 마스터 대상의 버전이 프로세스 서버 및 구성 서버의 버전과 같거나 그보다 이전 버전이어야 합니다. 예를 들어 구성 서버의 버전이 9.4인 경우 마스터 대상의 버전이 9.4 또는 9.3인 것은 괜찮지만 9.5는 안 됩니다.
* 마스터 대상은 VMware 가상 컴퓨터만 될 수 있고 물리적 서버는 안 됩니다.

## <a name="master-target-sizing-guideline"></a>마스터 대상 크기 조정 지침

다음 크기 조정 지침을 따라 마스터 대상을 만들어야 합니다.
    * RAM - 6GB 이상
    * OS 디스크 크기 - 100GB 이상(CentOS6.6 설치에 필요)
    * 보존 드라이브에 대한 추가 디스크 크기 - 1TB
    * CPU 코어 - 코어 4개 이상


## <a name="steps-to-deploy-the-master-target-server"></a>마스터 대상 서버를 배포하는 단계

### <a name="install-centos-66-minimal"></a>CentOS 6.6 minimal 설치

다음 단계에 따라 64비트 CentOS 6.6 운영 체제를 설치합니다.

1. 다음 링크에서 가장 가까운 미러를 선택하여 CentOS 6.6 minimal 64비트 ISO를 다운로드합니다.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    DVD 드라이브에 CentOS 6.6 minimal 64비트 ISO를 삽입하고 시스템을 부팅합니다.

    ![CentoOS 6.6 대화 상자 시작](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. **건너뛰기**를 클릭하여 미디어 테스트 프로세스를 무시합니다.

    ![건너뛰기를 선택하여 미디어 테스트 프로세스 무시](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. 설치 시작 화면에서 **다음** 단추를 클릭합니다.

    ![설치 시작 화면의 다음 단추](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. 원하는 언어로 **영어**를 선택하고 **다음**을 클릭합니다.

    ![언어 선택](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. 자판 배열로 **영어(미국)**을 선택한 후 **다음**을 클릭합니다.

    ![영어 자판 배열 선택](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. **기본 저장소 장치**를 선택하고 **다음**을 클릭합니다.

    ![저장소 장치 선택](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. 하드 드라이브의 기존 데이터가 삭제된다는 경고 메시지가 나타납니다. 하드 드라이브에 중요한 데이터가 없는지 확인한 다음 **예, 데이터 모두 무시**를 클릭합니다.

    ![계속 진행하면 데이터가 삭제된다는 경고](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. **호스트 이름** 상자에 서버 호스트 이름을 입력하고 **네트워크 구성**을 클릭합니다. **네트워크 연결** 대화 상자에서 네트워크 인터페이스를 선택하고 **편집** 단추를 클릭하여 IPV4Settings를 구성합니다.

    ![호스트 이름을 선택하고 IPV4 구성](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. **eth0 시스템 편집** 대화 상자에서 **자동으로 연결** 확인란을 선택합니다. **IPv4 설정** 탭에서 **방법**을 **수동**으로 선택하고 **추가** 단추를 클릭합니다. **고정 IP**, **네트워크 마스크**, **게이트웨이** 및 **DNS 서버** 세부 정보를 입력합니다. **적용**을 클릭하여 세부 정보를 저장합니다.

    ![네트워크 구성에 대한 설정](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. 표준 시간대를 선택하고 **다음**을 클릭합니다.

    ![표준 시간대 선택](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. **루트 암호**를 입력하고, 암호를 확인한 후 **다음**을 클릭합니다.

    ![암호 추가](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. **사용자 지정 레이아웃 만들기**를 클릭하고 **다음**을 클릭합니다.

    ![설치 유형 선택](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. **무료** 파티션을 선택하고, **만들기**를 클릭하여 파일 시스템 형식이 **ext4**인 **/**, **/var/crash** 및 **/home** 파티션을 만듭니다. 파일 시스템 형식이 **swap**인 **스왑 파티션**을 만듭니다. 파티션 크기를 할당하려면 다음 테이블의 크기 할당 수식을 따릅니다.

    > [!NOTE]
    > Linux 마스터 대상 서버는 루트 또는 보존 저장소 공간에 LVM(Logical Volume Manager)을 사용하지 않습니다. Linux 마스터 대상은 기본적으로 LVM 파티션 및 디스크 검색을 방지하도록 구성됩니다.

    ![파티션 이름, 파티션 크기 및 파일 시스템 유형 테이블](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. 파티션을 만든 후 **다음**을 클릭합니다.

    ![파티션에 선택된 값을 보여주는 대화 상자](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. 기존 장치가 있으면 포맷에 대한 경고 메시지가 나타납니다. **포맷**을 클릭하여 하드 드라이브를 최신 파티션 테이블로 포맷합니다.

    ![디스크를 포맷하려면 수식 단추 클릭](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. **디스크에 변경 내용 쓰기**를 클릭하여 디스크에 파티션 변경 내용을 적용합니다.

    !["디스크에 변경 내용 쓰기" 클릭](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. **부팅 로더 설치** 옵션을 선택하고 **다음**을 클릭하여 루트 파티션에 부팅 로더를 설치합니다.

    ![루트 파티션에 부팅 로더 설치](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. 설치 프로세스가 시작됩니다. 진행률을 모니터링할 수 있습니다.

    ![설치 진행률을 보여주는 대화 상자](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. 설치가 성공적으로 완료되면 다음 화면이 표시됩니다. **다시 부팅**을 클릭합니다.

    ![설치 성공 화면](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>설치 후 단계
다음으로 마스터 대상 서버로 구성할 컴퓨터를 준비합니다.

Linux 가상 컴퓨터에 있는 각 SCSI 하드 디스크의 SCSI ID를 가져오려면 **disk.EnableUUID = TRUE** 매개 변수를 사용하도록 설정해야 합니다.

이 매개 변수를 사용하려면 다음 단계를 따르세요.

1. 가상 컴퓨터를 종료합니다.

2. 왼쪽 창에서 가상 컴퓨터의 항목을 마우스 오른쪽 단추로 클릭한 다음 **설정 편집**을 선택합니다.

3. **옵션** 탭을 클릭합니다.

4. 왼쪽 창에서 **고급 &gt;일반**을 선택한 다음 오른쪽에서 **구성 매개 변수** 단추를 클릭합니다.

    ![옵션 탭](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    **구성 매개 변수** 옵션은 컴퓨터가 실행 중인 동안에는 사용할 수 없습니다. 이 탭을 활성화하려면 가상 컴퓨터를 종료합니다.

5. **disk.EnableUUID**가 있는 행이 있는지 확인합니다.

    - 값이 있고 **False**로 설정되어 있으면 **True**로 변경합니다(이 값은 대/소문자를 구분하지 않음).

    - 값이 있고 **True**로 설정되어 있으면 **취소**를 클릭합니다.

    - 값이 없으면 **행 추가**를 클릭합니다.

    - **이름** 열에 **disk.EnableUUID**를 추가하고 값을 **TRUE**로 설정합니다.

    ![disk.EnableUUID가 있는지 확인](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-additional-packages"></a>추가 패키지를 다운로드하여 설치

> [!NOTE]
> 추가 패키지를 다운로드하여 설치할 수 있도록 인터넷에 연결되어 있는지 확인합니다. 인터넷에 연결되지 않았으면 이러한 RPM 패키지를 수동으로 찾아서 설치해야 합니다.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

이전 명령은 CentOS 6.6 리포지토리에서 다음 15개 패키지를 다운로드하여 설치합니다. 인터넷에 액세스할 수 없는 경우 다음 RPM 패키지를 다운로드해야 합니다.


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
> 보호된 원본 컴퓨터에서 루트 또는 부팅 장치에 ReiserFS 또는 XFS 파일 시스템을 사용하는 경우 보호하기 전에 다음 추가 패키지를 Linux 마스터 대상에 다운로드하여 설치해야 합니다.


***ReiserFS(Suse11SP3에서 사용되는 경우. ReiserFS는 Suse11SP3의 기본 파일 시스템이 아님)***

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
마스터 대상에서 다중 경로 패키지를 사용하도록 설정하는 데 필요합니다.

### <a name="get-the-installer-for-setup"></a>설치를 위한 설치 프로그램 가져오기

마스터 대상이 인터넷에 연결된 경우 다음 단계에 따라 설치 프로그램을 다운로드할 수 있습니다. 인터넷에 연결되지 않은 경우 프로세스 서버에서 설치 프로그램을 복사하여 설치할 수 있습니다.

#### <a name="download-the-master-target-installation-packages"></a>마스터 대상 설치 패키지 다운로드

[최신 Linux 마스터 대상 설치 비트를 다운로드합니다](https://aka.ms/latestlinuxmobsvc).

Linux를 사용하여 다운로드하려면 다음을 입력합니다.

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

설치 관리자를 다운로드하고 홈 디렉터리에 압축을 풉니다. /usr/Local에 압축을 풀면 설치가 실패합니다.


#### <a name="access-the-installer-from-the-process-server"></a>프로세스 서버에서 설치 프로그램 액세스

1. 프로세스 서버에서 C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository로 이동합니다.

2. 프로세스 서버에서 필요한 설치 프로그램 파일을 복사하고 홈 디렉터리에 latestlinuxmobsvc.tar.gz로 저장합니다.


### <a name="apply-custom-configuration-changes"></a>사용자 지정 구성 변경 내용 적용

사용자 지정 구성 변경 내용을 적용하려면 다음 단계를 따릅니다.


1. 다음 명령을 실행하여 바이너리를 untar합니다.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![실행된 명령 스크린샷](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. 다음 명령을 실행하여 권한을 부여합니다.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 다음 명령을 사용하여 스크립트를 실행합니다.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> 서버에서 스크립트를 한 번만 실행합니다. 서버를 종료합니다. 다음 단계에 설명된 대로 디스크를 추가한 후 서버를 다시 부팅합니다.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>보존 디스크를 Linux 마스터 대상 가상 컴퓨터에 추가

다음 단계에 따라 보존 디스크를 만듭니다.

1. Linux 마스터 대상 가상 컴퓨터에 새 **1TB** 디스크를 연결하고 컴퓨터를 **부팅**합니다.

2. **multipath -ll** 명령을 사용하여 보존 디스크의 다중 경로 ID를 확인합니다.

    ```
    multipath -ll
    ```

    ![보존 디스크의 다중 경로 ID](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. 드라이브를 포맷하고 새 드라이브에 파일 시스템을 만듭니다.

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![드라이브에 파일 시스템 만들기](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. 파일 시스템을 만든 후 보존 디스크를 탑재합니다.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![보존 디스크 탑재](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. 부팅할 때마다 보존 드라이브를 탑재하도록 **fstab** 항목을 만듭니다.
    ```
    vi /etc/fstab
    ```
    **Insert** 키를 눌러 파일 편집을 시작합니다. 새 줄을 만들고 다음 텍스트를 삽입합니다. 이전 명령에서 강조 표시된 다중 경로 ID에 따라 디스크 다중 경로 ID를 편집합니다.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    **Esc** 키를 눌러 **:wq**(쓰기 및 종료)를 입력하고 편집기 창을 닫습니다.

### <a name="install-the-master-target"></a>마스터 대상 설치

> [!IMPORTANT]
> 마스터 대상 서버의 버전이 프로세스 서버 및 구성 서버의 버전과 같거나 그보다 이전 버전이어야 합니다. 이 조건이 충족되지 않으면 다시 보호가 성공하지만 복제는 실패합니다.


> [!NOTE]
> 마스터 대상 서버를 설치하기 전에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 가상 컴퓨터의 /etc/hosts 파일에 포함되어 있는지 확인합니다.

1. 다음 명령을 실행하여 구성 서버의 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase에서 암호를 복사한 다음 동일한 로컬 디렉터리에 있는 passphrase.txt에 저장합니다.

    ```
    echo <passphrase> >passphrase.txt
    ```
    예제: echo itUx70I47uxDuUVY >passphrase.txt

2. 구성 서버의 IP 주소를 적어둡니다. 다음 단계에서 필요합니다.

3. 다음 명령을 실행하여 마스터 대상 서버를 설치하고 이 서버를 구성 서버에 등록합니다.

    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    예제: ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    스크립트가 완료될 때까지 기다립니다. 마스터 대상이 성공적으로 등록되면 해당 마스터 대상이 포털의 Site Recovery 인프라 페이지에 나열됩니다.

#### <a name="install-the-master-target-by-using-interactive-install"></a>대화식 설치를 사용하여 마스터 대상 설치

1. 다음 명령을 실행하여 마스터 대상을 설치합니다. 에이전트 역할을 **마스터 대상**으로 선택합니다.

    ```
    ./install
    ```

2. 기본 설치 위치를 선택하고 **Enter** 키를 눌러 계속합니다.

    ![마스터 대상의 기본 설치 위치 선택](./media/site-recovery-how-to-install-linux-master-target/image17.png)


3. 구성할 **전역** 설정을 선택합니다.

    ![전역 설정 구성](./media/site-recovery-how-to-install-linux-master-target/image18.png)

4. 구성 서버의 IP 주소를 지정합니다.

5. 구성 서버의 포트를 443으로 지정합니다.

    ![구성 서버의 IP 주소 및 포트 지정](./media/site-recovery-how-to-install-linux-master-target/image19.png)

6. 구성 서버의 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase에서 구성 서버 암호를 복사하여 **암호** 상자에 붙여 넣습니다. 텍스트를 붙여 넣은 후에도 상자는 비어 있습니다.

7. 메뉴에서 **종료**로 이동합니다.

8. 설치 및 등록을 완료합니다.

### <a name="install-vmware-tools-on-the-master-target-server"></a>마스터 대상 서버에 VMware 도구 설치

VMware 도구가 데이터 저장소를 찾을 수 있도록 마스터 대상에 설치해야 합니다. 도구가 설치되지 않으면 다시 보호 화면에서 데이터 저장소를 나열하지 않습니다. VMware 도구를 설치한 후 다시 부팅해야 합니다.

## <a name="next-steps"></a>다음 단계
마스터 대상의 설치 및 등록이 완료되면 구성 서버 개요 아래에 있는 **Site Recovery 인프라**의 **마스터 대상** 섹션에서 해당 마스터 대상을 확인할 수 있습니다.

이제 [다시 보호](site-recovery-how-to-reprotect.md)와 장애 복구를 계속 진행할 수 있습니다.

## <a name="common-issues"></a>일반적인 문제

* 마스터 대상 같은 관리 구성 요소에서 Storage vMotion을 설정하지 않아야 합니다. 마스터 대상이 다시 보호 후에 이동되면 VMDK(가상 컴퓨터 디스크)를 분리할 수 없고 따라서 장애 복구도 실패합니다.
* 마스터 대상에는 가상 컴퓨터에 대한 스냅숏이 없어야 합니다. 스냅숏이 있으면 장애 복구가 실패합니다.
* 일부 고객의 일부 사용자 지정 NIC 구성 때문에 부팅 시에 네트워크 인터페이스가 비활성화되고 마스터 대상 에이전트를 초기화할 수 없습니다. 다음 속성이 올바르게 설정되어 있는지 확인합니다. 이더넷 카드 파일의 /etc/sysconfig/network-scripts/ifcfg-eth*에서 다음 속성을 확인합니다.
        * BOOTPROTO=dhcp * ONBOOT=yes

