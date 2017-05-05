---
title: "웹앱 모니터링 | Microsoft Docs"
description: "웹앱의 모니터링을 설정하는 방법 알아보기"
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 8d9b4a4fa3b62659fc7e2aa1c6329fdc5e01fe39
ms.lasthandoff: 04/21/2017

---
# <a name="monitor-app-service"></a>App Service 모니터링
이 자습서에서는 앱을 모니터링하고 내장된 플랫폼 도구를 사용하여 문제가 발생했을 때 이를 해결하는 방법을 안내합니다.

이 문서의 각 섹션은 특정 기능을 설명합니다. 기능을 함께 사용하면 다음을 수행할 수 있습니다.
- 앱 문제 식별
- 코드 또는 플랫폼으로 인해 문제가 발생한 시기 확인
- 코드에서 문제가 발생한 원본 범위 좁히기
- 문제 디버깅 및 해결

## <a name="before-you-begin"></a>시작하기 전에
- 모니터링할 웹앱을 준비하고 설명된 단계를 따라야 합니다. 
    - [SQL Database를 사용하여 Azure에서 ASP.NET 응용 프로그램 만들기](app-service-web-tutorial-dotnet-sqldatabase.md) 자습서에 설명된 단계를 따라 응용 프로그램을 만들 수 있습니다.

- 응용 프로그램의 **원격 디버깅**을 시험해 보려면 Visual Studio가 필요합니다. 
    - Visual Studio 2017이 아직 설치되지 않은 경우 무료 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. 
    - Visual Studio를 설정하는 동안 **Azure 개발**을 사용할 수 있는지 확인합니다.

## <a name="metrics"></a> 1단계 - 메트릭 보기
**메트릭**을 통해 다음을 이해할 수 있습니다. 
- 응용 프로그램 상태
- 앱 성능
- 리소스 사용

응용 프로그램 문제를 조사할 때는 제일 먼저 메트릭을 검토하는 것이 좋습니다. Azure Portal에서는 **Azure Monitor**를 사용하여 응용 프로그램의 메트릭을 빠르게 보고 검사할 수 있습니다.

메트릭은 앱의 여러 주요 집계에 대한 기록 보기를 제공합니다. App Service에서 호스트되는 모든 응용 프로그램의 Web App 및 App Service 계획을 모두 모니터링해야 합니다.

> [!NOTE]
> App Service 계획은 앱을 호스트하는 데 사용되는 실제 리소스의 컬렉션을 나타냅니다. App Service 계획에 할당된 모든 응용 프로그램은 정의된 리소스를 공유하므로 여러 앱을 호스팅할 때 비용을 절감할 수 있습니다.
>
> App Service 계획은 다음을 정의합니다.
> * 지역: 북유럽, 미국 동부, 동남 아시아 등
> * 인스턴스 크기: 소, 중, 대 등
> * 확장 개수: 1, 2, 3개 인스턴스 등
> * SKU: 무료, 공유, 기본, 표준, 프리미엄 등

Web App에 대한 메트릭을 검토하려면 모니터링할 앱의 **개요** 블레이드로 이동합니다. 여기에서 앱의 메트릭에 대한 차트를 **모니터링 타일**로 볼 수 있습니다. 보고 싶은 메트릭과 표시할 시간 범위를 편집 및 구성하려면 타일을 클릭합니다. 

