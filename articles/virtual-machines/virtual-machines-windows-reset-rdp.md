<properties
	pageTitle="Windows VM에서 암호 또는 원격 데스크톱 구성 다시 설정 | Microsoft Azure"
	description="Azure 포털 또는 Azure PowerShell을 사용하여 Windows VM에서 계정 암호 또는 원격 데스크톱 서비스를 다시 설정하는 방법을 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# Windows VM에서 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Windows 가상 컴퓨터에 연결할 수 없는 경우 로컬 관리자 암호를 다시 설정할 수도 있고 원격 데스크톱 서비스 구성을 다시 설정할 수도 있습니다. 암호를 다시 설정하려면 Azure 포털이나 Azure PowerShell의 VM 액세스 확장을 사용할 수 있습니다. PowerShell을 사용하는 경우 회사 컴퓨터에 최신 PowerShell 모듈을 설치하고 Azure 구독에 로그인하도록 합니다. 자세한 단계는 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

> [AZURE.TIP] `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`을 사용하여 설치한 PowerShell의 버전을 확인할 수 있습니다.

## 리소스 관리자 배포 모델에서 Windows VM

### Azure 포털
**찾아보기** > **가상 컴퓨터** > *Windows 가상 컴퓨터* > **모든 설정** > **암호 다시 설정**을 클릭하여 Vm을 선택합니다. 암호 다시 설정 블레이드가 표시됩니다.

