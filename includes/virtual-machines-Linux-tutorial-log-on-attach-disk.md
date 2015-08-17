

## <a id="logon"> </a>가상 컴퓨터를 만든 후 로그온하는 방법 ##

가상 컴퓨터와 컴퓨터에서 실행되는 응용 프로그램의 설정을 관리하기 위해 SSH 클라이언트를 사용할 수 있습니다. 이렇게 하려면 가상 컴퓨터에 액세스하는 데 사용할 컴퓨터에 SSH 클라이언트를 설치해야 합니다. 선택할 수 있는 많은 SSH 클라이언트 프로그램이 있습니다. 가능한 선택 항목은 다음과 같습니다.

- Windows 운영 체제를 실행하는 컴퓨터를 사용하는 경우 PuTTY 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [PuTTY 다운로드](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)를 참조하세요.
- Linux 운영 체제를 실행하는 컴퓨터를 사용하는 경우 OpenSSH 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [OpenSSH](http://www.openssh.org/)를 참조하세요.

이 자습서에서는 PuTTY 프로그램을 사용하여 가상 컴퓨터에 액세스하는 방법을 보여 줍니다.

1. 관리 포털에서 **Host Name** 및 **Port information**을 찾습니다. 가상 컴퓨터 대시보드에서 필요한 정보를 찾을 수 있습니다. 가상 컴퓨터 이름을 클릭하고 대시보드의 **간략 상태** 섹션에서 **SSH 세부 정보**를 찾습니다.

	![SSH 세부 정보 찾기](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/SSHdetails.png)

2. PuTTY 프로그램을 엽니다.

3. 대시보드에서 수집한 **Host Name** 및 **Port information**을 입력하고 **Open**을 클릭합니다.

	![호스트 이름 및 포트 정보 입력](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/putty.png)

4. 가상 컴퓨터를 만들 때 추가한 NewUser1 계정을 사용하여 가상 컴퓨터에 로그온합니다.

	![새 가상 컴퓨터에 로그온](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/sshlogin.png)

	이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.


## <a id="attachdisk"> </a>새 가상 컴퓨터에 데이터 디스크를 연결하는 방법 ##

응용 프로그램에서 데이터를 저장해야 할 수도 있습니다. 설정하려면 이전에 만든 가상 컴퓨터에 데이터 디스크를 연결합니다. 가장 쉬운 방법은 컴퓨터에 빈 데이터 디스크를 연결하는 것입니다.

Linux에서 리소스 디스크는 일반적으로 Azure Linux 에이전트에 의해 관리되며 **/mnt/resource**(또는 Ubuntu 이미지의 **/mnt**)에 자동으로 탑재됩니다. 반면, Linux에서 데이터 디스크 이름은 커널에 의해 `/dev/sdc`로 지정될 수 있으며 사용자는 해당 리소스를 파티셔닝, 형식 지정 및 마운트해야 합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md)(영문)를 참조하세요.

