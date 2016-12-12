---
title: "런타임 재구성 패턴 | Azure | Microsoft Docs"
description: "재배포를 요구하거나 응용 프로그램을 다시 시작하지 않고 다시 구성할 수 있도록 응용 프로그램을 디자인합니다."
categories:
- design-implementation
- management-monitoring
keywords: "디자인 패턴"
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: 2a7c30eca77809e08fd8621027daef60146408a0

---
   
# <a name="runtime-reconfiguration"></a>런타임 재구성

재배포를 요구하거나 응용 프로그램을 다시 시작하지 않고 다시 구성할 수 있도록 응용 프로그램을 디자인합니다. 이를 통해 가용성을 유지 관리하고 가동 중지 시간을 최소화할 수 있습니다. 

## <a name="context-and-problem"></a>컨텍스트 및 문제점

상용 및 비즈니스 웹 사이트와 같은 응용 프로그램의 주요 목표는 고객과 사용자를 위해 가동 중지 시간과 중단을 최소화하는 것입니다. 경우에 따라 배포하여 사용 중인 경우 특정 동작이나 설정을 변경하기 위해 응용 프로그램을 다시 구성해야 합니다. 따라서 응용 프로그램이 실행되는 동안 이러한 구성 변경 내용을 적용하고 응용 프로그램의 구성 요소가 변경 내용을 감지하고 가능한 한 빨리 적용하도록 설계할 수 있는 이점이 있습니다.

적용할 구성 변경 내용의 예는 로깅의 세분성을 조정하여 응용 프로그램으로 문제를 디버깅하거나 다른 데이터 저장소를 사용하기 위해 연결 문자열을 교체하거나 응용 프로그램의 특정 섹션 또는 기능을 켜거나 끄는 작업을 지원할 수 있습니다.

## <a name="solution"></a>해결 방법

이 패턴을 구현하는 솔루션은 응용 프로그램 호스팅 환경에서 사용할 수 있는 기능에 따라 달라집니다. 일반적으로 응용 프로그램 코드는 응용 프로그램 구성에 변경 내용이 감지되면 호스팅 인프라에 의해 발생되는 하나 이상의 이벤트에 응답합니다. 이는 일반적으로 새 구성 파일 업로드의 결과이거나 관리 포털을 통해 또는 API에 액세스하여 구성 변경에 대한 응답입니다.

구성 변경 이벤트를 처리하는 코드는 변경 내용을 검토하고 이를 응용 프로그램의 구성 요소에 적용할 수 있습니다. 이들 요소는 변경 내용을 감지하고 대응해야 하며, 그 결과 이들 요소가 사용하는 값은 일반적으로 이벤트 처리기의 코드가 새 값으로 설정되거나 실행할 수 있는 쓰기 가능한 속성 또는 메서드로서 노출됩니다. 이 때부터 구성 요소는 새 값을 사용하여 응용 프로그램 동작에 필요한 변경 내용이 발생하도록 해야 합니다.

구성 요소가 런타임 시 변경 내용을 적용할 수 없는 경우, 응용 프로그램을 다시 시작하여 응용 프로그램을 다시 시작할 때 이러한 변경 내용이 적용되도록 해야 합니다. 일부 호스팅 환경에서 이러한 유형의 변경 내용을 감지하여 응용 프로그램을 다시 시작해야 하는 환경에 나타낼 수 있습니다. 또 설정 변경 내용을 분석하고 필요 시 응용 프로그램을 강제로 다시 시작하는 코드를 구현해야 하는 경우도 있습니다.

그림은 이 패턴의 개요를 보여줍니다.

![그림 1 - 이 패턴의 기본 개요](images/runtime-reconfiguration-pattern.png)


