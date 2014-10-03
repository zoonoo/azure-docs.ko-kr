Azure 가상 컴퓨터의 디스크에 대한 자세한 내용은 [Azure의 가상 컴퓨터 디스크 정보][](영문)를 참조하세요.

## <span id="attachempty"></span></a>방법: 빈 디스크 연결

Azure가 .vhd 파일을 자동으로 만들어 저장소 계정에 저장하므로, 빈 디스크를 연결하는 것이 데이터 디스크를 추가하는 더욱 간단한 방법입니다.

1.  **가상 컴퓨터**를 클릭하고 해당 가상 컴퓨터를 선택합니다.

2.  명령 모음에서 **Attach**를 클릭한 후 **Attach Empty Disk**를 선택합니다.

    ![빈 디스크 연결][]

    **Attach Empty Disk** 대화 상자가 나타납니다.

    ![새로운 빈 디스크 연결][]

3.  **파일 이름**에서, 자동으로 생성된 이름을 그대로 사용하거나 설명적인 이름을 입력합니다. .vhd 파일에서 만든 데이터 디스크는 항상 자동으로 생성된 이름을 사용합니다.

4.  **Size**에 데이터 디스크의 크기를 입력합니다.

5.  확인 표시를 클릭하여 빈 데이터 디스크를 연결합니다.

    이제 가상 컴퓨터의 대시보드에 데이터 디스크가 나열됩니다.

    ![빈 데이터 디스크 연결됨][]

## <span id="attachexisting"></span></a>방법: 기존 디스크 연결

기존 디스크를 연결하려면 저장소 계정에 사용 가능한 .vhd가 있어야 합니다. [Add-AzureVhd][] cmdlet을 사용하여 .vhd 파일을 저장소 계정에 업로드합니다. .vhd 파일을 만들어 업로드한 후에는 가상 컴퓨터에 연결할 수 있습니다.

1.  **가상 컴퓨터**를 클릭하고 해당 가상 컴퓨터를 선택합니다.

2.  명령 모음에서 **Attach**를 클릭한 후 **Attach Disk**를 선택합니다.

    ![데이터 디스크 연결][]

    **Attach Disk** 대화 상자가 나타납니다.

    ![데이터 디스크 세부 정보 입력][]

3.  가상 컴퓨터에 연결할 데이터 디스크를 선택합니다.
4.  확인 표시를 클릭하여 가상 컴퓨터에 데이터 디스크를 연결합니다.

    이제 가상 컴퓨터의 대시보드에 데이터 디스크가 나열됩니다.

    ![데이터 디스크 연결됨][]

> [WACOM.NOTE]
> 데이터 디스크를 추가한 후 가상 컴퓨터가 디스크를 저장소에 사용할 수 있도록 가상 컴퓨터에 로그온한 다음 디스크를 초기화해야 합니다.

  [Azure의 가상 컴퓨터 디스크 정보]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [빈 디스크 연결]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [새로운 빈 디스크 연결]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [빈 데이터 디스크 연결됨]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [데이터 디스크 연결]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [데이터 디스크 세부 정보 입력]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [데이터 디스크 연결됨]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
