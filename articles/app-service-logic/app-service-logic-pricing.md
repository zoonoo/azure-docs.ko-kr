<properties 
	pageTitle="논리 앱 가격 책정 모델 | Microsoft Azure" 
	description="논리 앱에서 가격 책정 방식에 대한 세부 정보" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/27/2016"
	ms.author="klam"/>

# 논리 앱 가격 책정 모델

Azure Logic Apps을 사용하면 클라우드에서 통합 워크플로의 크기를 조정하고 실행할 수 있습니다. 논리 앱의 청구 및 가격 책정은 다음과 같습니다.

## 소비 가격

새로 만든 논리 앱에서는 소비 요금제를 사용합니다. 논리 앱 소비 가격 책정 모델로 사용한 양만큼만 요금을 지불합니다. 소비 요금제를 사용할 경우 논리 앱은 제한되지 않습니다. 논리 앱 인스턴스 실행 시 실행되는 모든 작업은 요금이 청구됩니다.

### 작업 실행이란 무엇인가요?

논리 앱 정의의 모든 단계는 작업(action)입니다. 여기에는 트리거, 조건과 같은 제어 흐름 단계, 범위, for each 루프, do until 루프, 커넥터와 네이티브 작업에 대한 호출이 포합됩니다. 트리거는 특정 이벤트가 발생할 때 논리 앱의 새 인스턴스를 인스턴스화하기 위해 설계된 특수한 작업입니다. 논리 앱을 측정하는 방식에 영향을 줄 수 있는 트리거의 다양한 동작이 있습니다.

-	**폴링 트리거** – 이 트리거는 논리 앱의 새 인스턴스를 만들기 위한 기준을 만족하는 메시지를 수신할 때까지 끝점을 지속적으로 폴링합니다. 논리 앱 디자이너의 트리거에서 폴링 간격을 구성할 수 있습니다. 각 폴링 요청은 논리 앱의 새 인스턴스를 만들지 않더라도 작업 실행으로 계산됩니다.

-	**웹후크 트리거** – 이 트리거는 클라이언트가 특정 끝점에서 요청을 보낼 때까지 대기합니다. 웹후크 끝점으로 전송된 각 요청은 작업 실행으로 계산됩니다. 요청 및 HTTP 웹후크 트리거는 둘 다 웹후크 트리거입니다.

-	**되풀이 트리거** – 이 트리거는 트리거에 구성된 되풀이 간격을 기반으로 논리 앱의 새 인스턴스를 만듭니다. 예를 들어 3일 또는 1분마다 실행하도록 되풀이 트리거를 구성할 수 있습니다.

트리거 기록 부분의 논리 앱 리소스 블레이드에서 트리거 실행을 볼 수 있습니다.

실행된 모든 작업은 성공 여부에 관계없이 작업 실행으로 측정됩니다. 조건이 충족되지 않아 건너뛴 작업 또는 논리 앱에서 완료하기 전에 종료되어 실행되지 않은 작업은 작업 실행으로 계산되지 않습니다.

루프 내에서 실행된 작업은 루프의 반복당 계산됩니다. 예를 들어 10개 항목 목록을 반복하는 for each 루프의 단일 작업은 목록에 있는 항목 수(10)에 루프에 있는 작업 수(1)를 곱한 값에 루프의 시작인 경우 1을 더하여 계산합니다. 이 예에서는 (10 * 1) + 1 = 11 작업 실행이 됩니다.

비활성화된 논리 앱은 인스턴스화된 새 인스턴스를 포함할 수 없으므로 비활성화된 기간 중에는 비용이 청구되지 않습니다. 논리 앱을 비활성화한 후에는 논리 앱이 완전히 비활성화되기 전에 인스턴스가 정지되는 데 약간의 시간이 걸릴 수 있습니다.

## 앱 서비스 계획

논리 앱을 만드는 데 앱 서비스 계획이 더 이상 필요하지 않습니다. 기존 논리 앱과 앱 서비스 계획도 참조할 수 있습니다. 앱 서비스 계획으로 이전에 만든 논리 앱은 선택한 계획에 따라 계속해서 이전처럼 작동하며 일일 실행 수를 초과하면 실행이 제한되며 작업 실행 측정기를 사용하여 요금이 청구되지 않습니다.

앱 서비스 계획 및 일일 허용된 작업 실행:

| |무료/공유/기본|표준|Premium|
|---|---|---|---|
|일일 작업 실행| 200|10000|50,000|

### 소비에서 앱 서비스 계획 가격 책정으로 변환

소비 논리 앱에 대한 앱 서비스 계획을 참조하기 위해 단순히 [아래의 PowerShell 스크립트를 실행](https://github.com/logicappsio/ConsumptionToAppServicePlan)할 수 있습니다. 먼저 [Azure PowerShell 도구](https://github.com/Azure/azure-powershell)를 설치했는지 확인합니다.

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
	[string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### 앱 서비스 계획 가격 책정에서 소비로 변환

연결된 앱 서비스 계획이 있는 논리 앱을 소비 모델로 변경하려면 논리 앱 정의에서 앱 서비스 계획에 대한 참조를 제거합니다. 이 작업은 PowerShell cmdlet을 호출하여 수행할 수 있습니다.

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## 가격

가격 책정 세부 정보는 [논리 앱 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요.

## 다음 단계

- [논리 앱 개요][whatis]
- [첫 번째 논리 앱 만들기][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0810_2016-->