![암호 다시 설정 페이지](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

사용자 이름 및 새 암호를 입력한 후 **저장**을 클릭합니다. VM에 연결을 다시 시도하세요.

### VMAccess 확장 및 PowerShell

Azure PowerShell 1.0 이상을 설치하고 `Login-AzureRmAccount` cmdlet을 사용하여 계정에 로그인했는지 확인합니다.

#### **로컬 관리자 계정 암호를 다시 설정**

[Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell 명령을 사용하여 관리자 암호 또는 사용자 이름을 다시 설정할 수 있습니다.

다음 명령을 사용하여 로컬 관리자 계정 자격 증명을 만듭니다.

	$cred=Get-Credential

현재 계정이 아닌 다른 이름을 입력하는 경우 다음 VMAccess 확장 명령은 로컬 관리자 계정의 이름을 바꾸며, 해당 계정에 암호를 할당하고 원격 데스크톱 로그 오프를 실행합니다. 로컬 관리자 계정이 비활성화 된 경우 VMAccess 확장으로 사용합니다.

다음과 같이 새 자격 증명을 설정하려면 VM 액세스 확장을 사용합니다.

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
		-Location WestUS -UserName $cred.GetNetworkCredential().Username `
		-Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


`myRG`, `myVM`, `myVMAccess` 및 위치를 설정과 관련된 값으로 바꿉니다.


#### **원격 데스크톱 서비스 구성을 다시 설정**

다음과 같이 [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) 또는 [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) 중 하나를 사용하여 VM에 원격 액세스를 다시 설정할 수 있습니다. (`myRG`, `myVM`, `myVMAccess` 및 위치를 고유한 값으로 대체합니다.)

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

또는 <br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] 두 명령 모두 새로 명명된 VM 액세스 에이전트를 가상 컴퓨터에 추가합니다. 언제든지 VM은 단일 VM 액세스 에이전트를 하나만 사용할 수 있습니다. VM 액세스 에이전트 속성을 성공적으로 설정하려면 `Remove-AzureRmVMAccessExtension` 또는 `Remove-AzureRmVMExtension`를 사용하여 이전에 설정한 액세스 에이전트를 제거합니다. Azure PowerShell 버전 1.2.2를 시작하여 `-ForceRerun` 옵션으로 `Set-AzureRmVMExtension`를 사용할 때 이 단계를 피할 수 있습니다. `-ForceRerun` 옵션을 사용하는 경우 이전 명령으로 설정된 VM 액세스 에이전트에 동일한 이름을 사용해야 합니다.

가상 컴퓨터에 원격으로 연결할 수 없으면 시도에 더 많은 단계를 [Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md)을 참조하세요.


## 클래식 배포 모델의 Windows VM

### Azure 포털

클래식 배포 모델을 사용하여 만든 가상 컴퓨터의 경우 [Azure 포털](https://portal.azure.com)을 사용하여 원격 데스크톱 서비스를 다시 설정할 수 있습니다. **찾아보기** > **가상 컴퓨터(클래식)** > *Windows 가상 컴퓨터* > **원격 액세스 다시 설정**을 클릭합니다. 다음 페이지가 나타납니다.

![RDP 구성 다시 설정 페이지](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

로컬 관리자 계정의 이름 및 암호를 다시 설정해 볼 수 있습니다. **찾아보기** > **가상 컴퓨터(클래식)** > *Windows 가상 컴퓨터* > **모든 설정** > **암호 다시 설정**을 클릭합니다. 다음 페이지가 나타납니다.

![암호 다시 설정 페이지](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

새 사용자 이름 및 암호를 입력한 후 **저장**을 클릭합니다.

### VMAccess 확장 및 PowerShell

VM 에이전트가 가상 컴퓨터에 설치되어 있어야 합니다. VM 에이전트를 사용할 수 있는 한 VMAccess 확장은 설치하지 않아도 사용할 수 있습니다. 다음 명령을 사용하여 VM 에이전트가 이미 설치되어 있는지 확인합니다. "myCloudService" 및 "myVM"을 각각 클라우드 서비스 및 VM의 이름으로 바꿉니다. 매개 변수 없이 `Get-AzureVM`을 실행하면 이러한 이름을 파악할 수 있습니다.)

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

**write-host** 명령에서 **True**가 표시되면 VM 에이전트가 설치되어 있는 것입니다. **False**가 표시되면 [VM 에이전트 및 확장 - 2부](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure 블로그 게시물에서 지침 및 다운로드 링크를 참조합니다.

포털에서 가상 컴퓨터를 만든 경우 `$vm.GetInstance().ProvisionGuestAgent`이 **True**를 반환하는지 확인합니다. 그렇지 않은 경우 다음 명령을 사용하여 설정할 수 있습니다.

	$vm.GetInstance().ProvisionGuestAgent = $true

이 명령은 다음 단계인 “IaaS VM 액세스 확장을 설정하기 전에 VM 개체에서 게스트 에이전트 프로비전을 사용하도록 설정”에서 **Set-AzureVMExtension** 명령을 실행할 때 다음과 같은 오류를 방지합니다.

#### **로컬 관리자 계정 암호를 다시 설정**

현재 로컬 관리자 계정 이름 및 새 암호로 로그인 자격 증명을 만들고 다음과 같이 `Set-AzureVMAccessExtension`을 실행합니다.

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
		-Password $cred.GetNetworkCredential().Password  | Update-AzureVM

현재 계정이 아닌 다른 이름을 입력하는 경우 VMAccess 확장은 로컬 관리자 계정의 이름을 바꾸며, 해당 계정에 암호를 할당하고 원격 데스크톱 로그아웃을 실행합니다. 로컬 관리자 계정이 비활성화 된 경우 VMAccess 확장으로 사용합니다.

이 명령은 원격 데스크톱 서비스 구성을 다시 설정합니다.

#### **원격 데스크톱 서비스 구성을 다시 설정**

원격 데스크톱 서비스 구성을 재설정하려면 다음 명령을 실행합니다.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

VMAccess 확장은 가상 컴퓨터에서 다음 두 명령을 실행합니다.

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

이 명령은 들어오는 원격 데스크톱 트래픽을 허용하는 기본 제공 Windows 방화벽 그룹을 활성화하며 TCP 포트 3389를 사용합니다.

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

이 명령은 원격 데스크톱 연결을 사용하도록 설정하는 fDenyTSConnections 레지스트리 값을 0으로 설정합니다.


## 추가 리소스

[Azure VM 확장 및 기능](virtual-machines-windows-extensions-features.md)

[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0907_2016-->