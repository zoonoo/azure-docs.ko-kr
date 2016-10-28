
디스크에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md)를 참조하세요.

<a id="attachempty"></a>
## 빈 디스크 연결

1.  Azure CLI를 열고 [Azure 구독에 연결](../articles/xplat-cli-connect.md)합니다. Azure 서비스 관리 모드(`azure config mode asm`)에 있는지 확인합니다.

2.  `azure vm disk attach-new`을 입력하여 다음과 같이 새 디스크를 만들고 연결합니다. _TestVM_을 Linux 가상 컴퓨터의 이름으로 바꾸고 디스크의 크기(GB)를 지정합니다. 이 예제에는 100GB입니다.

        azure vm disk attach-new TestVM 100

3.	데이터 디스크를 만들고 연결한 후 `azure vm disk list <virtual-machine-name>` 출력에 다음과 같이 나열됩니다.

        $ azure vm disk list TestVM
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        TestVM-2645b8030676c8f8.vhd  Linux
        data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## 기존 디스크 연결

기존 디스크를 연결하려면 저장소 계정에 사용 가능한 .vhd가 있어야 합니다.

1. 	Azure CLI를 열고 [Azure 구독에 연결](../articles/xplat-cli-connect.md)합니다. Azure 서비스 관리 모드(`azure config mode asm`)에 있는지 확인합니다.

2.	연결하려는 VHD가 Azure 구독에 이미 업로드되었는지 확인합니다.

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
    	data:    TestVM-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

3.  사용하려는 디스크를 찾을 수 없으면 `azure vm disk create` 또는 `azure vm disk upload`를 사용하여 로컬 VHD를 구독에 업로드할 수 있습니다. `disk create`의 예는 다음과 같습니다.

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://mystorageaccount.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	`azure vm disk upload` 명령을 사용하여 특정 저장소 계정에 VHD를 업로드할 수도 있습니다. Azure 가상 컴퓨터의 데이터 디스크를 관리하는 명령에 대한 자세한 내용은 [여기](virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks)를 참조하세요.

4.  이제 원하는 VHD를 가상 컴퓨터에 연결합니다.

		$azure vm disk attach TestVM myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	_TestVM_을 가상 컴퓨터의 이름으로 바꾸고 _myTestVhd_를 원하는 VHD로 바꿔야 합니다.

5.	다음과 같이 `azure vm disk list <virtual-machine-name>` 명령을 사용하여 디스크가 가상 컴퓨터에 연결되어 있는지 확인할 수 있습니다.

		$azure vm disk list TestVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        TestVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]
데이터 디스크를 추가한 후 가상 컴퓨터가 디스크를 저장소에 사용할 수 있도록 가상 컴퓨터에 로그온한 다음 디스크를 초기화해야 합니다(이를 실행하는 방법에 대한 자세한 내용은 아래 단계를 참조하세요).
