<properties
	pageTitle="Azure 미리 보기 포털에서 Windows를 실행하는 가상 컴퓨터 만들기"
	description="Azure 미리 보기 포털의 Azure Marketplace를 사용하여 Windows를 실행하는 Azure VM(가상 컴퓨터)을 만드는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/03/2015"
	ms.author="kathydav"/>

# Azure 미리 보기 포털에서 Windows를 실행하는 가상 컴퓨터 만들기#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

이 자습서에서는 Azure Preview 포털에서 Azure VM(가상 컴퓨터)을 간편하게 만드는 방법을 보여줍니다. 한 예로, Windows Server 이미지를 사용할 것이지만, 해당 이미지는 Azure가 제공하는 여러 이미지 중 하나일 뿐입니다. 참고: 이미지 선택은 구독에 따라 달라집니다. 예를 들어 데스크톱 이미지는 MSDN 구독자가 사용할 수 있습니다.

[사용자 고유의 이미지](virtual-machines-create-upload-vhd-windows-server.md)를 사용하여 VM을 만들 수도 있습니다. 이 방법 및 다른 방법에 대한 자세한 내용은, [Windows 가상 머신을 만드는 다양한 방법](virtual-machines-windows-choices-create-vm.md)을 참조하십시오.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## 가상 컴퓨터 만들기

한 예로, Windpows Server 2012 R2 Datacenter를 사용하여, 단 몇 분간 사용해 볼 수 있도록 VM을 만들 수 있습니다. 대부분의 구성에서 Azure의 기본 설정을 사용할 수 있습니다.

1. [미리 보기 포털](https://portal.azure.com)에 로그인합니다.

2. 허브 메뉴에서 **새로 만들기**를 클릭합니다.

3. **새로운** 블레이드에서, **계산** **>** **Windows Server 2012 R2 Datacenter**를 클릭합니다.

	![Marketplace에서 VM 이미지를 선택합니다.](./media/virtual-machines-windows-tutorial/marketplace_portal.png)

4. **VM 만들기** 블레이드에서 VM에 대해 원하는 **호스트 이름**, 관리 **사용자 이름** 및 강력한 **암호**를 입력합니다. **사용자 이름**은 서버를 관리하는 데 사용할 관리 계정을 나타냅니다. 자신이 기억할 수 있지만 다른 사람이 추측하기 어려운 암호를 만듭니다. **가상 컴퓨터에 로그온하는 데 이 사용자 이름과 암호가 필요합니다**. 암호를 잊은 경우, [이러한 지침](virtual-machines-windows-reset-password.md)을 사용하여 다시 설정할 수 있습니다.

	![호스트 이름 및 로그온 자격 증명 구성](./media/virtual-machines-windows-tutorial/create_vm_name_pwd_portal.png)

5. **가격 계층** 및 **선택적 구성**과 같은 기본 설정을 검토합니다. 이러한 선택 항목은 VM의 크기 및 도메인 구성원 자격과 같은 네트워킹 옵션에 영향을 줍니다. 예를 들어 가상 컴퓨터에서 프리미엄 저장소를 체험하려면 이 기능을 지원하는 지역 및 크기를 선택해야 합니다. 첫번째 가상 컴퓨터에 대해, 기본값이 일반적으로 좋습니다.

	>[AZURE.NOTE]프리미엄 저장소는 특정 지역에서 DS 시리즈에 대해 사용할 수 있습니다. 프리미엄 저장소는 데이터베이스와 같은 데이터 집약적인 작업에 대해 최상의 저장소 옵션입니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](storage-premium-storage-preview-portal.md)를 참조하세요.

6. 설정 검토 또는 업데이트가 완료되면 **만들기**를 클릭합니다.

7. Azure가 VM을 만드는 동안 허브 메뉴의 **알림**에서 진행 상태를 추적할 수 있습니다. **VM** 블레이드 만들기에서, **시작 보드**에 고정을 해제하지 않으면, Azure가 VM을 만든 후에, 시작 보드에서 해당 VM을 보게 됩니다.

## 가상 컴퓨터에 로그온

VM을 만든 후에, 해당 VM에 로그인을 하여, 자체 설정 및 실행할 응용 프로그램을 관리할 수 있습니다.

>[AZURE.NOTE]자세한 요구 사항 및 문제 해결 팁은 [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](https://msdn.microsoft.com/library/azure/dn535788.aspx)을 참조하십시오.

1. 아직 로그인을 하지 않은 경우 [미리보기 포털](https://portal.azure.com)에 로그인합니다.

2. 시작 보드에서 VM을 클릭합니다. 해당 VM을 찾아야 할 경우 **찾아보기** > **가상 컴퓨터**를 차례로 클릭합니다. 그런 다음 목록에서 VM을 선택합니다.

3. VM 블레이드에서 **연결**을 클릭합니다.

	![가상 컴퓨터에 로그온](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. **열기**를 클릭하여 가상 컴퓨터에 대해 자동으로 만들어진 원격 데스크톱 프로토콜 파일을 사용합니다.

5. **연결**을 클릭합니다.

6. 가상 컴퓨터를 만들 때에 지정한 사용자 이름 및 암호를 입력하고 **OK**를 클릭합니다.

7. **예**를 클릭하여 가상 컴퓨터의 ID를 확인합니다.

	이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

## 다음 단계

Azure에서 Windows 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

[가상 컴퓨터를 가상 네트워크 또는 클라우드 서비스와 연결하기](cloud-services-connect-virtual-machine.md)

[가상 컴퓨터에 데이터 디스크 연결](storage-windows-attach-disk.md)

[가상 컴퓨터의 가용성 관리](../manage-availability-virtual-machines.md)

[Azure VM 구성 설정 정보](http://msdn.microsoft.com/library/azure/dn763935.aspx)
 

<!---HONumber=July15_HO2-->