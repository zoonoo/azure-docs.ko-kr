<properties 
	pageTitle="가상 컴퓨터 시작 및 중지 - Graph | Microsoft Azure"
	description="기존 가상 컴퓨터를 시작 또는 중지하기 위한 runbook을 포함하는 Azure 자동화 시나리오의 PowerShell 워크플로 버전"
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="jwhit"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="07/06/2016"
	ms.author="bwren" />

# Azure 자동화 시나리오 - 가상 컴퓨터 시작 및 중지

Azure 자동화 시나리오는 기존 가상 컴퓨터를 시작하고 중지하는 Runbook을 포함합니다. 다음과 같은 경우에 이 시나리오를 사용할 수 있습니다.

- 사용자의 환경에서 수정하지 않고 Runbook을 사용합니다.
- 사용자 지정 기능을 수행하도록 Runbook을 수정합니다.
- 전체 솔루션의 일부로 다른 Runbook에서 Runbook을 호출합니다.
- Runbook을 자습서로 사용하여 Runbook 작성 개념을 알아봅니다.

> [AZURE.SELECTOR]
- [그래픽](automation-solution-startstopvm-graphical.md)
- [PowerShell 워크플로](automation-solution-startstopvm-psworkflow.md)

이것은 이 시나리오의 그래픽 Runbook 버전입니다. [PowerShell 워크플로 Runbook](automation-solution-startstopvm-psworkflow.md)을 사용하여 사용할 수도 있습니다.

## 시나리오 가져오기

이 시나리오는 다음 링크에서 다운로드할 수 있는 두 개의 그래픽 Runbook으로 구성됩니다. PowerShell 워크플로 Runbook에 대한 링크는 이 시나리오의 [PowerShell 워크플로 버전](automation-solution-startstopvm-psworkflow.md)을 참조하세요.


