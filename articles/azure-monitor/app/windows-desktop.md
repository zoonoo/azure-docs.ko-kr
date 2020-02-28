---
title: Windows 데스크톱 앱의 사용량 및 성능 모니터링
description: Application Insights를 사용하여 Windows 데스크톱 앱의 사용량 및 성능을 분석합니다.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 8234b9ba2c92fc64cfa8f598db99954e00caab45
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670834"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>클래식 Windows 데스크톱 앱에서 사용량 및 성능 모니터링

온-프레미스 호스팅된 애플리케이션은 Azure 및 다른 클라우드에서 Application Insights를 충분히 활용할 수 있습니다. 유일한 제한은 Application Insights 서비스와 [통신을 허용](../../azure-monitor/app/ip-addresses.md)해야 한다는 것입니다. UWP(유니버설 Windows 플랫폼) 애플리케이션 모니터링에는 [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md)가 좋습니다.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>클래식 Windows 애플리케이션에서 Application Insights에 원격 분석을 전송하려면
1. [Azure Portal](https://portal.azure.com)에서 [Application Insights 리소스를 만듭니다](../../azure-monitor/app/create-new-resource.md ). 애플리케이션 유형으로 ASP.NET 앱을 선택합니다.
2. 계측 키를 복사합니다. 방금 만든 새 리소스의 필수 드롭다운에서 키를 찾습니다. 
3. Visual Studio에서 앱 프로젝트의 NuGet 패키지를 편집하고 Microsoft.ApplicationInsights.WindowsServer를 추가합니다. (또는 표준 원격 분석 수집 모듈 없이 API를 사용하려면 Microsoft.ApplicationInsights를 선택합니다.)
4. 코드에서 계측 키를 설정합니다.
   
    *키* `";` `TelemetryConfiguration.Active.InstrumentationKey = "`
   
    또는 ApplicationInsights.config에서(표준 원격 분석 패키지 중 하나를 설치한 경우).
   
    `<InstrumentationKey>`*키*`</InstrumentationKey>` 
   
    ApplicationInsights.config를 사용하는 경우 솔루션 탐색기에서 해당 속성이 **빌드 작업 = 콘텐츠, 출력 디렉터리로 복사 = 복사**로 설정되도록 합니다.
5. [API를 사용](../../azure-monitor/app/api-custom-events-metrics.md) 하여 원격 분석을 전송합니다.
6. 앱을 실행 하 고 Azure Portal에서 만든 리소스의 원격 분석을 확인 합니다.

## <a name="telemetry"></a>예제 코드
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

## <a name="override-storage-of-computer-name"></a>컴퓨터 이름 저장소 재정의

기본적으로이 SDK는 원격 분석을 내보내는 시스템의 컴퓨터 이름을 수집 하 고 저장 합니다. 컬렉션을 재정의 하려면 원격 분석 이니셜라이저를 사용 해야 합니다.

**아래와 같이 사용자 지정 TelemetryInitializer을 작성 합니다.**

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
계측 키를 설정 하는 `Program.cs` `Main()` 메서드에서 이니셜라이저를 인스턴스화합니다.

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
* [메트릭 탐색](../../azure-monitor/app/metrics-explorer.md)
* [분석 쿼리 작성](../../azure-monitor/app/analytics.md)

