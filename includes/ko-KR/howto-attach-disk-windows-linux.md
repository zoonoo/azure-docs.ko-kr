


## <a name="attach-an-empty-disk"></a>빈 디스크 연결
Azure가 .vhd 파일을 자동으로 만들어 저장소 계정에 저장하므로, 빈 디스크를 연결하는 것이 데이터 디스크를 추가하는 간단한 방법입니다.

1. **가상 컴퓨터(클래식)**를 클릭한 다음 적절한 VM을 선택합니다.

2. 설정 메뉴에서 **디스크**를 클릭합니다.

   ![새로운 빈 디스크 연결](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. 명령 모음에서 **새 연결**을 클릭합니다.  
    **새 디스크 연결** 대화 상자가 나타납니다.

    ![새 디스크 연결](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    다음 정보를 입력합니다.
    - **파일 이름**에서 기본 이름을 그대로 사용하거나 .vhd 파일에 대한 다른 이름을 입력합니다. .vhd 파일용으로 다른 이름을 입력하는 경우에도 데이터 디스크는 자동으로 생성된 이름을 사용합니다.
    - 데이터 디스크의 **유형**을 선택합니다. 모든 가상 컴퓨터에서 표준 디스크를 지원합니다. 많은 가상 컴퓨터에서 프리미엄 디스크도 지원합니다.
    - 데이터 디스크의 **크기(GB)**를 선택합니다.
    - **호스트 캐싱**에 대해 없음 또는 읽기 전용을 선택합니다.
    - [확인]을 클릭하여 마칩니다.

4. 데이터 디스크가 생성되어 연결된 후에는 VM의 디스크 섹션에 나열됩니다.

   ![새 빈 데이터 디스크가 성공적으로 연결됨](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> 데이터 디스크를 추가한 후 VM에 로그온하여 디스크를 사용할 수 있도록 초기화해야 합니다.

## <a name="how-to-attach-an-existing-disk"></a>방법: 기존 디스크 연결
기존 디스크를 연결하려면 저장소 계정에 사용 가능한 .vhd가 있어야 합니다. [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet을 사용하여 .vhd 파일을 저장소 계정에 업로드합니다. .vhd 파일을 만들어 업로드한 후에는 VM에 연결할 수 있습니다.

1. **가상 컴퓨터(클래식)**를 클릭하고 해당 가상 컴퓨터를 선택합니다.

2. 설정 메뉴에서 **디스크**를 클릭합니다.

3. 명령 모음에서 **기존 디스크 연결**을 클릭합니다.

    ![데이터 디스크 연결](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. **위치**를 클릭합니다. 사용 가능한 저장소 계정이 표시됩니다. 다음으로 목록에서 해당 저장소 계정을 선택합니다.

    ![디스크 저장소 계정 제공](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. **저장소 계정**은 디스크 드라이브(vhds)가 있는 하나 이상의 컨테이너를 보유합니다. 목록에서 해당 컨테이너를 선택합니다.

    ![virtual-machines-windows의 컨테이너 제공](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. **vhds** 패널에는 컨테이너에 있는 디스크 드라이브가 나열됩니다. 디스크 중 하나를 클릭한 후 [선택]을 클릭합니다.

    ![virtual-machines-windows의 디스크 이미지 제공](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. 저장소 계정, 컨테이너 및 가상 컴퓨터에 추가할 특정 하드 디스크(vhd)가 포함된 위치와 함께 **기존 디스크 연결** 패널이 다시 표시됩니다.

  **호스트 캐싱**을 없음 또는 읽기 전용으로 설정한 후 [확인]을 클릭합니다.

    ![데이터 디스크 연결됨](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
