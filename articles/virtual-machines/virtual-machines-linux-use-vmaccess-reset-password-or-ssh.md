<properties
	pageTitle="Azure CLI에서 Linux VM 암호 재설정 | Microsoft Azure"
	description="Azure 포털 또는 CLI에서 VMAccess 확장을 사용하여 Linux VM 암호 및 SSH 키, SSH 구성을 재설정하고 사용자 계정을 삭제하는 방법입니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="cynthn"/>

# Linux 가상 컴퓨터에 대한 암호 또는 SSH를 다시 설정하는 방법 #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


암호를 잊어버리거나, SSH(Secure Shell) 키가 잘못되었거나, SSH 구성 문제가 발생하여 Linux 가상 컴퓨터에 연결할 수 없는 경우, Azure 미리 보기 포털이나 VMAccessforLinux 확장을 사용하여 암호 또는 SSH 키를 다시 설정하거나 SSH 구성을 수정할 수 있습니다. 이 문서는 **클래식** 배포 모델을 사용하여 만든 가상 컴퓨터에 적용됩니다.

## Azure Preview 포털

[Azure 미리 보기 포털](https://portal.azure.com)에서 SSH 구성을 재설정하려면, **찾아보기** > **가상 컴퓨터** > *Linux 가상 컴퓨터* > **원격 액세스 재설정**을 클릭합니다. 다음은 예제입니다.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-RDP-Reset-Linux.png)

[Azure 미리 보기 포털](https://portal.azure.com)에서 sudo 권한이 있는 사용자 계정의 이름과 암호 또는 SSH 공개 키를 재설정하려면, **찾아보기** > **가상 컴퓨터** > *Linux 가상 컴퓨터* > **모든 설정** > **암호 재설정**을 클릭합니다. 다음은 예제입니다.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-PW-Reset-Linux.png)


## Azure CLI 및 PowerShell

다음이 필요합니다.

- Microsoft Azure Linux 에이전트 버전2.0.5 이상. 가상 컴퓨터 갤러리에 있는 대부분의 Linux 이미지에는 버전 2.0.5가 포함되어 있습니다. 설치된 버전을 확인하려면 **waagent -version**을 실행합니다. 에이전트를 업데이트하려면 [Azure Linux 에이전트 사용자 가이드]의 지침을 따릅니다.
- Azure CLI(명령줄 인터페이스) Azure CLI 설정에 대한 자세한 내용은 [Azure 명령줄 인터페이스 설치 및 구성](../xplat-cli-install.md)을 참조하세요.
- Azure PowerShell. Set-AzureVMExtension cmdlet의 명령을 사용하여 VMAccessForLinux 확장을 자동으로 로드 및 구성할 수 있습니다. Azure PowerShell 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법]을 참조하세요.
- 새 암호 또는 SSH 키 집합(둘 중 하나를 다시 설정하려는 경우). SSH 구성을 다시 설정하려는 경우에는 필요 없습니다.

### 설치가 필요 없음

VMAccess 확장은 설치하지 않아도 사용할 수 있습니다. Linux 에이전트가 가상 컴퓨터에 설치되어 있으면 **Set-AzureVMExtension** cmdlet을 사용하는 Azure PowerShell 명령을 실행할 때 확장이 자동으로 로드됩니다.

## Azure CLI 사용

Azure CLI를 사용하여, 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트)에서 **azure** 명령을 사용하여 명령에 액세스할 수 있습니다. 자세한 확장 사용은 **azure vm extension set –help**을 실행합니다.

Azure CLI를 사용하여, 다음 작업을 수행할 수 있습니다.

