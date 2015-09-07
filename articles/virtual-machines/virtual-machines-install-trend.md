<properties
	pageTitle="Azure VM에 Trend Micro Deep Security as a Service를 설치하고 구성하는 방법 | Microsoft Azure"
	description="이 문서에서는 Azure의 VM에서 Trend Micro 보안을 설치하고 구성하는 방법을 설명합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="dkshir"/>


# Azure VM에 Trend Micro Deep Security as a Service를 설치하고 구성하는 방법

이 문서에서는 Windows Server가 실행되는 새 VM(가상 컴퓨터) 또는 기존 VM에서 Trend Micro Deep Security as a Service를 설치 및 구성하는 방법을 보여 줍니다. Deep Security as a Service는 맬웨어 방지 보호, 방화벽, 침입 방지 시스템 및 무결성 모니터링을 포함합니다. 이 문서는 클래식 배포 모델을 사용하여 만든 가상 컴퓨터를 참조합니다.

이 클라이언트는 VM 에이전트를 통해 보안 확장 프로그램으로 설치됩니다. 새 가상 컴퓨터에서 Deep Security Agent와 함께 VM 에이전트를 설치합니다. VM 에이전트가 없는 기존 가상 컴퓨터에서는 이 에이전트를 먼저 다운로드하여 설치해야 합니다. 이 문서에서는 두 상황을 모두 다룹니다.

온-프레미스 솔루션에 대해 Trend Micro에 기존 구독이 있는 경우 이를 사용하여 Azure 가상 컴퓨터를 보호할 수 있습니다. 아직 고객이 아닌 경우에는 평가판 구독에 등록할 수 있습니다. 이 솔루션에 대한 자세한 내용은 Trend Micro 블로그 게시물 [Deep Security에 대한 Microsoft Azure VM 에이전트 확장](http://go.microsoft.com/fwlink/p/?LinkId=403945)을 참조하세요.

## 새 가상 컴퓨터에 Deep Security Agent 설치

**갤러리에서** 옵션을 사용하여 가상 컴퓨터를 만들 경우 [Azure 포털](http://manage.windowsazure.com)을 사용하여 VM 에이전트 및 Trend Micro 보안 확장을 설치할 수 있습니다. 이 방법을 사용하면 단일 가상 컴퓨터를 만들 때 Trend Micro의 보호 기능을 쉽게 추가할 수 있습니다.

이 **갤러리에서** 옵션은 가상 컴퓨터 설치에 도움이 되는 마법사를 엽니다. 마법사 마지막 페이지에서 VM 에이전트 및 Trend Micro 보안 확장을 설치합니다. 일반적인 지침은 [Azure 포털에서 Windows Server를 실행하는 가상 컴퓨터 만들기](virtual-machines-windows-tutorial-classic-portal.md)를 참조하세요. 마법사 마지막 페이지가 되면 다음을 수행합니다.

1.	**VM 에이전트**에서 **VM 에이전트 설치**를 선택합니다.

2.	**보안 확장**에서 **Trend Micro Deep Security Agent**를 선택합니다.

	![VM 에이전트 및 Deep Security Agent 설치](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

3.	확인 표시를 클릭하여 가상 컴퓨터를 만듭니다.

## 기존 가상 컴퓨터에 Deep Security Agent 설치

이 작업을 수행하려면 다음이 필요합니다.

- Azure PowerShell 모듈 버전 0.8.2 이상이 로컬 컴퓨터에 설치되어 있어야 합니다. **Get-Module azure | format-table version** 명령을 사용하여 설치한 Azure PowerShell의 버전을 확인할 수 있습니다. 지침 및 최신 버전으로 연결되는 링크를 보려면 [Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)을 참조하세요.

- VM 에이전트가 대상 가상 컴퓨터에 설치되어 있어야 합니다.

먼저 VM 에이전트가 이미 설치되어 있는지 확인합니다. 클라우드 서비스 이름과 가상 컴퓨터 이름을 입력하고 관리자 수준의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

클라우드 서비스 및 가상 컴퓨터 이름을 모르는 경우 **Get-AzureVM**을 실행하여 현재 구독의 모든 가상 컴퓨터에 대한 해당 정보를 표시합니다.

**write-host** 명령에서 **True**가 반환되면 VM 에이전트가 설치되어 있는 것입니다. **False**가 반환되면 Azure 블로그 게시물 [VM 에이전트 및 확장 - 2부](http://go.microsoft.com/fwlink/p/?LinkId=403947)에서 지침 및 다운로드 링크를 참조합니다.

VM 에이전트가 설치되어 있는 경우 다음 명령을 실행합니다.

	$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

	Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## 다음 단계

에이전트가 설치되어 있는 경우 실행을 시작하는 데 몇 분 정도 걸립니다. 그 이후에는 Deep Security Manager에서 관리될 수 있게 가상 컴퓨터에서 Deep Security 정품 인증을 받아야 합니다. 추가 지침은 다음을 참조하세요.

- 이 솔루션에 대한 Trend의 문서, [Microsoft Azure에 대한 Instant-On 클라우드 보안](http://go.microsoft.com/fwlink/?LinkId=404101)
- 가상 컴퓨터 구성을 위한 [샘플 Windows PowerShell 스크립트](http://go.microsoft.com/fwlink/?LinkId=404100)
- 샘플에 대한 [지침](http://go.microsoft.com/fwlink/?LinkId=404099)

## 추가 리소스

[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]

[Azure VM 확장 및 기능]


<!--Link references-->
[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]: virtual-machines-log-on-windows-server.md
[Azure VM 확장 및 기능]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=August15_HO9-->