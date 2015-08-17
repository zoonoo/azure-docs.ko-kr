<properties 
	pageTitle="스트리밍 로그 및 콘솔" 
	description="스트리밍 로그 및 콘솔 개요"
	authors="adamabdelhamed" 
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
	ms.date="04/25/2015" 
	ms.author="adamab"/>

#스트리밍 로그 및 콘솔

### 스트리밍 로그 ###

[Azure preview 포털](http://go.microsoft.com/fwlink/?LinkId=529715)은 통합 스트리밍 로그 뷰어를 제공하며, 이 뷰어에서 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹앱의 추적 이벤트를 실시간으로 볼 수 있습니다.

이를 설정하려면 몇 가지 간단한 단계를 수행해야 합니다.

- 코드에 추적 쓰기
- Azure Preview 포털에서 응용 프로그램 진단 사용
- 웹앱의 블레이드에서 스트리밍 로그 타일 클릭

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

### 스트리밍 로그 사용 및 보기 ###

진단은 웹 앱별로 사용하도록 설정됩니다.

1. [Azure Preview 포털](https://portal.azure.com) 내에서 **찾아보기** > **웹앱**을 클릭하여 모든 웹앱의 목록을 가져옵니다.  

2. 구성할 웹 앱의 이름을 클릭합니다.

3. **모든 설정** > **진단 로그**를 클릭하고 **응용 프로그램 로깅(파일 시스템)** 스위치를 **설정**으로 전환합니다.

4. 그러면 **수준** 옵션이 표시되므로, 캡처할 추적의 보안 수준을 변경할 수 있습니다. **세부 정보 표시**는 모든 추적 스트리밍을 로깅하므로, 이 기능에 익숙해지고 싶으면 이 설정을 지정해야 합니다.

5. 블레이드의 위에 있는 **저장**을 클릭하면 로그를 볼 수 있습니다.

6. 포털에서 스트리밍 로그를 보려면 웹앱 블레이드에서 **스트리밍 로그** 타일을 클릭합니다. 앱에서 추적 문을 활발하게 쓰는 경우 결과 창에서 거의 실시간으로 볼 수 있습니다.

![][StreamingLogsScreenshot]

## 콘솔 액세스 ##

Azure Preview 포털에서는 웹앱에 대한 콘솔 액세스를 제공합니다. 웹 앱의 파일 시스템을 탐색하고 Powershell/cmd 스크립트를 실행할 수 있습니다. 콘솔 명령을 실행할 때 실행 중인 웹 앱 코드와 동일한 권한 집합이 적용됩니다. 보호된 디렉터리에 액세스할 수 없거나 관리자 권한이 필요한 스크립트를 실행할 수 없습니다.

1. 위의 섹션에 설명된 대로 웹앱의 블레이드로 이동합니다.

2. **콘솔** 타일을 클릭하면 콘솔이 열립니다.

![][ConsoleScreenshot]

콘솔에 친숙해지려면 다음과 같은 기본 명령을 시도하세요.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=August15_HO6-->