대부분의 환경은 구성 변경에 대한 응답으로 발생하는 이벤트를 노출합니다. 그렇지 않은 경우 구성의 변경 내용을 정기적으로 확인하고 이러한 변경 내용을 적용하는 폴링 메커니즘이 있어야 합니다. 또 런타임 시 변경 내용을 적용할 수 없는 경우 응용 프로그램을 다시 시작 해야 할 수도 있습니다. 예를 들어 미리 설정된 간격으로 구성 파일의 시간과 날짜를 비교하고 최신 버전이 있을 경우 변경 내용을 적용하는 코드를 실행할 수 있습니다. 다른 접근 방식은 응용 프로그램의 관리 UI에서 컨트롤을 통합하거나, 응용 프로그램 외부에서 액세스하여 업데이트된 구성을 읽고 적용하는 코드를 실행하는 할 수 있는 보안 끝점을 노출하는 것입니다. 

또는 응용 프로그램이 환경에서 일부 변경 내용에 응답할 수도 있습니다. 예를 들어 한 특정 런타임 오류가 발생하면 로깅 구성이 변경되어 자동으로 추가 정보를 수집할 수 있습니다. 또는 코드가 현재 날짜를 사용하여 시즌 또는 특별 이벤트를 반영하는 테마를 일고 적용할 수 있습니다. 

## <a name="issues-and-considerations"></a>문제 및 고려 사항

이 패턴을 구현할 방법을 결정할 때 다음 사항을 고려하세요.

전체 패키지를 다시 배포하지 않고 업데이트할 수 있도록 구성 설정은 배포된 응용 프로그램 외부에 저장 되어야 합니다. 일반적으로 설정은 구성 파일 또는 데이터베이스나 온라인 저장소와 같은 외부 저장소에 저장됩니다. 런타임 구성 메커니즘에 액세스하는 것은 엄격하게 관리해야 하며 사용 시 엄격하게 감사해야 합니다.

호스트 인프라가 자동으로 구성 변경 이벤트를 검색하지 못하고 이러한 이벤트를 응용 프로그램 코드에 노출하지 않는 경우 대체 메커니즘을 구현하여 변경 내용을 감지하고 적용해야 합니다. 이는 폴링 메커니즘을 통하거나 대화형 컨트롤 또는 업데이트 프로세스를 시작하는 끝점을 노출함으로써 이루어집니다. 

폴링 메커니즘을 구현해야 하는 경우 구성에 대한 업데이트 검사를 얼마나 자주 수행해야 할지 고려해야 합니다. 긴 폴링 간격은 변경 내용이 일정 시간 동안 적용되지 않을 수 있음을 의미합니다. 짧은 간격은 사용할 수 있는 연산 및 I/O 리소스를 흡수하여 작업에 악영향을 줄 수 있습니다.

응용 프로그램의 인스턴스가 여러 개 있는 경우 변경 내용을 어떻게 감지하는가에 따라 추가 요인을 고려해야 합니다. 호스팅 인프라에서 발생한 이벤트를 통해 변경 내용이 자동으로 감지되는 경우 이 변경 내용은 동시에 모든 응용 프로그램 인스턴스에 의해 감지되지 않을 수 있습니다. 즉, 다른 인스턴스가 새로운 설정을 사용하는 동안 일부 인스턴스는 원래의 구성을 사용할 것임을 의미합니다. 폴링 메커니즘을 통해 업데이트가 감지된 경우 일관성을 유지하기 위해 모든 인스턴스에 변경 내용을 전달해야 합니다.

일부 구성 변경 내용은 응용 프로그램을 다시 시작하거나 호스팅 서버를 다시 부팅하는 것이 필요합니다. 이러한 유형의 구성 설정을 확인하고 각각에 대해 적절한 조치를 수행해야 합니다. 예를 들어, 응용 프로그램을 다시 시작해야 하는 변경 내용은 이를 자동으로 수행할 수 있습니다. 또 응용 프로그램이 과부하 하에 있지 않고 응용 프로그램의 다른 인스턴스가 부하를 처리할 수 있는 경우 관리자가 다시 시작 여부를 판단할 수 있습니다. 

업데이트의 단계적 롤아웃을 계획하고 업데이트가 성공했는지 그리고 업데이트를 적용하기 전에 업데이트된 응용 프로그램 인스턴스가 올바르게 작동하는 확인합니다. 이를 통해 오류가 발생한 경우 응용 프로그램의 전체 중단을 방지할 수 있습니다. 업데이트가 응용 프로그램의 다시 시작 또는 다시 부팅이 필요한 경우, 특히 응용 프로그램이 상당한 시작 또는 웜업 시간이 필요한 경우 단계적 롤아웃을 사용하여 여러 인스턴스가 동시에 오프라인되는 것을 방지합니다.

