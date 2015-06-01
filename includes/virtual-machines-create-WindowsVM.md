1. Azure [클래식 포털](http://manage.windowsazure.com)에 로그인합니다. 아직 구독이 없으면 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/) 서비스를 확인하세요.

2. 창 맨 아래의 명령 모음에서 **새로 만들기**를 클릭합니다.

3. **계산**에서 **가상 컴퓨터**를 클릭한 후 **갤러리에서**를 클릭합니다.

	![명령 모음의 갤러리에서로 이동](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. 첫 번째 화면에서는 이미지 갤러리의 목록 중 하나에 포함된 가상 컴퓨터의 **이미지를 선택**할 수 있습니다. 사용 가능한 이미지는 사용 중인 구독에 따라 달라집니다. 화살표를 클릭하여 계속합니다.

	![이미지 선택](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. 두 번째 화면에서는 컴퓨터 이름, 크기 및 관리자 사용자 이름과 암호를 선택할 수 있습니다. Azure 가상 컴퓨터를 시험적으로 사용해 보려면 아래 이미지에 표시된 대로 필드를 채웁니다. 그렇지 않은 경우 앱 또는 작업을 실행하는 데 필요한 계층과 크기를 선택합니다. 다음은 이러한 정보를 채우는 데 도움이 되는 몇 가지 세부 정보입니다.
	
	- **새 사용자 이름**은 서버를 관리하는 데 사용하는 관리 계정을 나타냅니다. 이 계정의 고유한 암호를 만들고 기억해 둡니다. **가상 컴퓨터에 로그온하는 데 이 사용자 이름과 암호가 필요합니다**. 

	- 가상 컴퓨터의 크기는 사용 비용과 구성 옵션(예: 연결할 수 있는 데이터 디스크 수)에 영향을 미칩니다. 자세한 내용은 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://go.microsoft.com/fwlink/p/?LinkId=466520)(영문)를 참조하세요.

	![가상 컴퓨터의 속성 구성](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. 세 번째 화면에서는 네트워킹, 저장소 및 가용성에 대한 리소스를 구성할 수 있습니다. 다음은 이러한 정보를 채우는 데 도움이 되는 몇 가지 세부 정보입니다. 
	

	- The **Cloud Service DNS Name** is the global DNS name that becomes part of the URI that's used to contact the virtual machine. You'll need to come up with your own cloud service name because it must be unique in Azure. Cloud services are important for scenarios using [multiple virtual machines](../articles/cloud-services-connect-virtual-machine.md).
 
	- For **Region/Affinity Group/Virtual Network**, use a region that's appropriate to your location. You can also choose to specify a virtual network instead.
 
	>[AZURE.NOTE] If you want a virtual machine to use a virtual network, you **must** specify the virtual network when you create the virtual machine. You can't join the virtual machine to a virtual network after you create the VM. For more information, see [Azure Virtual Network Overview](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- For details about configuring endpoints, see [How to Set Up Endpoints to a Virtual Machine](../articles/virtual-machines-set-up-endpoints-classic-portal.md).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. 네 번째 구성 화면에서는 VM 에이전트와 사용 가능한 확장 중 일부를 구성할 수 있습니다. 확인 표시를 클릭하여 가상 컴퓨터를 만듭니다.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] The VM agent provides the environment for you to install extensions that can help you interact with or manage the virtual machine. For details, see [Using Extensions](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. 가상 컴퓨터가 만들어지면 클래식 포털의 **가상 컴퓨터** 아래에 새 가상 컴퓨터가 나열됩니다. 해당 클라우드 서비스 및 저장소 계정도 만들어진 후 해당 섹션에 나열됩니다. 가상 컴퓨터와 클라우드 서비스가 둘 다 자동으로 시작되고 해당 상태가 **실행 중**으로 나열됩니다. 

	![가상 컴퓨터의 VM 에이전트 및 끝점 구성](./media/virtual-machines-create-WindowsVM/vmcreated.png) <!--HONumber=52-->
