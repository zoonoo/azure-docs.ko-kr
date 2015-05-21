<properties title="Create a Virtual Machine Running Windows Server" pageTitle="Windows Server를 실행하는 가상 컴퓨터를 만드는 방법" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Windows Server를 실행하는 가상 컴퓨터 만들기 #

이 자습서에서는 Microsoft Azure 관리 포털의 이미지 갤러리를 사용하여 Windows Server를 실행하는 Azure 가상 컴퓨터를 쉽게 만드는 방법을 보여 줍니다. 이미지 갤러리는 Windows 운영 체제, Linux 기반 운영 체제, 응용 프로그램 이미지 등 다양한 이미지를 제공합니다. 

> [WACOM.NOTE]이전에 Azure VM을 사용한 경험이 없어도 이 자습서를 완료할 수 있습니다. 그러나 Azure 계정은 필요합니다. 몇 분 만에 무료 체험 계정을 만들 수 있습니다. 자세한 내용은 [Azure 계정 만들기](http://www.windowsazure.com/ko-kr/develop/php/tutorials/create-a-windows-azure-account/)(영문)를 참조하세요. 

이 자습서에서는 다음을 보여 줍니다.

- [가상 컴퓨터를 만드는 방법](#createvirtualmachine)
- [가상 컴퓨터를 만든 후 로그온하는 방법](#logon)
- [새 가상 컴퓨터에 데이터 디스크를 연결하는 방법](#attachdisk)

더 자세히 알고 싶으면 [가상 컴퓨터](http://go.microsoft.com/fwlink/p/?LinkID=271224)를 참조하세요.


##<a id="createvirtualmachine"> </a>가상 컴퓨터를 만드는 방법##

이 섹션에서는 관리 포털의 **갤러리에서** 방법을 사용하여 가상 컴퓨터를 만드는 방법을 보여 줍니다. 이 옵션은 **빠른 생성** 옵션보다 많은 구성 선택 항목을 제공합니다. 예를 들어 가상 컴퓨터를 가상 네트워크에 가입하려는 경우 **갤러리에서** 옵션을 사용해야 합니다.

[WACOM.INCLUDE [가상 컴퓨터 만들기 WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>가상 컴퓨터를 만든 후 로그온하는 방법 ##

이 섹션에서는 가상 컴퓨터에서 실행되는 설정 및 응용 프로그램을 관리할 수 있도록 가상 컴퓨터에 로그인하는 방법을 보여 줍니다.

1. Azure [관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. **Virtual Machines**을 클릭하고 **MyTestVM** 가상 컴퓨터를 선택합니다.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. 명령 모음에서 **Connect**를 클릭합니다.

	![Connect to MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. **Open**를 클릭하여 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.

	![Open the rdp file](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. **Connect**를 클릭합니다.

	![Continue with connecting](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. 암호 상자에 가상 컴퓨터를 만들 때 지정한 사용자 이름과 암호를 입력하고 **OK**를 클릭합니다.

7. **Yes**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.

	![Verify the identity of the machine](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	이제 사무실에 있는 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

## <a id="attachdisk"> </a>새 가상 컴퓨터에 데이터 디스크를 연결하는 방법 ##

이 섹션에서는 가상 컴퓨터에 빈 데이터 디스크를 연결하는 방법을 보여 줍니다. 빈 디스크 연결 및 기존 디스크를 연결하는 방법에 대한 자세한 내용은 [데이터 디스크 연결 자습서](http://www.windowsazure.com/ko-kr/documentation/articles/storage-windows-attach-disk/)를 참조하세요.

1. Azure [관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. **Virtual Machines**을 클릭하고 **MyTestVM** 가상 컴퓨터를 선택합니다.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. 먼저 빠른 시작 페이지로 이동될 수 있습니다. 이동하면 맨 위에서 **Dashboard**를 선택합니다.

	![Select Dashboard](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. 명령 모음에서 **Attach**를 클릭한 다음, 팝업이 표시되면 **Attach Empty Disk**를 클릭합니다.

	![Select Attach from the command bar](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)	

5. **Virtual Machine Name**, **Storage Location**, **File Name** 및 **Host Cache Preference**는 이미 정의되어 있습니다. 원하는 디스크 크기만 입력하면 됩니다. **Size** 필드에 **5**를 입력합니다. 확인 표시를 클릭하여 가상 컴퓨터에 빈 디스크를 연결합니다.

	![Specify the size of the empty disk](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)	
	
	>[WACOM.NOTE]모든 디스크는 Microsoft Azure 저장소의 VHD 파일에서 만들어집니다. **File Name**에서 저장소에 추가되는 VHD 파일의 이름은 지정할 수 있지만 디스크 이름은 Azure에서 자동으로 생성합니다.

6. 대시보드로 돌아와 빈 데이터 디스크가 가상 컴퓨터에 연결되어 있는지 확인합니다. 이 디스크는 **Disks** 목록에서 OS 디스크와 함께 두 번째 디스크로 나열됩니다.

	![Attach empty disk](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	데이터 디스크를 가상 컴퓨터에 연결한 후 디스크는 오프라인 상태이며 초기화되지 않았습니다. 가상 컴퓨터에 로그온한 다음 디스크를 초기화해야 데이터를 저장하는 데 사용할 수 있습니다.

7. 이전 섹션인 [가상 컴퓨터를 만든 후 로그온하는 방법] (#logon)의 단계에 따라 가상 컴퓨터에 연결합니다.

8. 가상 컴퓨터에 로그온한 후 **Server Manager**를 엽니다. 왼쪽 창에서 **파일 및 저장소 서비스**를 선택합니다.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. 확장된 메뉴에서 **디스크**를 선택합니다.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)	
	
10. **디스크** 섹션의 목록에는 디스크 0, 디스크 1, 디스크 2의 3개 디스크가 있습니다. 디스크 0은 OS 디스크이며, 디스크 1은 임시 리소스 디스크(데이터 저장소에 사용되지 않음), 디스크 2는 가상 컴퓨터에 연결한 데이터 디스크입니다. 데이터 디스크의 용량은 앞서 지정한 대로 5GB입니다. 디스크 2를 마우스 오른쪽 단추로 클릭한 다음 **초기화**를 선택합니다.

	![Start initialization](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. **예**를 클릭하여 초기화 프로세스를 시작합니다.

	![Continue initialization](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. 디스크 2를 마우스 오른쪽 단추로 클릭하고** 새 볼륨**을 선택합니다. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. 제공된 기본값을 사용하여 마법사를 완료합니다. 마법사를 완료하면 새 볼륨이 **볼륨** 섹션에 나열됩니다. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	이제 디스크가 온라인 상태이며 새 드라이브 문자로 사용할 수 있습니다. 
	
##다음 단계 

Azure에서 Windows 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

[클라우드 서비스에서 가상 컴퓨터를 연결하는 방법](http://www.windowsazure.com/ko-kr/documentation/articles/cloud-services-connect-virtual-machine/)

[Windows Server 운영 체제를 포함하는 가상 하드 디스크를 만들고 업로드하는 방법](http://www.windowsazure.com/ko-kr/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[가상 컴퓨터에 데이터 디스크 연결](http://www.windowsazure.com/ko-kr/documentation/articles/storage-windows-attach-disk/)

[가상 컴퓨터의 가용성 관리](http://www.windowsazure.com/ko-kr/documentation/articles/manage-availability-virtual-machines/)

[Azure의 가상 컴퓨터 정보]: #virtualmachine
[가상 컴퓨터를 만드는 방법]: #custommachine
[가상 컴퓨터를 만든 후 로그온하는 방법]: #logon
[새 가상 컴퓨터에 데이터 디스크를 연결하는 방법]: #attachdisk
[가상 컴퓨터와의 통신을 설정하는 방법]: #endpoints


