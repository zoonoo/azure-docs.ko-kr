---
title: Azure 자동화 원하는 상태 구성(DSC) 문제 해결
description: 이 문서에는 DSC(Desired State Configuration) 문제 해결에 대한 정보가 제공됩니다.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8e0b874e0ce56d7b4ed9fce15b2d88503d6c49b7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309139"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Azure 자동화 원하는 상태 구성(DSC)의 문제 해결

이 문서에는 DSC(Desired State Configuration)에 관한 문제를 해결하는 방법에 대한 정보가 제공됩니다.

## <a name="diagnosing-an-issue"></a>문제 진단

Azure 상태 구성에서 구성을 컴파일하거나 배포하는 동안 오류가 있는 경우 문제를 진단하는 데 도움이 되는 몇 가지 단계는 다음과 같습니다.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 구성이 로컬 컴퓨터에서 성공적으로 컴파일되도록 합니다.

Azure 상태 구성은 PowerShell DSC에 빌드됩니다. [PowerShell DSC 문서에서](https://docs.microsoft.com/powershell/scripting/overview)DSC 언어 및 구문에 대한 설명서를 찾을 수 있습니다.

로컬 컴퓨터에서 DSC 구성을 컴파일하면 다음과 같은 일반적인 오류를 검색하고 해결할 수 있습니다.

   - 누락된 모듈
   - 구문 오류
   - 논리 오류

### <a name="2-view-dsc-logs-on-your-node"></a>2. 노드에서 DSC 로그 보기

구성이 성공적으로 컴파일되지만 노드에 적용될 때 실패하는 경우 DSC 로그에서 자세한 정보를 찾을 수 있습니다. 이러한 로그를 찾을 수 있는 위치에 대한 자세한 내용은 [DSC 이벤트 로그 위치를](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)참조하십시오.

[xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) 모듈은 DSC 로그의 자세한 정보를 구문 분석하는 데 도움이 될 수 있습니다. 지원 팀에 문의하는 경우 문제를 진단하기 위해 이러한 로그가 필요합니다.

안정 버전 모듈 설치에 있는 지침을 사용하여 로컬 컴퓨터에 xDscDiagnostics [모듈을](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)설치할 수 있습니다.

Azure 컴퓨터에 xDscDiagnostics 모듈을 설치하려면 [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)을 사용합니다. 실행 명령을 사용 하 여 [Windows VM에서 PowerShell 실행 스크립트에](../../virtual-machines/windows/run-command.md)있는 단계에 따라 포털에서 **명령** 실행 옵션을 사용할 수도 있습니다.

xDscDiagnostics 사용에 대한 자세한 내용은 [xDscDiagnostics 사용을 참조하여 DSC 로그를 분석합니다.](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs) 또한 [참조 xDsc진단 Cmdlet.](https://github.com/PowerShell/xDscDiagnostics#cmdlets)

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 노드 및 자동화 작업 영역에 모듈이 필요한지 확인

DSC는 노드에 설치된 모듈에 따라 다릅니다. Azure 자동화 상태 구성을 사용하는 경우 필수 모듈을 [가져오기 모듈에](../shared-resources/modules.md#importing-modules)나열된 단계를 사용하여 자동화 계정으로 가져옵니다. 구성은 특정 버전의 모듈에 종속될 수도 있습니다. 자세한 내용은 [문제 해결 모듈을](shared-resources.md#modules)참조하십시오.

## <a name="common-errors-when-working-with-dsc"></a>DSC로 작업할 때 일반적인 오류

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>시나리오: 특수 문자가 있는 구성은 포털에서 삭제할 수 없습니다.

#### <a name="issue"></a>문제

포털에서 DSC 구성을 삭제하려고 하면 다음과 같은 오류가 표시됩니다.

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>원인

이 오류는 해결될 임시 문제입니다.

#### <a name="resolution"></a>해결 방법

* Az Cmdlet "제거-AzAutomationDscConfiguration"를 사용하여 구성을 삭제합니다.
* 이 cmdlet에 대한 설명서는 아직 업데이트되지 않았습니다.  그때까지는 AzureRM 모듈에 대한 설명서를 참조하십시오.
  * [제거-AzureRm자동화DSC구성](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>시나리오: Dsc 에이전트를 등록하지 못했습니다.

#### <a name="issue"></a>문제

실행하거나 다른 `Set-DscLocalConfigurationManager` DSC cmdlet을 실행하려고 할 때 오류가 발생합니다.

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>원인

이 오류는 일반적으로 방화벽, 컴퓨터가 프록시 서버 뒤에 있거나 다른 네트워크 오류로 인해 발생합니다.

#### <a name="resolution"></a>해결 방법

컴퓨터가 Azure 자동화 DSC에 대한 적절한 끝점에 액세스할 수 있는지 확인하고 다시 시도하십시오. 필요한 포트 및 주소 목록은 [네트워크 계획](../automation-dsc-overview.md#network-planning) 참조

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>시나리오: 상태 보고서 반환 응답 코드 "무단"

#### <a name="issue"></a>문제

노드를 DSC(상태 구성)로 등록할 때 다음 오류 메시지 중 하나가 나타납니다.

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>원인

이 문제는 잘못되었거나 만료된 인증서로 인해 발생합니다.  자세한 내용은 [인증서 만료 및 재등록을](../automation-dsc-onboarding.md#re-registering-a-node)참조하십시오.

이 문제는 ***.azure-automation.net**에 대한 액세스를 허용하지 않는 프록시 구성으로 인해 발생할 수도 있습니다. 자세한 내용은 [개인 네트워크 구성을](../automation-dsc-overview.md#network-planning)참조하십시오. 

### <a name="resolution"></a>해결 방법

실패한 DSC 노드를 다시 등록하려면 아래 나열된 단계에 따라

먼저 다음 단계를 사용하여 노드등록을 취소합니다.

1. Azure 포털에서 **홈** -> **자동화 계정**-> {자동화 계정} -> 상태 **구성(DSC)**
2. "노드"를 클릭하고 문제가 있는 노드를 클릭합니다.
3. "등록 취소"를 클릭하여 노드등록을 취소합니다.

둘째, 노드에서 DSC 확장을 제거합니다.

1. Azure 포털에서 **홈** -> **가상 머신** -> {실패 노드} -> **확장**
2. "Microsoft.Powershell.DSC"를 클릭합니다.
3. "제거"를 클릭하여 PowerShell DSC 확장을 제거합니다.

셋째, 노드에서 불량 또는 만료된 모든 인증서를 제거합니다.

상승된 Powershell 프롬프트에서 실패한 노드에서 다음을 실행합니다.

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

마지막으로 다음 단계를 사용하여 실패한 노드를 다시 등록합니다.

1. Azure 포털에서 **홈** -> **자동화 계정** -> {자동화 계정} -> 상태 **구성(DSC)**
2. "노드"를 클릭합니다.
3. "추가" 버튼을 클릭합니다.
4. 실패한 노드를 선택합니다.
5. "연결"을 클릭하고 원하는 옵션을 선택합니다.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>시나리오: 노드가 "찾을 수 없음" 오류로 실패한 상태임

#### <a name="issue"></a>문제

노드에 **실패** 상태의 보고서가 있으며 오류가 포함되어 있습니다.

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>원인

이 오류는 일반적으로 노드가 노드 구성 이름(예: ABC.WebServer) 대신 구성 이름(예: ABC)에 할당된 경우에 발생합니다.

#### <a name="resolution"></a>해결 방법

* "구성 이름"이 아닌 "노드 구성 이름"으로 노드를 할당해야 합니다.
* Azure 포털 또는 PowerShell cmdlet을 사용하여 노드 구성을 노드에 할당할 수 있습니다.

  * Azure 포털을 사용하여 노드 구성을 노드에 할당하려면 **DSC 노드** 페이지를 연 다음 노드를 선택하고 **노드 구성** 단추 할당 단추를 클릭합니다.
  * PowerShell cmdlet을 사용하여 노드 구성을 노드에 할당하려면 **Set-AzureRmAutomationDscNode** cmdlet을 사용합니다.

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>시나리오: 구성을 컴파일할 때 노드 구성(MOF 파일)이 생성되지 않았습니다.

#### <a name="issue"></a>문제

DSC 컴파일 작업이 오류로 인해 중단됩니다.

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>원인

DSC 구성에서 **Node** 키워드 다음에 오는 식이 `$null`로 평가되면 노드 구성이 생성되지 않습니다.

#### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* 구성 정의의 **Node** 키워드 옆에 있는 표현식이 $null 평가하지 않는지 확인합니다.
* 구성을 컴파일할 때 ConfigurationData를 전달하는 경우 [ConfigurationData](../automation-dsc-compile.md)에서 구성에 필요한 값을 전달해야 합니다.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>시나리오: DSC 노드 보고서가 "진행 중" 상태로 중단됨

#### <a name="issue"></a>문제

DSC 에이전트 출력:

```error
No instance found with given property values
```

#### <a name="cause"></a>원인

WMF 버전을 업그레이드했고 WMI가 손상되었습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 [DSC에서 알려진 문제 및 제한](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) 사항 문서의 지침을 따르십시오.

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>시나리오: DSC 구성에서 자격 증명을 사용할 수 없음

#### <a name="issue"></a>문제

DSC 컴파일 작업이 오류로 인해 중단되었습니다.

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>원인

구성에서 자격 증명을 사용했지만 적절한 **ConfigurationData를** 제공하지 않아 각 노드 구성에 대해 **PSDscAllowPlainTextPassword를** true로 설정합니다.

#### <a name="resolution"></a>해결 방법

* 구성에 언급 된 각 노드 구성에 대 한 **true로 PSDsc허용플레인텍스트Password를** 설정 하려면 적절 한 **ConfigurationData에** 전달 해야 합니다. 자세한 내용은 [Azure 자동화 상태 구성의 DSC 구성 컴파일을](../automation-dsc-compile.md)참조하십시오.

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>시나리오: dsc 확장에서 온보딩, "실패 처리 확장" 오류

#### <a name="issue"></a>문제

DSC 확장을 사용하여 온보딩할 때 오류가 포함된 오류가 발생합니다.

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>원인

이 오류는 일반적으로 노드가 서비스에 존재하지 않는 노드 구성 이름을 할당할 때 발생합니다.

#### <a name="resolution"></a>해결 방법

* 서비스의 이름과 정확히 일치하는 노드 구성 이름으로 노드를 할당해야 합니다.
* 노드 구성 이름을 포함하지 않도록 선택할 수 있으며, 노드는 온보딩되지만 노드 구성은 할당되지 않습니다.

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>시나리오: PowerShell을 사용 하 여 노드를 등록 하 여 "하나 이상의 오류가 발생 했습니다" 오류가 반환 됩니다.

#### <a name="issue"></a>문제

또는 `Register-AzAutomationDSCNode` `Register-AzureRMAutomationDSCNode`를 사용하여 노드를 등록할 때 다음과 같은 오류가 나타납니다.

```error
One or more errors occurred.
```

#### <a name="cause"></a>원인

이 오류는 자동화 계정보다 별도의 구독에 있는 노드를 등록하려고 할 때 발생합니다.

#### <a name="resolution"></a>해결 방법

구독 간 노드는 별도의 클라우드 또는 온-프레미스에 있는 것처럼 처리합니다.

아래 단계에 따라 노드를 등록합니다.

* Windows - [온-프레미스 또는 Azure/AWS 이외의 클라우드에서 물리적/가상 Windows 컴퓨터](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux - [온-프레미스 또는 Azure 이외의 클라우드에서 물리적/가상 Linux 컴퓨터](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>시나리오: 오류 메시지 - "프로비저닝 실패"

#### <a name="issue"></a>문제

노드를 등록할 때 다음과 같은 오류가 표시됩니다.

```error
Provisioning has failed
```

#### <a name="cause"></a>원인

이 메시지는 노드와 Azure 간에 연결 문제가 있을 때 발생합니다.

#### <a name="resolution"></a>해결 방법

노드가 개인 가상 네트워크에 있는지 또는 Azure에 연결하는 다른 문제가 있는지 확인합니다.

자세한 내용은 [솔루션을 온보딩할 때 오류 해결을](onboarding.md)참조하세요.

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>시나리오: Linux에서 구성을 적용하면 일반적인 오류로 오류가 발생합니다.

#### <a name="issue"></a>문제

Linux에서 구성을 적용할 때 오류가 포함된 오류가 발생합니다.

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>원인

고객은 `/tmp` 위치가 `noexec`설정된 경우 현재 버전의 DSC가 구성을 적용하지 못하는 것을 확인했습니다.

#### <a name="resolution"></a>해결 방법

* 위치에서 `noexec` 옵션을 제거합니다. `/tmp`

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>시나리오: 겹치는 노드 구성 이름으로 인해 릴리스가 잘못 될 수 있음

#### <a name="issue"></a>문제

단일 구성 스크립트를 사용하여 여러 노드 구성을 생성하고 일부 노드 구성에 다른 구성의 하위 집합인 이름이 있는 경우 컴파일 서비스의 문제로 인해 잘못된 구성이 할당될 수 있습니다.  이 문제는 단일 스크립트를 사용하여 노드당 구성 데이터로 구성을 생성하는 경우에만 발생하며 문자열의 시작 부분에서 이름 중복이 발생하는 경우에만 발생합니다.

예를 들어 cmdlet을 사용하여 해시 테이블로 전달되는 노드 데이터를 기반으로 구성을 생성하기 위해 단일 구성 스크립트를 사용하고 노드 데이터에 "서버" 및 "1server"라는 서버가 포함된 경우를 예로 들 수 있습니다.

#### <a name="cause"></a>원인

컴파일 서비스에 대해 알려진 문제입니다.

#### <a name="resolution"></a>해결 방법

가장 좋은 해결 방법을 사용할 수 있는 것은 로컬 또는 CI/CD 파이프라인에서 컴파일하고 MOF 파일을 서비스에 직접 업로드하는 것입니다.  서비스의 컴파일이 필수 인 경우 다음 해결 방법은 컴파일 작업을 분할하여 이름에 겹치지 않도록하는 것입니다.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>시나리오: DSC 구성 업로드시 게이트웨이 시간 설정 오류

#### <a name="issue"></a>문제

DSC `GatewayTimeout` 구성을 업로드할 때 오류가 발생합니다. 

#### <a name="cause"></a>원인

컴파일하는 데 시간이 오래 걸리는 DSC 구성으로 인해 이 오류가 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

모든 호출에 대한 매개 변수를 명시적으로 포함시켜 `ModuleName` DSC `Import-DscResource` 구성을 더 빠르게 구문 분석할 수 있습니다. 자세한 내용은 [가져오기-DSCResource 를 참조하십시오.](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
