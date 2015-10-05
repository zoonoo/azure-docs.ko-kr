<properties 
   pageTitle="Azure 자동화로 가상 컴퓨터 시작 및 중지 - PowerShell 워크플로 | Microsoft Azure"
   description="기존 가상 컴퓨터를 시작 또는 중지하기 위한 runbook을 포함하는 Azure 자동화 솔루션의 그래픽 버전"
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
   ms.date="09/17/2015"
   ms.author="bwren" />

# Azure 자동화 솔루션 - 가상 컴퓨터 시작 및 중지

Azure 자동화 솔루션은 기존 가상 컴퓨터를 시작하고 중지하는 Runbook을 포함합니다. 다음과 같은 경우에 이 솔루션을 사용할 수 있습니다.

- 사용자의 환경에서 수정하지 않고 Runbook을 사용합니다. 
- 사용자 지정 기능을 수행하도록 Runbook을 수정합니다.  
- 전체 솔루션의 일부로 다른 Runbook에서 Runbook을 호출합니다. 
- Runbook을 자습서로 사용하여 Runbook 작성 개념을 알아봅니다. 

> [AZURE.SELECTOR]
- [Graphical](automation-solution-startstopvm-graphical.md)
- [PowerShell Workflow](automation-solution-startstopvm-psworkflow.md)

이 솔루션의 PowerShell 워크플로 Runbook 버전입니다. [그래픽 Runbook](automation-solutions-startstopvm-graphical.md)을 사용하여 사용할 수도 있습니다.

## 솔루션 가져오기

이 솔루션은 다음 링크에서 다운로드할 수 있는 PowerShell 워크플로 Runbook으로 구성됩니다. 그래픽 Runbook에 대한 링크는 이 솔루션의 [그래픽 버전](automation-solutions-startstopvm-graphical.md)을 참조하세요.