문제를 일으키거나 응용 프로그램에 오류를 발생시키는 구성 변경 내용을 어떻게 롤백할지를 고려합니다. 예를 들어 변경 내용을 감지하는 폴링 간격을 기다리지 않고 즉시 변경 내용을 롤백하는 것도 가능합니다. 

구성 설정의 위치가 응용 프로그램 성능에 어떻게 영향을 미칠지 고려합니다. 예를 들어 응용 프로그램이 시작될 때 외부 저장소를 사용할 수 없는 경우 또는 구성 변경 내용을 적용하는 경우 발생할 수 있는 오류를 처리합니다. 기본 구성을 사용하거나 캐싱 서버의 로컬 설정을 캐싱하고 원격 데이터 저장소에 액세스를 시도하는 동안 이러한 값을 다시 사용함으로써 이를 수행할 수 있습니다.

캐싱은 구성 요소가 반복적으로 구성 설정에 액세스해야 하는 경우 지연 시간을 줄이는 데 도움이 됩니다. 그러나 구성이 변경되면 응용 프로그램 코드는 캐시된 설정을 무효화하고 구성 요소는 업데이트된 설정을 사용해야 합니다. 

## <a name="when-to-use-this-pattern"></a>이 패턴을 사용해야 하는 경우

이 패턴은 다음에 유용합니다.

- 변경 내용을 응용 프로그램 구성에 적용하는 동안 모든 불필요한 가동 중지 시간을 방지해야 하는 응용 프로그램.

- 기본 구성이 변경될 때 자동으로 발생된 이벤트를 노출하는 환경. 이는 일반적으로 새 구성 파일이 발견되거나 변경 사항이 기존 구성 파일에 적용되는 경우에 해당합니다. 

- 응용 프로그램 구성이 자주 변경되고 응용 프로그램을 다시 시작할 필요 없거나 호스팅 서버를 다시 부팅할 필요 없이 변경 내용을 구성 요소에 적용할 수 있는 애플리케이션.

이 패턴은 초기화 시간에만 구성할 수 있고 해당 구성 요소의 업데이트 과정이 응용 프로그램을 다시 시작하고 가동 중지 시간이 짧게 지속되는 것에 비해 복잡한 경우 유용하지 않을 수도 있습니다. 

## <a name="example"></a>예제

Microsoft Azure Cloud Services 역할은 호스팅 환경에서 ServiceConfiguration.cscfg 파일에 대한 변경이 감지될 때 발생하는 두 개의 이벤트를 감지하고 노출합니다.

- **RoleEnvironment.Changing**. 이 이벤트는 구성 변경이 감지된 후 응용 프로그램에 적용되기 전에 발생합니다. 변경 내용을 쿼리하고 런타임 재구성을 취소하는 이벤트를 처리할 수 있습니다. 변경 내용을 취소하면 응용 프로그램이 새 구성을 사용하도록 웹 또는 작업자 역할이 자동으로 다시 시작됩니다.
- **RoleEnvironment.Changed**. 이 이벤트는 응용 프로그램 구성이 적용된 후에 발생합니다. 적용된 변경 내용을 쿼리하는 이벤트를 처리할 수 있습니다.

`RoleEnvironment.Changing` 이벤트에서 변경 내용을 취소하는 경우 응용 프로그램이 실행되는 동안 새 설정을 적용할 수 없고 새 값을 사용하기 위해 다시 시작해야 함을 Azure에 나타내야 합니다. 실제로 응용 프로그램 또는 구성 요소가 런타임 시 변경에 반응할 수 없는 경우에만 변경 내용을 취소할 수 있고 새 값을 사용하기 위해 다시 시작해야 합니다.

>  자세한 내용은 [RoleEnvironment.Changing Event](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)를 참조하세요. 

