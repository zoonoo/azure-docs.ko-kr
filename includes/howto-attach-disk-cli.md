
디스크에 대한 자세한 내용은 [Azure의 가상 컴퓨터 디스크 정보](http://go.microsoft.com/fwlink/p/?LinkId=403697)를 참조하세요.

##<a id="cliattachempty"></a>방법: 빈 디스크 연결
빈 디스크 연결은 데이터 디스크를 추가하는 간단한 방법입니다. 다음 명령을 실행하여 새로운 빈 디스크를 연결합니다.

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

`vm-name`을 가상 컴퓨터의 이름으로 바꾸고 `size-in-gb`를 새 디스크의 크기로 바꿉니다. 필요에 따라 Blob URL을 마지막 인수로 사용하여 만들 대상 Blob을 명시적으로 지정할 수 있습니다. Blob URL을 지정하지 않을 경우 Blob 개체가 자동으로 생성됩니다.

다음 명령을 실행하여 디스크가 생성된 것을 확인합니다.

    vm disk list <vm-name>

터미널 출력을 포함하여 위 명령의 샘플 연습은 다음과 같습니다.

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO2-->