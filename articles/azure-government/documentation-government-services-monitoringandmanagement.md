---
title: Azure Government 모니터링 및 관리 | Microsoft 문서
description: 이 설명서에서는 Azure Government의 애플리케이션 개발에 대한 기능 및 지침을 비교합니다.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: b6f395964c286aca1bc8a1c190edeea00ba6e15c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362260"
---
# <a name="azure-government-monitoring--management"></a>Azure Government 모니터링 및 관리
이 문서에서는 Azure Government 환경의 모니터링 및 관리 서비스 변형과 고려 사항을 개략적으로 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
Advisor는 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [Advisor 공개 문서](../advisor/advisor-overview.md)를 참조 하세요.

### <a name="variations"></a>변형
다음 Advisor 권장 사항은 현재 Azure Government에서 사용할 수 없습니다.

* 고가용성
  * 연결 복원 력을 위해 VPN gateway를 활성-활성으로 구성
  * Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고 만들기
  * 복원력을 위해 Traffic Manager 엔드포인트 구성
  * Azure Storage 계정에 대해 일시 삭제 사용
* 성능
  * App Service 성능 및 안정성 향상
  * 정상 엔드포인트로 더 빠르게 장애 조치(failover)할 수 있게 Traffic Manager의 DNS TTL(Time To Live) 단축
  * SQL Data Warehouse 성능 향상
  * Premium Storage 사용
  * 저장소 계정을 Azure Resource Manager로 마이그레이션
* 비용
  * 예약 가상 머신 인스턴스를 구매 하 여 종 량 제 비용 보다 비용 절감
  * 프로 비전 되지 않은 Express 경로 회로 제거
  * 유휴 상태의 가상 네트워크 게이트웨이를 삭제하거나 다시 구성

미달 사용 가상 컴퓨터를 적절 하 게 크기 조정 하거나 종료 하는 것을 권장 하는 데 사용 되는 계산은 Azure Government에서 다음과 같습니다.

Advisor는 7 일 동안 가상 머신 사용량을 모니터링 하 고 사용률이 낮은 가상 머신을 식별 합니다. 가상 머신은 CPU 사용률이 5%이 하이 고 네트워크 사용률이 2% 이하인 경우 또는 더 작은 가상 머신 크기로 현재 워크 로드를 수용할 수 있는 경우 사용률이 낮은 것으로 간주 됩니다. 미달 사용 가상 컴퓨터를 더 적극적으로 식별 하려면 구독 별로 CPU 사용률 규칙을 조정할 수 있습니다.

## <a name="automation"></a>Automation
Automation은 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [Automation 공개 문서](../automation/automation-intro.md)를 참조하세요.

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate은 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [Azure Migrate 설명서](../migrate/migrate-overview.md)를 참조 하세요.

### <a name="variations"></a>변형
다음 Azure Migrate 기능은 현재 Azure Government에서 사용할 수 없습니다.

* Azure Government에서 종속성 시각화 기능을 사용할 수 없습니다. Azure Migrate는 현재 Azure Government에서 사용할 수 없는 종속성 시각화에 대 한 서비스 맵에 따라 달라 집니다.
* 대상 지역 및 Azure Government 제품을 사용 하 여 Azure Government에 대 한 평가만 만들 수 있습니다.

## <a name="backup"></a>Backup
Backup은 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [Azure Government Backup](documentation-government-services-backup.md)을 참조하세요.

## <a name="policy"></a>정책
정책은 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [Azure Policy](../governance/policy/overview.md)를 참조하세요.

## <a name="site-recovery"></a>Site Recovery
Azure Site Recovery는 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [Site Recovery 상용 설명서](../site-recovery/site-recovery-overview.md)를 참조 하세요.

### <a name="variations"></a>변형
현재 Azure Government에서 사용할 수 없는 Site Recovery 기능은 다음과 같습니다.
* 메일 알림

| Site Recovery | 클래식 | 리소스 관리자 |
| --- | --- | --- |
| VMware/물리적  | GA | GA |
| Hyper-V | GA | GA |
| 사이트-사이트 | GA | GA |

Azure Government에서 Site Recovery에 대한 다음 URL은 서로 다릅니다.

| Azure 공용 | Azure Government | 메모 |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | Site Recovery 서비스 액세스 |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | 보호 서비스 액세스 |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | VM 스냅샷 저장 |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | MySQL 다운로드 |

## <a name="monitor"></a>모니터
Azure Monitor은 일반적으로 Azure Government에서 사용할 수 있습니다.

자세한 내용은 [상업용 문서 모니터링](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)을 참조 하세요.

### <a name="variations"></a>변형
다음 섹션에서는 Azure Government의 Azure Monitor 기능에 대해 자세히 설명 하 고 해결 방법을 설명 합니다.