`RoleEnvironment.Changing` 및 `RoleEnvironment.Changed` 이벤트를 처리하려면 일반적으로 사용자 지정 처리기를 추가합니다. 예를 들어 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/runtime-reconfiguration)에서 사용할 수 있는 런타임 재구성 솔루션에 있는 `Global.asax.cs` 클래스의 다음 코드가 `RoleEnvironment_Changed`라는 사용자 지정 함수를 이벤트 처리기 체인에 추가하는 방법을 보여줍니다. 이는 예제 파일 Global.asax.cs에서 나온 것입니다.

>  이 패턴의 예제는 RuntimeReconfiguration 솔루션의 RuntimeReconfiguration.Web 프로젝트에 있습니다. 

```csharp
protected void Application_Start(object sender, EventArgs e)
{
  ConfigureFromSetting(CustomSettingName);
  RoleEnvironment.Changed += this.RoleEnvironment_Changed;
}
```

웹 또는 작업자 역할에서 `RoleEnvironment.Changing` 이벤트를 처리하기 위해 역할의 `OnStart` 이벤트 처리기에 있는 유사 코드를 사용할 수 있습니다. 이는 예제 파일 WebRole.cs에서 나온 것입니다.

```csharp
public override bool OnStart()
{
  // Add the trace listener. The web role process isn't configured by web.config.
  Trace.Listeners.Add(new DiagnosticMonitorTraceListener());

  RoleEnvironment.Changing +=   this.RoleEnvironment_Changing;
  return base.OnStart();
}
```

웹 역할의 경우 `OnStart` 이벤트 처리기는 웹 응용 프로그램 프로세스가 아닌 별도의 프로세스에서 실행됩니다. 이것이 일반적으로 Global.asax 파일에서 `RoleEnvironment.Changed` 이벤트 처리기를 처리하여 처리 하는 이유는 웹 응용 프로그램의 런타임 구성과 역할에서 `RoleEnvironment.Changing` 이벤트를 업데이트할 수 있도록 하는 이유입니다. 작업자 역할의 경우 `OnStart` 이벤트 처리기에서 `RoleEnvironment.Changing` 및 `RoleEnvironment.Changed` 이벤트를 구독할 수 있습니다.

>  사용자 지정 구성 설정을 서비스 구성 파일, 사용자 지정 구성 파일, 가상 컴퓨터의 Azure SQL Database나 SQL Server와 같은 데이터베이스, Azure Blob이나 Table Storage에 저장할 수 있습니다. 응용 프로그램에서 구성 요소 속성을 설정하여 사용자 지정 구성 설정에 액세스할 수 있고 응용 프로그램&mdash;에 적용할 수 있는 코드를 만들어야 합니다.  

예를 들어 다음 사용자 지정 함수는 Azure 서비스 구성 파일에서 설정 값을 읽은 다음 `SomeRuntimeComponent`라는 런타임 구성 요소의 현재 인스턴스에 적용합니다. 이는 예제 파일 Global.asax.cs에서 나온 것입니다.

>  Windows Identity Framework의 설정과 같은 일부 구성 설정은 Azure 서비스 구성 파일에 저장할 수 없으며 App.config 또는 Web.config 파일에 있어야 합니다. 

```csharp
private static void ConfigureFromSetting(string settingName)
{
  var value = RoleEnvironment.GetConfigurationSettingValue(settingName);
  SomeRuntimeComponent.Instance.CurrentValue = value;
}
```

Azure에서 일부 구성 변경 내용은 자동으로 검색되고 적용됩니다. 여기에는 수집할 정보 유형과 로그 파일을 유지하는 방법을 지정하는 Diagnostics.wadcfg 파일의 Azure 진단 시스템의 구성이 포함됩니다. 따라서 서비스 구성 파일에 추가하는 사용자 지정 설정을 처리하는 코드를 작성해야 합니다. 코드는 다음을 수행해야 합니다.

- 업데이트된 구성의 사용자 지정 설정을 런타임 시 응용 프로그램의 적절한 구성 요소에 적용하여 해당 동작이 새 구성을 반영하도록 합니다.

- 변경 내용을 취소하여 런타임 시 새 값을 적용할 수 없고 변경 내용을 취소하기 위해 응용 프로그램을 다시 시작해야 함을 Azure에 알립니다. 

