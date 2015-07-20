<properties
   pageTitle="서비스 패브릭 응용 프로그램에 대한 Application Insights 설정"
   description="Application Insights의 응용 프로그램에 대한 서비스 패브릭 이벤트를 수신합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="mattrow"/>

# 서비스 패브릭 응용 프로그램에 대한 Application Insights 설정
 이 문서에서는 서비스 패브릭 응용 프로그램에 대한 Application Insights 사용을 연습하게 됩니다.

## 필수 조건

이 문서에서는 Visual Studio에서 만든 서비스 패브릭 응용 프로그램을 이미 가지고 있다고 가정합니다. 방법을 알아 보려면 [여기를 클릭](service-fabric-reliable-services-quick-start.md)하십시오.

## NuGet 패키지 설치
서비스 패브릭 SDK의 일부로 출시된 시험판 버전 NuGet 패키지 Microsoft.ServiceFabric.Telemetry.ApplicationInsights입니다. 이 패키지는 서비스 패브릭 EventSource 이벤트를 Application Insights와 연결하여 서비스 패브릭 앱의 자동화된 계측을 제공합니다. 이 패키지는 응용 프로그램에 의해 자동으로 내보내지는 새 이벤트로 계속 업데이트됩니다.

다음 단계를 따라 패키지를 설치할 수 있습니다.

1. 서비스 패브릭 앱에 대해 NuGet 패키지 관리자를 엽니다. Visual Studio에서 마우스 오른쪽 단추를 클릭하고 ‘NuGet 패키지 관리...’를 눌러서도 수행할 수 있습니다.
2. 서비스 패브릭 SDK에 포함된 패키지를 나열하기 위해서는 패키지 소스로 ‘Microsoft Azure 서비스 패브릭’을 선택해야 합니다. ![VS2015 NuGet 패키지 관리자](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. 왼쪽에서 Microsoft.ServiceFabric.Telemetry.ApplicationInsights 패키지를 선택합니다.
4. 설치를 클릭하여 설치 프로세스를 시작합니다.
5. EULA를 검토하고 동의합니다.

## 서비스 패브릭 이벤트 사용
Application Insights에서 서비스 패브릭 이벤트를 자동으로 수신하려면 수신기를 사용하도록 설정해야 합니다. 앱에 코드의 다음 줄을 삽입하여 수행할 수 있습니다.

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### StatefulActor\\Program.cs의 예:

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

신뢰할 수 있는 행위자 런타임에서 내보내진 이벤트에 대해서는 [여기](service-fabric-reliable-actors-diagnostics.md), 신뢰할 수 있는 서비스 런타임에서 내보내진 이벤트에 대해서는 [여기](service-fabric-reliable-services-diagnostics.md)에서 알아볼 수 있습니다.

신뢰할 수 있는 행위자 런타임 메서드 호출을 받기 위해서는 EventLevel.Verbose를 사용해야 합니다(위 예제에 나타낸 바와 같음).

## Application Insights 설정
계측 키는 서비스 패브릭 앱을 Application Insights 리소스에 연결합니다. 다음 [Application Insight 가이드](../app-insights-create-new-resource.md#create-an-application-insights-resource)를 따르면 계측 키를 가져오는 방법을 알아볼 수 있습니다. 리소스를 만들 때 응용 프로그램 형식에 ‘기타’를 선택합니다.

![AI 앱 형식에 대해 기타 선택](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

일단 계측 키를 만들면 이를 다음과 같이 ApplicationInsights.config 파일에 삽입할 수 있습니다.

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## 데이터 보기
필요에 맞춰 [App Insights 블레이드를 사용자 지정](../app-insights-metrics-explorer.md)할 수 있습니다. 패브릭 행위자 메서드 호출 및 서비스 RunAsync() 호출은 요청으로 표시되지만 대부분의 서비스 패브릭 이벤트는 ‘사용자 지정 이벤트’로 표시됩니다. 이 이벤트를 요청으로 모델링하면 차트를 빌드할 때 ‘요청 이름’ 차원 및 ‘요청 기간’ 메트릭을 사용할 수 있습니다. 새 차트, 매트릭 및 이벤트는 향후 활용할 수 있도록 계속 추가됩니다.

## 다음 단계
Application Insights를 사용하여 서비스 패브릭 앱을 계측하는 방법에 대해 더 알아보기

- [Application Insights로 시작하기](../app-insights-get-started.md)
- [고유의 사용자 지정 이벤트 및 매트릭 생성 방법 알아보기](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=July15_HO2-->