#### <a name="action-groups"></a>작업 그룹
작업 그룹은 상용 Azure와 차이가 없는 Azure Government에서 일반적으로 사용할 수 있습니다.   

#### <a name="activity-log-alerts"></a>활동 로그 경고
활동 로그 경고는 일반 상용 Azure와는 차이가 없는 Azure Government에서 일반적으로 사용할 수 있습니다.

#### <a name="alerts-experience"></a>경고 환경
통합 경고 UI 환경은 Azure Government의 메트릭 및 로그 경고에 사용할 수 있습니다.

#### <a name="autoscale"></a>자동 크기 조정
자동 크기 조정은 일반적으로 Azure Government에서 사용할 수 있습니다.

PowerShell/ARM/REST 호출을 사용 하 여 설정을 지정 하는 경우 자동 크기 조정의 "Location"을 "미국 정부 버지니아" 또는 "미국 정부 아이오"로 설정 합니다. 자동 크기 조정에서 대상으로 지정 된 리소스는 모든 지역에 있을 수 있습니다. 설정의 예는 다음과 같습니다.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

리소스에서 자동 크기 조정을 구현 하는 데 관심이 있는 경우 PowerShell/ARM/Rest 호출을 사용 하 여 설정을 지정 합니다.

PowerShell 사용에 대 한 자세한 내용은 [공개 문서](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings)를 참조 하세요.

#### <a name="metrics"></a>메트릭
메트릭은 일반적으로 Azure Government에서 사용할 수 있습니다. 그러나 다차원 메트릭은 REST API 통해서만 지원 됩니다. [다차원 메트릭을 표시](../azure-monitor/platform/metrics-charts.md) 하는 기능은 Azure Government 포털에서 미리 보기로 제공 됩니다.

#### <a name="metric-alerts"></a>메트릭 경고
처음 생성 된 메트릭 경고는 Azure Government와 상용 Azure에서 일반 공급 됩니다. 첫 번째 세대를 *경고 (클래식)* 라고 합니다. 이제 두 번째 생성 된 메트릭 경고 ( [통합 경고 환경](../azure-monitor/platform/alerts-overview.md)이 라고도 함)는 [공용 클라우드와 비교](../azure-monitor/platform/alerts-metric-near-real-time.md)하 여 리소스 공급자의 축소 된 집합을 사용할 수도 있습니다. 시간이 지남에 따라 추가 될 예정입니다. 

현재 두 번째 생성 경고 환경에서 지원 되는 리소스는 다음과 같습니다.
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft.EventGrid/domains
- Microsoft.EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft.Insights/components
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft. Network/natGateways
- Microsoft. Network/privateEndpoints
- Microsoft. Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

두 번째 경고 생성에서 아직 사용할 수 없는 리소스에 대 한 [클래식 경고](../azure-monitor/platform/alerts-classic.overview.md) 를 계속 사용할 수 있습니다. 

PowerShell/ARM/Rest 호출을 사용 하 여 메트릭 경고를 만들 때 메트릭 경고의 "Location"을 "미국 정부 버지니아" 또는 "미국 정부 아이오"로 설정 해야 합니다. 설정의 예는 다음과 같습니다.

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

PowerShell 사용에 대 한 자세한 내용은 [공개 문서](../azure-monitor/platform/powershell-quickstart-samples.md)를 참조 하세요.

## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Azure 앱 Services에 대 한 코드 없는 agent/extension 기반 모니터링은 **현재 지원 되지 않습니다**. 이 기능을 사용할 수 있게 되 면이 문서가 업데이트 됩니다.

