# Windows Server를 실행하는 가상 컴퓨터 만들기

Azure 관리 포털에서 이미지 갤러리를 사용할 때 Windows Server 운영 체제를 실행하는 가상 컴퓨터를 쉽게 만들 수
있습니다. 이 자습서에서는 클라우드에 Windows Server를 실행하는 가상 컴퓨터를 만든 후 액세스하고 사용자 지정하는
방법을 설명합니다. 이전에 Azure를 사용한 경험이 없어도 이 자습서를 사용할 수 있습니다.

다음 내용을 배웁니다.

* [Azure의 가상 컴퓨터 정보](#virtualmachine)
* [가상 컴퓨터를 만드는 방법](#custommachine)
* [가상 컴퓨터를 만든 후 로그온하는 방법](#logon)
* [새 가상 컴퓨터에 데이터 디스크를 연결하는 방법](#attachdisk)

**참고:** 이 자습서에서는 가상 네트워크에 연결되지 않은 가상 컴퓨터를 만듭니다. 가상 컴퓨터에서 가상 네트워크를 사용하려는
경우 가상 컴퓨터를 만들 때 가상 네트워크를 지정해야 합니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크
개요][1]를 참조하십시오.
## <a  id="virtualmachine"> </a>Azure의 가상 컴퓨터 정보

Azure의 가상 컴퓨터는 제어 및 관리할 수 있는 클라우드의 서버입니다. Azure에서 가상 컴퓨터를 만든 후 필요할 때마다
삭제하고 다시 만들 수 있으며 다른 서버와 마찬가지로 가상 컴퓨터에 액세스할 수 있습니다. 자세한 내용은 [가상 컴퓨터][2]를
참조하십시오.
## <a  id="custommachine"> </a>가상 컴퓨터를 만드는 방법

이 자습서에서는 관리 포털의 **From Gallery** 방법을 사용하여 사용자 지정 가상 컴퓨터를 만드는 방법을 보여 줍니다.
이 방법은 가상 컴퓨터를 만들 때 사용 가능한 구성 옵션을 **Quick Create** 방법보다 더 많이 제공합니다.

1.  [Azure 관리 포털][3]을 사용할 수 있는 구독에 로그인합니다. 구독이 없는 경우 [무료 체험][4]을 등록할 수
    있습니다.

2.  명령 모음에서 **New**를 클릭합니다.

3.  **Virtual Machine**을 클릭한 후 **From Gallery**를 클릭합니다.

4.  **Choose an Image**에서 **Windows Server 2012 R2 Datacenter**를 선택합니다.
    사용 가능한 이미지는 사용 중인 구독에 따라 달라집니다. 화살표를 클릭하여 계속합니다.

5.  여러 버전의 이미지를 사용할 수 있는 경우 **Version Release Date**에서 사용할 버전을 선택합니다.

6.  **Virtual Machine Name**에서 가상 컴퓨터에 사용할 이름을 입력합니다. 이 가상 컴퓨터의 경우
    **MyTestVM1**을 입력합니다.

7.  **Size**에서 가상 컴퓨터의 크기를 선택합니다. 선택해야 하는 크기는 응용 프로그램을 실행하는 데 필요한 코어 수에
    따라 달라집니다. 이 가상 컴퓨터의 경우 사용 가능한 가장 작은 크기를 선택합니다.

8.  **New User Name**에서 서버 관리에 사용하려는 관리자 계정의 이름을 입력합니다. 이 가상 컴퓨터의 경우
    **MyTestVM1Admin**을 입력합니다.

9.  **New Password**에서 가상 컴퓨터의 관리자 계정에 대한 강력한 암호를 입력합니다. **Confirm
    Password**에 암호를 다시 입력합니다. 화살표를 클릭하여 계속합니다.

10. 클라우드 서비스에 가상 컴퓨터를 함께 배치하여 강력한 응용 프로그램을 제공할 수 있지만, 이 자습서에서는 단일 가상
    컴퓨터만 만듭니다. 이렇게 하려면 **Create a new cloud service**를 선택합니다.

11. **Cloud Service DNS Name**에 3자에서 24자 사이의 소문자와 숫자를 사용하는 이름을 입력합니다. 이
    이름은 클라우드 서비스를 통해 가상 컴퓨터에 연결하는 데 사용되는 URI의 일부가 됩니다. 이 가상 컴퓨터의 경우
    **MyService1**을 입력합니다.

12. **Region/Affinity Group/Virtual Network**에서 가상 컴퓨터를 찾을 위치를 선택합니다.

13. 가상 컴퓨터가 사용할 VHD를 저장하려면 기본 설정인 **Use an Automatically Generated
    Storage Account**를 적용합니다.

14. 이 자습서의 경우 **Availability Set**에서 기본 설정인 **None**을 사용합니다. 화살표를 클릭하여
    계속합니다.

15. **VM Agent**에서 VM 에이전트를 설치할지 여부를 결정합니다. 이 에이전트는 가상 컴퓨터를 조작하는 데 유용한
    확장을 설치하기 위한 환경을 제공합니다. 자세한 내용은 [확장 사용][5]을 참조하십시오.

16. **Endpoints**에서 원격 데스크톱 및 Windows PowerShell 원격을 통해서 등 가상 컴퓨터에 대한
    연결을 허용하기 위해 만들어지는 새 끝점을 검토합니다. 지금 끝점을 추가하거나 나중에 만들 수도 있습니다. 나중에 끝점을
    만드는 방법에 대한 지침은 [가상 컴퓨터와의 통신을 설정하는 방법][6](영문)을 참조하십시오.

17. 확인 표시를 클릭하여 가상 컴퓨터를 만듭니다.
    
    가상 컴퓨터와 클라우드 서비스가 만들어진 후 관리 포털의 **Virtual Machines** 아래에 새 가상 컴퓨터가
    나열되고 **Cloud Services** 아래에 클라우드 서비스가 나열됩니다. 가상 컴퓨터와 클라우드 서비스는 둘 다
    자동으로 시작됩니다.
## <a  id="logon"> </a>가상 컴퓨터를 만든 후 로그온하는 방법

만든 가상 컴퓨터에 로그온하여 가상 컴퓨터의 설정과 가상 컴퓨터에서 실행되는 응용 프로그램을 둘 다 관리할 수 있습니다.

1.  [Azure 관리 포털][3]에 로그인합니다.

2.  **Virtual Machines**를 클릭하고 **MyTestVM1** 가상 컴퓨터를 선택합니다.

3.  명령 모음에서 **Connect**를 클릭합니다.

4.  **Open**을 클릭하여 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.

5.  **Connect**를 클릭합니다.
    
    ![연결
    계속](./media/CreateVirtualMachineWindowsTutorial/connectpublisher.png)

6.  암호 상자에 가상 컴퓨터를 만들 때 지정한 사용자 이름과 암호를 입력하고 **OK**을 클릭합니다.

7.  **Yes**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.
    
    ![컴퓨터의 ID
    확인](./media/CreateVirtualMachineWindowsTutorial/connectverify.png)
    
    이제 사무실에 있는 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.
## <a  id="attachdisk"> </a>새 가상 컴퓨터에 데이터 디스크를 연결하는 방법

응용 프로그램에서 데이터를 저장해야 할 수도 있습니다. 설정하려면 가상 컴퓨터에 데이터 디스크를 연결합니다. 가장 쉬운 방법은
가상 컴퓨터에 빈 데이터 디스크를 연결하는 것입니다.

1.  [Azure 관리 포털][3]에 로그인합니다.

2.  **Virtual Machines**를 클릭하고 **MyTestVM1** 가상 컴퓨터를 선택합니다.

3.  명령 모음에서 **Attach**를 클릭한 후 **Attach Empty Disk**를 클릭합니다.
    
    **Attach Empty Disk** 대화 상자가 나타납니다.
    
    > [WACOM.NOTE] 대시보드 및 명령 모음 대신 Quick Start 페이지가 표시될 수 있습니다. 이 경우에는
    > 맨 위에서 **대시보드**를 클릭하십시오.

4.  **Virtual Machine Name**, **Storage Location**, **File Name** 및
    **Host Cache Preference**는 이미 정의되어 있습니다. 원하는 디스크 크기만 입력하면 됩니다.
    **Size** 필드에 **5**를 입력합니다.
    
    **참고:** 모든 디스크는 Azure 저장소의 VHD 파일에서 만들어집니다. 저장소에 추가되는 VHD 파일의 이름은
    지정할 수 있지만 디스크 이름은 Azure에서 자동으로 생성합니다.

5.  확인 표시를 클릭하여 가상 컴퓨터에 데이터 디스크를 연결합니다.

6.  가상 컴퓨터의 이름을 클릭합니다. 그러면 대시보드가 표시되므로 데이터 디스크가 가상 컴퓨터에 연결되었는지 확인할 수
    있습니다.
    
    이제 가상 컴퓨터에 두 개의 디스크가 있습니다. 연결한 디스크가 **Disks** 테이블에 나열됩니다.
    
    ![빈 디스크
    연결](./media/CreateVirtualMachineWindowsTutorial/attachemptysuccess.png)
    
    데이터 디스크를 가상 컴퓨터에 연결한 후 디스크는 오프라인 상태이며 초기화되지 않았습니다. 가상 컴퓨터에 로그온한 다음
    디스크를 초기화해야 데이터를 저장하는 데 사용할 수 있습니다.

7.  이전 섹션인 **가상 컴퓨터를 만든 후 로그온하는 방법**의 단계에 따라 가상 컴퓨터에 연결합니다.

8.  가상 컴퓨터에 로그온한 후 **Server Manager**를 엽니다. 왼쪽 창에서 **Storage**를 확장하고
    **Disk Management**를 클릭합니다.
    
    ![서버 관리자에서 디스크
    초기화](./media/CreateVirtualMachineWindowsTutorial/servermanager.png)

9.  **Disk 2**를 마우스 오른쪽 단추로 클릭한 후 **Initialize Disk**를 클릭합니다.
    
    ![초기화
    시작](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

10. **OK**를 클릭하여 초기화 프로세스를 시작합니다.

11. 디스크 2에 대한 공간 할당 영역을 마우스 오른쪽 단추로 클릭하고 **New Simple Volume**을 클릭한 다음
    기본값을 사용하여 마법사를 완료합니다.
    
    ![볼륨
    만들기](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)
    
    이제 디스크가 온라인 상태이며 새 드라이브 문자로 사용할 수 있습니다.
    
    ![초기화
    성공](./media/CreateVirtualMachineWindowsTutorial/initializesuccess.png)
## 다음 단계

Azure에서 Windows 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하십시오.

-[클라우드 서비스에서 가상 컴퓨터를 연결하는 방법][7]

-[가상 컴퓨터의 가용성 관리][8]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkID=271224
[3]: http://manage.windowsazure.com
[4]: http://go.microsoft.com/fwlink/p/?LinkID=23435
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/
[7]: http://www.windowsazure.com/en-us/documentation/articles/cloud-services-connect-virtual-machine/
[8]: http://www.windowsazure.com/en-us/documentation/articles/manage-availability-virtual-machines/
