
# Linux를 실행하는 가상 컴퓨터 만들기

Azure 관리 포털에서 이미지 갤러리를 사용할 때 Linux 운영 체제를 실행하는 가상 컴퓨터를 쉽게 만들 수 있습니다. 이 가이드에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 액세스 및 사용자 지정할 수 있는 클라우드에 Linux 운영 체제를 실행하는 가상 컴퓨터를 만들 수 있습니다.

다음 내용을 배웁니다.

* [Azure의 가상 컴퓨터 정보](#virtualmachine)
* [가상 컴퓨터를 만드는 방법](#custommachine)
* [가상 컴퓨터를 만든 후 로그온하는 방법](#logon)
* [새 가상 컴퓨터에 데이터 디스크를 연결하는 방법](#attachdisk)

**참고**: 이 자습서에서는 가상 네트워크에 연결되지 않은 가상 컴퓨터를 만듭니다. 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요][1]를 참조하십시오.

## <a id="virtualmachine"> </a>Azure의 가상 컴퓨터 정보

Azure의 가상 컴퓨터는 제어 및 관리할 수 있는 클라우드의 서버입니다. Azure에서 가상 컴퓨터를 만든 후 필요할 때마다 삭제하고 다시 만들 수 있으며 사무실에 있는 서버와 마찬가지로 가상 컴퓨터에 액세스할 수 있습니다. VHD(가상 하드 디스크) 파일은 가상 컴퓨터를 만드는 데 사용됩니다. 가상 컴퓨터에 사용되는 VHD 유형은 다음과 같습니다.

* **이미지** - 새 가상 컴퓨터를 만들기 위한 템플릿으로 사용되는 VHD입니다. 이미지에는 컴퓨터 이름, 사용자 계정 설정
  등 실행 중인 가상 컴퓨터와 같은 특정 설정이 없으므로 템플릿입니다. 이미지를 사용하여 가상 컴퓨터를 만드는 경우 새 가상
  컴퓨터에 대한 운영 체제 디스크가 자동으로 만들어집니다.
* **디스크** - 디스크는 운영 체제의 실행 버전으로 부팅 및 탑재할 수 있는 VHD입니다. 이미지가 프로비전되면 디스크가
  됩니다. 이미지를 사용하여 가상 컴퓨터를 만드는 경우 항상 디스크가 만들어집니다. 가상화된 하드웨어에 연결되어 서비스의 일부로
  실행되는 모든 VHD는 디스크입니다.

이미지를 사용하여 가상 컴퓨터를 만드는 경우 사용할 수 있는 옵션은 다음과 같습니다.

* Azure 관리 포털의 이미지 갤러리에 제공된 이미지를 사용하여 가상 컴퓨터를 만듭니다.
* 이미지가 포함된 .vhd 파일을 만들고 Azure에 업로드한 후 이미지를 사용하여 가상 컴퓨터를 만듭니다. 사용자 지정
  이미지를 만들고 업로드하는 방법에 대한 자세한 내용은 [Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및
  업로드](/en-us/manage/linux/common-tasks/upload-a-vhd/)를 참조하십시오.

각 가상 컴퓨터는 개별적으로 또는 다른 가상 컴퓨터와 그룹화되어 클라우드 서비스에 상주합니다. 동일한 클라우드 서비스에 가상
컴퓨터를 배치하여 가상 컴퓨터가 서로 통신할 수 있게 하고, 가상 컴퓨터 간에 네트워크 트래픽 부하를 분산하며, 컴퓨터의
고가용성을 유지할 수 있습니다. 클라우드 서비스 및 가상 컴퓨터에 대한 자세한 내용은 [Azure 소개][2]를 참조하십시오.

## <a id="custommachine"> </a>가상 컴퓨터를 만드는 방법

관리 포털에서 사용자 지정 가상 컴퓨터를 만들려면 **From Gallery** 방법을 사용합니다. 이 방법은 필요한 경우 연결된
리소스, DNS 이름, 네트워크 연결 등 가상 컴퓨터를 만들 때 사용 가능한 구성 옵션을 더 많이 제공합니다.

1.  [Azure 관리 포털][3]에 로그인합니다. 명령 모음에서 **New**를 클릭합니다.

2.  **Virtual Machine**을 클릭한 후 **From Gallery**를 클릭합니다.

3.  **Choose an Image**의 목록 중 하나에서 이미지를 선택합니다. 사용 가능한 이미지는 사용 중인 구독에 따라 달라집니다. 화살표를 클릭하여 계속합니다.

4.  여러 버전의 이미지를 사용할 수 있는 경우 **Version Release Date**에서 사용할 버전을 선택합니다.

5.  **Virtual Machine Name**에서 사용할 이름을 입력합니다. 이 가상 컴퓨터의 경우 **MyTestVM1**을 입력합니다.

6.  **Size**에서 가상 컴퓨터에 사용할 크기를 선택합니다. 선택하는 크기는 응용 프로그램에 필요한 코어 수에 따라 달라집니다. 이 가상 컴퓨터의 경우 사용 가능한 가장 작은 크기를 선택합니다.

7.  **New User Name**에서 가상 컴퓨터를 관리하는 데 사용할 계정 이름을 입력합니다. 사용자 이름에 루트를 사용할 수는 없습니다. 이 가상 컴퓨터의 경우 **NewUser1**을 입력합니다.

8.  인증에서 **Provide a Password**를 선택합니다. 그런 다음 필요한 정보를 제공하고 화살표를 클릭하여 계속합니다.

9.  클라우드 서비스에 가상 컴퓨터를 함께 배치할 수 있지만, 이 자습서에서는 단일 가상 컴퓨터만 만듭니다. 이렇게 하려면 **Create a new cloud service**를 선택합니다.

10. **Cloud Service DNS Name**에 3자에서 24자 사이의 소문자와 숫자를 사용하는 이름을 입력합니다. 이 이름은 클라우드 서비스를 통해 가상 컴퓨터에 연결하는 데 사용되는 URI의 일부가 됩니다. 이 가상 컴퓨터의 경우 **MyService1**을 입력합니다.

11. **Region/Affinity Group/Virtual Network**에서 가상 컴퓨터를 찾을 위치를 선택합니다.

12. VHD 파일이 저장된 저장소 계정을 선택할 수 있습니다. 이 자습서에서는 기본 설정인 **Use an Automatically Generated Storage Account**를 적용합니다.

13. 이 자습서의 경우 **Availability Set**에서 기본 설정인 **None**을 사용합니다. 확인 표시를 클릭하여 가상 컴퓨터를 만든 다음 화살표를 클릭하여 계속합니다.

14. **VM Agent**에서 VM 에이전트를 설치할지 여부를 결정합니다. 이 에이전트는 가상 컴퓨터를 조작하는 데 유용한 확장을 설치하기 위한 환경을 제공합니다. 자세한 내용은 [확장 사용][4]을 참조하십시오.

15. **Endpoints**에서 가상 컴퓨터에 대한 SSH(보안 셸) 연결을 허용하기 위해 자동으로 만들어지는 끝점을 검토합니다. 끝점을 통해 인터넷이나 다른 가상 네트워크의 리소스가 가상 컴퓨터와 통신할 수 있습니다. 지금 끝점을 추가하거나 나중에 만들 수 있습니다. 나중에 끝점을 만드는 방법에 대한 지침은 [가상 컴퓨터와의 통신을 설정하는 방법][5](영문)을 참조하십시오.

가상 컴퓨터와 클라우드 서비스가 만들어진 후 관리 포털의 **Virtual Machines** 아래에 새 가상 컴퓨터가 나열되고 **Cloud Services** 아래에 클라우드 서비스가 나열됩니다. 가상 컴퓨터와 클라우드 서비스는 둘 다 자동으로 시작됩니다.

## <a id="logon"> </a>가상 컴퓨터를 만든 후 로그온하는 방법

가상 컴퓨터와 컴퓨터에서 실행되는 응용 프로그램의 설정을 관리하기 위해 SSH 클라이언트를 사용할 수 있습니다. 이렇게 하려면
가상 컴퓨터에 액세스하는 데 사용할 컴퓨터에 SSH 클라이언트를 설치해야 합니다. 선택할 수 있는 많은 SSH 클라이언트
프로그램이 있습니다. 가능한 선택 항목은 다음과 같습니다.

* Windows 운영 체제를 실행하는 컴퓨터를 사용하는 경우 PuTTY 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [PuTTY 다운로드][6]를 참조하십시오.
* Linux 운영 체제를 실행하는 컴퓨터를 사용하는 경우 OpenSSH 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [OpenSSH][7]를 참조하십시오.

이 자습서에서는 PuTTY 프로그램을 사용하여 가상 컴퓨터에 액세스하는 방법을 보여 줍니다.

1.  관리 포털에서 **Host Name** 및 **Port information**을 찾습니다. 가상 컴퓨터 대시보드에서 필요한 정보를 찾을 수 있습니다. 가상 컴퓨터 이름을 클릭하고 대시보드의 **Quick Glance** 섹션에서 **SSH Details**를 찾습니다.
    
    ![SSH 세부 정보
    찾기](./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png)

2.  PuTTY 프로그램을 엽니다.

3.  대시보드에서 수집한 **Host Name** 및 **Port information**을 입력하고 **Open**을 클릭합니다.
    
    ![호스트 이름 및 포트 정보 입력](./media/CreateVirtualMachineLinuxTutorial/putty.png)

4.  컴퓨터를 만들 때 지정한 NewUser1 계정을 사용하여 가상 컴퓨터에 로그온합니다.
    
    ![새 가상 컴퓨터에 로그온](./media/CreateVirtualMachineLinuxTutorial/sshlogin.png)
    
    이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

## <a id="attachdisk"> </a>새 가상 컴퓨터에 데이터 디스크를 연결하는 방법

응용 프로그램에서 데이터를 저장해야 할 수도 있습니다. 설정하려면 이전에 만든 가상 컴퓨터에 데이터 디스크를 연결합니다. 가장
쉬운 방법은 컴퓨터에 빈 데이터 디스크를 연결하는 것입니다.

**참고: 데이터 디스크 및 리소스 디스크**  
 데이터 디스크는 Azure 저장소에 상주하며 파일 및 응용 프로그램 데이터의 영구적 저장소에 사용될 수 있습니다.

만들어진 각 가상 컴퓨터에는 임시 로컬 *리소스 디스크*도 연결되어 있습니다. 리소스 디스크의 데이터는 다시 부팅 후 지속되지
않을 수도 있으므로 가상 컴퓨터에서 실행되는 응용 프로그램 및 프로세스에서 임시 데이터 저장소에 사용되는 경우가 많습니다. 또한
운영 체제의 페이지 또는 스왑 파일을 저장하는 데 사용됩니다.

Linux에서 리소스 디스크는 일반적으로 Azure Linux 에이전트에 의해 관리되며 **/mnt/resource**(또는 Ubuntu 이미지의 **/mnt**)에 자동으로 탑재됩니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드][8](영문)를 참조하십시오.

1.  아직 로그인하지 않은 경우 Azure 관리 포털에 로그인합니다.

2.  **Virtual Machines**를 클릭하고 이전에 만든 **MyTestVM1** 가상 컴퓨터를 선택합니다.

3.  명령 모음에서 **Attach**를 클릭한 후 **Attach Empty Disk**를 클릭합니다.
    
    **Attach Empty Disk** 대화 상자가 나타납니다.
    
    ![디스크 세부 정보 정의](./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png)

4.  **Virtual Machine Name**, **Storage Location** 및 **File Name**은 이미 정의되어 있습니다. 원하는 디스크 크기만 입력하면 됩니다. **Size** 필드에 **5**를 입력합니다.
    
    **참고:** 모든 디스크는 Azure 저장소의 VHD 파일에서 만들어집니다. 저장소에 추가되는 VHD 파일의 이름은 지정할 수 있지만 디스크 이름은 자동으로 생성됩니다.

5.  확인 표시를 클릭하여 가상 컴퓨터에 데이터 디스크를 연결합니다.

6.  대시보드를 보고 데이터 디스크가 가상 컴퓨터에 연결되었는지 확인할 수 있습니다. 가상 컴퓨터의 이름을 클릭하여 대시보드를 표시합니다.
    
    이제 가상 컴퓨터에 대한 디스크 수가 2이고 연결한 디스크가 **Disks** 테이블에 나열됩니다.
    
    ![디스크 연결 성공](./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png)

방금 가상 컴퓨터에 연결한 데이터 디스크는 오프라인 상태이며 추가한 후 초기화되지 않았습니다. 데이터를 저장하는 데 사용하려면
컴퓨터에 로그온한 다음 디스크를 초기화해야 합니다.

1.  위의 **가상 컴퓨터를 만든 후 로그온하는 방법**에 나열된 단계에 따라 가상 컴퓨터에 연결합니다.

2.  SSH 창에서 다음 명령을 입력한 후 계정 암호를 입력합니다.
    
    `sudo grep SCSI /var/log/messages`
    
    표시되는 메시지에서 추가된 마지막 데이터 디스크의 식별자를 찾을 수 있습니다.
    
    ![디스크 식별](./media/CreateVirtualMachineLinuxTutorial/diskmessages.png)

3.  SSH 창에서 다음 명령을 입력하여 새 장치를 만들고 계정 암호를 입력합니다.
    
    `sudo fdisk /dev/sdc`


   
    > [WACOM.NOTE] 이 예에서는 `$PATH`에 /sbin 또는 /usr/sbin이 없는 경우 일부 배포에서 `sudo -i`를 사용해야 할 수도 있습니다.

4.  **n**을 입력하여 새 파티션을 만듭니다.
    
    ![새 장치
    만들기](./media/CreateVirtualMachineLinuxTutorial/diskpartition.png)

5.  **p**를 입력하여 파티션을 주 파티션으로 설정하고, **1**을 입력하여 첫 번째 파티션으로 설정한 다음 enter를 입력하여 실린더에 대한 기본값을 적용합니다.
    
    ![파티션
    만들기](./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png)

6.  **p**를 입력하여 분할되는 디스크에 대한 세부 정보를 확인합니다.
    
    ![디스크 정보 나열](./media/CreateVirtualMachineLinuxTutorial/diskinfo.png)

7.  **w**를 입력하여 디스크에 대한 설정을 씁니다.
    
    ![디스크 변경 내용
    쓰기](./media/CreateVirtualMachineLinuxTutorial/diskwrite.png)

8.  새 파티션에 파일 시스템을 만들어야 합니다. 예를 들어 다음 명령을 입력하여 파일 시스템을 만들고 계정 암호를 입력합니다.
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![파일 시스템
    만들기](./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png)


   
    > [WACOM.NOTE] SUSE Linux Enterprise 11 시스템에서는 ext4 파일 시스템에 대해 읽기 전용 권한만 제공합니다. 이 시스템에서 새 파일 시스템 형식을 ext4 대신 ext3으로 지정하는 것이 좋습니다.

9.  다음으로 새 파일 시스템을 탑재하는 데 사용할 수 있는 디렉터리가 있어야 합니다. 예를 들어 다음 명령을 입력하여
    드라이브를 탑재할 새 디렉터리를 만들고 계정 암호를 입력합니다.
    
    `sudo mkdir /datadrive`

10. 다음 명령을 입력하여 드라이브를 탑재합니다.
    
    `sudo mount /dev/sdc1 /datadrive`
    
    이제 데이터 디스크를 **/datadrive**로 사용할 준비가 되었습니다.

11. /etc/fstab에 새 드라이브를 추가합니다.
    
    다시 부팅 후 드라이브가 자동으로 다시 탑재되도록 하려면 /etc/fstab 파일에 추가해야 합니다. 또한 /etc/fstab에 UUID(Universally Unique IDentifier)를 사용하여 장치 이름(즉, /dev/sdc1) 대신 드라이브를 가리키는 것이 좋습니다. 새 드라이브의 UUID를 찾으려면 **blkid** 유틸리티를 사용할 수 있습니다.
    
        `sudo -i blkid`
    
    출력은 다음과 유사합니다.
    
        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`


   
    > [WACOM.NOTE] blkid에 항상 sudo 액세스가 필요한 것은 아니지만 /sbin 또는 /usr/sbin이 `$PATH`에 없는 경우 일부 배포에서 `sudo -i`를 사용하여 실행하는 것이 더 쉬울 수도 있습니다.
    
    **주의:** /etc/fstab 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하십시오. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.
    
    텍스트 편집기를 사용하여 /etc/fstab 파일의 끝에 새 파일 시스템에 대한 정보를 입력합니다. 이 예제에서는 이전 단계에서 만든 새 **/dev/sdc1** 장치의 UUID 값과 탑재 지점 **/datadrive**를 사용합니다.
    
        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`
    
    또는 SUSE Linux 기반 시스템에서는 약간 다른 형식을 사용해야 할 수 있습니다.
    
        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`
    
    추가 데이터 드라이브 또는 파티션이 만들어진 경우 /etc/fstab에 별도로 입력해야 합니다.
    
    이제 이전 단계에서 만든 예제 탑재 지점 `/datadrive`를 사용하여 파일 시스템을 탑재 해제했다가 다시 탑재하여
    파일 시스템이 제대로 탑재되었는지 테스트할 수 있습니다.
    
        `sudo umount /datadrive`
        `sudo mount /datadrive`
    
    두 번째 명령에서 오류가 발생하는 경우 /etc/fstab 파일에서 구문이 올바른지 확인합니다.


   
    > [WACOM.NOTE] 이후에 fstab를 편집하지 않고 데이터 디스크를 제거하면 VM이 부팅되지 않을 수 있습니다. 이런 경우가 자주 발생하면 대부분의 배포에서 디스크가 없는 경우에도 시스템이 부팅되도록 하는 `nofail` 및/또는 `nobootwait` fstab 옵션을 제공합니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하십시오.

## 다음 단계

Azure의 Linux에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [Azure의 Linux 소개][9]

* [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법][10]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkId=311926
[3]: http://manage.windowsazure.com
[4]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[5]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/
[6]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[7]: http://www.openssh.org/
[8]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/
[9]: http://www.windowsazure.com/en-us/documentation/articles/introduction-linux/
[10]: http://www.windowsazure.com/en-us/documentation/articles/xplat-cli/