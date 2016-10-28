<properties 
	pageTitle="스트리밍 로그 및 콘솔" 
	description="스트리밍 로그 및 콘솔 개요" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/26/2016" 
	ms.author="byvinyal"/>

#스트리밍 로그 및 콘솔

### 스트리밍 로그 ###

Microsoft Azure 포털은 통합 스트리밍 로그 뷰어를 제공하며, 이 뷰어에서 앱 서비스 앱의 추적 이벤트를 실시간으로 볼 수 있습니다.

이를 설정하려면 몇 가지 간단한 단계를 수행해야 합니다.

- 코드에 추적 쓰기
- Azure 포털에서 응용 프로그램 진단 사용
- 웹 앱의 블레이드에서 스트리밍 로그 부분 클릭

### 코드에서 추적을 쓰는 방법 ###

코드에 추적을 쓰는 작업은 쉽습니다. C#에서는 다음 코드를 쓰기만 하면 될 정도로 쉽습니다.

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

The Trace class lives in the System.Diagnostics namespace.

node.js 앱에서는 다음 코드를 쓰면 동일한 결과를 얻을 수 있습니다.

`````````````````````````
console.log("My trace statement").
`````````````````````````

### 스트리밍 로그를 사용하고 보는 방법 ###
![][BrowseSitesScreenshot] 진단은 웹 앱별로 사용하도록 설정됩니다. [포털](https://portal.azure.com) 내에서 이 기능을 사용하도록 설정할 사이트로 이동합니다.
  
![][DiagnosticsLogs] 그런 다음 **(1) 설정** > **(2) 진단 로그**를 클릭하고 **응용 프로그램 로깅(파일 시스템)** 또는 **응용 프로그램 로깅(blob)**을 **(3) 설정**합니다. **수준** 옵션을 통해 캡처할 추적의 심각도 수준을 변경할 수 있습니다. **세부 정보 표시**는 모든 추적 스트리밍을 로깅하므로, 이 기능에 익숙해지고 싶으면 이 설정을 지정해야 합니다.

블레이드의 위에 있는 **저장**을 클릭하면 로그를 볼 수 있습니다.

**참고:** 더 높은 **심각도 수준**을 설정하면 로그에 더 많은 리소스를 사용하여 더 상세한 추적을 얻게 됩니다. 이 기능을 트래픽이 많은 사이트나 프로덕션 사이트에 사용할 때는 적합한 수준으로 설정하도록 하십시오.

![][StreamingLogsScreenshot]포털 내에서 스트리밍 로그를 보려면 **(1) 도구** > **(2) 로그 스트림**을 클릭합니다. 앱에서 추적 문을 활발하게 쓰는 경우 **(3)** 결과 창에서 거의 실시간으로 볼 수 있습니다.

## 콘솔 ##
Azure 포털에서는 웹 앱 환경에 대한 콘솔 액세스를 제공합니다. 웹 앱의 파일 시스템을 탐색하고 Powershell/cmd 스크립트를 실행할 수 있습니다. 콘솔 명령을 실행할 때 실행 중인 웹 앱 코드와 동일한 권한 집합이 적용됩니다. 보호된 디렉터리에 액세스할 수 없거나 관리자 권한이 필요한 스크립트를 실행할 수 없습니다.

![][ConsoleScreenshot] 콘솔에 액세스하려면 위의 섹션에 설명된 대로 웹 앱으로 이동합니다. **(1) 도구** > **(2) 콘솔**을 클릭하면 **(3)** 콘솔이 열립니다.

콘솔에 친숙해지려면 다음과 같은 기본 명령을 시도하세요.

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!---HONumber=AcomDC_0727_2016-->