이 섹션에서는 Azure Government에서 Application Insights를 사용 하는 데 필요한 추가 구성에 대해 설명 합니다. Azure Monitor 및 Application Insights에 대 한 자세한 내용은 [전체 설명서](https://docs.microsoft.com/azure/azure-monitor/overview)를 참조 하세요.

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Visual Studio에서 ASP.NET & ASP.NET Core에 대 한 Application Insights 사용

현재 Azure Government 고객의 경우 Visual Studio의 기존 **응용 프로그램 추가 정보 원격 분석** 단추를 통해 Application Insights를 사용 하도록 설정 하는 유일한 방법은 약간의 수동 해결 방법이 필요 합니다. 관련 문제가 발생 하는 고객은 _"이 계정과 연결 된 Azure 구독이 없습니다_ ."와 같은 오류 메시지가 표시 될 수 있습니다. `microsoft.insights` 리소스 공급자가 구독에 대해 등록 상태가 된 경우에도 _선택한 구독은 Application Insights을 지원 하지_ 않습니다. 이 문제를 완화 하려면 다음 단계를 수행 해야 합니다.

1. [Azure Government 클라우드를 대상](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs)으로 하는 Visual Studio를 전환 합니다.

2. 다음과 같이 AzureGraphApiVersion에 대 한 사용자 환경 변수를 만듭니다 (또는 이미 기존 집합). 사용자 환경 변수를 만들려면 **제어판** > **System** > **고급 시스템 설정** > **고급** > **환경 변수**를 참조 하세요.)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. 프로젝트 형식에 따라 [ASP.NET](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) 또는 [ASP.NET Core](#aspnet-core) 에 대 한 적절 한 Application Insights SDK 끝점을 수정 합니다.

### <a name="snapshot-debugger"></a>스냅샷 디버거

스냅숏 디버거은 현재 Azure Government 고객에 게 제공 됩니다. 스냅숏 디버거 사용 하려면 [Snapshot Collector 버전 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) 이상을 사용 하 고 있는지만 추가 해야 합니다. 그런 다음 표준 [스냅숏 디버거 설명서](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger)를 따릅니다.

### <a name="sdk-endpoint-modifications"></a>SDK 끝점 수정

Application Insights에서 Azure Government 지역으로 데이터를 보내려면 Application Insights Sdk에서 사용 하는 기본 끝점 주소를 수정 해야 합니다. 각 SDK에는 약간 다른 수정이 필요 합니다.

### <a name="net-with-applicationinsightsconfig"></a>Applicationinsights .config를 사용 하는 .NET

> [!NOTE]
> SDK 업그레이드가 수행 될 때마다 applicationinsights .config 파일을 자동으로 덮어씁니다. SDK 업그레이드를 수행한 후에는 지역별 끝점 값을 다시 입력 해야 합니다.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

다음과 같이 프로젝트에서 appsettings 파일을 수정 하 여 기본 끝점을 조정 합니다.

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

라이브 메트릭 및 프로필 쿼리 끝점에 대 한 값은 코드를 통해서만 설정할 수 있습니다. 코드를 통해 모든 끝점 값의 기본값을 재정의 하려면 `Startup.cs` 파일의 `ConfigureServices` 메서드에서 다음과 같이 변경 합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Azure 기능

함수 프로젝트에 다음 패키지를 설치 하세요.

- Microsoft ApplicationInsights 버전 2.10.0
- 2\.10.0. PerfCounterCollector 버전
- TelemetryChannel 버전 (2.10.0)

또한 함수 응용 프로그램의 시작 코드를 추가 (또는 수정) 합니다.

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Applicationinsights .xml 파일을 수정 하 여 기본 끝점 주소를 변경 합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

`application.properties` 파일을 수정 하 고 다음을 추가 합니다.

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

환경 변수를 통해 끝점을 구성할 수도 있습니다.

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>방화벽 예외

Azure 애플리케이션 Insights 서비스는 많은 IP 주소를 사용 합니다. 모니터링하는 앱이 방화벽 뒤에서 호스팅되는 경우 이러한 주소를 알아야 할 수도 있습니다.

> [!NOTE]
> 이러한 주소는 고정이지만 경우에 따라 변경해야 할 수 있습니다. 모든 Application Insights 트래픽은 인바운드 방화벽 규칙을 필요로 하는 웹 후크 및 가용성 모니터링을 제외 하 고 아웃 바운드 트래픽을 나타냅니다.

### <a name="outgoing-ports"></a>발신 포트
Application Insights SDK 및/또는 상태 모니터가 데이터를 포털에 보낼 수 있도록 서버 방화벽에서 일부 나가는 포트를 열어야 합니다.

| 목적 | URL | IP | 포트 |
| --- | --- | --- | --- |
| 원격 분석 | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Azure Monitor 로그
Azure Monitor 로그는 Azure Government에서 일반적으로 사용할 수 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>변형

* Azure Government에서 사용할 수 있는 솔루션은 다음과 같습니다.
  * [네트워크 성능 모니터 (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) -NPM는 공용 및 하이브리드 클라우드 환경에 대 한 클라우드 기반 네트워크 모니터링 솔루션입니다. 조직은 NPM를 사용 하 여 온-프레미스 및 클라우드 환경에서 네트워크 가용성을 모니터링 합니다.  엔드포인트 모니터-NPM의 하위 기능으로, 응용 프로그램에 대 한 네트워크 연결을 모니터링 합니다.

다음 Azure Monitor는 현재 Azure Government에서 사용할 수 없는 기능 및 솔루션을 기록 합니다.

* 다음은 Mcrosoft Azure에서 미리 보기로 제공되는 솔루션입니다.
  * 서비스 맵
  * Windows 10 Analytics 업그레이드 솔루션
  * Application Insights 솔루션
  * Azure 네트워킹 보안 그룹 분석 솔루션
  * Azure Automation 분석 솔루션
  * Key Vault 분석 솔루션
* 다음은 온-프레미스 소프트웨어 업데이트가 필요한 솔루션 및 기능입니다.
  * Surface Hub 솔루션
* 다음을 포함 하 여 글로벌 Azure에서 미리 보기에 있는 기능
  * Power BI로 데이터 내보내기
* Azure 메트릭 및 Azure 진단

Azure Monitor 로그의 Url은 Azure Government 다릅니다.

| Azure 공용 | Azure Government | 메모 |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics 작업 영역 포털 |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[데이터 수집기 API](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |에이전트 통신 - [방화벽 설정 구성](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |에이전트 통신 - [방화벽 설정 구성](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |에이전트 통신 - [방화벽 설정 구성](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |고급 분석 포털- [방화벽 설정 구성](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |고급 분석 포털- [방화벽 설정 구성](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |고급 분석 포털- [방화벽 설정 구성](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*azure-automation.us |Azure Automation- [방화벽 설정 구성](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| 해당 없음 | *. usgovtrafficmanager.net | Azure Traffic Manager- [방화벽 설정 구성](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

다음 Azure Monitor는 Azure Government에서 다르게 동작 하는 기능을 기록 합니다.

* System Center Operations Manager 관리 그룹을 Azure Monitor 로그에 연결 하려면 업데이트 된 관리 팩을 다운로드 하 여 가져와야 합니다.
  + System Center Operations Manager 2016
    1. [System Center Operations Manager 2016용 업데이트 롤업 2](https://support.microsoft.com/help/3209591)를 설치합니다.
    2. 업데이트 롤업 2의 일부로 포함된 관리 팩을 Operations Manager로 가져옵니다. 디스크에서 관리 팩을 가져오는 방법에 대한 정보는 [Operations Manager 관리 팩을 가져오는 방법](https://technet.microsoft.com/library/hh212691.aspx)을 참조하세요.
    3. Azure Monitor 로그에 Operations Manager를 연결 하려면 [Azure Monitor 로그에 Operations Manager 연결](../azure-monitor/platform/om-agents.md)의 단계를 따르세요.
  + System Center Operations Manager 2012 R2 UR3(이상)/Operations Manager 2012 SP1 UR7(이상)
    1. [업데이트된 관리 팩](https://go.microsoft.com/fwlink/?LinkId=828749)을 다운로드하고 저장합니다.
    2. 다운로드한 파일의 압축을 풉니다.
    3. 관리 팩을 Operations Manager로 가져옵니다. 디스크에서 관리 팩을 가져오는 방법에 대한 정보는 [Operations Manager 관리 팩을 가져오는 방법](https://technet.microsoft.com/library/hh212691.aspx)을 참조하세요.
    4. Azure Monitor 로그에 Operations Manager를 연결 하려면 [Azure Monitor 로그에 Operations Manager 연결](../azure-monitor/platform/om-agents.md)의 단계를 따르세요.

* Configuration Manager에서 컴퓨터 그룹을 사용 하는 방법에 대 한 자세한 내용은 [Azure Monitor에 Configuration Manager 연결](../azure-monitor/platform/collect-sccm.md)을 참조 하세요.

### <a name="frequently-asked-questions"></a>질문과 대답
* Microsoft Azure에서 Azure Monitor 로그의 데이터를 Azure Government로 마이그레이션할 수 있나요?
  * 아니요. Microsoft Azure에서 Azure Government로 데이터 또는 작업 영역을 이동하는 것은 불가능합니다.
* Operations Management Suite 포털에서 Microsoft Azure와 Azure Government 작업 영역 간에 전환할 수 있나요?
  * 아니요. Microsoft Azure 포털과 Azure Government 포털은 각각 별도이며 정보를 공유하지 않습니다.

자세한 내용은 [Azure Monitor logs public 설명서](../log-analytics/log-analytics-overview.md)를 참조 하세요.

## <a name="scheduler"></a>Scheduler
이 서비스 및 사용 방법에 대 한 자세한 내용은 [Azure Scheduler 설명서](../scheduler/index.md)를 참조 하세요.

## <a name="azure-portal"></a>Azure portal
[여기](https://portal.azure.us)에서 Azure Government 포털에 액세스할 수 있습니다.

## <a name="azure-resource-manager"></a>Azure 리소스 관리자
이 서비스 및 사용 방법에 대 한 자세한 내용은 [Azure Resource Manager 설명서](../azure-resource-manager/management/overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Government 블로그](https://blogs.msdn.microsoft.com/azuregov/) 구독
* [Azure .gov](https://stackoverflow.com/questions/tagged/azure-gov) 를 사용 하 여 Stack Overflow에 대 한 도움말 보기
* 피드백 제공 또는 [Azure Government 피드백 포럼](https://feedback.azure.com/forums/558487-azure-government) 을 통해 새로운 기능 요청
