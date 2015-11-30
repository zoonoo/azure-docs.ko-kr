<properties
   pageTitle="Azure 자동화 Hybrid Runbook Worker"
   description="이 문서에서는 로컬 데이터 센터의 컴퓨터에서 Runbook을 실행할 수 있도록 해주는 Azure 자동화의 기능인 Hybrid Runbook Worker를 설치하고 사용하는 방법에 대한 정보를 제공합니다."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/03/2015"
   ms.author="bwren" />

# Azure 자동화 Hybrid Runbook Worker

Azure 자동화의 Runbook은 Azure 클라우드에서 실행되므로 로컬 데이터 센터의 리소스에 액세스할 수 없습니다. Azure 자동화의 Hybrid Runbook Worker 기능을 사용하면 데이터 센터에 있는 컴퓨터에서 Runbook을 실행하여 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure 자동화에서 저장되고 관리된 다음 실행되는 하나 이상의 온-프레미스 컴퓨터에 전달됩니다.

이 기능은 다음 그림에 나와 있습니다.

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

데이터 센터에서 Hybrid Runbook Worker 역할을 할 컴퓨터를 하나 이상 지정하고 Azure 자동화에서 Runbook을 실행할 수 있습니다. 각 작업자에는 Microsoft Operations Management Suite 및 Azure 자동화 Runbook 환경에 연결된 Microsoft 관리 에이전트가 필요합니다. Operations Management Suite는 관리 에이전트를 설치 및 유지 관리하고 작업자의 기능을 모니터링하는 데에만 사용됩니다. Runbook 및 Runbook 실행 지침은 Azure 자동화에 의해 전달됩니다.

