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
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 63bfc6922de224f56186003991f4a51d8f99ed35
ms.lasthandoff: 04/15/2017

---
# <a name="monitor-app-service"></a>App Service 모니터링
이 자습서에서는 기본 제공 플랫폼 도구를 사용하여 App Service에서 호스트되는 앱을 모니터링 및 진단하는 방법을 보여 줍니다. 

## <a name="in-this-tutorial"></a>자습서 내용

1. [Process Explorer](#explorer)
    - App Service 계획의 모든 인스턴스에서 실행 중인 앱과 관련된 상세 정보 보기
1. [App Service 메트릭](#metrics) 
   - 기본 제공 차트를 사용하여 앱을 모니터링하는 방법 알아보기
   - 요구 사항에 맞게 차트 구성
   - 사용자 지정 차트를 고정하여 사용자 지정 대시보드 만들기
1. [경고 구성](#alerts)
    - 앱 및 App Service 계획 모두의 경고를 구성하는 방법 알아보기
1. [App Service 도우미](#Companion)
    - 모바일 장치를 사용하여 앱을 모니터링하고 문제를 해결합니다.
1. [로깅 구성](#logging)
    - 서버 및 응용 프로그램 로그를 수집하는 방법을 알아봅니다.
    - 로그를 저장하는 여러 위치와 그 위치에 접근하는 방법에 대해 알아봅니다.
1. [스트리밍 로그](#streaming)
    - 응용 프로그램 및 wc3 로그를 내보낼 때 스트리밍 로그를 사용하여 볼 수 있습니다.
1. [원격 디버깅](#remote)
    - Visual studio를 사용하여 앱 서비스에서 실행하는 프로젝트를 원격으로 디버깅합니다.
1. [문제 진단 및 해결](#diagnose)
    - 응용 프로그램의 문제를 식별하고 해결 방법에 관한 정보를 얻습니다.
1. [Application Insights](#insights)
    - 앱의 고급 프로파일링 및 모니터링

## <a name="before-you-begin"></a>시작하기 전에

- 모니터링할 웹앱을 준비하고 설명된 단계를 따라야 합니다. 
    - [SQL Database를 사용하여 Azure에서 ASP.NET 응용 프로그램 만들기](app-service-web-tutorial-dotnet-sqldatabase.md) 자습서에 설명된 단계를 따라 응용 프로그램을 만들 수 있습니다.

- 응용 프로그램의 **원격 디버깅**을 시험해 보려면 Visual Studio가 필요합니다. 
    - Visual Studio 2017이 아직 설치되지 않은 경우 무료 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. 
    - Visual Studio를 설정하는 동안 **Azure 개발**을 사용할 수 있는지 확인합니다.

## <a name="explorer"></a>1 단계 - Process Explorer

Process Explorer는 App Service 계획의 내부 작업에 관해 자세한 정보를 얻을 수 있게 해 주는 도구입니다.

**Process Explorer**를 사용하여 다음을 수행할 수 있습니다.

- App Service 계획의 모든 인스턴스에서 모든 프로세스를 열거합니다.
- 드릴다운하고 각 프로세스와 연결된 핸들 및 모듈을 봅니다. 
- 런어웨이 프로세스를 쉽게 식별할 수 있도록 프로세스 수준에서 CPU, 작업 집합 및 스레드 수를 봅니다.
- 열려 있는 파일 핸들을 찾고, 필요하면 특정 프로세스 인스턴스를 종료합니다.

Process Explorer는 **모니터링** > **Process Explorer**에 있을 수 있습니다.

![Process Explorer](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)

## <a name="metrics"></a>2 단계 - App Service 메트릭 보기
**메트릭**은 웹앱, 그리고 웹앱과 사용자 및 플랫폼 사이의 상호 작용에 관해 자세한 정보를 제공합니다.

메트릭을 사용하여 다음을 파악할 수 있습니다.
- 앱에서 사용 중인 리소스의 수
- 앱의 트래픽 볼륨
- 전체 요청/실패
- 데이터 입력/출력 볼륨

앱 서비스에서 호스트되는 모든 응용 프로그램의 웹앱 및 App Service 계획을 모니터링해야 합니다.

- **응용 프로그램** 메트릭은 http 요청/실패와 평균 응답 시간에 대한 정보를 제공합니다.
- **App Service 계획** 메트릭은 리소스 사용률에 대한 정보를 제공합니다.

Azure Portal에서는 **Azure Monitor**를 사용하여 응용 프로그램의 메트릭을 빠르게 보고 검사할 수 있습니다.

- 모니터링할 응용 프로그램의 **개요** 블레이드로 이동합니다.

![앱 모니터링](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

- 응용 프로그램의 메트릭을 **모니터링 타일**로 볼 수 있습니다.
- 보고 싶은 메트릭과 표시할 시간 범위를 편집 및 구성하려면 타일을 클릭합니다.

![차트 구성](media/app-service-web-tutorial-monitoring/app-service-monitor-configure.png)

- 사용자 지정 차트를 대시보드에 고정하여 쉽게 액세스하고 빠르게 참조할 수 있습니다.

![차트 고정](media/app-service-web-tutorial-monitoring/app-service-monitor-pin.png)

> [!TIP]
> 다음 링크를 통해 Azure Monitor에 대해 자세히 알아봅니다.
> - [Azure Monitor 시작](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Azure 메트릭](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Azure Monitor에서 지원되는 메트릭](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Azure Portal 대시보드](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a>3 단계 - 경고 구성

**경고**를 사용하면 응용 프로그램의 모니터링을 자동화할 수 있습니다.

응용 프로그램에 영향을 주는 흥미로운 조건이 검색될 때 알림을 받으려면 경고를 사용합니다.

경고를 만들려면:
- 모니터링할 응용 프로그램의 **개요** 블레이드로 이동합니다.
- 메뉴에서 **모니터링** > **경고**로 이동합니다.
- **[+] 경고 추가**를 선택합니다.
- 필요에 따라 경고를 구성합니다.

![경고](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

> [!TIP]
> 다음 링크를 통해 Azure 경고에 대해 자세히 알아봅니다.
> - [Microsoft Azure의 경고란?](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [메트릭에 대한 작업](..\monitoring-and-diagnostics\monitoring-overview.md)

## <a name="companion"></a>4단계 - App Service 도우미
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

## <a name="logging"></a>5 단계 - 로깅
로깅을 사용하면 웹앱의 **응용 프로그램 진단** 및 **웹 서버 진단** 로그를 모두 수집할 수 있습니다.

진단 로그를 사용하여 응용 프로그램 동작을 이해하고, 응용 프로그램 문제를 해결하고, 오류 조건을 이해합니다.

### <a name="application-diagnostics"></a>응용 프로그램 진단
응용 프로그램 진단을 사용하면 런타임에 응용 프로그램에서 생성된 추적 정보를 캡처할 수 있습니다. 

응용 프로그램 로깅을 사용하도록 설정하려면:

- **모니터링** > **진단 로그**로 이동합니다. 
- 설정/해제를 사용하여 응용 프로그램 로깅을 사용하도록 설정합니다.

응용 프로그램 로그는 웹앱의 파일 시스템에 저장하거나 Blob Storage에 푸시할 수 있습니다.

> [!TIP]
> 프로덕션 시나리오에서는 Blob Storage를 사용하는 것이 좋습니다.

![앱 모니터링](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

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

> [!IMPORTANT]
> 로깅을 사용하도록 설정하면 응용 프로그램 성능과 리소스 사용률에 영향을 미칠 수 있습니다. 프로덕션 시나리오에서는 오류 로그를 사용하는 것이 좋습니다. 자세한 로깅은 문제를 조사할 때만 사용하도록 설정합니다.

 ### <a name="web-server-diagnostics"></a>웹 서버 진단
App Service에서는 세 가지 종류의 서버 로그를 수집할 수 있습니다.

- **웹 서버 로깅** 
    - [W3C 확장 로그 파일 형식](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)을 사용하는 HTTP 트랜잭션에 대한 정보입니다. 
    - 처리된 요청 수, 특정 IP 주소에서 들어온 요청 수 등의 전체 사이트 메트릭을 확인하는 경우에 유용합니다.
- **자세한 오류 로깅** 
    - 오류를 나타내는 HTTP 상태 코드(상태 코드 400 이상)의 자세한 오류 정보입니다. 
- **실패한 요청 추적** 
    - 요청을 처리하는 데 사용된 IIS 구성 요소 추적 및 각 구성 요소에 소요된 시간을 포함하여 실패한 요청에 대해 자세한 정보입니다. 
    - 실패한 요청 로그는 특정 HTTP 오류를 일으키는 조건을 격리하려는 경우에 유용합니다.

서버 로깅을 사용하도록 설정하려면:
- **모니터링** > **진단 로그**로 이동합니다. 
- 설정/해제를 사용하여 다양한 종류의 웹 서버 진단을 사용하도록 설정합니다.

![앱 모니터링](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> 로깅을 사용하도록 설정하면 응용 프로그램 성능과 리소스 사용률에 영향을 미칠 수 있습니다. 프로덕션 시나리오에서는 오류 로그를 사용하는 것이 좋으며, 자세한 로깅은 문제를 조사할 때만 사용하도록 설정합니다.

### <a name="accessing-logs"></a>로그 액세스
Blob Storage에 저장된 로그에는 Azure Storage Explorer를 사용하여 액세스합니다.

웹앱의 파일 시스템에 저장된 로그에는 다음 경로로 FTP를 통해 액세스합니다.

- **응용 프로그램 로그** - /LogFiles/Application/입니다. 
    - 이 폴더에는 응용 프로그램 로깅으로 생성된 정보가 포함된 하나 이상의 텍스트 파일이 포함됩니다.
- **실패한 요청 추적** - /LogFiles/W3SVC#########/입니다. 
    - 이 폴더에는 하나의 XSL 파일 및 하나 이상의 XML 파일이 포함되어 있습니다. 
- **Detailed Error Logs** - /LogFiles/DetailedErrors/입니다. 
    - 이 폴더에는 응용 프로그램에서 생성되는 HTTP와 관련된 정보가 광범위하게 포함된 .htm 파일이 하나 이상 있습니다.
- **웹 서버 로그** - /LogFiles/http/RawLogs입니다. 
    - 이 폴더에는 W3C 확장 로그 파일 형식을 사용하여 서식이 지정된 하나 이상의 텍스트 파일이 포함되어 있습니다.

## <a name="streaming"></a>6 단계 - 스트리밍 로그
App Service에서는 **응용 프로그램 로그** 및 **웹 서버 로그**가 생성될 때 스트림할 수 있습니다. 

스트리밍 로그는 FTP를 통해 다른 방법으로 로그에 액세스할 때보다 시간이 절약되므로 응용 프로그램을 디버깅할 때 편리합니다.

> [!TIP]
> 로그를 스트림하기 전에, [로깅](#logging) 섹션에 설명된 대로 로그 수집을 사용하도록 설정했는지 확인합니다.

로그를 스트림하려면:
- **모니터링**> **로그 스트림**으로 이동합니다.
- 원하는 정보에 따라 **응용 프로그램 로그** 또는 **웹 서버 로그**를 선택합니다.
- 여기에서 버퍼를 일시 중지, 다시 시작하거나 지울 수도 있습니다.

![스트리밍 로그](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> 로그는 응용 프로그램에 트래픽이 있을 때만 생성되며, 로그를 더 상세하게 설정하면 더 많은 이벤트와 정보를 기록할 수 있습니다.

## <a name="remote"></a>7 단계 - 원격 디버깅
**원격 디버깅**을 사용하면 클라우드에서 실행 중인 웹앱에 디버거를 연결할 수 있습니다. 중단점을 설정하고 메모리를 직접 조작하며 코드를 단계별로 실행하고 심지어는 응용 프로그램을 로컬로 실행할 때처럼 코드 경로를 변경할 수 있습니다.

진단 로그와 원격 디버깅을 함께 사용하여 응용 프로그램의 문제를 찾고 해결합니다.

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
> 프로덕션 사이트에서 디버그 모드로 실행하는 것은 권장되지 않습니다. 프로덕션 응용 프로그램이 여러 서버 인스턴스로 확장되지 않은 경우 디버깅으로 인해 웹 서버에서 다른 요청에 응답할 수 없습니다. 프로덕션 문제를 해결할 때 가장 좋은 리소스는 [로깅](#logging) 및 [Application Insights](#insights)를 구성하는 것입니다.

## <a name="diagnose"></a> 8단계 - 문제 진단 및 해결
**문제 진단 및 해결**은 최근 24시간 동안의 웹앱 활동을 검색하는 기본 제공 도구입니다. UX는 식별된 문제에 대한 요약 보기를 표시합니다.

이 기능을 사용하면 응용 프로그램 문제를 플랫폼 문제와 구별하고 웹앱을 다시 정상 상태로 되돌릴 수 있는 완화 방법을 찾는 데 도움이 됩니다.

![문제 진단 및 해결](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

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
