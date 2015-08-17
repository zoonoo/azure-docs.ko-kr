<properties
	pageTitle="Azure Preview 포털에서 Windows를 실행하는 가상 컴퓨터 만들기 | Microsoft Azure"
	description="Azure Preview 포털의 Azure 마켓플레이스를 사용하여 Windows를 실행하는 Azure VM(가상 컴퓨터) 리소스를 만드는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="kathydav"/>

# Azure Preview 포털에서 Windows를 실행하는 가상 컴퓨터 만들기#

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource manager](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service management](virtual-machines-ps-create-preconfigure-windows-vms.md)

이 자습서에서는 Preview 포털에서 Azure VM(가상 컴퓨터)을 몇 분 내에 간편하게 만드는 방법을 보여 줍니다. 여기에서는 Windows Server 2012 R2 Datacenter 이미지를 예로 들어 Azure 리소스 관리자에서 VM을 만들지만 이는 Azure에서 제공하는 여러 이미지 중 하나일 뿐입니다. 참고: 이미지 선택은 구독에 따라 달라집니다. 예를 들어 데스크톱 이미지는 MSDN 구독자가 사용할 수 있습니다.

사용자 고유의 이미지, 리소스 관리자 템플릿 또는 자동화 도구를 사용하여 VM을 만들 수도 있습니다. 여러 방법에 대한 자세한 내용은 [Windows 가상 컴퓨터를 만드는 다양한 방법](virtual-machines-windows-choices-create-vm.md)을 참조하세요.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 이미지 선택

Preview 포털의 Azure 마켓플레이스로 이동하여 원하는 Windows Server VM 이미지를 찾습니다.

1. [Preview 포털](https://portal.azure.com)에 로그인합니다.

2. 허브 메뉴에서 **새로 만들기** > **계산** > **Windows Server 2012 R2 Datacenter**를 클릭합니다.

	![마켓플레이스](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]다른 이미지를 찾으려면 **마켓플레이스**를 클릭하고 사용 가능한 항목을 검색하거나 필터링합니다.

3. **Windows Server 2012 R2 Datacenter** 페이지에서 **리소스 관리자 스택 사용**을 선택하여 Azure 리소스 관리자에서 VM을 만듭니다. 대부분의 새 작업에 대해 리소스 관리자 스택을 사용하는 것이 좋습니다. 고려 사항은 [Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)를 참조하세요. 그런 다음에 **만들기**를 클릭합니다.

	![마켓플레이스에서 검색](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## 가상 컴퓨터 만들기

이미지를 선택한 후에는 대부분의 구성에서 Azure 기본 설정을 사용할 수 있고 신속하게 VM을 만들 수 있습니다.

1. **가상 컴퓨터 만들기** 블레이드에서 **기본 사항**을 클릭합니다. VM에 대한 원하는 **이름**을 입력하고 **관리 사용자 이름** 및 강력한 **암호**를 입력합니다. 구독이 둘 이상인 경우 새 VM에 대해 하나를 지정하고 새로운 또는 기존 **리소스 그룹** 및 Azure 데이터 센터 **위치**를 지정합니다.

	![VM 기본 사항 구성](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE]**사용자 이름**은 서버를 관리하는 데 사용할 관리 계정을 나타냅니다. 자신이 기억할 수 있지만 다른 사람이 추측하기 어려운 암호를 만듭니다. **가상 컴퓨터에 로그온하는 데 이 사용자 이름과 암호가 필요합니다**.

2. **크기**를 클릭하고 요구에 적합한 VM 크기를 선택합니다. 각각의 크기는 계산 코어, 메모리 및 기타 기능(예: 프리미엄 저장소에 대한 지원)의 수를 지정하며 가격에 영향을 미칩니다. Azure는 선택하는 이미지에 따라 특정 크기를 자동으로 권장합니다.

	![VM 크기 구성](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]프리미엄 저장소는 특정 지역에서 DS 시리즈에 대해 사용할 수 있습니다. 프리미엄 저장소는 데이터베이스와 같은 데이터 집약적인 작업에 대해 최상의 저장소 옵션입니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](storage-premium-storage-preview-portal.md)를 참조하세요.

3. **설정**을 클릭하여 새 VM에 대한 저장소 및 네트워킹 설정을 봅니다. 첫 번째 VM에 대해 일반적으로 기본 설정을 적용할 수 있습니다. 지원하는 VM 크기를 선택한 경우 **디스크 유형** 아래에서 **프리미엄(SSD)**을 선택해 볼 수 있습니다.

	![VM 설정 구성](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. **요약**을 클릭하여 구성 선택 사항을 검토합니다. 설정 검토 또는 업데이트가 완료되면 **만들기**를 클릭합니다.

	![VM 설정 구성](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Azure에서 VM을 만드는 동안 허브 메뉴의 **알림**에서 진행 상황을 확인할 수 있습니다. Azure에서 VM을 만든 후에, **가상 컴퓨터 만들기** 블레이드에서 **시작 보드에 고정**을 해제하지 않으면, 시작 보드에서 해당 VM을 볼 수 있습니다.

## 가상 컴퓨터에 로그온

VM을 만든 후에, 해당 VM에 로그인을 하여, 자체 설정 및 실행할 응용 프로그램을 관리할 수 있습니다.

>[AZURE.NOTE]자세한 요구 사항 및 문제 해결 팁은 [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](https://msdn.microsoft.com/library/azure/dn535788.aspx)을 참조하십시오.

1. 아직 로그인하지 않은 경우 [Preview 포털](https://portal.azure.com)에 로그인합니다.

2. 시작 보드에서 VM을 클릭합니다. 해당 VM을 찾아야 하는 경우 **모두 찾아보기** > **최근** 또는 **모두 찾아보기** > **가상 컴퓨터**를 클릭합니다. 그런 다음 목록에서 VM을 선택합니다.

3. VM 블레이드에서 **연결**을 클릭합니다.

	![가상 컴퓨터에 로그온](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. **열기**를 클릭하여 Windows Server 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.

5. **Connect**를 클릭합니다.

6. 가상 컴퓨터를 만들 때에 설정한 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다.

7. **예**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.

	이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

## 다음 단계

* Azure PowerShell 및 Azure CLI를 사용하여 [VM 이미지를 찾아서 선택](resource-groups-vm-searching.md)합니다.
* [Azure 리소스 관리자](virtual-machines-how-to-automate-azure-resource-manager.md) 및 [Azure 리소스 관리자 템플릿](http://azure.microsoft.com/documentation/templates/)을 사용하여 VM 및 작업 배포 및 관리를 자동화합니다.

<!---HONumber=August15_HO6-->