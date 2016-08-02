<properties pageTitle="앱 서비스의 느린 웹앱 성능 | Microsoft Azure" description="이 문서에서는 Azure 앱 서비스의 느린 웹앱 성능 문제를 해결하는 데 도움을 줍니다." services="app-service\\web" documentationCenter="" authors="cephalin" manager="wpickett" editor="" tags="top-support-issue keywords="웹앱 성능, 느린 앱, 앱이 느림"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="cephalin"/>

# Azure 앱 서비스에서 느린 웹앱 성능 문제 해결

이 문서에서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)의 느린 웹앱 성능 문제를 해결하는 데 도움을 줍니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동한 다음 **지원 받기**를 클릭합니다.

## 증상

웹앱을 탐색할 때, 페이지 로딩이 느려지거나 멈춤.

## 원인

이 문제는 응용 프로그램 수준 문제가 원인이 되는 경우가 많습니다. 예를 들어:

-	긴 요청 시간
-	높은 메모리/CPU를 사용하는 응용 프로그램
-	예외로 인해 충돌하는 응용 프로그램

## 문제 해결 단계

문제 해결은 해결하는 순서대로 세 가지로 구분할 수 있습니다.

1.	[응용 프로그램 작동을 관찰 및 감시](#observe)
2.	[데이터 수집](#collect)
3.	[문제 완화](#mitigate)

[앱 서비스 웹앱](/services/app-service/web/)은 각 단계별로 다양한 옵션을 제공합니다.

<a name="observe" />
### 1\. 응용 프로그램 작동을 관찰 및 감시

#### 서비스 상태를 추적합니다.

Microsoft Azure는 서비스가 중단되거나 성능이 저하될 때마다 경고를 표시합니다. [Azure 포털](https://portal.azure.com/)에서 서비스의 상태를 추적할 수 있습니다. 자세한 내용은 [서비스 상태 추적](../azure-portal/insights-service-health.md)을 참조하세요.

#### 웹앱 모니터링

이 옵션은 응용 프로그램의 문제를 해결할 수 있게 도와줍니다. 웹앱의 블레이드에서 **요청 및 오류** 타일을 클릭합니다. **메트릭** 블레이드는 추가 가능한 모든 메트릭을 보여줍니다.

모니터링 하고자 하는 웹앱의 일부 메트릭은 다음과 같습니다.

-	평균 메모리 작업 집합
-	평균 응답 시간
-	CPU 시간
-	메모리 작업 집합
-	요청

![웹앱 성능 모니터링](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

자세한 내용은 다음을 참조하세요.

-	[Azure 앱 서비스에서 웹앱 모니터링](web-sites-monitor.md)
-	[경고 알림 받기](../azure-portal/insights-receive-alert-notifications.md)

#### 웹 끝점 상태 모니터링

**표준** 가격 정책에서 웹앱을 실행할 경우, 3곳의 지리적 위치에서 끝점 2개를 모니터링할 수 있습니다.

끝점 모니터링은 지리적으로 분산된 위치에서 웹 URL의 응답 시간 및 가동 시간을 테스트하는 웹 테스트를 구성합니다. 테스트는 웹 URL의 HTTP 가져오기 작업을 수행하여 각 위치의 응답 시간 및 가동 시간을 확인합니다. 구성된 각 위치에서는 5분마다 테스트를 실행합니다.

가동 시간은 HTTP 응답 코드를 사용하여 모니터링되며 응답 시간은 밀리초로 측정됩니다. HTTP 응답 코드가 400 이상이거나 응답에 30초 넘게 걸리는 경우 모니터링 테스트가 실패합니다. 지정된 모든 위치에서 모니터링 테스트가 성공하는 경우 끝점은 사용 가능한 것으로 간주됩니다.

설정하려면, [웹 끝점 상태 모니터링 하는 방법](web-sites-monitor.md#webendpointstatus)을 참조하세요.

또한, 끝점 모니터링의 비디오에 대해서는[Azure 웹 사이트 가동 및 끝 점 모니터링 - 스테판 스차코우(Stefan Schackow)](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)를 참조하세요.

#### 확장을 사용하여 응용 프로그램 성능 모니터링

응용 프로그램 성능을 _사이트 확장_을 활용하여 모니터링 할 수도 있습니다.

각 응용 프로그램 서비스 웹앱은 사이트 확장처럼 활용할 수 있는 배포 된 강력한 도구 집합인 확장 가능한 관리 끝점을 제공합니다. 이 도구의 범위는 [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx)같은 원본 코드 에디터부터 웹앱에 연결된 MySQL 데이터베이스와 같은 연결된 리소스까지 입니다.

[Azure Application Insights](/services/application-insights/) 및 [New Relic](/marketplace/partners/newrelic/newrelic/)은 두 가지 사이트 확장 성능 모니터링이 가능합니다. New Relic을 사용하려면 런타임에 에이전트를 설치해야 합니다. Azure Application Insights를 사용하려면, SDK를 사용하여 코드를 다시 작성해야 하고 추가 데이터 접근을 제공하는 확장 프로그램을 설치해야 할 수도 있습니다. SDK를 통해 앱의 사용과 성능을 보다 자세하게 모니터링하기 위한 코드를 작성할 수 있습니다.

Application Insights를 사용하려면 [웹 응용 프로그램에서 모니터 성능](../application-insights/app-insights-web-monitor-performance.md)을 참조하세요.

New Relic을 사용하려면 [Azure에서 New Relic 응용 프로그램 성능 관리](../store-new-relic-cloud-services-dotnet-application-performance-management.md)를 참조하세요.

<a name="collect" />
### 2\. 데이터 수집

####	웹앱에 진단 로깅 사용하기

웹앱 환경은 웹 서버 및 웹 응용 프로그램 두 곳 모두에서 로깅 정보에 대한 진단 기능을 제공합니다. 이는 논리적으로 웹 서버 진단 및 응용 프로그램 진단으로 구분됩니다.

##### 웹 서버 진단

다음과 같은 종류의 로그를 사용하거나 사용하지 않도록 설정할 수 있습니다.

-	**자세한 오류 로깅** - 오류를 나타내는 HTTP 상태 코드(상태 코드 400 이상)의 자세한 오류 정보입니다. 여기에는 서버에서 오류 코드를 반환한 이유를 확인하는 데 도움이 되는 정보가 포함될 수 있습니다.
-	**실패한 요청 추적** - 요청을 처리하는 데 사용된 IIS 구성 요소 추적 및 각 구성 요소에 소요된 시간을 포함하여 실패한 요청에 대해 자세한 정보입니다. 이 기능은 웹앱 성능을 개선하거나 특정 HTTP 오류 원인을 격리하려는 경우에 유용할 수 있습니다.
-	**웹 서버 로깅** - W3C 확장 로그 파일 형식을 사용하는 HTTP 트랜잭션에 대한 정보입니다. 이 기능은 처리된 요청의 수 또는 특정 IP 주소에서 넘어온 많은 요청처럼 전반적인 웹앱 메트릭을 결정할 때 유용합니다.

##### 응용 프로그램 진단

응용 프로그램 진단은 웹 응용 프로그램에서 생성되는 정보를 캡처할 수 있게 해줍니다. ASP.NET 응용 프로그램은 정보를 응용 프로그램 진단 로그에 기록하기 위해 `System.Diagnostics.Trace` 클래스를 사용할 수 있습니다.

로깅에 대한 응용 프로그램을 구성하는 방법에 대한 자세한 설명은 [Azure 앱 서비스에서 웹앱에 대한 진단 로깅 설정](web-sites-enable-diagnostic-log.md)을 참조하세요.

#### 원격 프로파일링 사용하기

Azure 앱 서비스에서 웹앱, API 앱 그리고 WebJob을 원격으로 프로파일할 수 있습니다. 프로세스가 예상보다 느리게 실행되거나, HTTP 요청 대기시간이 평상시 보다 높고 프로세스의 CPU 사용량 또한 높은 경우, 원격으로 프로세스를 프로파일할 수 있으며, 프로세스 활동과 코드 hot path를 분석하기 위해 CPU 샘플링 호출 스택을 가져옵니다.

자세한 내용은 [Azure 앱 서비스에서 원격 프로파일링 지원](/blog/remote-profiling-support-in-azure-app-service)을 참조하세요.


#### Azure 앱 서비스 지원 포털 사용

웹앱은 HTTP 로그, 이벤트 로그, 프로세스 덤프 등등을 확인하여 사용자의 웹앱과 연관된 문제 해결 기능을 제공합니다. 저희의 지원 포털 **http://&lt;your app name>.scm.azurewebsites.net/Support**를 사용하여 모든 정보에 액세스할 수 있습니다.

Azure 앱 서비스 지원 포털은 일반적인 문제 해결 시나리오의 세 단계를 지원하기 위해 3개의 별도 탭을 제공합니다.

1.	현재 동작을 관찰하기
2.	진단 정보 수집 및 기본 제공 분석기를 실행하여 분석하기
3.	완화

지금 문제가 발생했다면, **분석**>**진단**> **바로 진단**을 클릭하여 HTTP 로그, 이벤트 뷰어 로그, 메모리 덤프, PHP 오류 로그 및 PHP 보고서를 수집하는 진단 세션을 생성합니다.

데이터가 수집되면, 데이터에 대한 분석하고 HTML 보고서를 제공합니다.

데이터를 다운로드할 경우 기본적으로 D:\\home\\data\\DaaS folder에 저장됩니다.

Azure 앱 서비스 지원 포털에 대한 자세한 내용은 [Azure 웹 사이트의 지원 사이트 확장에 대한 새 업데이트](/blog/new-updates-to-support-site-extension-for-azure-websites)를 참조하세요.

#### Kudu 디버그 콘솔 사용

웹앱은 사용자 환경에 대한 정보를 얻을 수 있는 JSON 끝점과 비슷한 디버깅, 탐색, 파일 업로드할 수 있는 디버그 콘솔과 함께 제공됩니다. 이 콘솔은 _Kudu 콘솔_ 또는 사용자 웹앱에서는 _SCM 대시보드_라고 합니다.

**https://&lt;Your app name >.scm.azurewebsites.net/** 링크에 가서 대시보드에 액세스할 수 있습니다.

Kudu가 제공하는 것은 다음과 같습니다.

-	응용 프로그램에 대한 환경 설정
-	로그 스트림
-	진단 덤프
-	Powershell cmdlet 및 기본 DOS 명령을 실행할 수 있는 콘솔을 디버깅합니다.


Kudu의 또 다른 유용한 기능은 응용 프로그램에 첫 번째 예외가 발생할 경우, 메모리 덤프를 만들기 위해 Kudu와 SysInternal 도구 Procdump를 사용할 수 있습니다. 메모리 덤프는 프로세스의 스냅숏이며 웹앱의 더욱 복잡한 문제를 해결하는 데 많은 도움을 줍니다.

Kudu에서 사용 가능한 기능에 대한 자세한 내용은 [사용자가 꼭 알아야 할 Azure 웹 사이트 팀 서비스 도구](/blog/windows-azure-websites-online-tools-you-should-know-about/)를 참조하세요.

<a name="mitigate" />
### 3\. 문제 완화

####	웹앱의 크기를 조정합니다.

Azure 앱 서비스에서 성능과 처리량의 증가를 위해 사용자가 실행하는 응용 프로그램의 크기를 조정할 수 있습니다. 웹앱의 크기를 키우려면 두 가지 사항이 수반됩니다. 앱 서비스 계획을 더 높은 가격 책정 계층으로 바꿔야 하며, 높은 가격 계층으로 전환한 후에 특정 설정을 구성해야 합니다.

크기 조정에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 크기 조정](web-sites-scale.md)을 참조하세요.

또한 둘 이상의 인스턴스에서 응용 프로그램을 실행할 수 있습니다. 더 많은 처리 기능을 제공할 만 아니라, 어느 정도의 내결함성을 제공합니다. 하나의 인스턴스에서 프로세스가 다운되면, 또 다른 인스턴스에서 계속 요청을 처리합니다.

수동 또는 자동으로 크기를 조정할 수 있습니다.

####	AutoHeal를 사용

AutoHeal은 사용자가 선택한 설정(예: 구성 변경, 요청, 메모리 기반 제한 또는 요청을 실행하는데 필요한 시간)에 따라 앱에 대한 작업자 프로세스를 재활용 합니다. 대부분의 경우에는 프로세스를 재활용하는 방법이 문제를 해결하는 가장 빠른 방법입니다. Azure 포털 내에서 직접 웹앱을 재시작할 수도 있지만, AutoHeal은 자동으로 재시작을 수행하게 해줍니다. 몇 개의 트리거를 웹앱의 root web.config에 추가하기만 하면 됩니다. 이 설정은 사용자의 응용 프로그램이 .Net이 아니라도 같은 방식으로 작동됩니다.

자세한 내용은 [Auto-Healing Azure 웹 사이트](/blog/auto-healing-windows-azure-web-sites/)를 참조하세요.

####	웹앱 재시작

이 방법은 일회성 문제를 해결하는 가장 간단한 방법입니다. [Azure 포털](https://portal.azure.com/) 또는 웹앱의 블레이드에서 앱을 멈추거나 재시작 하는 옵션을 사용할 수 있습니다.

 ![웹앱을 다시 시작하여 성능 문제 해결](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

또한, Azure Powershell을 사용하여 웹앱을 관리할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

<!---HONumber=AcomDC_0713_2016-->