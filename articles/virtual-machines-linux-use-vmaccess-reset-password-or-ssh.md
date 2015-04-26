<properties 
	pageTitle="Linux 가상 컴퓨터에 VMAccess를 사용하는 방법" 
	description="Linux용 VMAccess 확장을 사용하여 암호 및 SSH 키를 다시 설정하고, SSH 구성을 다시 보내고, Linux 사용자를 삭제하는 방법" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# Linux 가상 컴퓨터에 대한 암호 또는 SSH를 다시 설정하는 방법 #

암호를 잊어버리거나, SSH(Secure Shell) 키가 잘못되었거나, SSH 구성 문제가 발생하여 Linux 가상 컴퓨터에 연결할 수 없는 경우 VMAccessforLinux 확장을 사용하여 암호 또는 SSH 키를 다시 설정하거나 SSH 구성을 수정할 수 있습니다. 

## 요구 사항

- Microsoft Azure Linux 에이전트 버전2.0.5 이상. 가상 컴퓨터 갤러리에 있는 대부분의 Linux 이미지에는 버전 2.0.5가 포함되어 있습니다. 설치된 버전을 확인하려면 `waagent -version`을 실행합니다. 에이전트를 업데이트하려면 [Azure Linux 에이전트 사용자 가이드]의 지침을 따릅니다.
- Azure PowerShell. **Set-AzureVMExtension** cmdlet의 명령을 사용하여 **VMAccessForLinux** 확장을 자동으로 로드 및 구성할 수 있습니다. Azure PowerShell 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법]을 참조하세요.
- 새 암호 또는 SSH 키 집합(둘 중 하나를 다시 설정하려는 경우). SSH 구성을 다시 설정하려는 경우에는 필요 없습니다. 

## 설치가 필요 없음

VMAccess 확장은 설치하지 않아도 사용할 수 있습니다. Linux 에이전트가 가상 컴퓨터에 설치되어 있으면 **Set-AzureVMExtension** cmdlet을 사용하는 Azure PowerShell 명령을 실행할 때 확장이 자동으로 로드됩니다. 

## 이 확장을 사용하여 암호, SSH 키 또는 SSH 구성을 다시 설정하거나 사용자를 삭제할 수 있습니다.

**Set-AzureVMExtension** cmdlet을 사용하여 VMAccess에서 허용하는 변경 작업을 수행합니다. 어떤 경우에서든 먼저 클라우드 서비스 이름 및 가상 컴퓨터 이름을 사용하여 가상 컴퓨터 개체를 가져와 변수에 저장해야 합니다. 

클라우드 서비스 이름과 가상 컴퓨터 이름을 입력하고 관리자 수준의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

클라우드 서비스 및 가상 컴퓨터 이름을 모르는 경우 **Get-AzureVM**을 실행하여 현재 구독의 모든 VM에 대한 해당 정보를 표시합니다.


> [AZURE.NOTE] $로 시작하는 명령줄에서는 나중에 PowerShell 명령에서 사용되는 PowerShell 변수를 설정합니다.

Azure 관리 포털을 사용하여 가상 컴퓨터를 만든 경우 다음 추가 명령을 실행합니다.

	$vm.GetInstance().ProvisionGuestAgent = $true

이 명령은 다음 섹션에서 Set-AzureVMExtension 명령을 실행할 때 "IaaS VM Access 확장을 설정하기 전에 VM 개체에서 게스트 에이전트 프로비전을 사용하도록 설정해야 합니다" 오류를 방지합니다. 

그런 다음 아래 작업을 수행할 수 있습니다.

+ [암호 다시 설정](#password)
+ [SSH 키 다시 설정](#SSHkey)
+ [암호 및 SSH 키 다시 설정](#both)
+ [SSH 구성 다시 설정](#config)
+ [사용자 삭제](#delete)

### <a name="password"></a>암호 다시 설정

현재 Linux 사용자 이름 및 새 암호를 입력하고 다음 명령을 실행합니다.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] 기존 사용자 계정에 대한 암호 또는 SSH 키를 다시 설정하려면 정확한 사용자 이름을 입력해야 합니다. 다른 이름을 입력하면 VMAccess 확장에서 새 사용자 계정을 만들어 이 계정에 암호를 할당합니다.


### <a name="SSHKey"></a>SSH 키 다시 설정

현재 Linux 사용자 이름 및 SSH 키가 포함된 인증서의 경로를 입력하고 다음 명령을 실행합니다.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>암호 및 SSH 키 다시 설정

현재 Linux 사용자 이름, 새 암호 및 SSH 키가 포함된 인증서의 경로를 입력하고 다음 명령을 실행합니다.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>SSH 구성 다시 설정

SSH 구성 오류로 인해 가상 컴퓨터에 액세스하지 못할 수 있습니다. SSH 구성을 해당 기본 상태로 다시 설정하여 이 오류를 해결할 수 있습니다. 이 경우 사용자 이름, 암호, SSH 키 등 구성의 새 액세스 매개 변수가 모두 제거되지만 사용자 계정의 암호 또는 SSH 키는 변경되지 않습니다. 확장에서 SSH 서버를 다시 시작하고, 가상 컴퓨터에서 SSH 포트를 연 다음, SSH 구성을 기본값으로 다시 설정합니다. 

다음 명령을 실행합니다.

	$PrivateConfig = '{"reset_ssh": "True"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] SSH 구성 파일은 /etc/ssh/sshd_config에 있습니다.

### <a name="delete"></a> 사용자 삭제

삭제할 Linux 사용자 이름을 입력하고 다음 명령을 실행합니다.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## 추가 리소스

[Azure VM 확장 및 기능] []

[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결] []


<!--Link references-->
[Azure Linux 에이전트 사용자 가이드]: ../virtual-machines-linux-agent-user-guide
[Azure PowerShell을 설치 및 구성하는 방법]: ../install-configure-powershell
[Azure VM 확장 및 기능]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
