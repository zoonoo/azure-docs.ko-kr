
디스크에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD 정보](virtual-machines-linux-about-disks-vhds.md)를 참조하세요.

##<a id="attachempty"></a>방법: 빈 디스크 연결

Azure가 .vhd 파일을 자동으로 만들어 저장소 계정에 저장하므로, 빈 디스크를 연결하는 것이 데이터 디스크를 추가하는 간단한 방법입니다.

1. **가상 컴퓨터**를 클릭한 다음 적절한 VM을 선택합니다.

2. 명령 모음에서 **Attach**를 클릭한 후 **Attach Empty Disk**를 클릭합니다.


	![빈 디스크 연결](./media/howto-attach-disk-windows-linux/AttachEmptyDisk.png)

3.	**빈 디스크 연결** 대화 상자가 나타납니다.


	![새로운 빈 디스크 연결](./media/howto-attach-disk-windows-linux/AttachEmptyDetail.png)


	다음을 수행합니다.

	- **파일 이름**에서 기본 이름을 그대로 사용하거나 .vhd 파일에 대한 다른 이름을 입력합니다. .vhd 파일용으로 다른 이름을 입력하는 경우에도 데이터 디스크는 자동으로 생성된 이름을 사용합니다.

	- 데이터 디스크의 **크기(GB)**를 입력합니다.

	- 확인 표시를 클릭하여 마칩니다.

4.	데이터 디스크가 생성되어 연결된 후에는 VM의 대시보드에 나열됩니다.

	![빈 데이터 디스크 연결됨](./media/howto-attach-disk-windows-linux/AttachEmptySuccess.png)

> [AZURE.NOTE] 새 데이터 디스크를 추가한 후 VM에 로그온하여 디스크를 사용할 수 있도록 초기화해야 합니다.


##<a id="attachexisting"></a>방법: 기존 디스크 연결

기존 디스크를 연결하려면 저장소 계정에 사용 가능한 .vhd가 있어야 합니다. [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet을 사용하여 .vhd 파일을 저장소 계정에 업로드합니다. .vhd 파일을 만들어 업로드한 후에는 VM에 연결할 수 있습니다.

1. **가상 컴퓨터**를 클릭하고 해당 가상 컴퓨터를 선택합니다.

2. 명령 모음에서 **Attach**를 클릭한 후 **Attach Disk**를 선택합니다.


	![데이터 디스크 연결](./media/howto-attach-disk-windows-linux/AttachExistingDisk.png)


3. 데이터 디스크를 선택하고 확인 표시를 클릭하여 연결합니다.

	![데이터 디스크 세부 정보 입력](./media/howto-attach-disk-windows-linux/AttachExistingDetail.png)

4.	데이터 디스크가 연결되고 나면 VM의 대시보드에 나열됩니다.


	![데이터 디스크 연결됨](./media/howto-attach-disk-windows-linux/AttachExistingSuccess.png)

<!---HONumber=AcomDC_0323_2016-->