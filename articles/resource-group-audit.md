<properties 
	pageTitle="리소스 관리자로 작업 감사 | Microsoft Azure" 
	description="리소스 관리자에서 감사 로그를 사용하여 사용자 작업 및 오류를 검토합니다. PowerShell, Azure CLI 및 REST를 보여 줍니다." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="tomfitz"/>

# 리소스 관리자로 작업 감사

배포 또는 솔루션의 수명 기간 동안 문제가 발생하면 무엇이 잘못되었는지 검색해야 합니다. 리소스 관리자에서는 두 가지 방법으로 무슨 일이 발생했는지와 발생 이유를 확인할 수 있습니다. 특정 배포 및 작업에 대한 정보를 검색하는 배포 명령을 사용할 수 있습니다. 또는 감사 로그를 사용하여 솔루션의 수명 동안 배포 및 수행한 다른 작업에 대한 정보를 검색할 수 있습니다. 이 항목에서는 감사 로그에 중점을 둡니다.

감사 로그는 리소스에서 수행된 모든 작업을 포함합니다. 따라서 조직에서 사용자가 리소스를 수정하는 경우 작업, 시간 및 사용자를 식별할 수 있습니다.

감사 로그를 작업할 때 염두에 두어야 할 두 가지 중요한 제한 사항이 있습니다.

1. 감사 로그는 90일 동안만 유지됩니다.
2. 15일 이하의 범위에 대한 쿼리만 수행할 수 있습니다.

Azure PowerShell, Azure CLI, REST API 또는 Azure 포털을 통해 감사 로그에서 정보를 검색할 수 있습니다.

## PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

로그 항목을 검색하려면 **Get-AzureRmLog** 명령(또는 1.0보다 이전 버전 PowerShell의 경우 **Get-AzureResourceGroupLog**)을 실행합니다. 항목의 목록을 필터링하는 추가 매개 변수를 제공합니다.

다음 예제에서는 솔루션의 수명 주기 동안 수행하는 작업을 조사하는 감사 로그를 사용하는 방법을 보여 줍니다. 작업이 발생한 시점과 요청한 사용자를 볼 수 있습니다. 시작 및 종료 날짜는 날짜 형식으로 지정됩니다.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

또는 날짜 기능을 사용하여 지난 15일 같은 날짜 범위를 지정할 수 있습니다.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15)

지정한 시작 시간에 따라 이전 명령은 해당 리소스 그룹에 대한 긴 목록 작업을 반환할 수 있습니다. 검색 조건을 제공하여 찾고자 하는 결과를 필터링할 수 있습니다. 예를 들어 웹앱이 중지된 방법을 조사하려는 경우 다음 명령을 실행하고 중지 작업이 someone@example.com(으)로 수행된 것을 볼 수 있습니다.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

다음 예제에서는 지정된 시작 시간 후에 실패된 작업을 확인합니다. 또한 **DetailedOutput** 매개 변수를 포함하여 오류 메시지를 참조합니다.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) -Status Failed –DetailedOutput
    
이 명령이 너무 많은 항목 및 속성을 반환하는 경우 **properties** 속성을 검색하여 감사에 집중할 수 있습니다.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

또한 상태 메시지를 보고 결과를 더욱 구체화할 수 있습니다.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Azure CLI

로그 항목을 검색하려면 **Azure 그룹 로그 표시** 명령을 실행합니다.

    azure group log show ExampleGroup

[jq](http://stedolan.github.io/jq/download/)와 같은 JSON 유틸리티로 결과 필터링할 수 있습니다. 다음 예제에서는 웹 구성 파일을 업데이트하는 작업을 찾는 방법을 보여 줍니다.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

**–-마지막-배포** 매개 변수를 추가하여 반환된 항목을 마지막 배포의 작업으로만 제한할 수 있습니다.

    azure group log show ExampleGroup --last-deployment

마지막 배포의 작업 목록이 너무 긴 경우 실패한 작업에 대한 결과만 필터링할 수 있습니다.

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == "Failed")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## REST API

감사 로그로 작업하기 위한 REST 작업은 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)의 일부입니다. 감사 로그 이벤트를 검색하려면 [구독에서 관리 이벤트 나열](https://msdn.microsoft.com/library/azure/dn931934.aspx)을 참조하세요.

## 포털

또한 포털을 통해 기록된 작업을 볼 수 있습니다. 감사 로그 블레이드를 선택하면 됩니다.

![감사 로그 선택](./media/resource-group-audit/select-audit.png)

또한 최신 작업의 목록을 확인합니다.

![작업 표시](./media/resource-group-audit/show-actions.png)

자세한 정보에 대한 모든 작업을 선택할 수 있습니다.

## 다음 단계

- 보안 정책 설정에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.
- 서비스 주체에게 액세스를 부여하는 방법에 대한 내용은 [Azure 리소스 관리자를 사용하여 서비스 사용자 인증](resource-group-authenticate-service-principal.md)을 참조하세요.
- 모든 사용자에 대한 리소스의 작업 방법에 대한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

<!---HONumber=AcomDC_1223_2015-->