예를 들어 다음 코드는 다시 시작하지 않고도 런타임 시 적용할 수 설정을 제외한 모든 설정에 대한 업데이트 작업을 취소하기 위해 `RoleEnvironment.Changing` 이벤트를 사용하는 방법을 보여줍니다. 이 예제에는 응용 프로그램을 다시 시작하지 않고도 런타임 시 적용할 "CustomSetting"에 변경을 허용합니다. 이 설정을 사용하는 구성 요소는 새 값을 읽고 런타임 시 그에 따라 동작을 변경할 수 있습니다. 기타 구성 변경은 자동으로 웹 또는 작업자 역할을 다시 시작하게 합니다. 

```csharp
private void RoleEnvironment_Changing(object sender,
                               RoleEnvironmentChangingEventArgs e)
{
  var changedSettings = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>()
                                 .Select(c => c.ConfigurationSettingName).ToList();
  Trace.TraceInformation("Changing notification. Settings being changed: "
                         + string.Join(", ", changedSettings));

  if (changedSettings
    .Any(settingName => !string.Equals(settingName, CustomSettingName,
                               StringComparison.Ordinal)))
  {
    Trace.TraceInformation("Cancelling dynamic configuration change (restarting).");

    // Setting this to true will restart the role gracefully. If Cancel isn't 
    // set to true, and the change isn't handled by the application, the 
    // application won't use the new value until it's restarted (either 
    // manually or for some other reason).
    e.Cancel = true; 
  }
  Else
  {
    Trace.TraceInformation("Handling configuration change without restarting. ");
  }
}
```

>  이러한 접근 방식은 응용 프로그램 코드가 인식하지 못하는(따라서 런타임 시 적용할 수 있는지 확신할 수 없음) 설정을 변경하기 때문에 우수한 사례를 보여줍니다. 변경 내용 중 어느 하나를 취소하는 경우 역할이 다시 시작됩니다.  

새 구성이 Azure 프레임워크에 의해 승인된 후 `RoleEnvironment.Changing` 이벤트 처리기에서 취소되지 않아 검색되어 응용 프로그램 구성 요소에 적용할 수 있는 업데이트. 예를 들어 예제 솔루션의 `Global.asax` 파일에 있는 다음 코드는 `RoleEnvironment.Changed` 이벤트를 처리합니다. 이는 각 구성 설정을 검사하고, "CustomSetting"를 발견하면 응용 프로그램의 적절한 구성 요소에 새 설정을 적용하는 함수를 호출합니다. 

```csharp
private void RoleEnvironment_Changed(object sender, 
                               RoleEnvironmentChangedEventArgs e)
{
  Trace.TraceInformation("Updating instance with new configuration settings.");

  foreach (var settingChange in
           e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>())
  {
    if (string.Equals(settingChange.ConfigurationSettingName, 
                      CustomSettingName, 
                      StringComparison.Ordinal))
    {
      // Execute a function to update the configuration of the component.
      ConfigureFromSetting(CustomSettingName );
    }
  }
}
```

구성 변경 내용을 취소하지 못했지만 새 값을 응용 프로그램 구성에 적용하지 않은 경우 다음 번에 응용 프로그램을 다시 시작할 때까지 변경 내용이 적용되지 않습니다. 이는 특히 새 설정 값이 적용되는 시점에 정기적인 유지 관리 작업&mdash;의 일부로서 Azure가 호스팅 역할 인스턴스를 자동으로 다시 시작하는 경우 예기치 않은 동작이 발생할 수 있습니다.

## <a name="related-patterns-and-guidance"></a>관련 패턴 및 지침

- 이 패턴을 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/runtime-reconfiguration)에서 사용할 수 있음을 보여주는 샘플.
- 응용 프로그램 배포 패키지에서 구성 정보를 중앙 집중화된 위치로 이동하면 구성 데이터를 쉽게 관리하고 제어할 수 있으며, 응용 프로그램 및 응용 프로그램 인스턴스에서 구성 데이터를 공유할 수 있습니다. 자세한 내용은 [외부 구성 저장소 패턴](external-configuration-store.md)을 참조하세요. 



<!--HONumber=Nov16_HO3-->