| Runbook | 링크 | 형식 | 설명 |
|:---|:---|:---|:---|
| StartAzureClassicVM | [Azure 클래식 VM 그래픽 Runbook 시작](https://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Classic-VM-c6067b3d) | 그래픽 | Azure 구독의 모든 기존 가상 컴퓨터 또는 특정 서비스의 모든 가상 컴퓨터를 시작합니다. |
| StopAzureClassicVM | [Azure 클래식 VM 그래픽 Runbook 중지](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-Classic-VM-397819bd) | 그래픽 | 자동화 계정의 모든 가상 컴퓨터 또는 특정 서비스 이름의 모든 가상 컴퓨터를 중지합니다. |


## 시나리오 설치 및 구성

### 1\. Runbook 설치

Runbook을 다운로드한 후에 [그래픽 Runbook 절차](automation-graphical-authoring-intro.md#graphical-runbook-procedures)의 절차를 사용하여 Runbook을 가져올 수 있습니다.

### 2\. 설명 및 요구 사항 검토
Runbook은 설명 및 필수 자산을 포함하는 **Read Me**라고 하는 활동을 포함합니다. **Read Me** 활동 및 **Workflow Script** 매개 변수를 선택하면 이 정보를 볼 수 있습니다. 이 문서에서 동일한 정보를 얻을 수 있습니다.

### 3\. 자산 구성
Runbook은 적절한 값으로 생성하고 채워야 하는 다음 자산을 필요로 합니다. 이름은 기본값입니다. Runbook을 시작할 때 [입력 매개 변수](#using-the-runbooks)에 이름을 지정한 경우 다른 이름의 자산을 사용할 수 있습니다.

| 자산 형식 | 기본 이름 | 설명 |
|:---|:---|:---|:---|
| [자격 증명](automation-credentials.md) | AzureCredential | Azure 구독에서 가상 컴퓨터를 시작 및 중지할 권한이 있는 계정에 대한 자격 증명을 포함합니다. |
| [변수](automation-variables.md) | AzureSubscriptionId | Azure 구독의 구독 ID를 포함합니다. |

## 시나리오 사용

### 매개 변수

각각의 Runbook에는 다음과 같은 [입력 매개 변수](automation-starting-a-runbook.md#runbook-parameters)가 있습니다. 모든 필수 매개 변수에 대한 값을 제공해야 하며 요구 사항에 따라 다른 매개 변수에 대한 값을 선택적으로 제공할 수 있습니다.

| 매개 변수 | 형식 | 필수 | 설명 |
|:---|:---|:---|:---|
| ServiceName | string | 아니요 | 값이 제공되면 해당 서비스 이름의 모든 가상 컴퓨터가 시작되거나 중지됩니다. 값이 제공되지 않으면 Azure 구독의 모든 기존 가상 컴퓨터가 시작되거나 중지됩니다. |
| AzureSubscriptionIdAssetName | string | 아니요 | Azure 구독의 구독 ID를 포함하는 [변수 자산](#installing-and-configuring-the-scenario)의 이름을 포함합니다. 값을 지정하지 않으면 *AzureSubscriptionId*가 사용됩니다. |
| AzureCredentialAssetName | string | 아니요 | 사용할 Runbook에 대한 자격 증명을 포함하는 [자격 증명 자산](#installing-and-configuring-the-scenario)의 이름을 포함합니다. 값을 지정하지 않으면 *AzureCredential*이 사용됩니다. |

### Runbook 시작

이 문서의 Runbook 중 하나를 시작하도록 [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md)의 메서드를 사용할 수 있습니다.

다음 샘플 명령은 서비스 이름이 *MyVMService*인 모든 가상 컴퓨터를 시작하기 위하여 **StartAzureClassicVM**을 실행하도록 Windows PowerShell을 사용합니다.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "StartAzureClassicVM" –Parameters $params

### 출력

Runbook은 각각의 가상 컴퓨터에 대해 시작 또는 중지 명령이 성공적으로 제출되었는지 여부를 나타내는 [메시지를 출력](automation-runbook-output-and-messages.md)합니다. 출력물에서 특정 문자열을 찾아서 각 Runbook에 대한 결과를 판단할 수 있습니다. 가능한 출력 문자열이 다음 테이블에 나열되어 있습니다.

| Runbook | 조건 | Message |
|:---|:---|:---|
| StartAzureClassicVM | 가상 컴퓨터 이미 실행 중 | MyVM 이미 실행 중 |
| StartAzureClassicVM | 가상 컴퓨터에 대한 시작 요청이 성공적으로 제출됨 | MyVM 시작됨 |
| StartAzureClassicVM | 가상 컴퓨터에 대한 시작 요청 실패 | MyVM 시작 실패 |
| StopAzureClassicVM | 가상 컴퓨터 이미 실행 중 | MyVM 이미 중지됨 |
| StopAzureClassicVM | 가상 컴퓨터에 대한 시작 요청이 성공적으로 제출됨 | MyVM 시작됨 |
| StopAzureClassicVM | 가상 컴퓨터에 대한 시작 요청 실패 | MyVM 시작 실패 |


다음은 샘플 그래픽 Runbook에서 [자식 Runbook](automation-child-runbooks.md)의 하나로 **StartAzureClassicVM**을 사용하는 이미지입니다. 다음 테이블의 조건부 링크를 사용합니다.

| 링크 | 조건 |
|:---|:---|
| 성공 링크 | $ActivityOutput['StartAzureClassicVM'] -like "* 시작됨" |
| 오류 링크 | $ActivityOutput['StartAzureClassicVM'] -notlike "* 시작됨" |

![자식 runbook 예제](media/automation-solution-startstopvm/graphical-childrunbook-example.png)


## 자세한 분석

다음은 이 시나리오의 Runbook에 대한 자세한 분석입니다. 이 정보를 사용하여 Runbook을 사용자 지정하거나 자체 자동화 시나리오를 작성하기 위한 학습에 활용할 수 있습니다.
 

### 인증

![인증](media/automation-solution-startstopvm/graphical-authentication.png)

Runbook은 [자격 증명](automation-configuring.md#configuring-authentication-to-azure-resources) 및 나머지 Runbook에 사용되는 Azure 구독을 설정하는 활동으로 시작됩니다.

처음 두 활동 즉, **Get Subscription Id**(구독 ID 가져오기) 및 **Get Azure Credential**(Azure 자격 증명 가져오기)는 다음 두 활동에서 사용되는 [자산](#installing-the-runbook)을 검색합니다. 이들 활동은 자산을 직접 지정할 수 있지만 자산 이름이 필요합니다. 이들 이름을 [입력 매개 변수](#using-the-runbooks)에 지정할 수 있도록 허용하기 때문에 입력 매개 변수로 지정된 이름의 자산을 활동을 통해 검색하도록 해야 합니다.

**Add-AzureAccount**는 나머지 runbook에 사용될 자격 증명을 설정합니다. **Get Azure Credential**(Azure 자격 증명 가져오기)에서 검색하는 자격 증명 자산은 Azure 구독에서 가상 컴퓨터를 시작 및 중지시킬 액세스 권한이 있어야 합니다. 사용되는 구독은 **Get Subscription Id**(구독 ID 가져오기)의 구독 ID를 사용하는 **Select-AzureSubscription**에 의해 선택됩니다.

### 가상 컴퓨터 가져오기

![VM 가져오기](media/automation-solution-startstopvm/graphical-getvms.png)

Runbook은 함께 작업할 가상 컴퓨터를 결정하고 컴퓨터가 이미 시작 또는 중지되었는지(runbook에 따라서) 판단해야 합니다. 둘 중 하나의 활동은 VM을 검색합니다. Runbook에 대한 *ServiceName* 입력 매개 변수에 값이 포함되면 **Get VMs in Service**(서비스의 VM 가져오기)가 실행됩니다. Runbook에 대한 *ServiceName* 입력 매개 변수에 값이 포함되지 않으면 **Get All VMs**(모든 VM 가져오기)가 실행됩니다. 이 논리는 각 활동 앞의 조건부 링크에 의해 수행됩니다.

두 활동 모두 **Get-AzureVM** cmdlet을 사용합니다. **Get All VMs**(모든 VM 가져오기)는 **ListAllVMs** 매개 변수 집합을 사용하여 모든 가상 컴퓨터를 반환합니다. **Get VMs in Service**(서비스의 VM 가져오기)는 **GetVMByServiceAndVMName** 매개 변수 집합을 사용하며 **ServiceName** 매개 변수에 대한 **ServiceName** 입력 매개 변수를 제공합니다.

### VM 병합

![VM 병합](media/automation-solution-startstopvm/graphical-mergevms.png)

**Merge VMs**(VM 병합) 활동은 시작을 위해 VM의 서비스 이름과 이름을 필요로 하는 **Start-AzureVM**에 입력을 제공하는데 필요합니다. 입력은 **Get All VMs**(모든 VM 가져오기) 또는 **Get VMs in Service**(서비스의 VM 가져오기)에서 가져올 수 있지만 **Start-AzureVM**은 입력에 대해 하나의 활동만 지정할 수 있습니다.

이 시나리오는 **Write-Output** cmdlet을 실행하는 **Merge VM**(VM 병합)을 만드는 것입니다. 이 cmdlet에 대한 **InputObject** 매개 변수는 이전 두 개 활동의 입력을 결합하는 PowerShell 식입니다. 이들 활동 중 하나만 실행되기 때문에 하나의 출력 세트만 예상됩니다. **Start-AzureVM**은 입력 매개 변수에 이 출력을 사용할 수 있습니다.

### 가상 컴퓨터 시작/중지

![VM 시작](media/automation-solution-startstopvm/graphical-startvm.png) ![VM 중지](media/automation-solution-startstopvm/graphical-stopvm.png)

Runbook에 따라서 다음 활동은 **Start-AzureVM** 또는 **Stop-AzureVM**을 사용하여 Runbook을 시작 또는 중지하려 시도합니다. 활동은 파이프라인 링크로 시작되기 때문에 **Merge VMs**(VM 병합)에서 반환되는 각각 개체에 대해 한 번 실행됩니다. 링크는 조건부입니다. 즉 활동은 가상 컴퓨터의 *RunningState*가 **Start-AzureVM**에 대해 *중지*되고 **Stop-AzureVM**에 대해 *시작*된 경우에만 실행됩니다. 조건이 충족되지 않으면 **Write-Output**을 사용하여 메시지를 전송하도록 **Notify Already Started**(이미 시작됨 알림) 또는 **Notify Already Stopped**(이미 중지됨 알림)가 실행됩니다.

### 출력 보내기

![VM 시작 알림](media/automation-solution-startstopvm/graphical-notifystart.png) ![VM 중지 알림](media/automation-solution-startstopvm/graphical-notifystop.png)

Runbook의 마지막 단계는 각 가상 컴퓨터에 대한 시작 또는 중지 요청이 성공적으로 제출되었는지에 대한 출력을 보내는 것입니다. 각각에 대해 개별적인 **Write-Output** 활동이 있으며 조건부 링크로 무엇을 실행할지를 결정합니다. *OperationStatus*가 *성공*하면 **Notify VM Started**(VM 시작 알림) 또는 **Notify VM Stopped**(VM 중지 알림)가 실행됩니다. *OperationStatus*가 그 밖의 값이면 **Notify Failed To Start**(시작 실패 알림) 또는 **Notify Failed to Stop**(중지 실패 알림)이 실행됩니다.


## 다음 단계

- [Azure 자동화에서 그래픽 작성](automation-graphical-authoring-intro.md)
- [Azure 자동화의 자식 runbook](automation-child-runbooks.md)
- [Azure 자동화에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0713_2016-->