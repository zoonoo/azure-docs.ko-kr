VM(가상 컴퓨터)에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 VM에서 디스크가 제거되지만, 저장소에서는 제거되지 않습니다. 디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 VM에 다시 연결할 수 있습니다.

> [AZURE.NOTE] Azure의 VM은 운영 체제 디스크, 로컬 임시 디스크, 선택적 데이터 디스크 등 다양한 유형의 디스크를 사용합니다. 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md)를 참조하십시오. 또한 VM을 삭제하지 않는 한 운영 체제 디스크를 분리할 수 없습니다.

## 디스크 찾기

VM에서 디스크를 분리하기 전에 분리할 디스크에 대한 식별자인 LUN 번호를 확인해야 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1. 	Azure CLI를 열고 [Azure 구독에 연결](../articles/xplat-cli-connect.md)합니다. Azure 서비스 관리 모드(`azure config mode asm`)에 있는지 확인합니다.

2. 	`azure vm disk list
	<virtual-machine-name>`를 사용하여 디스크가 VM에 연결되어 있는지 확인합니다.

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

3. 	분리하려는 디스크에 대한 LUN 또는 **논리 단위 번호**를 확인합니다.


## 디스크 분리

디스크의 LUN 번호를 찾으면 디스크를 분리할 준비가 된 것입니다.

1. 	`azure vm disk detach
 	<virtual-machine-name> <LUN>` 명령을 실행하여 가상 컴퓨터에서 선택된 디스크를 분리합니다.

		$azure vm disk detach UbuntuVM 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	이 명령을 실행하여 디스크가 분리되었는지 확인할 수 있습니다.

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

분리된 디스크가 저장소에 남아 있지만 더 이상 가상 컴퓨터에 연결되지 않습니다.

<!---HONumber=AcomDC_0608_2016-->