| Runbook | 링크 | 형식 | 설명 |
|:---|:---|:---|:---|
| Start-AzureVMs | [Azure 클래식 VM 시작](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | PowerShell 워크플로 | Azure 구독의 모든 기존 가상 컴퓨터 또는 특정 서비스 이름의 모든 가상 컴퓨터를 시작합니다. |
| Stop-AzureVMs | [Azure 클래식 VM 중지](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | PowerShell 워크플로 | 자동화 계정의 모든 가상 컴퓨터 또는 특정 서비스 이름의 모든 가상 컴퓨터를 중지합니다. |


## 솔루션 설치

### 1\. Runbook 설치

Runbook을 다운로드한 후에 [Runbook 가져오기](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook)의 절차를 사용하여 가져올 수 있습니다.

### 2\. 설명 및 요구 사항 검토
Runbook은 설명 및 필수 자산을 포함하는 주석 처리된 도움말 텍스트를 포함합니다. 이 문서에서 동일한 정보를 얻을 수 있습니다.

### 3\. 자산 구성
Runbook은 적절한 값으로 생성하고 채워야 하는 다음 자산을 필요로 합니다.

| 자산 형식 | 자산 이름 | 설명 |
|:---|:---|:---|
| 자격 증명 | AzureCredential | Azure 구독에서 가상 컴퓨터를 시작 및 중지할 권한이 있는 계정에 대한 자격 증명을 포함합니다. **Add-AzureAccount** 활동의 **Credential** 매개 변수에 다른 자격 증명 자산을 지정할 수도 있습니다. |
| 변수 | AzureSubscriptionId | Azure 구독의 구독 ID를 포함합니다. |

## 솔루션 사용

### 매개 변수

각 Runbook에는 다음과 같은 매개 변수가 있습니다. 모든 필수 매개 변수에 대한 값을 제공해야 하며 요구 사항에 따라 다른 매개 변수에 대한 값을 선택적으로 제공할 수 있습니다.

| 매개 변수 | 형식 | 필수 | 설명 |
|:---|:---|:---|
| ServiceName | string | 아니요 | 값이 제공되면 해당 서비스 이름의 모든 가상 컴퓨터가 시작되거나 중지됩니다. 값이 제공되지 않으면 Azure 구독의 모든 기존 가상 컴퓨터가 시작되거나 중지됩니다. |
| AzureSubscriptionIdAssetName | string | 아니요 | Azure 구독의 구독 ID를 포함하는 [변수 자산](#installing-the-solution)의 이름을 포함합니다. 값을 지정하지 않으면 *AzureSubscriptionId*가 사용됩니다. |
| AzureCredentialAssetName | string | 아니요 | 사용할 Runbook에 대한 자격 증명을 포함하는 [자격 증명 자산](#installing-the-solution)의 이름을 포함합니다. 값을 지정하지 않으면 *AzureCredential*이 사용됩니다. |

### Runbook 시작

이 솔루션의 Runbook 중 하나를 시작하도록 [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md)의 메서드를 사용할 수 있습니다.

다음 샘플 명령은 서비스 이름이 *MyVMService*인 모든 가상 컴퓨터를 시작하기 위하여 **StartAzureVMs**을 실행하도록 Windows PowerShell을 사용합니다.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### 출력

Runbook은 각각의 가상 컴퓨터에 대해 시작 또는 중지 명령이 성공적으로 제출되었는지 여부를 나타내는 [메시지를 출력](automation-runbook-output-and-messages.md)합니다. 출력물에서 특정 문자열을 찾아서 각 Runbook에 대한 결과를 판단할 수 있습니다. 가능한 출력 문자열이 다음 테이블에 나열되어 있습니다.

| Runbook | 조건 | Message |
|:---|:---|:---|
| Start-AzureVMs | 가상 컴퓨터 이미 실행 중 | MyVM 이미 실행 중 |
| Start-AzureVMs | 가상 컴퓨터에 대한 시작 요청이 성공적으로 제출됨 | MyVM 시작됨 |
| Start-AzureVMs | 가상 컴퓨터에 대한 시작 요청 실패 | MyVM 시작 실패 |
| Stop-AzureVMs | 가상 컴퓨터 이미 실행 중 | MyVM 이미 중지됨 |
| Stop-AzureVMs | 가상 컴퓨터에 대한 시작 요청이 성공적으로 제출됨 | MyVM 시작됨 |
| Stop-AzureVMs | 가상 컴퓨터에 대한 시작 요청 실패 | MyVM 시작 실패 |

예를 들어 Runbook의 다음 코드 조각은 서비스 이름이 *MyServiceName*인 모든 가상 컴퓨터를 시작하려고 합니다. 실패하는 시작 요청이 있으면 오류 작업에 착수할 수 있습니다.

	$results = Start-AzureVMs -ServiceName "MyServiceName"
	foreach ($result in $results) {
		if ($result -like "* has been started" ) {
			# Action to take in case of success.
		}
		else {
			# Action to take in case of error.
		}
	}


## 자세한 분석

다음은 이 솔루션의 Runbook에 대한 자세한 분석입니다. 이 정보를 사용하여 Runbook을 사용자 지정하거나 학습에 활용할 수 있습니다.

### 매개 변수

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

워크플로는 [입력 매개 변수](#using-the-solution)의 값을 가져오면서 시작됩니다. 자산 이름이 제공되지 않으면 기본 이름이 사용됩니다.

### 출력

    # Returns strings with status messages
    [OutputType([String])]

이 줄은 Runbook의 출력이 문자열임을 선언합니다. 꼭 필요하지는 않지만 Runbook이 [자식 Runbook](automation-child-runbooks.md)으로 사용되는 경우 부모 Runbook이 예상 출력 형식을 알 수 있도록 하기 위한 모범 사례입니다.

### 인증

	# Connect to Azure and select the subscription to work against
	$Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
	$null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
	$SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

다음 줄은 Runbook 나머지 부분에서 사용될 Azure 구독 및 [자격 증명](automation-configuring.md#configuring-authentication-to-azure-resources)을 설정합니다. 우선 Azure 구독에서 가상 컴퓨터를 시작 및 중지하기 위해 자격 증명을 포함하는 자산을 가져오도록 **Get-AutomationPSCredential**을 사용합니다. 그러면 **Add-AzureAccount**에서 이 자산을 사용하여 자격 증명을 설정합니다. 출력은 Runbook 출력에 포함되지 않도록 더미 변수에 할당됩니다.

**Get-AutomationVariable**으로 구독 ID를 포함하는 변수 자산이 검색되며 **Select-AzureSubscription**으로 구독 세트가 검색됩니다.

### VM 가져오기

	# If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
	{ 
		$VMs = Get-AzureVM -ServiceName $ServiceName
	}
    else 
	{ 
		$VMs = Get-AzureVM
	}

Runbook과 작업할 가상 컴퓨터를 검색하도록 **Get-AzureVM**이 사용됩니다. **ServiceName** 입력 변수에 값이 제공되면 해당 서비스 이름을 갖는 가상 컴퓨터만 검색됩니다. **ServiceName**을 비워두면 모든 가상 컴퓨터가 검색됩니다.

### 가상 컴퓨터 시작/중지 및 출력 보내기

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
		if ($VM.PowerState -eq "Started")
		{
			# The VM is already started, so send notice
			Write-Output ($VM.InstanceName + " is already running")
		}
		else
		{
			# The VM needs to be started
        	$StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

	        if ($StartRtn.OperationStatus -ne 'Succeeded')
	        {
				# The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
	        }
			else
			{
				# The VM started, so send notice
				Write-Output ($VM.InstanceName + " has been started")
			}
		}
    }

다음 줄은 각각의 가상 컴퓨터에 적용됩니다. 우선 가상 컴퓨터가 실행 중인지, 중지되었는지(Runbook에 따라서) 파악하기 위해 가상 컴퓨터의 **PowerState**를 확인합니다. 이미 대상 상태이면 메시지가 출력을 위해 전송되고 runbook은 종료됩니다. 그렇지 않으면 변수에 저장된 요청의 결과를 사용하여 가상 컴퓨터를 시작 또는 중지하기 위해 **Start-AzureVM** 또는 **Stop-AzureVM**이 사용됩니다. 그러면 시작 또는 중지 요청이 성공적으로 제출되었는지를 알리는 메시지가 출력을 위해 전송됩니다.


## 관련된 문서

- [Azure 자동화의 자식 runbook](automation-child-runbooks.md) 
- [Azure 자동화에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)

<!---HONumber=Sept15_HO4-->