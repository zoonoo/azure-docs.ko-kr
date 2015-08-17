<properties
   pageTitle="Azure 자동화에서 Runbook 실행"
   description="Azure 자동화의 Runbook이 처리되는 방법에 대한 자세한 내용을 설명합니다."
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
   ms.date="07/22/2015"
   ms.author="bwren" />

# Azure 자동화에서 Runbook 실행


Azure 자동화에서 Runbook을 시작하면 작업이 생성됩니다. 작업은 Runbook의 단일 실행 인스턴스입니다. 각 작업을 실행하기 위해 Azure 자동화 작업자가 할당됩니다. 작업자는 여러 Azure 계정에서 공유하지만 여러 자동화 계정의 작업은 서로 격리됩니다. 사용자는 작업에 대한 요청을 처리할 작업자를 제어할 수 없습니다. 단일 Runbook에서 동시에 여러 작업을 실행할 수 있습니다. Azure 포털에서 Runbook 목록을 확인하면 각 Runbook에 대해 시작된 마지막 작업의 상태가 나열됩니다. 각 Runbook에 대한 작업 목록을 확인하여 각 작업의 상태를 추적할 수 있습니다. 다양한 작업 상태에 대한 설명은 [작업 상태](#job-statuses)를 참조하세요.

![작업 상태](./media/automation-runbook-execution/job-statuses.png)


Azure 구독에 연결하면 작업에서 Azure 리소스에 액세스할 수 있습니다. 단, 공용 클라우드에서 액세스할 수 있는 데이터 센터의 리소스에만 액세스할 수 있습니다.

## 작업 상태

다음 표에서는 작업의 가능한 여러 상태를 설명합니다.

| 상태| 설명|
|:---|:---|
|Completed|작업이 완료되었습니다.|
|Failed|작업이 오류와 함께 종료되었습니다.|
|Failed, waiting for resources|작업이 [공평 분배](#fairshare) 한도에 세 번 도달했기 때문에 실패했고 매번 동일한 검사점 또는 Runbook의 처음부터 시작되었습니다.|
|Queued|작업이 시작될 수 있도록 자동화 작업자의 리소스가 사용 가능한 상태가 되기를 기다리고 있습니다.|
|Starting|작업이 작업자에게 할당되었으며 시스템이 시작하는 중입니다.|
|Resuming|시스템이 일시 중단된 후 작업을 다시 시작하는 중입니다.|
|실행 중|작업이 실행 중입니다.|
|Running, waiting for resources|작업이 [공평 분배](#fairshare) 한도에 도달했기 때문에 언로드되었습니다. 잠시 후 마지막 검사점에서 작업이 다시 시작됩니다.|
|중지|작업이 완료되기 전에 사용자에 의해 중지되었습니다.|
|중지 중|시스템이 작업을 중지하는 중입니다.|
|일시 중단|작업이 Runbook의 사용자, 시스템 또는 명령에 의해 일시 중단되었습니다. 일시 중단된 작업은 다시 시작할 수 있으며, 마지막 검사점에서 다시 시작되거나, 검사점이 없을 경우 Runbook의 처음부터 다시 시작됩니다. Runbook은 예외적인 경우에만 시스템에 의해 일시 중단됩니다. 기본적으로 ErrorActionPreference는 **Continue**로 설정되며, 이는 오류 발생 시 작업이 계속 실행된다는 의미입니다. 이 기본 설정 변수가 **Stop**으로 설정된 경우 오류 발생 시 작업이 일시 중단됩니다.|
|Suspending|시스템이 사용자의 요청에 따라 작업을 일시 중단하려고 합니다. Runbook의 다음 검사점에 도달해야만 Runbook을 일시 중단할 수 있습니다. 이미 마지막 검사점을 지난 경우 완료되어야만 일시 중단할 수 있습니다.|

## Azure 관리 포털을 사용하여 작업 상태 보기

### 자동화 대시보드

자동화 대시보드는 모든 특정 자동화 계정에 대 한 Runbook의 요약을 보여줍니다. 계정에 대한 사용 개요도 포함됩니다. 요약 그래프는 지정된 된 일수 또는 시간 동안 각 상태로 전환된 모든 Runbook의 총 작업 수를 보여줍니다. 그래프의 오른쪽 위 모퉁이에서 시간 범위를 선택할 수 있습니다. 차트의 시간 축은 선택된 시간 범위 유형에 따라 변경됩니다. 화면 위쪽에서 특정 상태를 클릭하여 해당 상태에 대한 줄을 표시할지 여부를 선택할 수 있습니다.

다음 단계를 수행하면 자동화 대시보드를 표시할 수 있습니다.

1. Azure 관리 포털에서 **자동화**를 선택한 다음 자동화 계정의 이름을 클릭합니다.
1. **대시보드** 탭을 선택합니다.

### Runbook 대시보드

Runbook 대시보드는 단일 Runbook에 대한 요약을 보여줍니다. 요약 그래프는 지정된 된 일수 또는 시간 동안 각 상태로 전환된 Runbook의 총 작업 수를 보여줍니다. 그래프의 오른쪽 위 모퉁이에서 시간 범위를 선택할 수 있습니다. 차트의 시간 축은 선택된 시간 범위 유형에 따라 변경됩니다. 화면 위쪽에서 특정 상태를 클릭하여 해당 상태에 대한 줄을 표시할지 여부를 선택할 수 있습니다.

다음 단계를 수행하면 Runbook 대시보드를 표시할 수 있습니다.

1. Azure 관리 포털에서 **자동화**를 선택한 다음 자동화 계정의 이름을 클릭합니다.
1. Runbook의 이름을 클릭합니다.
1. **대시보드** 탭을 선택합니다.

### 작업 요약

특정 Runbook에 대해 생성된 모든 작업 및 해당 작업의 가장 최근 상태가 나와 있는 목록을 볼 수 있습니다. 이 목록에서 작업 상태 및 날짜 범위를 필터링하면 작업의 마지막 변경 사항을 볼 수 있습니다. 작업의 이름을 클릭하면 세부 정보 및 출력을 볼 수 있습니다. 작업의 세부 보기에는 해당 작업에 제공된 Runbook 매개 변수의 값이 포함됩니다.

다음 단계를 수행하여 Runbook의 작업을 볼 수 있습니다.

1. Azure 관리 포털에서 **자동화**를 선택한 다음 자동화 계정의 이름을 클릭합니다.
1. Runbook의 이름을 클릭합니다.
1. **작업** 탭을 선택합니다.
1. 세부 정보 및 출력을 볼 작업의 **생성된 작업** 열을 클릭합니다.

## Windows PowerShell을 사용하여 작업 상태 검색

[Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx)을 사용하여 Runbook에 대해 생성된 작업 및 특정 작업을 검색할 수 있습니다. Windows PowerShell에서 [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx)을 사용하여 Runbook을 시작하는 경우 결과 작업이 반환됩니다. 작업의 출력을 얻으려면 [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690263.aspx)을 사용합니다.

다음 명령 예제는 샘플 Runbook에 대한 마지막 작업을 검색하고 작업의 상태, Runbook 매개 변수에 제공된 값, 작업의 출력을 표시합니다.

	$job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
	$job.Status
	$job.JobParameters
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## 공평 분배

Azure 자동화에서는 클라우드의 모든 Runbook 간에 리소스를 공유할 수 있도록 작업을 실행한 후 3시간 동안 작업을 일시적으로 언로드한 다음 마지막 [검사점](http://aka.ms/runbookauthor/checkpoints)에서 다시 시작합니다. 이 시간 동안 작업은 실행 중, 리소스 대기 상태로 표시됩니다. Runbook에 검사점이 없거나 작업이 언로드되기 전에 첫 번째 검사점에 도달하지 않은 경우에는 처음부터 다시 시작됩니다.

Runbook이 동일한 검사점 또는 Runbook의 처음부터 세 번 연속 다시 시작된 경우에는 실패함, 리소스 대기 상태로 종료됩니다. 이는 Runbook이 완료되지 않고 무기한적으로 실행되어 다시 언로드되지 않은 상태로 다음 검사점에 도달하지 못하도록 하기 위한 것입니다. 이 경우 오류와 함께 다음 예외가 발생합니다.

작업이 동일한 검사점에서 반복적으로 제거되었기 때문에 실행을 계속할 수 없습니다. Runbook이 해당 상태를 유지하지 않고 시간이 오래 걸리는 작업을 수행하지 않는지 확인하세요.

Runbook을 만들 때 두 검사점 간의 모든 활동을 실행할 시간을 3시간을 초과하지 않도록 해야 합니다. 이 3시간 제한에 도달하지 않도록 하기 위해 Runbook에 검사점을 추가해야 할 수도 있습니다. 또한 오래 실행되는 작업을 나누어야 할 수도 있습니다. 예를 들어 Runbook에서 대용량 SQL 데이터베이스의 인덱스를 다시 작성할 수 있습니다. 이 단일 작업이 공평 분배 제한 내에 완료되지 않으면 작업이 언로드되고 처음부터 다시 시작됩니다. 이 경우 다시 인덱싱 작업을 여러 단계로 나눈 다음(예: 한 번의 하나의 테이블을 다시 인덱싱하도록) 작업이 완료할 마지막 작업 이후에 다시 시작될 수 있도록 각 작업 뒤에 검사점을 삽입해야 합니다.



## 관련된 문서

- [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook)
- [Azure 자동화에서 Runbook 작업의 상태 보기](automation-viewing-the-status-of-a-runbook-job)
 

<!---HONumber=August15_HO6-->