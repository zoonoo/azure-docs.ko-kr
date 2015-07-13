#CLI를 사용하여 가상 컴퓨터에서 데이터 디스크를 분리하는 방법

가상 컴퓨터에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 가상 컴퓨터에서 디스크가 제거되지만, 저장소에서는 제거되지 않습니다. 디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 가상 컴퓨터에 다시 연결할 수 있습니다.

> [AZURE.NOTE]Azure의 가상 컴퓨터는 운영 체제 디스크, 로컬 임시 디스크, 선택적 데이터 디스크 등 다양한 유형의 디스크를 사용합니다. 가상 컴퓨터에서 데이터를 저장하려는 경우 데이터 디스크를 사용하는 것을 권장합니다. 디스크에 대한 자세한 내용은 [디스크 및 이미지 정보](http://go.microsoft.com/fwlink/p/?LinkId=263439)를 참조하세요. 현재 운영 체제 디스크를 분리할 수 없습니다.


1. VM에 연결된 디스크 목록을 가져옵니다.

        vm disk list <vm-name>

    `<vm-name>`을 생략하면 구독에 포함된 모든 디스크 목록이 표시됩니다.


2. 디스크를 분리합니다.

        vm disk detach <vm-name> <lun>

    `lun`은 분리할 디스크를 식별하며, VM의 디스크 목록에서 확인할 수 있는 번호입니다.

터미널 출력을 포함하는 샘플 연습:

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=62-->