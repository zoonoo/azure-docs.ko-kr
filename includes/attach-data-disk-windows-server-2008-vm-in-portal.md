데이터 디스크를 연결하려면 다음 단계를 따르세요.

1.  [Azure 관리 포털][]에서 **Virtual Machines**를 클릭하고 방금 만든 가상 컴퓨터(**testwinvm**)를 선택합니다.

2.  명령 모음에서 **Attach**를 클릭한 후 **Attach Empty Disk**를 클릭합니다.

    **Attach empty disk to virtual machine** 대화 상자가 나타납니다.

3.  **Virtual Machine Name**, **Storage Location** 및 **File Name**은 이미 정의되어 있습니다. 원하는 디스크 크기만 입력하면 됩니다. **Size** 필드에 **5**를 입력합니다.

    ![빈 디스크 연결][]

    **참고:** 모든 디스크는 Azure 저장소의 VHD 파일에서 만들어집니다. 저장소에 추가되는 VHD 파일의 이름은 지정할 수 있지만 디스크 이름은 Azure에서 자동으로 생성합니다.

4.  확인 표시를 클릭하여 가상 컴퓨터에 데이터 디스크를 연결합니다.

5.  가상 컴퓨터의 이름을 클릭하여 대시보드를 표시합니다. 이 대시보드를 사용하여 데이터 디스크가 가상 컴퓨터에 연결되었는지 확인할 수 있습니다.

    이제 가상 컴퓨터에 대한 디스크 수가 2개입니다. 연결한 디스크가 Disks 테이블에 나열됩니다.

    ![빈 디스크 연결][1]

    데이터 디스크를 가상 컴퓨터에 연결한 후 디스크는 오프라인 상태이며 초기화되지 않았습니다. 가상 컴퓨터에 로그온한 다음 디스크를 초기화해야 데이터를 저장하는 데 사용할 수 있습니다.

## 원격 데스크톱을 사용하여 가상 컴퓨터에 연결 및 전체 설치

1.  가상 컴퓨터가 프로비전된 후 관리 포털에서 **Virtual Machines**를 클릭한 후 새 가상 컴퓨터를 클릭합니다. 가상 컴퓨터에 대한 정보가 표시됩니다.

2.  페이지 맨 아래에 있는 **연결**을 클릭합니다. Windows 원격 데스크톱 프로그램(*%windir%\\system32\\mstsc.exe*)을 사용하여 .rpd 파일을 엽니다.

3.  **Windows Security** 대화 상자에서 **Administrator** 계정의 암호를 지정합니다. 가상 컴퓨터의 자격 증명을 확인하도록 요청될 수도 있습니다. 이 가상 컴퓨터에 처음 로그온하는 경우 데스크톱 설정, Windows 업데이트 및 Windows 초기 구성 작업 완료를 포함하여 여러 프로세스를 완료해야 할 수 있습니다. Windows 원격 데스크톱을 사용하여 가상 컴퓨터에 연결된 후 가상 컴퓨터는 다른 컴퓨터와 동일하게 작동합니다.

4.  가상 컴퓨터에 로그온한 후 **Server Manager**를 엽니다. 왼쪽 창에서 **Storage**를 확장하고 **Disk Management**를 클릭합니다.

    ![서버 관리자][]

5.  **Initalize Disk** 창이 나타납니다. **확인**을 클릭합니다.

    ![디스크 초기화][]

6.  디스크 2에 대한 공간 할당 영역을 마우스 오른쪽 단추로 클릭하고 **New Simple Volume**을 클릭한 다음 기본값을 사용하여 마법사를 완료합니다.

    ![새 단순 볼륨][]

    이제 디스크가 온라인 상태이며 새 드라이브 문자로 사용할 수 있습니다.

    ![초기화 성공][]

  [Azure 관리 포털]: http://manage.windowsazure.com
  [빈 디스크 연결]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM2.png
  [1]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM3.png
  [서버 관리자]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/servermanager.png
  [디스크 초기화]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializedisk0.png
  [새 단순 볼륨]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializediskvolume.png
  [초기화 성공]: ./media/attach-data-disk-windows-server-2008-vm-in-portal/initializesuccess.png
