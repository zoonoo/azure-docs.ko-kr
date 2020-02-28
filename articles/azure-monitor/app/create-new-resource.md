---
title: 새 Azure Application Insights 리소스 만들기 | Microsoft Docs
description: 새 라이브 애플리케이션에 대한 Application Insights 모니터링을 수동으로 설정합니다.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 090c983dcca101557f8dec479d1267275d24908b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672041"
---
# <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

Azure Application Insights는 Microsoft Azure *리소스*에 애플리케이션에 대한 데이터를 표시합니다. 따라서 새 리소스 만들기는 [새 응용 프로그램을 모니터링 하 Application Insights를 설정 하][start]는 과정에 포함 됩니다. 새 리소스를 만든 후에는 해당 계측 키를 가져오고이를 사용 하 여 Application Insights SDK를 구성할 수 있습니다. 계측 키는 원격 분석을 리소스에 연결 합니다.

## <a name="sign-in-to-microsoft-azure"></a>Microsoft Azure에 로그인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

[Azure Portal](https://portal.azure.com)에 로그인 하 고 Application Insights 리소스를 만듭니다.

![왼쪽 위 모서리에 있는 ' + ' 기호를 클릭 합니다. 개발자 도구 다음에 Application Insights를 선택 합니다.](./media/create-new-resource/new-app-insights.png)

   | 설정        |  값           | Description  |
   | ------------- |:-------------|:-----|
   | **이름**      | 고유 값 | 모니터링할 앱을 식별 하는 이름입니다. |
   | **리소스 그룹**     | myResourceGroup      | Application Insights 데이터를 호스트할 새 리소스 그룹 또는 기존 리소스 그룹의 이름입니다. |
   | **위치** | 미국 동부 | 가까운 위치 또는 앱이 호스트 되는 위치를 선택 합니다. |

> [!NOTE]
> 서로 다른 리소스 그룹에서 동일한 리소스 이름을 사용할 수 있지만 전역적으로 고유한 이름을 사용 하는 것이 유용할 수 있습니다. 이는 필요한 구문을 간소화 하므로 [리소스 간 쿼리를 수행할](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) 계획인 경우에 유용할 수 있습니다.

필요한 필드에 적절 한 값을 입력 한 다음 **검토 + 만들기**를 선택 합니다.

![필수 필드에 값을 입력 하 고 "검토 + 만들기"를 선택 합니다.](./media/create-new-resource/review-create.png)

앱이 만들어지면 새 창이 열립니다. 이 창에는 모니터링 되는 응용 프로그램에 대 한 성능 및 사용 데이터가 표시 됩니다. 

## <a name="copy-the-instrumentation-key"></a>계측 키 복사

계측 키는 원격 분석 데이터를 연결 하려는 리소스를 식별 합니다. 응용 프로그램의 코드에 계측 키를 추가 하려면 복사본이 필요 합니다.

![계측 키를 클릭 하 고 복사 합니다.](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>응용 프로그램에 SDK를 설치합니다.

Application Insights SDK를 애플리케이션에 설치합니다. 이 단계는 애플리케이션의 형식에 따라 크게 달라 집니다.

계측 키를 사용 하 여 [응용 프로그램에 설치한 SDK][start]를 구성 합니다.

SDK에는 추가 코드를 작성 하지 않고도 원격 분석을 전송 하는 표준 모듈이 포함 되어 있습니다. 사용자 작업을 추적 하거나 문제를 자세히 진단 하려면 [API를 사용][api] 하 여 사용자 고유의 원격 분석을 보냅니다.

## <a name="creating-a-resource-automatically"></a>자동으로 리소스 만들기

### <a name="powershell"></a>PowerShell

새 Application Insights 리소스 만들기

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>예제

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>결과

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

이 cmdlet에 대 한 전체 PowerShell 설명서 및 계측 키를 검색 하는 방법을 알아보려면 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)를 참조 하세요.

### <a name="azure-cli-preview"></a>Azure CLI (미리 보기)

미리 보기 Application Insights Azure CLI 명령에 액세스 하려면 먼저 다음을 실행 해야 합니다.

```azurecli
 az extension add -n application-insights
```

`az extension add` 명령을 실행 하지 않으면 다음 오류 메시지가 표시 됩니다. `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

이제 다음을 실행 하 여 Application Insights 리소스를 만들 수 있습니다.

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>예제

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>결과

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

이 명령에 대 한 전체 Azure CLI 설명서 및 계측 키를 검색 하는 방법에 대 한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [진단 검색](../../azure-monitor/app/diagnostic-search.md)
* [메트릭 탐색](../../azure-monitor/app/metrics-explorer.md)
* [분석 쿼리 작성](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md