기본적으로 리소스 블레이드는 지난 1시간 동안의 응용 프로그램 요청 및 오류에 대한 보기를 제공합니다.
![앱 모니터링](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

이 예에서 알 수 있듯이 많은 **HTTP 서버 오류**를 생성하는 응용 프로그램이 있습니다. 많은 오류는 이 응용 프로그램을 조사해야 하는 첫 번째 이유가 됩니다.

> [!TIP]
> 다음 링크를 통해 Azure Monitor에 대해 자세히 알아봅니다.
> - [Azure Monitor 시작](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Azure 메트릭](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Azure Monitor에서 지원되는 메트릭](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Azure 대시보드](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a> 2단계 - 경고 구성
앱의 특정 조건에서 **경고**가 트리거되도록 구성할 수 있습니다.

[1단계 - 메트릭 보기](#metrics)에서 응용 프로그램에 많은 오류가 있음을 확인했습니다. 

오류가 발생하면 자동으로 알리도록 경고를 구성할 수 있습니다. 이 경우 HTTP 50X 오류 수가 특정 임계값을 초과할 때마다 경고를 보내고 메일로 보내려고 합니다.

경고를 만들려면 **모니터링** > **경고**로 이동하여 **[+] 경고 추가**를 클릭합니다.

![경고](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

경고 구성에 대한 값을 제공합니다.
- **리소스:** 경고를 모니터링하는 사이트입니다. 
- **이름:** 경고의 이름입니다(이 경우 *High HTTP 50X*).
- **설명:** 이 경고에 대한 일반 텍스트 설명입니다.
- **경고 대상:** 경고 대상은 메트릭 또는 이벤트(이 예제에서는 메트릭)가 될 수 있습니다.
- **메트릭:** 모니터링할 메트릭(이 경우 *HTTP 서버 오류*)입니다.
- **조건:** 경고할 시기입니다. 이 예제에서는 *보다 큼* 옵션을 선택합니다.
- **임계값:** 검색할 값(이 경우 *400*)입니다.
- **기간:** 경고는 메트릭의 평균값에 대해 작동합니다. 시간이 짧을수록 민감한 경고가 발생합니다. 이 경우 *5분*입니다. 
- **메일 소유자 및 참가자:** 이 예에서는 *사용*입니다.

이제 경고가 생성되면 앱이 구성된 임계값을 초과할 때마다 메일이 전송됩니다. Azure Portal에서 활성 경고를 검토할 수도 있습니다.

![트리거된 경고](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts-triggered.png)


> [!TIP]
> 다음 링크를 통해 Azure 경고에 대해 자세히 알아봅니다.
> - [Microsoft Azure의 경고란?](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [메트릭에 대한 작업](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [메트릭 경고 만들기](..\monitoring-and-diagnostics\insights-alerts-portal.md)

## <a name="companion"></a> 3단계 - App Service 도우미
**App Service 도우미**를 사용하면 모바일 장치(iOS 또는 Android)의 기본 환경으로 간편하게 앱을 모니터링할 수 있습니다.

App Service 도우미를 사용하여 다음을 수행합니다.
- 응용 프로그램 메트릭 검토
- 경고 및 권장 사항 검토 및 조치
- 기본적인 문제 해결 수행(찾아보기, 시작, 중지, 응용 프로그램 다시 시작)
- 중요한 이벤트에 대한 푸시 알림을 가져옵니다.

![App Service 도우미](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![App Service 도우미 App Store](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![App Service 도우미 Google Play](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

App Service 도우미는 [App Store](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)나 [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps)에서 설치할 수 있습니다.

## <a name="diagnose"></a> 4단계 - 문제 진단 및 해결
**문제 진단 및 해결**은 플랫폼 문제와 응용 프로그램 문제를 구분할 수 있도록 도와 줍니다. 또한 Web App을 정상적으로 되돌릴 수 있는 완화 방법을 제안할 수도 있습니다.
 
![문제 진단 및 해결](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

이전 단계의 예제를 계속 진행하면 응용 프로그램에 가용성 문제가 있음을 알 수 있습니다. 반면, 플랫폼 가용성은 100%에서 이동되지 않습니다.

앱에 문제가 있고 플랫폼이 작동 중인 경우 응용 프로그램 문제를 다루고 있음을 분명히 알 수 있습니다.

## <a name="logging"></a>5 단계 - 로깅
이제 실패의 범위를 응용 프로그램 문제로 좁혔으므로 응용 프로그램 및 서버 로그를 확인하여 자세한 정보를 얻을 수 있습니다.

로깅을 사용하면 웹앱의 **응용 프로그램 진단** 및 **웹 서버 진단** 로그를 모두 수집할 수 있습니다.

### <a name="application-diagnostics"></a>응용 프로그램 진단
응용 프로그램 진단을 사용하면 런타임에 응용 프로그램에서 생성된 추적 정보를 캡처할 수 있습니다. 

응용 프로그램에 추적 기능을 추가하면 문제 디버깅 및 식별 기능이 크게 향상됩니다.

ASP.NET에서는 [System.Diagnostics.Trace 클래스](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx)를 사용하여 로그 인프라에서 캡처되는 이벤트를 생성하면 응용 프로그램 추적 정보를 로깅할 수 있습니다. 추적 정보의 심각도를 지정하여 필터링을 더 쉽게 만들 수도 있습니다.

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```
응용 프로그램 로깅을 사용하려면 **모니터링** > **진단 로그**로 이동한 다음 설정/해제를 사용하여 응용 프로그램 로깅을 사용하도록 설정합니다.

![앱 모니터링](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

응용 프로그램 로그는 웹앱의 파일 시스템에 저장하거나 Blob Storage에 푸시할 수 있습니다. 프로덕션 시나리오에서는 Blob Storage를 사용하는 것이 좋습니다.

> [!IMPORTANT]
> 로깅을 사용하도록 설정하면 응용 프로그램 성능과 리소스 사용률에 영향을 미칠 수 있습니다. 프로덕션 시나리오에서는 오류 로그를 사용하는 것이 좋습니다. 자세한 로깅은 문제를 조사할 때만 사용하도록 설정합니다.

 ### <a name="web-server-diagnostics"></a>웹 서버 진단
앱이 계측되지 않는 경우에도 웹 서버 로그는 생성됩니다. App Service에서는 세 가지 종류의 서버 로그를 수집할 수 있습니다.

- **웹 서버 로깅** 
    - [W3C 확장 로그 파일 형식](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)을 사용하는 HTTP 트랜잭션에 대한 정보입니다. 
    - 처리된 요청 수, 특정 IP 주소에서 들어온 요청 수 등의 전체 사이트 메트릭을 확인하는 경우에 유용합니다.
- **자세한 오류 로깅** 
    - 오류를 나타내는 HTTP 상태 코드(상태 코드 400 이상)의 자세한 오류 정보입니다. 
    - [자세한 오류 로깅에 대한 자세한 정보](https://www.iis.net/learn/troubleshoot/diagnosing-http-errors/how-to-use-http-detailed-errors-in-iis)
- **실패한 요청 추적** 
    - 요청을 처리하는 데 사용된 IIS 구성 요소 추적 및 각 구성 요소에 소요된 시간을 포함하여 실패한 요청에 대해 자세한 정보입니다. 
    - 실패한 요청 로그는 특정 HTTP 오류를 일으키는 조건을 격리하려는 경우에 유용합니다.
    - [실패한 요청 추적에 대한 자세한 정보](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis)

서버 로깅을 사용하도록 설정하려면:
- **모니터링** > **진단 로그**로 이동합니다. 
- 설정/해제를 사용하여 다양한 종류의 웹 서버 진단을 사용하도록 설정합니다.

![앱 모니터링](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> 로깅을 사용하도록 설정하면 응용 프로그램 성능과 리소스 사용률에 영향을 미칠 수 있습니다. 프로덕션 시나리오에서는 오류 로그를 사용하는 것이 좋으며, 자세한 로깅은 문제를 조사할 때만 사용하도록 설정합니다.

### <a name="accessing-logs"></a>로그 액세스
Blob Storage에 저장된 로그에는 Azure Storage Explorer를 사용하여 액세스합니다. 웹앱의 파일 시스템에 저장된 로그에는 다음 경로로 FTP를 통해 액세스합니다.

- **응용 프로그램 로그** - `%HOME%/LogFiles/Application/`.
    - 이 폴더에는 응용 프로그램 로깅으로 생성된 정보가 포함된 하나 이상의 텍스트 파일이 포함됩니다.
- **실패한 요청 추적** - `%HOME%/LogFiles/W3SVC#########/`. 
    - 이 폴더에는 하나의 XSL 파일 및 하나 이상의 XML 파일이 포함되어 있습니다. 
- **자세한 오류 로그** - `%HOME%/LogFiles/DetailedErrors/`. 
    - 이 폴더에는 응용 프로그램에서 생성되는 HTTP와 관련된 정보가 광범위하게 포함된 .htm 파일이 하나 이상 있습니다.
- **웹 서버 로그** - `%HOME%/LogFiles/http/RawLogs`. 
    - 이 폴더에는 W3C 확장 로그 파일 형식을 사용하여 서식이 지정된 하나 이상의 텍스트 파일이 포함되어 있습니다.

## <a name="streaming"></a>6 단계 - 스트리밍 로그
스트리밍 로그는 FTP를 통해 [로그에 액세스할](#Accessing-Logs) 때보다 시간이 절약되므로 응용 프로그램을 디버깅할 때 편리합니다.

App Service에서는 **응용 프로그램 로그** 및 **웹 서버 로그**가 생성될 때 스트림할 수 있습니다. 

> [!TIP]
> 로그를 스트림하기 전에, [로깅](#logging) 섹션에 설명된 대로 로그 수집을 사용하도록 설정했는지 확인합니다.

로그를 스트리밍하려면 **모니터링**> **로그 스트림**으로 이동합니다. 원하는 정보에 따라 **응용 프로그램 로그** 또는 **웹 서버 로그**를 선택합니다. 여기에서 버퍼를 일시 중지, 다시 시작하거나 지울 수도 있습니다.

![스트리밍 로그](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> 로그는 응용 프로그램에 트래픽이 있을 때만 생성되며, 로그를 더 상세하게 설정하면 더 많은 이벤트와 정보를 기록할 수 있습니다.

## <a name="remote"></a>7 단계 - 원격 디버깅
응용 프로그램 문제의 원인을 찾은 후에는 **원격 디버깅**을 사용하여 코드를 확인합니다.

원격 디버깅을 사용하면 클라우드에서 실행 중인 Web App에 디버거를 연결할 수 있습니다. 중단점을 설정하고 메모리를 직접 조작하며 코드를 단계별로 실행하고 심지어는 응용 프로그램을 로컬로 실행할 때처럼 코드 경로를 변경할 수 있습니다.

디버거를 클라우드에서 실행 중인 응용 프로그램에 연결하려면:

- Visual Studio 2017을 사용하여 디버그할 응용 프로그램의 솔루션을 엽니다. 
- 로컬 개발의 경우와 마찬가지로 중단점을 몇 개 설정합니다.
- **클라우드 탐색기**를 엽니다(ctr + /, ctrl + x).
- 필요하면 Azure 자격 증명으로 로그인합니다.
- 디버그할 응용 프로그램을 찾습니다.
- **작업** 창에서 **디버거 연결**을 선택합니다.

![원격 디버깅](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

Visual Studio에서는 응용 프로그램을 원격 디버깅에 적합하게 구성하고 응용 프로그램으로 이동하는 브라우저 창을 실행합니다. 응용 프로그램을 탐색하며 중단점을 트리거하고 코드를 단계별로 확인합니다.

> [!WARNING]
> 프로덕션 사이트에서 디버그 모드로 실행하는 것은 권장되지 않습니다. 프로덕션 응용 프로그램이 여러 서버 인스턴스로 확장되지 않은 경우 디버깅으로 인해 웹 서버에서 다른 요청에 응답할 수 없습니다. 프로덕션 문제를 해결할 때 가장 좋은 리소스는 [로깅 구성](#logging) 및 [Application Insights](#insights)를 구성하는 것입니다.



## <a name="explorer"></a> 8단계 - 프로세스 탐색기
응용 프로그램이 둘 이상의 인스턴스로 확장되면 **프로세스 탐색기**를 사용하여 인스턴스의 문제를 식별할 수 있습니다.

**Process Explorer**를 사용하여 다음을 수행할 수 있습니다.

- App Service 계획의 모든 인스턴스에서 모든 프로세스를 열거합니다.
- 드릴다운하고 각 프로세스와 연결된 핸들 및 모듈을 봅니다. 
- 런어웨이 프로세스를 쉽게 식별할 수 있도록 프로세스 수준에서 CPU, 작업 집합 및 스레드 수를 봅니다.
- 열려 있는 파일 핸들을 찾고, 필요하면 특정 프로세스 인스턴스를 종료합니다.

프로세스 탐색기는 **모니터링** > **프로세스 탐색기**에 있을 수 있습니다.

![Process Explorer](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)


## <a name="insights"></a> 9단계 - Application Insights
**Application Insights**에서는 응용 프로그램에 대한 응용 프로그램 프로파일링 및 고급 모니터링 기능을 제공합니다. 

웹앱에서 예외 및 성능 문제를 감지하고 진단하려면 Application Insights를 사용합니다.

**모니터링** > **Application Insights**에서 웹앱에 대해 Application Insights를 사용하도록 설정할 수 있습니다. 

> [!NOTE]
> Application Insights에서 데이터 수집을 시작하려면 Application Insights 사이트 확장을 설치하라는 메시지를 표시할 수도 있습니다. 사이트 확장을 설치하면 응용 프로그램이 다시 시작됩니다.

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

Application Insights에는 풍부한 기능 집합이 있으며, [다음 단계](#next) 섹션의 링크를 이용하면 자세한 정보를 볼 수 있습니다.

## <a name="next"></a> 다음 단계

 - [Application Insights란?](..\application-insights\app-insights-overview.md)
 - [Application Insights를 사용한 Azure 웹앱 성능 모니터링](..\application-insights\app-insights-azure-web-apps.md)
 - [Application Insights를 사용한 웹 사이트의 가용성 및 응답성 모니터링](..\application-insights\app-insights-monitor-web-app-availability.md)
