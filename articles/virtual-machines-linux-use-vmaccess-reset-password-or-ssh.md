<properties pageTitle="Linux 가상 컴퓨터에 VMAccess를 사용하는 방법" description="Linux용 VMAccess 확장을 사용하여 암호, SSH 키 및 SSH 구성을 다시 설정하는 방법" services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="kathydav"/>

#Linux 가상 컴퓨터에 대한 암호 또는 SSH를 다시 설정하는 방법#

암호 또는 SSH 키를 잊어버리거나 SSH 구성 문제가 발생하여 Linux VM에 연결할 수 없는 경우 VMAccessforLinux 확장을 사용하여 암호, SSH 키 및 SSH 구성을 다시 설정할 수 있습니다. 


##요구 사항

- Microsoft Azure Linux 에이전트 버전2.0.5 이상. 가상 컴퓨터 갤러리에 있는 대부분의 Linux 이미지에는 버전 2.0.5가 포함되어 있습니다. 설치된 버전을 확인하려면  `waagent -version`을 실행합니다. 에이전트를 업데이트하려면 [Azure Linux 에이전트 사용자 가이드]의 지침을 따릅니다.

- Azure PowerShell 모듈. 이 모듈에는 **VMAccessForLinux** 확장을 사용하기 위해 명령을 실행할 **Set-AzureVMExtension** cmdlet이 포함되어 있습니다. 모듈 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법]을 참조하세요.

- 새 암호 또는 SSH 키(둘 중 하나를 다시 설정하려는 경우). SSH 구성을 수정하려는 경우에는 필요 없습니다. 

##설치가 필요 없음

VMAccess는 설치하지 않아도 사용할 수 있습니다. Linux 에이전트와 Azure 모듈이 설치되어 있으면 **Set-AzureVMExtension** cmdlet을 호출하는 명령을 실행할 때 확장이 자동으로 로드됩니다. 

##이 확장을 사용하여 암호, SSH 키 또는 구성을 다시 설정하거나 사용자를 추가할 수 있습니다.

**Set-AzureVMExtension** cmdlet을 사용하여 VMAccess에서 허용하는 변경 작업을 수행합니다. 어떤 경우에서든 먼저 클라우드 서비스 이름 및 가상 컴퓨터 이름을 사용하여 가상 컴퓨터 개체를 가져와 변수에 저장해야 합니다.   

Azure PowerShell을 열고 명령 프롬프트에서 다음을 입력합니다. MyServiceName 및 MyVMName 자리 표시자를 실제 이름으로 바꿔야 합니다.

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

그런 다음 아래 작업을 수행할 수 있습니다.

+ [암호 다시 설정](#password)
+ [SSH 키 다시 설정](#SSHkey)
+ [암호 및 SSH 키 다시 설정](#both)
+ [SSH 구성 다시 설정](#config)

### <a name="password"></a>암호 다시 설정
사용자 이름과 암호를 입력하고 변수에 저장한 후 다음 명령에서 사용할 수 있도록 단일 변수를 만들어 값을 저장합니다.

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

이름, 게시자 및 버전 번호를 변수에 저장합니다. 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

필요한 값을 모두 변수에 저장했으면 다음 명령을 실행합니다.

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] 기존 사용자 계정에 대한 암호 또는 SSH 키를 다시 설정하려면 정확한 사용자 이름을 입력해야 합니다. 다른 이름을 입력하면 VMAccess 확장에서 새 사용자 계정을 만들어 이 계정에 암호를 할당합니다.

### <a name="SSHkey"></a>SSH 키 다시 설정

새 공용 SSH 키의 경로 및 사용자 이름을 입력하고 변수에 저장합니다.

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

다음 명령을 실행합니다.

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>암호 및 SSH 키 다시 설정

현재 사용자에 대해 SSH 공용 키와 함께 새 인증서의 새 암호 및 경로를 입력하고 변수에 저장합니다. 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

다음 명령을 실행합니다.

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>SSH 구성 다시 설정

SSH 구성 오류로 인해 VM에 액세스하지 못할 수 있습니다. 구성을 해당 기본값으로 다시 설정하여 이 오류를 해결할 수 있습니다. 이렇게 하면 구성에서 모든 새 액세스 매개 변수(사용자 이름, 암호 또는 SSH 키)가 제거됩니다. 사용자 계정의 암호 또는 SSH 키는 변경되지 않습니다. 확장에서 SSH 서버를 다시 시작하고, VM에서 SSH 포트를 연 다음, SSH 구성을 기본값으로 다시 설정합니다.  

구성을 다시 설정하여 변수에 저장할 것임을 나타내는 플래그를 설정합니다. 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

다음 명령을 실행합니다.

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] SSH 구성 파일은 /etc/ssh/sshd_config에 있습니다.

## 문제 해결

**Set-AzureVMExtension** cmdlet을 사용할 때 다음 오류가 발생할 수 있습니다. "IaaS VM 액세스 확장을 설정하려면 먼저 VM 개체에서 게스트 에이전트 프로비전을 사용하도록 설정해야 합니다." 

이 오류는 관리 포털을 사용하여 Linux VM을 만든 경우 게스트 에이전트 속성 값이 "True"로 설정되지 않았기 때문에 발생할 수 있습니다. 이 오류를 해결하려면 다음 명령을 실행합니다.

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#추가 리소스
[Azure VM 확장 및 기능] []

[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결] []


<!--Link references-->
[Azure Linux 에이전트 사용자 가이드]: ../virtual-machines-linux-agent-user-guide
[Azure PowerShell을 설치 및 구성하는 방법]: ../install-configure-powershell
[Azure VM 확장 및 기능]: http://msdn.microsoft.com/ko-kr/library/azure/dn606311.aspx
[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결]: http://msdn.microsoft.com/ko-kr/library/azure/dn535788.aspx

<!--HONumber=42-->
