---
title: Windows 데스크톱 앱의 사용량 및 성능 모니터링
description: Application Insights를 사용하여 Windows 데스크톱 앱의 사용량 및 성능을 분석합니다.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: eb9e0fc480098478a3a68265ac85e0d5450e27fe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537392"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>클래식 Windows 데스크톱 앱에서 사용량 및 성능 모니터링

온-프레미스 호스팅된 애플리케이션은 Azure 및 다른 클라우드에서 Application Insights를 충분히 활용할 수 있습니다. 유일한 제한은 Application Insights 서비스와 [통신을 허용](../../azure-monitor/app/ip-addresses.md)해야 한다는 것입니다. UWP(유니버설 Windows 플랫폼) 애플리케이션 모니터링에는 [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md)가 좋습니다.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>클래식 Windows 애플리케이션에서 Application Insights에 원격 분석을 전송하려면
1. Azure [포털에서](https://portal.azure.com) [응용 프로그램 인사이트 리소스를 만듭니다.](../../azure-monitor/app/create-new-resource.md ) 애플리케이션 유형으로 ASP.NET 앱을 선택합니다.
2. 계측 키를 복사합니다. 방금 만든 새 리소스의 필수 드롭다운에서 키를 찾습니다. 
3. Visual Studio에서 앱 프로젝트의 NuGet 패키지를 편집하고 Microsoft.ApplicationInsights.WindowsServer를 추가합니다. (또는 표준 원격 분석 수집 모듈 없이 API를 사용하려면 Microsoft.ApplicationInsights를 선택합니다.)
4. 코드에서 계측 키를 설정합니다.
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *키* `";`
   
    또는 ApplicationInsights.config에서(표준 원격 분석 패키지 중 하나를 설치한 경우).
   
    `<InstrumentationKey>`*키*`</InstrumentationKey>` 
   
    ApplicationInsights.config를 사용하는 경우 솔루션 탐색기에서 해당 속성이 **빌드 작업 = 콘텐츠, 출력 디렉터리로 복사 = 복사**로 설정되도록 합니다.
5. [API를 사용](../../azure-monitor/app/api-custom-events-metrics.md) 하여 원격 분석을 전송합니다.
6. 앱을 실행하고 Azure 포털에서 만든 리소스의 원격 분석을 확인합니다.

## <a name="example-code"></a><a name="telemetry"></a>예제 코드
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>컴퓨터 이름의 저장소 재정의

기본적으로 이 SDK는 원격 분석을 내보내는 시스템의 컴퓨터 이름을 수집하고 저장합니다. 컬렉션을 재정의하려면 원격 분석 초기화자를 사용해야 합니다.

**아래와 같이 사용자 지정 원격 분석 초기설정을 작성합니다.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
계측 키 설정 아래 `Program.cs` `Main()` 메서드에서 초기화자를 인스턴스화합니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>다음 단계
* [대시보드 만들기](../../azure-monitor/app/overview-dashboard.md)
* [진단 검색](../../azure-monitor/app/diagnostic-search.md)
* [메트릭 탐색](../../azure-monitor/platform/metrics-charts.md)
* [분석 쿼리 작성](../../azure-monitor/app/analytics.md)

