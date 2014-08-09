
# 가상 컴퓨터에 데이터 디스크를 연결하는 방법

* [개념](#concepts)
* [방법: 기존 디스크 연결](#attachexisting)
* [방법: 빈 디스크 연결](#attachempty)
* [방법: Windows Server 2008 R2에서 새 데이터 디스크 초기화](#initializeinWS)
* [방법: Linux에서 새 데이터 디스크 초기화](#initializeinlinux)

## <a id="concepts" ></a>개념

가상 컴퓨터에 데이터 디스크를 연결하여 응용 프로그램 데이터를 저장할 수 있습니다. 데이터 디스크는 고유한 컴퓨터를 사용하여
로컬에서 또는 Azure를 사용하여 클라우드에 만들 수 있는 VHD(가상 하드 디스크)입니다. 사무실에 있는 서버와 동일한
방식으로 가상 컴퓨터의 데이터 디스크를 관리합니다.

관리 포털을 사용하여 데이터가 포함된 데이터 디스크를 가상 컴퓨터에 업로드 및 연결하고 가상 컴퓨터에서 사용되는 것과 동일한
저장소 계정에서 빈 디스크를 추가할 수 있습니다. 이 문서에서는 이러한 프로세스에 대해 설명합니다. 다른 저장소 계정에 있는 빈
디스크를 연결하려면 Azure PowerShell 모듈에서 사용 가능한 [Add-AzureDataDisk][1] cmdlet을 사용합니다. 모듈을 다운로드하려면 [다운로드][2] 페이지를 참조하십시오.

가상 컴퓨터는 디스크를 추가하기 위해 중지되지 않습니다. 가상 컴퓨터에 연결할 수 있는 디스크 수는 가상 컴퓨터의 크기에 따라
달라집니다. 가상 컴퓨터 및 디스크 크기에 대한 자세한 내용은 [Azure에 대한 가상 컴퓨터 크기][3]를 참조하십시오.

> [WACOM.NOTE] 
> Azure 저장소는 최대 1TB 크기의 Blob을 지원하며, 최대 가상 크기가 999GB인 VHD를 수용합니다. 그러나 Hyper-V를 사용하여 새 VHD를 만드는 경우 지정한 크기는 가상 크기를 나타냅니다. Azure에서 VHD를 사용하려면 999GB 이하의 크기를 지정합니다.

**데이터 디스크 및 리소스 디스크**  
 데이터 디스크는 Azure 저장소에 상주하며 파일 및 응용 프로그램 데이터의 영구적 저장소에 사용될 수 있습니다.

각 가상 컴퓨터에는 임시 로컬 *리소스 디스크*도 연결되어 있습니다. 리소스 디스크의 데이터는 다시 부팅 후 지속되지 않을 수도
있으므로 가상 컴퓨터에서 실행되는 응용 프로그램 및 프로세스에서 임시 데이터 저장소에 사용되는 경우가 많습니다. 또한 운영 체제의
페이지 또는 스왑 파일을 저장하는 데 사용됩니다.

Windows에서 리소스 디스크는 **D:** 드라이브로 레이블이 지정됩니다. Linux에서 리소스 디스크는 일반적으로 Azure Linux 에이전트에 의해 관리되며 **/mnt/resource**(또는 Ubuntu 이미지의 **/mnt**)에 자동으로 탑재됩니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드][4](영문)를 참조하십시오.

데이터 디스크 사용에 대한 자세한 내용은 [디스크 및 이미지 관리][5]를 참조하십시오.

## <a id="attachexisting" ></a>방법: 기존 디스크 연결

1.  아직 로그인하지 않은 경우 [Azure 관리 포털][6]에 로그인합니다.

2.  **Virtual Machines**를 클릭하고 디스크를 연결할 가상 컴퓨터를 선택합니다.

3.  명령 모음에서 **Attach**를 클릭한 후 **Attach Disk**를 선택합니다.
    
    ![데이터 디스크
    연결](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)
    
    **Attach Disk** 대화 상자가 나타납니다.
    
    ![데이터 디스크 세부 정보
    입력](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

4.  가상 컴퓨터에 연결할 데이터 디스크를 선택합니다. 
5.  확인 표시를 클릭하여 가상 컴퓨터에 데이터 디스크를 연결합니다.
    
    이제 가상 컴퓨터의 대시보드에 데이터 디스크가 나열됩니다.
    
    ![데이터 디스크
    연결됨](./media/howto-attach-disk-window-linux/AttachSuccess.png)

## <a id="attachempty" ></a>방법: 빈 디스크 연결

빈 디스크로 사용할 .vhd 파일을 만들고 업로드한 후 가상 컴퓨터에 연결할 수 있습니다. [Add-AzureVhd][7]
cmdlet을 사용하여 .vhd 파일을 저장소 계정에 업로드합니다.

1.  **Virtual Machines**를 클릭하고 데이터 디스크를 연결할 가상 컴퓨터를 선택합니다.

2.  명령 모음에서 **Attach**를 클릭한 후 **Attach Empty Disk**를 선택합니다.
    
    ![빈 디스크
    연결](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)
    
    **Attach Empty Disk** 대화 상자가 나타납니다.
    
    ![새로운 빈 디스크
    연결](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

3.  **File Name**에서 자동으로 생성된 이름을 적용하거나 저장된 VHD 파일에 사용할 이름을 입력합니다. VHD
    파일에서 만든 데이터 디스크는 항상 자동으로 생성된 이름을 사용합니다.

4.  **Size**에 데이터 디스크의 크기를 입력합니다.

5.  확인 표시를 클릭하여 빈 데이터 디스크를 연결합니다.
    
    이제 가상 컴퓨터의 대시보드에 데이터 디스크가 나열됩니다.
    
    ![빈 데이터 디스크
    연결됨](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

> [WACOM.NOTE] 
> 데이터 디스크를 추가한 후 가상 컴퓨터가 디스크를 저장소에 사용할 수 있도록 가상 컴퓨터에 로그온한 다음 디스크를 초기화해야 합니다.

## <a id="initializeinWS" ></a>방법: Windows Server에서 새 데이터 디스크 초기화

1.  [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법](../virtual-machines-log-on-windows-server/)에 나열된 단계에 따라 가상 컴퓨터에 연결합니다.

2.  로그온한 후 **Server Manager**를 열고 왼쪽 창에서 **Storage**를 확장한 후 **Disk Management**를 클릭합니다.
    
    ![서버 관리자
    열기](./media/howto-attach-disk-window-linux/ServerManager.png)

3.  **Disk 2**를 마우스 오른쪽 단추로 클릭한 후 **Initialize Disk**를 클릭합니다.
    
    ![디스크
    초기화](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4.  **OK**를 클릭하여 초기화 프로세스를 시작합니다.

5.  디스크 2에 대한 공간 할당 영역을 마우스 오른쪽 단추로 클릭하고 **New Simple Volume**을 클릭한 다음 기본값을 사용하여 마법사를 완료합니다.
    
    ![볼륨
    초기화](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)
    
    이제 디스크가 온라인 상태이며 새 드라이브 문자로 사용할 수 있습니다.
    
    ![볼륨
    초기화됨](./media/howto-attach-disk-window-linux/InitializeSuccess.png)

## <a id="initializeinlinux" ></a>방법: Linux에서 새 데이터 디스크 초기화

1.  [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](../virtual-machines-linux-how-to-log-on/)에 나열된 단계에 따라 가상 컴퓨터에 연결합니다.

2.  SSH 창에서 다음 명령을 입력하고 가상 컴퓨터 관리를 위해 만든 계정의 암호를 입력합니다.
    
    `sudo grep SCSI /var/log/messages`
    
    표시되는 메시지에서 추가된 마지막 데이터 디스크의 식별자를 찾을 수 있습니다.
    
    ![디스크 메시지
    가져오기](./media/howto-attach-disk-window-linux/DiskMessages.png)

3.  SSH 창에서 다음 명령을 입력하여 새 장치를 만들고 계정 암호를 입력합니다.
    
    `sudo fdisk /dev/sdc`


   
    > [WACOM.NOTE] 이 예에서는 `$PATH`에 /sbin 또는 /usr/sbin이 없는 경우 일부 배포에서 `sudo -i`를 사용해야 할 수도 있습니다.

4.  **n**을 입력하여 새 파티션을 만듭니다.
    
    ![새 장치
    만들기](./media/howto-attach-disk-window-linux/DiskPartition.png)

5.  **p**를 입력하여 파티션을 주 파티션으로 설정하고, **1**을 입력하여 첫 번째 파티션으로 설정한 다음 enter를 입력하여 실린더에 대한 기본값을 적용합니다.
    
    ![파티션 만들기](./media/howto-attach-disk-window-linux/DiskCylinder.png)

6.  **p**를 입력하여 분할되는 디스크에 대한 세부 정보를 확인합니다.
    
    ![디스크 정보 나열](./media/howto-attach-disk-window-linux/DiskInfo.png)

7.  **w**를 입력하여 디스크에 대한 설정을 씁니다.
    
    ![디스크 변경 내용
    쓰기](./media/howto-attach-disk-window-linux/DiskWrite.png)

8.  새 파티션에 파일 시스템을 만들어야 합니다. 예를 들어 다음 명령을 입력하여 파일 시스템을 만들고 계정 암호를 입력합니다.
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![파일 시스템
    만들기](./media/howto-attach-disk-window-linux/DiskFileSystem.png)


   
    > [WACOM.NOTE] SUSE Linux Enterprise 11 시스템에서는 ext4 파일 시스템에 대해 읽기 전용 권한만 제공합니다. 이 시스템에서 새 파일 시스템 형식을 ext4 대신 ext3으로 지정하는 것이 좋습니다.

9.  다음으로 새 파일 시스템을 탑재하는 데 사용할 수 있는 디렉터리가 있어야 합니다. 예를 들어 다음 명령을 입력하여
    드라이브를 탑재할 새 디렉터리를 만들고 계정 암호를 입력합니다.
    
    `sudo mkdir /datadrive`

10. 다음 명령을 입력하여 드라이브를 탑재합니다.
    
    `sudo mount /dev/sdc1 /datadrive`
    
    이제 데이터 디스크를 **/datadrive**로 사용할 준비가 되었습니다.

11. /etc/fstab에 새 드라이브를 추가합니다.
    
    다시 부팅 후 드라이브가 자동으로 다시 탑재되도록 하려면 /etc/fstab 파일에 추가해야 합니다. 또한 /etc/fstab에 UUID(Universally Unique IDentifier)를 사용하여 장치 이름(즉, /dev/sdc1) 대신 드라이브를 가리키는 것이 좋습니다. 새 드라이브의 UUID를 찾으려면 **blkid** 유틸리티를
    사용할 수 있습니다.
    
        `sudo -i blkid`
    
    출력은 다음과 유사합니다.
    
        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`


   
    > [WACOM.NOTE] blkid에 항상 sudo 액세스가 필요한 것은 아니지만 /sbin 또는 /usr/sbin이
    > `$PATH`에 없는 경우 일부 배포에서 `sudo -i`를 사용하여 실행하는 것이 더 쉬울 수도 있습니다.
    
    **주의:** /etc/fstab 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은
    경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하십시오. 또한 편집하기 전에
    /etc/fstab 파일의 백업을 만드는 것이 좋습니다.
    
    텍스트 편집기를 사용하여 /etc/fstab 파일의 끝에 새 파일 시스템에 대한 정보를 입력합니다. 이 예제에서는 이전
    단계에서 만든 새 **/dev/sdc1** 장치의 UUID 값과 탑재 지점 **/datadrive**를 사용합니다.
    
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



[1]: http://go.microsoft.com/fwlink/p/?LinkId=391661
[2]: http://www.windowsazure.com/ko-kr/downloads/
[3]: http://go.microsoft.com/FWLink/p/?LinkID=294683
[4]: http://www.windowsazure.com/ko-kr/manage/linux/how-to-guides/linux-agent-guide/
[5]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj672979.aspx
[6]: http://manage.windowsazure.com
[7]: http://go.microsoft.com/FWLink/p/?LinkID=391684