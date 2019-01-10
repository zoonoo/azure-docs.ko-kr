---
title: Log Analytics에서 Azure로 경고 확장
description: 이 아티클에서는 Log Analytics에서 Azure Alerts로 경고를 확장할 수 있는 도구 및 API를 설명합니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: bdc3646116dfd5f16c0c039c4fb95d11c6593adf
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120996"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Log Analytics에서 Azure Alerts로 경고 확장
Azure Log Analytics에서 경고 기능은 Azure Alerts로 바뀝니다. 이 전환의 일부로 Log Analytics에서 원래 구성된 경고를 Azure로 확장합니다. 해당 경고가 Azure로 자동으로 이동하는 것을 기다리지 않으려면 다음 프로세스를 시작하면 됩니다.

- Operations Management Suite 포털에서 수동으로 
- AlertsVersion API를 사용하여 프로그래밍 방식으로  

> [!NOTE]
> Microsoft는 완료될 때까지 시리즈를 반복하면서 2018년 5월 14일부터 Log Analytics의 퍼블릭 클라우드 인스턴스에서 만든 경고를 자동으로 Azure Alerts로 확장합니다. [작업 그룹](../../azure-monitor/platform/action-groups.md)을 만드는 데 문제가 있는 경우 [이러한 재구성 단계](alerts-extend-tool.md#troubleshooting)를 사용하여 만든 작업 그룹을 자동으로 가져옵니다. 2018년 7월 5일까지는 이러한 단계를 사용할 수 있습니다. *Log Analytics의 Azure Government 및 소버린 클라우드 사용자에 대해서는 적용할 수 없습니다*. 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>옵션 1: Operations Management Suite 포털에서 시작
다음 단계에서는 Operations Management Suite 포털에서 작업 영역에 대한 경고를 확장하는 방법을 설명합니다.  

1. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
2. Log Analytics 구독 창에서 작업 영역을 선택한 다음, **OMS 포털** 타일을 선택합니다.
![OMS 포털 타일이 강조 표시된 Log Analytics 구독 창의 스크린샷](media/alerts-extend-tool/azure-portal-01.png) 
3. Operations Management Suite 포털로 리디렉션된 후에 **설정** 아이콘을 선택합니다.
![설정 아이콘이 강조 표시된 Operations Management Suite 포털의 스크린샷](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. **설정** 페이지에서 **경고**를 선택합니다.  
5. **Azure로 확장**을 선택합니다.
![Azure로 확장이 강조 표시된 Operations Management Suite 포털 경고 설정 페이지의 스크린샷](media/alerts-extend-tool/ExtendInto.png)
6. 3단계 마법사는 **경고** 창에 표시됩니다. 개요를 읽고 **다음**을 선택합니다.
![마법사의 1단계 스크린샷](media/alerts-extend-tool/ExtendStep1.png)  
7. 두 번째 단계에서 제안된 변경 사항의 요약을 표시하여 경고에 대해 적당한 [작업 그룹](../../azure-monitor/platform/action-groups.md)을 나열합니다. 둘 이상의 경고에 유사한 작업이 표시되면 마법사에서는 모든 작업을 단일 작업 그룹으로 연결하도록 제안합니다.  명명 규칙은 다음과 같습니다. *WorkspaceName_AG_#Number*. 계속하려면 **다음**을 선택합니다.
![마법사의 2단계 스크린샷](media/alerts-extend-tool/ExtendStep2.png)  
8. 마법사의 마지막 단계에서 **마침**을 선택하고 프로세스를 시작하라는 메시지가 나타나면 확인합니다. 필요에 따라 프로세스를 완료하고 모든 경고를 Azure 경고로 성공적으로 이동되면 알 수 있도록 이메일 주소를 제공할 수 있습니다.
![마법사의 3단계 스크린샷](media/alerts-extend-tool/ExtendStep3.png)

마법사가 완료되면 **경고 설정** 페이지에서 Azure에 경고를 확장하는 옵션이 제거됩니다. 백그라운드에서 경고를 Azure로 이동하는 데 다소 시간이 걸릴 수 있습니다. 작업 중에 Operations Management Suite 포털에서 경고를 변경할 수 없습니다. 포털의 위쪽에 있는 배너에서 현재 상태를 볼 수 있습니다. 이전에 이메일 주소를 제공했다면 프로세스가 성공적으로 완료되는 경우 이메일을 수신합니다.  


경고가 Azure에 성공적으로 이동된 후에도 계속 Operations Management Suite 포털에 나열됩니다.
![Operations Management Suite 포털 경고 설정 페이지의 스크린샷](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>옵션 2: AlertsVersion API 사용
REST API를 호출할 수 있는 Log Analytics에서 클라이언트의 Azure Alerts로 경고를 확장하는 데 Log Analytics AlertsVersion API를 사용할 수 있습니다. 오픈 소스 명령줄 도구인 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용하여 PowerShell에서 API에 액세스할 수 있습니다. JSON에서 결과를 출력할 수 있습니다.  

API를 사용하려면 먼저 GET 요청을 만듭니다. 그러면 실제로 POST 요청을 사용하여 Azure로 확장하려고 하기 전에 제안된 변경 내용의 요약을 계산하고 반환합니다. 결과는 JSON 형식으로 [작업 그룹](../../azure-monitor/platform/action-groups.md)의 경고 및 제안된 목록을 나열합니다. 둘 이상의 경고에 유사한 작업이 표시되면 서비스에서는 모든 작업을 단일 작업 그룹과 연결하도록 제안합니다. 명명 규칙은 다음과 같습니다. *WorkspaceName_AG_#Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

GET 요청에 성공한 경우 HTTP 상태 코드 200은 JSON 데이터에서 경고의 목록 및 제안된 작업 그룹과 함께 반환됩니다. 다음은 응답 예제입니다.

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
지정된 작업 영역에 경고 규칙이 정의되지 않으면 JSON 데이터는 다음을 반환합니다.

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

지정된 작업 영역의 모든 경고 규칙이 이미 Azure로 확장된 경우 GET 요청에 대한 응답은 다음과 같습니다.

```json
{
    "version": 2
}
```

경고를 Azure로 마이그레이션하기 시작하려면 POST 응답을 시작합니다. POST 응답은 승인뿐만 아니라 의도를 확인하여 경고가 Log Analytics에서 Azure Alerts로 확장되도록 합니다. 이전에 GET 응답을 수행한 경우 결과에 따라 표시된 대로 작업이 예약되고 경고가 처리됩니다. 필요에 따라 경고를 마이그레이션하는 예약된 백그라운드 프로세스가 성공적으로 완료되면 Log Analytics가 보고서를 보내는 이메일 주소 목록을 제공할 수 있습니다. 다음 요청 예제를 사용할 수 있습니다.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Azure Alerts에 경고를 마이그레이션하는 결과는 GET 응답에서 제공하는 요약에 따라 달라질 수 있습니다. 예약된 경우 Operations Management Suite 포털에서 수정하기 위해 Log Analytics의 경고는 일시적으로 사용할 수 없게 됩니다. 그러나 새 경고를 만들 수 있습니다. 

POST 요청에 성공한 경우 다음과 같은 응답과 함께 HTTP 200 확인 상태를 반환합니다.

```json
{
    "version": 2
}
```

이 응답에서는 경고가 Azure Alerts에 성공적으로 확장되었음을 나타냅니다. 이 버전 속성은 경고가 Azure로 확장되었는지만 확인하고 [Log Analytics 검색 API](../../azure-monitor/platform/api-alerts.md)와는 관련되지 않습니다. 경고를 성공적으로 Azure로 확장한 경우 POST 요청에서 제공된 이메일 주소가 보고서에 보내집니다. 지정된 작업 영역의 모든 경고가 Azure로 확장되도록 이미 예약된 경우 POST 요청에 대한 응답은 시도가 금지되었습니다(403 상태 코드)입니다. 오류 메시지를 보거나 프로세스가 멈췄는지 알아보려면 GET 요청을 제출할 수 있습니다. 오류 메시지가 없으면 요약 정보와 함께 반환됩니다.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```


## <a name="option-3-use-a-custom-powershell-script"></a>옵션 3: 사용자 지정 PowerShell 스크립트 사용
 Microsoft가 알림을 Operations Management Suite 포털에서 Azure로 성공적으로 확장하지 않은 경우 2018년 7월 5일까지 수동으로 수행할 수 있습니다. 수동 확장에 대한 두 가지 옵션은 이전의 두 섹션에서 다룹니다.

2018년 7월 5일부터 Operations Management Suite 포털의 모든 경고를 Azure로 확장합니다. [제안된 필요한 수정 단계](#troubleshooting)를 거치지 않은 사용자에게는 연결된 [작업 그룹](../../azure-monitor/platform/action-groups.md)의 부족으로 인해 작업 또는 알림을 발생시키지 않고 해당 경고를 실행합니다. 

Log Analytics에서 수동으로 [작업 그룹](../../azure-monitor/platform/action-groups.md) 경고를 만들면 다음 샘플 스크립트를 사용합니다.
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occurred while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>사용자 지정 PowerShell 스크립트 정보 
스크립트를 사용하는 방법에 대한 중요한 정보는 다음과 같습니다.
- 필수 구성 요소는 Azure Resource Manager API를 호출하여 간소화하는 오픈 소스 명령줄 도구인 [ARMClient](https://github.com/projectkudu/ARMClient)를 설치하는 것입니다.
- 스크립트를 실행하려면 Azure 구독의 기여자 또는 소유자 역할이 있어야 합니다.
- 다음과 같은 매개 변수를 제공해야 합니다.
    - $subscriptionId: Operations Management Suite Log Analytics 작업 영역과 연결된 Azure 구독 ID입니다.
    - $resourceGroup: Operations Management Suite Log Analytics 작업 영역에 대한 Azure 리소스 그룹입니다.
    - $workspaceName: Operations Management Suite Log Analytics 작업 영역의 이름입니다.

### <a name="output-of-the-custom-powershell-script"></a>사용자 지정 PowerShell 스크립트 출력
스크립트는 자세한 정보이며 실행되는 단계를 출력합니다. 
- 작업 영역의 기존 Operations Management Suite Log Analytics 경고에 대한 정보가 포함된 요약을 표시합니다. 요약에는 연결된 작업에 대해 생성될 Azure 작업 그룹에 대한 정보도 포함되어 있습니다. 
- 요약을 검토한 후에 확장을 계속하거나 종료할지를 묻는 메시지가 나타납니다.
- 확장을 계속 진행하는 경우 새 Azure 작업 그룹을 생성하고 모든 기존 경고를 연결합니다. 
- "확장 완료!" 메시지를 표시하여 스크립트가 종료됩니다. 중간 실패 시 스크립트는 후속 오류를 표시합니다.

## <a name="troubleshooting"></a>문제 해결 
경고를 확장하는 프로세스 중에 문제는 시스템에서 필요한 [작업 그룹](../../azure-monitor/platform/action-groups.md)을 만들지 않도록 방지할 수 있습니다. 이러한 경우에 Operations Management Suite 포털의 **경고** 섹션에 있는 배너 또는 API에 완료된 GET 호출에 오류 메시지가 표시됩니다.

> [!IMPORTANT]
> Azure 퍼블릭 클라우드 기반 Log Analytics 사용자가 2018년 7월 5일 전에 다음과 같은 재구성 단계를 취하지 않는 경우 경고는 Azure에서 실행되지만 작업 또는 알림이 발생하지 않습니다. 경고에 대한 알림을 가져오려면 [작업 그룹](../../azure-monitor/platform/action-groups.md)을 수동으로 편집하고 추가하거나 앞의 [사용자 지정 PowerShell 스크립트](#option-3---using-custom-powershell-script)를 사용해야 합니다.

각 오류에 대한 재구성 단계는 다음과 같습니다.
- **오류: 쓰기 작업에 대한 구독/리소스 그룹 수준에 범위 잠금이 있습니다**.   ![범위 잠금 오류 메시지가 강조 표시된 Operations Management Suite 포털 경고 설정 페이지 스크린샷](media/alerts-extend-tool/ErrorScopeLock.png)

    범위 잠금이 설정되면 기능은 Log Analytics(Operations Management Suite) 작업 영역을 포함하는 구독 또는 리소스 그룹의 새로운 변경 내용을 제한합니다. 시스템은 Azure에 경고를 확장하고 필요한 작업 그룹을 만들 수 없습니다.
    
    이를 해결하려면 작업 영역을 포함하는 구독 또는 리소스 그룹에 대해 *ReadOnly* 잠금을 삭제합니다. 이 작업은 Azure Portal, PowerShell, Azure CLI 또는 API를 사용하여 수행할 수 있습니다. 자세한 내용은 [리소스 잠금 사용량](../../azure-resource-manager/resource-group-lock-resources.md)을 참조하세요. 
    
    아티클에 표시된 단계를 사용하여 오류를 해결하는 경우 Operations Management Suite는 다음 날 예약된 실행 내에서 경고를 Azure로 확장합니다. 추가 작업을 수행하거나 어떤 작업도 시작할 필요가 없습니다.

- **오류: 정책이 구독/리소스 그룹 수준에 표시됩니다**.   ![정책 오류 메시지가 강조 표시된 Operations Management Suite 포털 경고 설정 페이지 스크린샷](media/alerts-extend-tool/ErrorPolicy.png)

    [Azure Policy](../../governance/policy/overview.md)가 적용되면 Log Analytics(Operations Management Suite) 작업 영역을 포함하는 구독 또는 리소스 그룹의 새로운 리소스를 제한합니다. 시스템은 Azure에 경고를 확장하고 필요한 작업 그룹을 만들 수 없습니다.
    
    이를 해결하려면 *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* 오류가 발생하는 정책을 편집합니다. 그러면 작업 영역이 포함된 구독 또는 리소스 그룹에 새 리소스를 만들 수 없도록 방지합니다. 이 작업은 Azure Portal, PowerShell, Azure CLI 또는 API를 사용하여 수행할 수 있습니다. 오류를 발생시키는 적절한 정책을 찾기 위해 작업을 감사할 수 있습니다. 자세한 내용은 [작업을 감사하기 위해 활동 로그 보기](../../azure-resource-manager/resource-group-audit.md)를 참조하세요. 
    
    아티클에 표시된 단계를 사용하여 오류를 해결하는 경우 Operations Management Suite는 다음 날 예약된 실행 내에서 경고를 Azure로 확장합니다. 추가 작업을 수행하거나 어떤 작업도 시작할 필요가 없습니다.


## <a name="next-steps"></a>다음 단계

* 새 [Azure Alerts 환경](../../azure-monitor/platform/alerts-overview.md)에 대해 자세히 알아봅니다.
* [Azure Alerts의 로그 경고](alerts-unified-log.md)에 대해 알아봅니다.