>[AZURE.NOTE]리소스 디스크에 데이터를 저장하지 마세요. 리소스 디스크는 응용 프로그램 및 프로세스에 임시 저장소를 제공하며, 스왑 파일과 같이 보관할 필요가 없는 데이터를 저장하는 데 사용됩니다. 데이터 디스크는 Azure 저장소에서 페이지 Blob의 .vhd 파일로 상주하며, 데이터 보호를 위한 중복 저장소를 제공합니다. 자세한 내용은 [Azure의 디스크 및 이미지 정보](http://msdn.microsoft.com/library/jj672979.aspx)를 참조하세요.

1. 아직 로그인하지 않은 경우 Azure 관리 포털에 로그인합니다.

2. **Virtual Machines**를 클릭하고 이전에 만든 **MyTestVM1** 가상 컴퓨터를 선택합니다.

3. 명령 모음에서 **Attach**를 클릭한 후 **Attach Empty Disk**를 클릭합니다.
	
	**Attach Empty Disk** 대화 상자가 나타납니다.

	![디스크 세부 정보 정의](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachnewdisklinux.png)

4. **Virtual Machine Name**, **Storage Location** 및 **File Name**은 이미 정의되어 있습니다. 원하는 디스크 크기만 입력하면 됩니다. **Size** 필드에 **5**를 입력합니다.

	**참고:** 모든 디스크는 Azure 저장소의 VHD 파일에서 만들어집니다. 저장소에 추가되는 VHD 파일의 이름은 지정할 수 있지만 디스크 이름은 자동으로 생성됩니다.

5. 확인 표시를 클릭하여 가상 컴퓨터에 데이터 디스크를 연결합니다.

6. 대시보드를 보고 데이터 디스크가 가상 컴퓨터에 연결되었는지 확인할 수 있습니다. 가상 컴퓨터의 이름을 클릭하여 대시보드를 표시합니다.

	이제 가상 컴퓨터에 대한 디스크 수가 2이고 연결한 디스크가 **Disks** 테이블에 나열됩니다.

	![디스크 연결 성공](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/attachemptysuccess.png)


방금 가상 컴퓨터에 연결한 데이터 디스크는 오프라인 상태이며 추가한 후 초기화되지 않았습니다. 데이터를 저장하는 데 사용하려면 컴퓨터에 로그온한 다음 디스크를 초기화해야 합니다.

1. 위의 **가상 컴퓨터를 만든 후 로그온하는 방법**에 나열된 단계에 따라 가상 컴퓨터에 연결합니다.


2. SSH 창에서 다음 명령을 입력한 후 계정 암호를 입력합니다.

	`sudo grep SCSI /var/log/messages`

	표시되는 메시지에서 추가된 마지막 데이터 디스크의 식별자를 찾을 수 있습니다.

	![디스크 식별](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskmessages.png)


3. SSH 창에서 다음 명령을 입력하여 새 장치를 만들고 계정 암호를 입력합니다.

	`sudo fdisk /dev/sdc`

	>[AZURE.NOTE]이 예제에서는 일부 분산에서 `$PATH`에 /sbin 또는 /usr/sbin이 없는 경우 `sudo -i`를 사용해야 할 수도 있습니다.


4. **n**을 입력하여 새 파티션을 만듭니다.

	![새 장치 만들기](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskpartition.png)


5. **p**를 입력하여 파티션을 주 파티션으로 설정하고, **1**을 입력하여 첫 번째 파티션으로 설정한 다음 enter를 입력하여 실린더에 대한 기본값을 적용합니다.

	![파티션 만들기](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskcylinder.png)


6. **p**를 입력하여 분할되는 디스크에 대한 세부 정보를 확인합니다.

	![디스크 정보 나열](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskinfo.png)


7. **w**를 입력하여 디스크에 대한 설정을 씁니다.

	![디스크 변경 내용 쓰기](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskwrite.png)


8. 새 파티션에 파일 시스템을 만들어야 합니다. 예를 들어 다음 명령을 입력하여 파일 시스템을 만들고 계정 암호를 입력합니다.

	`sudo mkfs -t ext4 /dev/sdc1`

	![파일 시스템 만들기](./media/virtual-machines-Linux-tutorial-log-on-attach-disk/diskfilesystem.png)

	>[AZURE.NOTE]SUSE Linux Enterprise 11 시스템에서는 ext4 파일 시스템에 대해 읽기 전용 액세스 권한만 제공됩니다. 이 시스템에서는 새 파일 시스템의 형식을 ext4 대신 ext3으로 지정하는 것이 좋습니다.


9. 새 파일 시스템을 탑재할 디렉터리를 만듭니다. 예를 들어, 다음 명령을 입력한 후 계정 암호를 입력합니다.

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

	>[AZURE.NOTE]blkid에 항상 sudo 액세스가 필요한 것은 아니지만 /sbin 또는 /usr/sbin이 `$PATH`에 없는 경우 일부 배포에서 `sudo -i`를 사용하여 실행하는 것이 더 쉬울 수도 있습니다.

	**주의:** /etc/fstab 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하세요. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.

	텍스트 편집기를 사용하여 /etc/fstab 파일의 끝에 새 파일 시스템에 대한 정보를 입력합니다. 이 예제에서는 이전 단계에서 만든 새 **/dev/sdc1** 장치의 UUID 값과 탑재 지점 **/datadrive**를 사용합니다.

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	또는 SUSE Linux 기반 시스템에서는 약간 다른 형식을 사용해야 할 수 있습니다.

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	추가 데이터 드라이브 또는 파티션이 만들어진 경우 /etc/fstab에 별도로 입력해야 합니다.

	이제 이전 단계에서 만든 예제 탑재 지점 `/datadrive`를 사용하여 파일 시스템을 탑재 해제했다가 다시 탑재하여 파일 시스템이 제대로 탑재되었는지 테스트할 수 있습니다.

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	두 번째 명령에서 오류가 발생하는 경우 /etc/fstab 파일에서 구문이 올바른지 확인합니다.


	>[AZURE.NOTE]이후에 fstab을 편집하지 않고 데이터 디스크를 제거하면 VM이 부팅되지 않을 수 있습니다. 이런 경우가 자주 발생하면 대부분의 배포에서 디스크가 없는 경우에도 시스템이 부팅되도록 하는 `nofail` 및/또는 `nobootwait` 옵션을 제공합니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하세요.

<!---HONumber=August15_HO6-->