<properties
	pageTitle="가상 컴퓨터에 대한 이미지 정보 | Microsoft Azure"
	description="Azure에서 가상 컴퓨터와 함께 이미지를 사용하는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/13/2015"
	ms.author="cynthn"/>

# 가상 컴퓨터에 대한 이미지 정보

이미지는 Azure에서 운영 체제에 새 가상 컴퓨터를 제공하는 데에 사용됩니다. 이미지에는 데이터 디스크가 하나 이상 있을 수 있습니다. 이미지는 여러 원본에서 사용할 수 있습니다.

-	Azure에서는 [마켓플레이스](http://azure.microsoft.com/gallery/virtual-machines/)에 있는 이미지를 제공합니다. 최신 버전의 Windows Server 및 Linux 운영 체제의 배포판을 찾을 수 있습니다. 일부 이미지에는 SQL Server와 같은 응용 프로그램이 포함되어 있습니다. MSDN 혜택 및 MSDN 종량제 구독자는 추가 이미지에 액세스할 수 있습니다.
-	오픈 소스 커뮤니티에서는 [VM 디포](http://vmdepot.msopentech.com/List/Index)를 통해 이미지를 제공합니다.
-	Azure에서 기존 Azure 가상 컴퓨터를 캡처하여 이미지로 사용하거나 이미지를 업로드하여 직접 이미지를 저장하고 사용할 수도 있습니다.

## VM 이미지 및 OS 이미지 정보

Azure에서 *VM 이미지* 및 *OS 이미지*의 두 가지 이미지 형식을 사용할 수 있습니다. VM 이미지에는 운영 체제 및 이미지를 만들 때 가상 컴퓨터에 연결된 모든 디스크가 포함됩니다. 이미지의 최신 형식입니다. VM 이미지가 도입되기 전에는 Azure에서 이미지에 일반화된 운영 체제만 포함할 수 있었고 추가 디스크가 없었습니다. 일반화된 운영 체제만 포함된 VM 이미지는 기본적으로 이미지의 원래 형식인 OS 이미지와 같습니다.

Azure의 가상 컴퓨터나 다른 곳에서 실행 중이지만 복사하여 업로드한 가상 컴퓨터를 기반으로 직접 이미지를 만들 수도 있습니다. 이미지를 사용하여 둘 이상의 가상 컴퓨터를 만들려는 경우에는 일반화를 통해 이미지로 사용할 준비를 해야 합니다. Windows Server 이미지를 만들려면 .vhd 파일을 업로드 하기 전에 서버에서 Sysprep 명령을 실행합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://go.microsoft.com/fwlink/p/?LinkId=392030)를 참조하세요. 소프트웨어 배포에 따라 Linux 이미지를 만들려면 Azure Linux 에이전트를 실행해야 함은 물론 배포에 관련된 명령 집합을 실행 해야 합니다.

## 이미지 작업

Azure 구독에 사용할 수 있는 이미지를 관리 하려면 Mac, Linux 및 Windows 또는 Azure PowerShell 모듈에 대한 Azure 명령줄 인터페이스(CLI)를 사용할 수 있습니다. 또한 일부 이미지 작업에 Azure 포털을 사용할 수 있지만 명령줄에는 추가 옵션이 있습니다.

이러한 도구를 사용하여 리소스 관리자를 배포하는 방법에 대한 자세한 내용은 [PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](resource-groups-vm-searching.md)을 참조하세요.

기존 배포에서의 도구 사용 예:

- CLI는 [Azure 서비스 관리에 Mac, Linux 및 Windows의 Azure CLI 사용](virtual-machines-command-line-tools.md)의 "Azure 가상 컴퓨터 이미지를 관리하는 명령"을 참조하세요.
- Azure PowerShell에 대한 내용은 다음 명령 목록을 참조하세요. VM을 만들 이미지를 찾는 경우의 예는 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md)의 "3단계: ImageFamily결정"을 참조하세요.

-	**모든 이미지 가져오기**:`Get-AzureVMImage`현재 구독에서 사용할 수 있는 모든 이미지의 목록을 반환 합니다. Azure 또는 파트너가 제공하는 이미지화 사용자 이미지를 가져옵니다. 이 작업을 수행하면 목록이 커질 수 있습니다. 다음 예에서는 짧은 목록을 가져오는 방법을 보여 줍니다.
-	**이미지 제품군 가져오기**:`Get-AzureVMImage | select ImageFamily` **ImageFamily** 속성 문자열을 표시하여 이미지 제품군 목록을 가져옵니다.
-	**특정 제품군의 모든 이미지 가져오기**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**VM 이미지 찾기**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` VM 이미지에만 적용되는 DataDiskConfiguration 속성을 필터링하여 작동합니다. 또한 이 예에서는 출력을 레이블 및 이미지 이름으로만 필터링합니다.
-	**일반화된 이미지 저장**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**특수화된 이미지 저장**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip]데이터 디스크 및 운영 체제 디스크를 포함 하는 VM 이미지를 만들려면 OSState 매개 변수가 필요합니다. 매개 변수를 사용하지 않으면 cmdlet에서 OS 이미지를 만듭니다. 매개 변수의 값은 운영 체제 디스크가 다시 사용하도록 준비되어 있는지 여부에 따라 이미지가 일반화되었는지 특수화되었는지를 나타냅니다.
-	**이미지 삭제**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## 추가 리소스

[Linux 가상 컴퓨터를 만드는 다양한 방법](virtual-machines-linux-choices-create-vm.md)

[Windows 가상 컴퓨터를 만드는 다양한 방법](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=Oct15_HO1-->