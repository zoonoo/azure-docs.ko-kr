1. Azure [관리 포털](http://manage.windowsazure.com)에 로그인합니다. 아직 구독이 없으면 [무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/) 서비스를 확인하세요.

2. 창 맨 아래의 명령 모음에서 **새로 만들기**를 클릭합니다.

3. **계산**에서 **가상 컴퓨터**를 클릭한 후 **갤러리에서**를 클릭합니다.

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. 첫 번째 화면에서는 이미지 갤러리의 목록 중 하나에 포함된 가상 컴퓨터에 대해 **이미지 선택**을 할 수 있습니다. 사용 가능한 이미지는 사용 중인 구독에 따라 달라집니다. 화살표를 클릭하여 계속합니다.

	![Choose an image](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. 두 번째 화면에서는 컴퓨터 이름, 크기 및 관리자 사용자 이름과 암호를 선택할 수 있습니다. Azure 가상 컴퓨터를 시험적으로 사용해 보려면 아래 이미지에 표시된 대로 필드를 채웁니다. 그렇지 않은 경우 앱 또는 작업을 실행하는 데 필요한 계층과 크기를 선택합니다. 다음은 이러한 정보를 채우는 데 도움이 되는 몇 가지 세부 정보입니다. 

	- **새 사용자 이름**은 서버를 관리하는 데 사용하는 관리 계정을 나타냅니다. 이 계정의 고유한 암호를 만들고 기억해 둡니다. **가상 컴퓨터에 로그온하려면 사용자 이름과 암호가 필요합니다**. 

	- 가상 컴퓨터의 크기는 사용 비용과 구성 옵션(예: 연결할 수 있는 데이터 디스크 수)에 영향을 미칩니다. 자세한 내용은 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://go.microsoft.com/fwlink/p/?LinkId=466520)(영문)를 참조하세요.

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)

6. 세 번째 화면에서는 네트워킹, 저장소 및 가용성에 대한 리소스를 구성할 수 있습니다. 다음은 이러한 정보를 채우는 데 도움이 되는 몇 가지 세부 정보입니다. 

	- **클라우드 서비스 DNS 이름**은 가상 컴퓨터에 연결하는 데 사용되는 URI의 일부가 되는 전역 DNS 이름입니다. 이 이름은 Azure에서 고유해야 하므로 자체 클라우드 서비스 이름을 새로 만들어야 합니다. 클라우드 서비스는 [여러 가상 컴퓨터](http://www.windowsazure.com/ko-kr/documentation/articles/cloud-services-connect-virtual-machine/)를 사용하는 시나리오에서 중요합니다.
 
	- **지역/선호도 그룹/가상 네트워크**에는 사용자 위치에 해당하는 지역을 사용합니다. 대신 가상 네트워크를 지정하도록 선택할 수도 있습니다.
 
	>[AZURE.NOTE] 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 가상 컴퓨터를 만들 때 **반드시** 가상 네트워크를 지정해야 합니다. VM을 만든 후에는 가상 컴퓨터를 가상 네트워크에 가입할 수 없습니다. 자세한 내용은 [Azure 가상 네트워크 개요](http://go.microsoft.com/fwlink/p/?LinkID=294063)(영문)를 참조하세요.

	- 끝점을 구성하는 방법에 대한 지침은 [가상 컴퓨터로 끝점을 설정하는 방법](http://www.windowsazure.com/ko-kr/documentation/articles/virtual-machines-set-up-endpoints/)(영문)을 참조하세요.

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. 네 번째 구성 화면에서는 VM 에이전트와 사용 가능한 확장 중 일부를 구성할 수 있습니다. 확인 표시를 클릭하여 가상 컴퓨터를 만듭니다.

	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] VM 에이전트는 가상 컴퓨터를 조작 또는 관리하는 데 유용한 확장을 설치하기 위한 환경을 제공합니다. 자세한 내용은 [확장 사용](http://go.microsoft.com/FWLink/p/?LinkID=390493)을 참조하세요.  

8. 가상 컴퓨터가 만들어지면 관리 포털의 **가상 컴퓨터** 아래에는 새 가상 컴퓨터가 나열됩니다. 해당 클라우드 서비스 및 저장소 계정도 만들어진 후 해당 섹션에 나열됩니다. 가상 컴퓨터와 클라우드 서비스 둘 다 자동으로 시작되고, 관리 포털에는 해당 상태가 **실행 중**으로 표시됩니다. 

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png)


<!--HONumber=42-->