![Hybrid Runbook Worker 구성 요소](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

>[AZURE.NOTE]Operational Insights가 Operations Management Suite로 통합되는 과정에 있기 때문에 두 가지 이름 중 하나를 포털 및 이 문서에서 볼 수 있습니다.

Hybrid Runbook Worker를 지원하기 위한 인바운드 방화벽 요구 사항은 없습니다. 로컬 컴퓨터의 에이전트는 클라우드에서 Azure 자동화와의 모든 통신을 시작합니다. Runbook이 시작되면 Azure 자동화에서 에이전트에게 전달되는 지침을 만듭니다. 그런 다음 에이전트는 Runbook을 실행하기 전에 Runbook 및 모든 매개 변수를 끌어옵니다. 또한 Azure 자동화에서 Runbook에 사용되는 모든 [자산](http://msdn.microsoft.com/library/dn939988.aspx)을 검색합니다.

>[AZURE.NOTE]하이브리드 Runbook Worker는 현재 [DSC 구성](automation-dsc-overview.md)을 지원하지 않습니다.

## Hybrid Runbook Worker 그룹

각 Hybrid Runbook Worker는 에이전트를 설치할 때 지정한 Hybrid Runbook Worker 그룹의 구성원입니다. 그룹은 단일 에이전트를 포함할 수 있지만 고가용성을 위해 그룹에 여러 에이전트를 설치할 수 있습니다.

Hybrid Runbook Worker에서 Runbook을 시작할 경우 이를 실행할 그룹을 지정합니다. 그룹의 구성원에 따라 요청을 처리할 작업자가 결정됩니다. 특정 작업자를 지정할 수 없습니다.

## Hybrid Runbook Worker 요구 사항

Hybrid Runbook 작업을 실행하려면 온-프레미스 컴퓨터를 적어도 하나 지정해야 합니다. 이 컴퓨터에는 다음 항목이 있어야 합니다.

- Windows Server 2012 이상
- Windows PowerShell 4.0 이상

Hybrid Worker에 대한 다음 권장 사항을 고려하십시오.

- 고가용성을 위해 각 그룹에 여러 Hybrid Worker를 지정합니다.  
- Hybrid Worker는 서비스 관리 자동화 또는 System Center Orchestrator Runbook 서버와 공존할 수 있습니다.
- 작업이 완료되면 작업 데이터가 Azure 자동화로 다시 전송되므로 자동화 계정 지역이나 그와 가까운 위치에 있는 컴퓨터를 사용하는 것이 좋습니다.

방화벽 요구 사항:

- Hybrid Runbook Worker를 실행 중인 온-프레미스 컴퓨터는 포트 443, 9354 및 30000-30199에서 *.cloudapp.net에 아웃바운드 액세스를 할 수 있어야 합니다.

## Hybrid Runbook Worker 설치
아래 절차는 Hybrid Runbook Worker를 설치하고 구성하는 방법을 설명합니다. 자동화 환경에 대해 처음 두 단계를 한 번 수행한 후 각 Worker 컴퓨터에 대해 나머지 단계를 반복합니다.

### 1\. Operations Management Suite 작업 영역 만들기
Operations Management Suite 작업 영역이 아직 없는 경우 [Operational Insights 작업 영역 설정](../operational-insights/operational-insights-onboard-in-minutes.md)의 지침에 따라 작업 영역을 만듭니다. 이미 있는 경우에는 기존 작업 영역을 사용할 수 있습니다.

### 2\. Operations Management Suite 작업 영역에 자동화 솔루션 추가
솔루션은 Operations Management Suite에 기능을 추가합니다. 자동화 솔루션은 Hybrid Runbook Worker에 대한 지원을 포함하여 Azure 자동화를 위한 기능을 추가합니다. 작업 영역에 솔루션을 추가할 때 다음 단계에서 설치할 에이전트 컴퓨터로 Worker 구성 요소를 자동으로 푸시다운합니다.

[솔루션 갤러리를 사용하여 솔루션을 추가하려면](../operational-insights/operational-insights-setup-workspace.md#1-add-solutions)의 지침에 따라 Operations Management Suite 작업 영역에 **자동화** 솔루션을 추가합니다.

### 3\. Microsoft 관리 에이전트 설치
Microsoft 관리 에이전트는 컴퓨터를 Operations Management Suite에 연결합니다. 온-프레미스 컴퓨터에 에이전트를 설치하고 작업 영역에 연결하면 Hybrid Runbook Worker에 필요한 구성 요소가 자동으로 다운로드됩니다.

[Operational Insights에 직접 컴퓨터 연결](../operational-insights/operational-insights-direct-agent.md)의 지침에 따라 온-프레미스 컴퓨터에 에이전트를 설치합니다. 이 과정을 여러 컴퓨터에 반복하여 사용자의 환경에 여러 작업자를 추가합니다.

에이전트가 Operations Management Suite에 연결되면 Operations Management Suite **설정** 창의 **연결된 원본** 탭에 나열됩니다. 에이전트에서 자동화 솔루션 다운로드를 완료했는지 확인하려면 C:\\Program Files\\Microsoft Monitoring Agent\\Agent에 **AzureAutomationFiles** 폴더가 있는지 확인합니다.

### 4\. Runbook 환경을 설치하고 Azure 자동화에 연결
에이전트를 Operations Management Suite에 추가하면 자동화 솔루션이 **Add-HybridRunbookWorker** cmdlet을 포함하는 **HybridRegistration** PowerShell 모듈을 푸시다운합니다. 이 cmdlet을 사용하여 컴퓨터에 Runbook 환경을 설치하고 Azure 자동화에 등록합니다.

관리자 모드에서 PowerShell 세션을 열고 다음 명령을 실행하여 모듈을 가져옵니다.

	cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation<version>\HybridRegistration"
	Import-Module HybridRegistration.psd1


그런 다음 아래 구문을 사용하여 **Add-HybridRunbookWorker** cmdlet을 실행합니다.

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Azure Preview 포털의 **키 관리** 블레이드에서 이 cmdlet에 필요한 정보를 가져올 수 있습니다. 자동화 계정의 요소 패널에서 키 아이콘을 클릭하여 이 블레이드를 엽니다.

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

- **Name**은 Hybrid Runbook Worker 그룹의 이름입니다. 이 그룹이 자동화 계정에 이미 있으면 현재 컴퓨터가 추가되고, 그렇지 않으면 이 그룹이 추가됩니다.
- **끝점**은 **키 관리** 블레이드의 **URL** 필드입니다.
- **Token**은 **키 관리** 블레이드의 **기본 액세스 키**입니다.  

설치에 대해 자세한 정보를 받으려면 **Add-HybridRunbookWorker**와 함께 **-Verbose** 스위치를 사용합니다.

### 5\. PowerShell 모듈 설치
Runbook은 Azure 자동화 환경에 설치된 모듈에 정의된 활동 및 cmdlet을 사용할 수 있습니다. 이러한 모듈은 온-프레미스 컴퓨터에 자동으로 배포되지 않으므로 수동으로 설치해야 합니다. 단, 기본적으로 설치되어 Azure 자동화의 모든 Azure 서비스 및 활동에 사용되는 cmdlet에 대한 액세스를 제공하는 Azure 모듈은 예외입니다.

Hybrid Runbook Worker 기능의 주 목적은 로컬 리소스를 관리하는 것이므로 이러한 리소스를 지원하는 모듈을 설치해야 할 수 있습니다. Windows PowerShell 모듈 설치에 대한 자세한 내용은 [모듈 설치](http://msdn.microsoft.com/library/dd878350.aspx)를 참조하세요.

## Hybrid Runbook Worker 제거

온-프레미스 컴퓨터에서 **Remove-HybridRunbookWorker** cmdlet을 실행하면 해당 컴퓨터에서 Hybrid Runbook Worker를 제거할 수 있습니다. 제거 과정에 대한 자세한 로그를 보려면 **-Verbose** 스위치를 사용합니다.

## Hybrid Runbook Worker에서 Runbook 시작

[Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md)에 Runbook을 시작하는 여러 가지 방법이 설명되어 있습니다. Hybrid Runbook Worker는 Hybrid Runbook Worker 그룹의 이름을 지정할 수 있는 **RunOn** 옵션을 추가합니다. 그룹을 지정하면 해당 그룹의 작업자가 Runbook을 검색하고 실행합니다. 이 옵션을 지정하지 않으면 평소처럼 Azure 자동화에서 Runbook이 실행됩니다.

Azure Preview 포털에서 Runbook을 시작하면 **Azure** 또는 **Hybrid Worker**를 선택할 수 있는 **실행 대상** 옵션이 표시됩니다. **Hybrid Worker**를 선택한 경우 드롭다운에서 그룹을 선택할 수 있습니다.

**RunOn** 매개 변수 사용 Windows PowerShell에서 다음 명령을 사용하여 MyHybridGroup이라는 Hybrid Runbook Worker 그룹에서 Test-Runbook이라는 Runbook을 시작할 수 있습니다.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE]**RunOn** 매개 변수는 Microsoft Azure PowerShell 버전 0.9.1에서 **Start-AzureAutomationRunbook** cmdlet에 추가되었습니다. 이전 버전을 설치한 경우 [최신 버전을 다운로드](http://azure.microsoft.com/downloads)해야 합니다. Windows PowerShell에서 Runbook을 시작할 워크스테이션에만 이 버전을 설치하면 됩니다. 작업자 컴퓨터에서 Runbook을 시작하려는 경우가 아니라면 해당 컴퓨터에 설치할 필요는 없습니다. 현재 Hybrid Runbook Worker에서 다른 Runbook을 통해 Runbook을 시작할 수는 없습니다. 이렇게 하려면 최신 버전의 Azure Powershell을 자동화 계정에 설치해야 합니다. 최신 버전은 Azure 자동화에서 자동으로 업데이트되며 곧 작업자에 자동으로 푸시될 예정입니다.

## Hybrid Runbook Worker에서 Runbook 문제 해결

[Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)는 클라우드에서 실행되는 Runbook 작업처럼 Hybrid Worker에서 Azure 자동화로 전송됩니다. Verbose 및 Progress 스트림을 다른 Runbook과 같은 방식으로 사용할 수도 있습니다.

로그는 각 Hybrid Worker의 로컬에 저장되며 위치는 C:\\ProgramData\\Microsoft\\System Center\\Orchestrator\\7.2\\SMA\\Sandboxes입니다.


## Hybrid Runbook Worker용 Runbook 만들기

Azure 자동화에서 실행되는 Runbook과 Hybrid Runbook Worker에서 실행되는 Runbook은 구조상 차이가 없습니다. 그러나 각각에서 사용하는 Hybrid Runbook Worker용 Runbook은 일반적으로 데이터 센터의 로컬 리소스를 관리하는 반면, Azure 자동화의 Runbook은 일반적으로 Azure 클라우드의 리소스를 관리하기 때문에 각각에서 사용하는 Runbook은 서로 크게 다를 수 있습니다.

### Runbook 사용 권한

Runbook은 Hybrid Runbook Worker에서 로컬 시스템 계정의 컨텍스트로 실행되므로 액세스할 리소스에 대한 자체 인증을 제공해야 합니다. Azure 외부의 리소스에 액세스하기 때문에 [일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것](automation-configuring.md#configuring-authentication-to-azure-resources)과 동일한 방법을 사용할 수 없습니다.

여러 리소스를 인증할 수 있도록 자격 증명을 지정할 수 있는 cmdlet과 함께 Runbook의 [자격 증명](http://msdn.microsoft.com/library/dn940015.aspx) 및 [Certificate](http://msdn.microsoft.com/library/dn940013.aspx) 자산을 사용할 수 있습니다. 다음 예제에서는 컴퓨터를 다시 시작하는 Runbook의 일부를 보여 줍니다. 이 예제에서는 자격 증명 자산에서 자격 증명을 검색하고 변수 자산에서 컴퓨터 이름을 검색한 다음 Restart-Computer cmdlet에서 이러한 값을 사용합니다.

	$Cred = Get-AutomationCredential "MyCredential"
	$Computer = Get-AutomationVariable "ComputerName"

	Restart-Computer -ComputerName $Computer  -Credential $Cred

[PSCredential 일반 매개 변수](http://technet.microsoft.com/library/jj129719.aspx)에 지정된 자격 증명으로 다른 컴퓨터에서 코드 블록을 실행할 수 있는 [InlineScript](automation-powershell-workflow.md#inline-script)를 활용할 수도 있습니다.


### Runbook 작성 및 테스트

Azure 자동화에서 Hybrid Runbook Worker용 Runbook을 편집할 수 있지만 편집기에서 Runbook을 테스트하려는 경우 어려움이 있을 수 있습니다. 로컬 리소스에 액세스하는 PowerShell 모듈을 Azure 자동화 환경에 설치하지 못할 수 있으며, 이 경우 테스트에 실패합니다. 필요한 모듈을 설치한 경우에는 Runbook이 실행되지만 완전한 테스트에 필요한 로컬 리소스에 액세스할 수 없습니다.

## Service Management Automation과의 관계

[SMA(Service Management Automation)](https://technet.microsoft.com/library/dn469260.aspx)는 Azure 자동화에서 지원하는 것과 동일한 Runbook을 로컬 데이터 센터에서 실행할 수 있도록 해주는 MAP(Microsoft Azure 팩)의 구성 요소입니다. Azure 자동화와 달리 SMA에는 Microsoft Azure 팩 관리 포털과 Runbook 및 SMA 구성을 유지할 데이터베이스를 포함하는 로컬 설치가 필요합니다. Azure 자동화는 클라우드에서 이러한 서비스를 제공하기 때문에 로컬 환경에서 Hybrid Runbook Worker를 유지 관리하기만 하면 됩니다.

기존 SMA 사용자는 [Hybrid Runbook Worker용 Runbook 만들기](#creating-runbooks-for-hybrid-runbook-worker)에 설명된 대로 리소스에 대한 자체 인증을 수행할 경우 Runbook을 Azure 자동화로 이동하여 변경 없이 Hybrid Runbook Worker에서 사용할 수 있습니다. SMA의 Runbook은 Runbook에 대한 인증을 제공할 수 있는 작업자 서버에서 서비스 계정의 컨텍스트로 실행됩니다.

다음 조건을 사용하여 Azure 자동화 Hybrid Runbook Worker와 Service Management Automation 중 어떤 것이 요구 사항에 보다 적합한지 결정할 수 있습니다.

- SMA에는 로컬 Runbook Worker에 에이전트를 설치하기만 하면 되는 Azure 자동화보다 많은 로컬 리소스 및 높은 유지 관리 비용이 드는 Microsoft Azure 팩의 로컬 설치가 필요합니다. Azure 자동화에서는 에이전트가 Operations Management Suite에 의해 관리되므로 유지 관리 비용이 절감됩니다.
- Azure 자동화는 해당 Runbook을 클라우드에 저장하여 온-프레미스 Hybrid Runbooks Worker에 전달합니다. 보안 정책에서 이 동작을 허용하지 않는 경우에는 SMA를 사용해야 합니다.
- Microsoft Azure 팩은 무료로 다운로드할 수 있지만 Azure 자동화에는 구독 요금이 발생할 수 있습니다.
- Azure 자동화 Hybrid Runbook Worker를 사용하면 Azure 자동화와 SMA를 둘 다 별도로 관리할 필요 없이 클라우드 리소스와 로컬 리소스의 Runbook을 한 곳에서 관리할 수 있습니다.
- Azure 자동화에는 SMA에서 사용할 수 없는 그래픽 작성 등의 고급 기능이 있습니다.


## 관련된 문서

- [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md)
- [Azure 자동화에서 Runbook 편집](https://msdn.microsoft.com/library/dn879137.aspx)
 

<!---HONumber=Nov15_HO4-->