+ [암호 재설정](#pwresetcli)
+ [SSH 키 재설정](#sshkeyresetcli)
+ [암호 및 SSH 키 재설정](#resetbothcli)
+ [새 sudo 사용자 계정 만들기](#createnewsudocli)
+ [SSH 구성 재설정](#sshconfigresetcli)
+ [사용자 삭제](#deletecli)
+ [VMAccess 확장 상태 표시](#statuscli)

### <a name="pwresetcli"></a>암호 재설정

1단계: 이 콘텐츠를 포함한 PrivateConf.json이라는 파일을 만들어 자리 표시자 값을 대체합니다.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01",
	}

2단계: 이 명령을 실행하여 "vmname"에 대한 가상 컴퓨터의 이름을 대체합니다

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>SSH 키 재설정

1단계: 이 콘텐츠를 포함한 PrivateConf.json이라는 파일을 만들어 자리 표시자 값을 대체합니다.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	}

2단계: 이 명령을 실행하여 "vmname"에 대한 가상 컴퓨터의 이름을 대체합니다

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>암호 및 SSH 키 재설정

1단계: 이 콘텐츠를 포함한 PrivateConf.json이라는 파일을 만들어 자리 표시자 값을 대체합니다.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword",
	}

2단계: 이 명령을 실행하여 "vmname"에 대한 가상 컴퓨터의 이름을 대체합니다

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>새 sudo 사용자 계정 만들기

사용자 이름을 잊은 경우 sudo 기관으로 새 레코드를 만들려면 VMAccess를 사용할 수 있습니다. 이 경우 기존 사용자 이름 및 암호는 수정되지 않습니다.

암호 액세스가 있는 새 sudo 사용자를 만들려면, [암호 재설정](#pwresetcli)에서 스크립트를 사용하여 새 사용자 이름을 지정합니다.

SSH 키 액세스가 있는 새 sudo 사용자를 만들려면, [SSH 키 재설정](#sshkeyresetcli)에서 스크립트를 사용하여 새 사용자 이름을 지정합니다.

[암호 및 SSH 키 재설정](#resetbothcli)을 사용하여 암호 및 SSH 키 액세스 모두가 있는 새 사용자를 만듭니다.

### <a name="sshconfigresetcli"></a>SSH 구성 재설정

SSH 구성이 원치 않는 상태인 경우, VM에 대한 액세스도 손실될 수 있습니다. 구성을 기본 상태로 다시 설정하려면 VMAccess 확장을 사용할 수 있습니다. 이렇게 하려면 "reset\_ssh" 키를 "True"로 설정하기만 하면 됩니다. 확장에서 SSH 서버를 다시 시작하고, VM에서 SSH 포트를 연 다음, SSH 구성을 기본값으로 다시 설정합니다. 사용자 계정(이름, 암호 또는 SSH 키)은 변경되지 않습니다.

> [AZURE.NOTE]재설정된 SSH 구성 파일은 /etc/ssh/sshd\_config에 있습니다.

1단계:이 콘텐츠를 포함한 PrivateConf.json이라는 파일을 만듭니다.

	{
	"reset_ssh":"True",
	}

2단계: 이 명령을 실행하여 "vmname"에 대한 가상 컴퓨터의 이름을 대체합니다

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>사용자 삭제

VM에 직접 로그인하지 않고 사용자 계정을 삭제하려면 이 스크립트를 사용합니다.

1단계: 이 콘텐츠를 포함한 PrivateConf.json이라는 파일을 만들어, 자리 표시자 값을 대체합니다.

	{
	"remove_user":"usernametoremove",
	}

2단계: 이 명령을 실행하여 "vmname"에 대한 가상 컴퓨터의 이름을 대체합니다

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>VMAccess 확장 상태 표시

VMAccess 확장 상태를 표시하려면, 다음 명령을 실행합니다.

	azure vm extension get


## Azure PowerShell 사용

**Set-AzureVMExtension**을 사용하여 VMAccess에서 허용하는 변경 작업을 수행합니다. 어떤 경우에서든 먼저 클라우드 서비스 이름 및 가상 컴퓨터 이름을 사용하여 가상 컴퓨터 개체를 가져와 변수에 저장해야 합니다.

클라우드 서비스 이름과 가상 컴퓨터 이름을 입력하고 관리자 수준의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

클라우드 서비스 및 가상 컴퓨터 이름을 모르는 경우 **Get-AzureVM**을 실행하여 현재 구독의 모든 VM에 대한 해당 정보를 표시합니다.


> [AZURE.NOTE]$로 시작하는 명령줄에서는 나중에 PowerShell 명령에서 사용되는 PowerShell 변수를 설정합니다.

Azure 관리 포털을 사용하여 가상 컴퓨터를 만든 경우 다음 추가 명령을 실행합니다.

	$vm.GetInstance().ProvisionGuestAgent = $true

이 명령은 다음 섹션에서 Set-AzureVMExtension 명령을 실행할 때 “IaaS VM Access 확장을 설정하기 전에 VM 개체에서 게스트 에이전트 프로비전을 사용하도록 설정해야 합니다” 오류를 방지합니다.

그런 다음 아래 작업을 수행할 수 있습니다.

+ [암호 재설정](#password)
+ [SSH 키 재설정](#SSHkey)
+ [암호 및 SSH 키 재설정](#both)
+ [SSH 구성 재설정](#config)
+ [사용자 삭제](#delete)
+ [VMAccess 확장 상태 표시](#status)

### <a name="password"></a>암호 재설정

현재 Linux 사용자 이름 및 새 암호를 입력하고 다음 명령을 실행합니다.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]기존 사용자 계정에 대한 암호 또는 SSH 키를 다시 설정하려면 정확한 사용자 이름을 입력해야 합니다. 다른 이름을 입력하면 VMAccess 확장에서 새 사용자 계정을 만들어 이 계정에 암호를 할당합니다.


### <a name="SSHKey"></a>SSH 키 재설정

현재 Linux 사용자 이름 및 SSH 키가 포함된 인증서의 경로를 입력하고 다음 명령을 실행합니다.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>암호 및 SSH 키 재설정

현재 Linux 사용자 이름, 새 암호 및 SSH 키가 포함된 인증서의 경로를 입력하고 다음 명령을 실행합니다.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>SSH 구성 재설정

SSH 구성 오류로 인해 가상 컴퓨터에 액세스하지 못할 수 있습니다. SSH 구성을 해당 기본 상태로 다시 설정하여 이 오류를 해결할 수 있습니다. 이 경우 사용자 이름, 암호, SSH 키 등 구성의 새 액세스 매개 변수가 모두 제거되지만 사용자 계정의 암호 또는 SSH 키는 변경되지 않습니다. 확장에서 SSH 서버를 다시 시작하고, 가상 컴퓨터에서 SSH 포트를 연 다음, SSH 구성을 기본값으로 다시 설정합니다.

다음 명령을 실행합니다.

	$PrivateConfig = '{"reset_ssh": "True"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]SSH 구성 파일은 /etc/ssh/sshd\_config에 있습니다.

### <a name="delete"></a> 사용자 삭제

삭제할 Linux 사용자 이름을 입력하고 다음 명령을 실행합니다.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>VMAccess 확장 상태 표시

VMAccess 확장 상태를 표시하려면, 다음 명령을 실행합니다.

	$vm.GuestAgentStatus


## 추가 리소스

[Azure VM 확장 및 기능][]

[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결][]


<!--Link references-->
[Azure Linux 에이전트 사용자 가이드]: virtual-machines-linux-agent-user-guide.md
[Azure PowerShell을 설치 및 구성하는 방법]: ../install-configure-powershell.md
[Azure VM 확장 및 기능]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=Oct15_HO3-->