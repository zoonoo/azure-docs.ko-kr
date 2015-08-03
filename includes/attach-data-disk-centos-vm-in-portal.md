1. Azure [관리 포털](http://manage.windowsazure.com)에서 **Virtual Machines**를 클릭하고 방금 만든 가상 컴퓨터(**testlinuxvm**)를 선택합니다.

2. 명령 모음에서 **Attach**를 클릭한 후 **Attach Empty Disk**를 클릭합니다.

	**Attach Empty Disk** 대화 상자가 나타납니다.


3. **Virtual Machine Name**, **Storage Location** 및 **File Name**은 이미 정의되어 있습니다. 원하는 디스크 크기만 입력하면 됩니다. **Size** 필드에 **5**를 입력합니다.

	![빈 디스크 연결][Image2]

	**참고:** 모든 디스크는 Azure 저장소의 VHD 파일에서 만들어집니다. 저장소에 추가되는 .vhd 파일의 이름은 지정할 수 있지만 디스크 이름은 Azure에서 자동으로 생성합니다.

4. 확인 표시를 클릭하여 가상 컴퓨터에 데이터 디스크를 연결합니다.

5. 가상 컴퓨터의 이름을 클릭하여 대시보드를 표시합니다. 이 대시보드를 사용하여 데이터 디스크가 가상 컴퓨터에 연결되었는지 확인할 수 있습니다. 연결한 디스크가 **Disks** 테이블에 나열됩니다.

	데이터 디스크를 연결하는 경우 로그인하여 설정을 완료해야만 사용할 준비가 된 것입니다.

##SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결 및 전체 설치
디스크를 사용하여 데이터를 저장할 수 있도록 가상 컴퓨터에 로그온하여 디스크의 설정을 완료합니다.

1. 가상 컴퓨터가 프로비전된 후 위 단계에 설명된 대로 SSH 또는 PuTTY를 사용하여 연결하고 **newuser**로 로그인합니다.	


2. SSH 또는 PuTTY 창에서 다음 명령을 입력한 후 계정 암호를 입력합니다.

	`$ sudo grep SCSI /var/log/messages`

	표시되는 메시지에서 추가된 마지막 데이터 디스크의 식별자(이 예제에서는 **sdc**)를 찾을 수 있습니다.

	![GREP][Image4]


3. SSH 또는 PuTTY 창에서 다음 명령을 입력하여 **/dev/sdc** 디스크를 분할합니다.

	`$ sudo fdisk /dev/sdc`


4. **n**을 입력하여 새 파티션을 만듭니다.

	![FDISK][Image5]


5. **p**를 입력하여 파티션을 주 파티션으로 설정하고, **1**을 입력하여 첫 번째 파티션으로 설정한 다음 enter를 입력하여 실린더에 대한 기본값(1)을 적용합니다.

	![FDISK][Image6]


6. **p**를 입력하여 분할되는 디스크에 대한 세부 정보를 확인합니다.

	![FDISK][Image7]


7. **w**를 입력하여 디스크에 대한 설정을 씁니다.

	![FDISK][Image8]


8. **mkfs** 명령을 사용하여 새 디스크를 포맷합니다.

	`$ sudo mkfs -t ext4 /dev/sdc1`

9. 다음으로 새 파일 시스템을 탑재하는 데 사용할 수 있는 디렉터리가 있어야 합니다. 예를 들어 다음 명령을 입력하여 드라이브를 탑재할 새 디렉터리를 만들고 계정 암호를 입력합니다.

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

	추가 데이터 드라이브 또는 파티션이 만들어진 경우 /etc/fstab에 별도로 입력해야 합니다.

	이제 이전 단계에서 만든 예제 탑재 지점 `/datadrive`를 사용하여 파일 시스템을 탑재 해제했다가 다시 탑재하여 파일 시스템이 제대로 탑재되었는지 테스트할 수 있습니다.

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	두 번째 명령에서 오류가 발생하는 경우 /etc/fstab 파일에서 구문이 올바른지 확인합니다.


	>[AZURE.NOTE]이후에 fstab을 편집하지 않고 데이터 디스크를 제거하면 VM이 부팅되지 않을 수 있습니다. 이런 경우가 자주 발생하면 대부분의 배포에서 디스크가 없는 경우에도 시스템이 부팅되도록 하는 `nofail` 및/또는 `nobootwait` 옵션을 제공합니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하세요.


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png

<!---HONumber=July15_HO4-->