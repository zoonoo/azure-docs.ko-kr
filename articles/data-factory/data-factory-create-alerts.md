<properties 
	pageTitle="Azure Data Factory 이벤트에 대한 경고를 생성합니다." 
	description="데이터 팩터리 작업에 대해 Azure에서 발생한 이벤트에 대한 경고를 만들 수 있는 방법에 대해 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Azure 이벤트 경고 만들기
Azure 이벤트는 Azure 리소스에서 일어나는 일에 대한 유용한 통찰력을 제공합니다. Azure는 Azure 리소스(예: 데이터 팩터리)가 생성, 업데이트 또는 삭제될 때 사용자 이벤트를 기록합니다. Azure Data Factory를 사용하면 다음의 경우에 이벤트가 생성됩니다.
 
1.	Azure Data Factory가 생성/업데이트/삭제됩니다.
2.	데이터 처리(실행이라고 하는)가 시작/완료되었습니다.
3.	주문형 HDInsight 클러스터가 생성되고 제거되는 경우.

이러한 사용자 이벤트에 대한 경고를 만들고 구독의 관리자 및 공동 관리자에게 메일 알림을 보내도록 구성할 수 있습니다. 또한 조건이 충족되는 경우 전자 메일 알림을 수신해야 하는 사용자의 추가 메일 주소를 지정할 수 있습니다.

## 경고 정의 지정
경고 정의를 지정하려면, 경고를 표시하려는 작업을 설명하는 JSON 파일로 만들 수 있습니다. 아래 예제에서 경고는 **RunFinished** 작업에 대한 전자 메일 알림을 보냅니다. 구체적으로 데이터 팩터리에서 실행이 완료되고 실행이 실패한 경우(상태 = FailedExecution) 전자 메일 알림이 전송됩니다.

	{
    	"contentVersion": "1.0.0.0",
   		 "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    	"parameters": {},
    	"resources": 
		[
        	{
            	"name": "ADFAlertsSlice",
            	"type": "microsoft.insights/alertrules",
            	"apiVersion": "2014-04-01",
            	"location": "East US",
            	"properties": 
				{
                	"name": "ADFAlertsSlice",
                	"description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                	"isEnabled": true,
                	"condition": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    	"dataSource": 
						{
                        	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        	"operationName": "RunFinished",
			         		"status": "Failed",
                        		"subStatus": "FailedExecution"   
                    	}
                	},
                	"action": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    	"customEmails": [ "<your alias>@contoso.com" ]
                	}
            	}
        	}
    	]
	}

특정 오류에 대한 경고를 표시하지 않으려면 위의 JSON 정의에서 **subStatus**를 제거할 수 있습니다.

작업 및 상태(및 하위 상태)의 목록은 [사용 가능한 작업 및 상태](#AvailableOperationsStatuses)를 참조하세요.

## 경고 배포
경고를 배포하려면 다음 예제와 같이 Azure PowerShell cmdlet, **New-AzureResourceGroupDeployment**를 사용합니다.

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

StorageAccountName은 배포된 경고 JSON 파일을 저장하기 위한 저장소 계정을 지정합니다.

리소스 그룹 배포가 성공적으로 완료되면 다음 메시지가 표시됩니다.
	
	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## Azure 리소스 그룹 배포의 목록 검색
배포된 Azure 리소스 그룹 배포의 목록을 검색하려면 다음 예와 같이 cmdlet, **Get-AzureResourceGroupDeployment**를 사용합니다.
	
	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## <a name="AvailableOperationsStatuses"></a>사용 가능한 작업 이름 및 상태 값

| 작업 이름 | 상태 | Sub Status |
| -------------- | ------ | ---------- |
| RunStarted | 시작 | Starting |
| RunFinished | 실패/성공 |	<p>FailedResourceAllocation </p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p>Canceled</p><p>FailedValidation</p><p>Abandoned</p> | 
| SliceOnTime | In Progress | Ontime |
| SliceDelayed | In Progress | Late |
| OnDemandClusterCreateStarted | 시작 | |
| OnDemandClusterCreateSuccessful | Succeeded | | 
| OnDemandClusterDeleted | Succeeded | |


## 사용자 이벤트 문제 해결
다음 명령을 실행하여 생성된 이벤트를 봅니다.

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"
 

<!---HONumber=July15_HO4-->