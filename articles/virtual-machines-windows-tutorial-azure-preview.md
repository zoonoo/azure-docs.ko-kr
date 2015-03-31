<properties 
	pageTitle="Azure 미리 보기 포털에서 Windows를 실행하는 가상 컴퓨터 만들기" 
	description="Azure 미리 보기 포털의 Azure Marketplace를 사용하여 Windows를 실행하는 Azure VM(가상 컴퓨터)을 만드는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="danlep,kathydav,rasquill"/>

<!---Workflow can be confusing...have to select the correct size with **Browse all pricing tiers** then click Select on that pane and the **Recommended pricing tiers** pane to apply. But even after that, it didn't seem to pick the Premium storage account...showed up as 'standard GRS' when I inspected what type of storage account would be used for the VM. 
-->

<!-- Preview portal screenshots getting a little out of date. Please refresh at next update.
-->

# Azure 미리 보기 포털에서 가상 컴퓨터 만들기#

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Azure 포털</a><a href="/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Azure 미리 보기 포털</a></div>

이 자습서에서는 Azure 미리 보기 포털의 Azure Marketplace에서 Windows Server 이미지를 예로 사용하여 Windows를 실행하는 Azure VM(가상 컴퓨터)을 쉽게 만드는 방법을 보여 줍니다. 이 Marketplace는 Windows 운영 체제, Linux 기반 운영 체제, 응용 프로그램 이미지 등 다양한 이미지를 제공합니다. 

> [AZURE.NOTE] 이전에 Azure VM을 사용한 경험이 없어도 이 자습서를 완료할 수 있습니다. 그러나 Azure 계정이 필요합니다. 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 계정 만들기](http://www.windowsazure.com/develop/php/tutorials/create-a-windows-azure-account/)(영문)를 참조하세요. 

이 자습서에서는 다음을 보여 줍니다.

- [가상 컴퓨터를 만드는 방법](#createvirtualmachine)
- [가상 컴퓨터를 만든 후 로그온하는 방법](#logon)

더 자세히 알고 싶으면 [가상 컴퓨터](http://go.microsoft.com/fwlink/p/?LinkID=271224)를 참조하세요.


## <a id="createvirtualmachine"> </a>가상 컴퓨터를 만드는 방법##

이 섹션에서는 Windows Server 2012 R2 Datacenter를 예로 사용하여 미리 보기 포털을 통해 VM을 만드는 방법을 보여 줍니다. 대부분의 구성에서 Azure 기본 설정을 사용할 수 있고 단 몇 분 만에 VM을 만들 수 있습니다.

> [AZURE.NOTE] 사용 가능한 이미지는 구독에 따라 달라집니다. 이 자습서에서는 Windows Server 이미지를 사용하지만 MSDN 구독에서 데스크톱 이미지를 비롯한 추가 이미지를 제공할 수도 있습니다. 
 

1. [Azure 미리 보기 포털](https://portal.azure.com)에 로그인합니다. 아직 구독이 없으면 [무료 평가판](http://www.windowsazure.com/pricing/free-trial/) 서비스를 확인하세요.

2. 허브 메뉴에서 **새로 만들기**를 클릭합니다.

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. **새로 만들기** 블레이드에서 **모두**, **가상 컴퓨터**, **Windows Server 2012 R2 Datacenter**, **만들기**를 차례로 클릭합니다.

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. **VM 만들기** 블레이드에서 VM에 사용할 **호스트 이름**, 관리 **사용자 이름** 및 강력한 **암호**를 입력합니다.  

	>[AZURE.NOTE] **사용자 이름**은 서버를 관리하는 데 사용하는 관리 계정을 나타냅니다. 이 계정의 고유한 암호를 만들고 기억해 둡니다. **가상 컴퓨터에 로그온하려면 사용자 이름과 암호가 필요합니다**.
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. **가격 계층**, **선택적 구성** 및 **위치**와 같은 기본 설정을 검토합니다. 이러한 선택 항목은 VM의 크기 및 도메인 구성원 자격과 같은 네트워킹 옵션에 영향을 줍니다. 예를 들어 가상 컴퓨터에서 프리미엄 저장소를 체험하려면 이 기능을 지원하는 지역 및 크기를 선택해야 합니다. 

	>[AZURE.NOTE] 프리미엄 저장소는 미리 보기 상태이며 특정 지역의 DS 시리즈 가상 컴퓨터에서 사용할 수 있습니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](http://azure.microsoft.com/documentation/articles/storage-premium-storage-preview-portal/)를 참조하세요.

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. 설정 검토 또는 업데이트가 완료되면 **만들기**를 클릭합니다.	

7. Azure가 VM을 만드는 동안 허브 메뉴의 **알림**에서 진행 상태를 추적할 수 있습니다. Azure가 VM을 만든 후에는 시작 보드에 해당 VM이 표시됩니다.

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>가상 컴퓨터를 만든 후 로그온하는 방법 ##

이 섹션에서는 VM에서 실행되는 설정 및 응용 프로그램을 관리할 수 있도록 VM에 로그온하는 방법을 보여 줍니다.

>[AZURE.NOTE] 자세한 요구 사항 및 문제 해결 팁은 [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](http://go.microsoft.com/fwlink/p/?LinkId=398294)을 참조하세요.

1. 아직 로그인하지 않은 경우 [Azure 미리 보기 포털](https://portal.azure.com)에 로그인합니다.

2. 시작 보드에서 VM을 클릭합니다. 해당 VM을 찾아야 할 경우 **찾아보기**, **가상 컴퓨터**를 차례로 클릭합니다. 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. 그런 다음 목록에서 VM을 선택합니다. 

	![Select your VM name from the list](./media/virtual-machines-windows-tutorial-azure-preview/vm_select_preview_portal.png)

4. VM 블레이드 맨 위에 있는 **연결**을 클릭합니다.

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

5. **열기**를 클릭하여 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.
	
6. **연결**을 클릭하여 연결 프로세스를 진행합니다.

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

7. 가상 컴퓨터에서 관리자 계정의 사용자 이름 및 암호를 입력한 후 **확인**을 클릭합니다.
	
8. **예**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

## 다음 단계 

Azure에서 Windows 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

[클라우드 서비스에서 가상 컴퓨터를 연결하는 방법](http://www.windowsazure.com/documentation/articles/cloud-services-connect-virtual-machine/)

[Windows Server 운영 체제를 포함하는 가상 하드 디스크를 만들고 업로드하는 방법](http://www.windowsazure.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[가상 컴퓨터에 데이터 디스크 연결](http://www.windowsazure.com/documentation/articles/storage-windows-attach-disk/)

[가상 컴퓨터의 가용성 관리](http://www.windowsazure.com/documentation/articles/manage-availability-virtual-machines/)

[Azure VM 구성 설정 정보](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[가상 컴퓨터를 만드는 방법]: #custommachine
[가상 컴퓨터를 만든 후 로그온하는 방법]: #logon

<!--HONumber=47-->
