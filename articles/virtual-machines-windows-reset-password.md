<properties 
	pageTitle="Windows 가상 컴퓨터에 대한 원격 데스크톱 서비스 또는 암호를 다시 설정하는 방법" 
	description="PowerShell 명령을 사용하여 Windows 가상 컴퓨터에 대한 원격 데스크톱 서비스 또는 로컬 관리자 암호를 신속하게 다시 설정합니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="josephd"/>

# Windows 가상 컴퓨터에 대한 원격 데스크톱 서비스 또는 암호를 다시 설정하는 방법

암호를 잊었거나 원격 데스크톱 서비스 구성의 문제로 Windows 가상 컴퓨터에 연결할 수 없는 경우, VMAccess 확장을 사용하여 로컬 관리자 암호를 재설정하거나 원격 데스크톱 서비스 구성을 다시 설정합니다.
 
## 요구 사항

이 작업을 수행하려면 다음이 필요합니다.

- Azure PowerShell 모듈, 버전 0.8.5 이상. 설치한 Azure PowerShell의 버전은 **Get-Module azure | format-table version** 명령을 사용하여 확인할 수 있습니다. 지침 및 최신 버전으로의 링크를 보려면 [Azure PowerShell을 설치 및 구성하는 방법](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409)을 참조하세요. 
- 새 로컬 관리자 계정 암호입니다. 원격 데스크톱 서비스 구성을 다시 설정하려는 경우 필요하지 않습니다. 
- VM 에이전트. 

VMAccess 확장은 설치하지 않아도 사용할 수 있습니다. VM 에이전트가 가상 컴퓨터에 설치되어 있으면 **Set-AzureVMExtension** cmdlet을 사용하는 Azure PowerShell 명령을 실행할 때 확장이 자동으로 로드됩니다.
 
먼저 VM 에이전트가 이미 설치되어 있는지 확인합니다. 클라우드 서비스 이름과 가상 컴퓨터 이름을 입력하고 관리자 수준의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

클라우드 서비스 및 가상 컴퓨터 이름을 모르는 경우 **Get-AzureVM**을 실행하여 현재 구독의 모든 가상 컴퓨터에 대한 해당 정보를 표시합니다.

**write-host** 명령에서 **True**가 표시되면 VM 에이전트가 설치되어 있는 것입니다. **False**가 표시되면 [VM 에이전트 및 확장 - 2부](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure 블로그 게시물에서 지침 및 다운로드 링크를 참조합니다.

Azure 관리 포털을 사용하여 가상 컴퓨터를 만든 경우 다음 추가 명령을 실행합니다.

	$vm.GetInstance().ProvisionGuestAgent = $true

이 명령은 다음 섹션에서 Set-AzureVMExtension 명령을 실행할 때 "IaaS VM Access 확장을 설정하기 전에 VM 개체에서 게스트 에이전트 프로비전을 사용하도록 설정해야 합니다" 오류를 방지합니다. 

이제 다음 작업을 수행할 수 있습니다.

- 로컬 관리자 계정 암호를 다시 설정
- 원격 데스크톱 서비스 구성을 다시 설정

## 로컬 관리자 계정 암호를 다시 설정

현재 로컬 관리자 계정 이름 및 새 암호를 입력하고 다음 명령을 실행합니다.

	$cred=Get-Credential -Message "Type the name of the current local administrator account and the new password."	
	Set-AzureVMAccessExtension -vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- 현재 계정이 아닌 다른 이름을 입력하는 경우 VMAccess 확장은 로컬 관리자 계정의 이름을 바꾸며, 해당 계정에 암호를 할당하고 원격 데스크톱 로그 오프를 실행합니다.
- 로컬 관리자 계정이 비활성화 된 경우 VMAccess 확장으로 사용합니다.
 
이 명령은 원격 데스크톱 서비스 구성을 다시 설정합니다.

## 원격 데스크톱 서비스 구성을 다시 설정

원격 데스크톱 서비스 구성을 다시 설정 하려면 이 명령을 실행 합니다.

	Set-AzureVMAccessExtension -vm $vm | Update-AzureVM

VMAccess 확장은 VM에서 다음 두 명령을 실행합니다.

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	이 명령은 들어오는 원격 데스크톱 트래픽을 허용하는 기본 제공 Windows 방화벽 그룹을 활성화하며 TCP 포트 3389를 사용합니다.

- **Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0**

	이 명령은 원격 데스크톱 연결을 사용하도록 설정하는 fDenyTSConnections 레지스트리 값을 0으로 설정합니다.

원격 데스크톱 액세스 문제를 해결하지 못 한 경우, [Azure IaaS(Windows) 진단 패키지](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)를 실행합니다. 

1.	이 페이지에서 **Microsoft Azure IaaS(Windows) 진단 패키지**를 클릭하여 새 진단 세션을 만듭니다.
2.	**다음 중 Azure VM에서 발생하는 문제는 무엇인가요?** 페이지에서 **Azure VM 에 대한 RDP 연결(다시 부팅 필요)** 문제를 선택합니다. 

자세한 내용은 [Microsoft Azure IaaS(Windows) 진단 패키지 기술 자료 문서](http://support.microsoft.com/kb/2976864)를 참조하세요. 

Azure IaaS(Windows) 진단 패키지를 실행할 수 없거나 실행해도 문제가 해결되지 않는 경우, [Windows 기반 Azure 가상 컴퓨터에 원격 데스크톱 연결 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md)을 참조하세요.


## 추가 리소스

[Azure VM 확장 및 기능](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](http://msdn.microsoft.com/library/azure/dn535788.aspx)


<!--HONumber=52-->