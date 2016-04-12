


## 가상 컴퓨터 찾기

1. Azure 포털에 로그인합니다.

2. 허브 메뉴에서 **가상 컴퓨터**를 클릭합니다.

3.	목록에서 가상 컴퓨터를 선택합니다.

4. 오른쪽의 **Essentials** 아래에서 **모든 설정**을 클릭한 다음 **디스크**를 클릭합니다.

	![디스크 설정 열기](./media/virtual-machines-common-attach-disk-portal/find-disk-settings.png)

새 디스크 또는 기존 디스크를 연결하려면 다음 지침에 따라 계속합니다.

## 옵션 1: 새 디스크 연결

1.	**디스크** 블레이드에서 **새 연결**을 클릭합니다.

2.	기본 설정을 검토하고 필요에 따라 업데이트한 다음 **확인**을 클릭합니다.

 	![디스크 설정 검토](./media/virtual-machines-common-attach-disk-portal/attach-new.png)

3.	Azure가 디스크를 만들고 가상 컴퓨터에 연결하면 가상 컴퓨터의 디스크 설정의 **데이터 디스크** 아래에 새 디스크가 나열됩니다.

## 옵션 2: 기존 디스크 연결

1.	**디스크** 블레이드에서 **기존 연결**을 클릭합니다.

2.	**기존 디스크 연결**에서 **VHD 파일**을 클릭합니다.

	![기존 디스크 연결](./media/virtual-machines-common-attach-disk-portal/attach-existing.png)

3.	**저장소 계정**에서 계정 및 .vhd 파일을 보관하는 컨테이너를 선택합니다.

	![VHD 위치 찾기](./media/virtual-machines-common-attach-disk-portal/find-storage-container.png)

4.	.vhd 파일을 업로드합니다.

5.	**기존 디스크 연결**에서 방금 선택한 파일이 **VHD 파일** 아래에 나열됩니다. **확인**을 클릭합니다.

6.	Azure가 디스크를 가상 컴퓨터에 연결한 후 가상 컴퓨터의 디스크 설정의 **데이터 디스크** 아래에 해당 디스크가 나열됩니다.

<!---HONumber=AcomDC_0330_2016-->