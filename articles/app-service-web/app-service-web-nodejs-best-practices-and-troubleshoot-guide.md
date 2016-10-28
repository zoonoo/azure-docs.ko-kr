<properties
	pageTitle="Azure 웹앱에서 노드 응용 프로그램에 대한 모범 사례 및 문제 해결 가이드"
	description="Azure 웹앱에서 노드 응용 프로그램에 대한 모범 사례 및 문제 해결 단계에 대해 알아봅니다."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="ranjithr"
	manager="wadeh"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="06/06/2016"
	ms.author="ranjithr;wadeh"/>
    
# Azure 웹앱에서 노드 응용 프로그램에 대한 모범 사례 및 문제 해결 가이드

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

이 문서에서는 Azure 웹앱에서 실행되는 [노드 응용 프로그램](app-service-web-nodejs-get-started.md)([iisnode](https://github.com/azure/iisnode) 사용)에 대한 모범 사례 및 문제 해결 단계에 대해 배워봅니다.

>[AZURE.WARNING] 프로덕션 사이트에서 문제 해결 단계를 사용할 때는 주의하세요. 비프로덕션 설정(예: 스테이징 슬롯)에서 앱의 문제를 해결하는 것이 좋으며 문제가 해결되면 스테이징 슬롯을 프로덕션 슬롯으로 교환하는 것이 좋습니다.

## IISNODE 구성

이 [스키마 파일](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml)에서는 iisnode에 대해 구성할 수 있는 모든 설정을 보여 줍니다. 응용 프로그램에 대해 유용할 일부 설정은 다음과 같습니다.

* nodeProcessCountPerApplication

    이 설정은 IIS 응용프로그램마다 실행되는 노드 프로세스 수를 제어합니다. 기본값은 1입니다. 0으로 설정하면 VM 코어 개수 만큼 node.exe를 실행할 수 있습니다. 권장되는 값은 대부분의 응용 프로그램에서 0이므로 컴퓨터에서 모든 코어를 활용할 수 있습니다. Node.exe는 단일 스레드이므로 하나의 node.exe에서 최대 1개의 코어를 사용하며 노드 응용 프로그램에서 최대 성능을 얻기 위해 모든 코어를 활용하길 원합니다.

* nodeProcessCommandLine

    이 설정은 node.exe에 대한 경로를 제어합니다. node.exe 버전을 가리키도록 이 값을 설정할 수 있습니다.

* maxConcurrentRequestsPerProcess

    이 설정은 iisnode에서 각 node.exe로 보내는 최대 동시 요청 수를 제어합니다. azure 웹앱에서 기본값은 무한입니다. 이 설정에 대해 걱정하지 않아도 됩니다. azure 웹앱 이외에서는 기본값이 1024입니다. 응용 프로그램이 받는 요청 수와 응용 프로그램이 각 요청을 처리하는 속도에 따라 이를 구성할 수 있습니다.

* maxNamedPipeConnectionRetry

    이 설정은 요청을 node.exe에 전송하기 위해 iisnode에서 명명된 파이프에 연결을 재시도하는 최대 횟수를 제어합니다. 이 설정과 namedPipeConnectionRetryDelay를 조합하면 iisnode 내에서 각 요청의 총 시간 제한이 결정됩니다. azure 웹앱에서 기본값이 200입니다. 총 시간 제한(초) = (maxNamedPipeConnectionRetry * namedPipeConnectionRetryDelay) / 1000

* namedPipeConnectionRetryDelay

    이 설정은 명명된 파이프를 통해 node.exe로 요청을 전송하기 위해 iisnode가 각 재시도 간에 대기하는 시간(ms)을 제어합니다. 기본값은 250ms입니다. 총 시간 제한(초) = (maxNamedPipeConnectionRetry * namedPipeConnectionRetryDelay) / 1000

    기본적으로 azure 웹앱의 iisnode에서 총 시간 제한은 200 * 250ms = 50초입니다.

* logDirectory

    이 설정은 iisnode에서 stdout/stderr를 로깅할 디렉터리를 제어합니다. 기본값은 주 스크립트 디렉터리(기본 server.js가 있는 디렉터리)를 기준으로 iisnode입니다.

* debuggerExtensionDll

    이 설정은 노드 응용 프로그램을 디버깅할 때 node-inspector iisnode가 사용할 버전을 제어합니다. 현재 iisnode-inspector-0.7.3.dll 및 iisnode-inspector.dll은 이 설정에 대해 2가지 값만 유효합니다. 기본값은 iisnode-inspector-0.7.3.dll입니다. iisnode-inspector-0.7.3.dll 버전에서는 node-inspector-0.7.3 및 WebSocket을 사용하므로 이 버전을 사용하려면 azure 웹앱에서 WebSocket을 사용하도록 설정해야 합니다. 새 node-inspector를 사용하도록 iisnode를 구성하는 방법에 대한 자세한 내용은 <http://www.ranjithr.com/?p=98>을 참조하세요.

* flushResponse

    IIS의 기본 동작은 응답 데이터를 플러시하기 전 또는 응답이 끝날 때까지 중 먼저 도달하는 것으로 최대 4MB까지 응답 데이터를 버퍼링합니다. iisnode는 이 동작을 재정의할 구성 설정을 제공합니다. 응답 엔터티 본문의 조각을 iisnode가 node.exe로부터 수신하는 즉시 플러시하려면 web.config에서 iisnode/@flushResponse 특성을 'true'로 설정해야 합니다.
    
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```

    응답 엔터티 본문의 모든 조각을 플러시하도록 설정하면 성능 오버헤드가 추가되어 시스템 처리량이 ~5%(v0.1.13 기준)까지 줄어들며 이 설정을 응답 스트리밍이 필요한 끝점으로만 국한하는 데 적합합니다(예: web.config에서 <location> 요소 사용).

    이 외에도 스트리밍 응용 프로그램의 경우 iisnode 처리기의 responseBufferLimit를 0으로 설정해야 합니다.
    
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```

* watchedFiles

    변경 내용을 감시하는 세미콜론으로 구분된 파일 목록입니다. 파일에 대한 변경 내용으로 인해 응용 프로그램 재활용이 발생합니다. 각 항목은 주 응용 프로그램 진입점이 있는 디렉터리를 기준으로 선택적 디렉터리 이름 및 필수 파일 이름으로 구성됩니다. 파일 이름 부분에만 와일드 카드가 허용됩니다. 기본값은 "*.js;web.config"입니다.

* recycleSignalEnabled

    기본값은 False입니다. 설정된 경우 노드 응용 프로그램에서 명명된 파이프(환경 변수 IISNODE\_CONTROL\_PIPE)에 연결할 수 있으며 "재활용" 메시지를 보낼 수 있습니다. 그러면 w3wp가 정상적으로 재활용됩니다.

* idlePageOutTimePeriod

    기본값은 이 기능을 사용하지 않는다는 의미의 0입니다. 0보다 큰 값으로 설정되면 iisnode에서 모든 하위 프로세스를 'idlePageOutTimePeriod' 밀리초마다 페이지 아웃합니다. 페이지 아웃에 대해 알아보려면 이 [설명서](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx)를 참조하세요. 이 설정은 대량의 메모리를 사용하고 간혹 메모리를 디스크로 페이지 아웃하여 일부 RAM을 확보하려는 경우 유용합니다.

>[AZURE.WARNING] 프로덕션 응용 프로그램에서 다음 구성 설정을 사용하도록 설정할 대는 주의하세요. 라이브 프로덕션 응용 프로그램에서 사용하지 않도록 설정하는 것이 좋습니다.

* debugHeaderEnabled

    기본값은 False입니다. true로 설정되면 iisnode에서 HTTP 응답 헤더 iisnode-debug를 보내는 모든 HTTP 응답에 추가하고 iisnode-debug 헤더 값은 URL입니다. 개별 진단 정보 조각은 URL 조각을 확인하여 얻을 수 있지만 브라우저에서 URL을 열면 훨씬 향상된 시각화를 달성할 수 있습니다.

* loggingEnabled

    이 설정은 iisnode에 의한 stdout 및 stderr의 로깅을 제어합니다. Iisnode는 시작된 노드 프로세스에서 stdout/stderr을 캡처하여 'logDirectory' 설정에 지정된 디렉터리에 씁니다. 사용하도록 설정하면 응용 프로그램에서 로그를 파일 시스템에 기록하고 응용 프로그램에서 완료한 로깅 양에 따라 성능 저하가 발생할 수 있습니다.

* devErrorsEnabled

    기본값은 False입니다. true로 설정하면 iisnode에서 HTTP 상태 코드 및 Win32 오류 코드가 브라우저에 표시됩니다. win32 코드는 특정 유형의 문제를 디버깅하는 데 유용합니다.
    
* debuggingEnabled(라이브 프로덕션 사이트에서 사용 안 함)

    이 설정은 디버깅 기능을 제어합니다. Iisnode는 node-inspector에 통합됩니다. 이 설정을 사용하여 노드 응용 프로그램의 디버깅을 사용하도록 설정합니다. 이 설정을 사용하도록 설정하면 iisnode가 필요한 'debuggerVirtualDir' 디렉터리의 node-inspector 파일을 노드 응용 프로그램에 대한 첫 번째 디버그 요청에 배치합니다. 요청을 http://yoursite/server.js/debug로 전송하여 node-inspector를 로드할 수 있습니다. ‘debuggerPathSegment’ 설정으로 디버그 URL 세그먼트를 제어할 수 있습니다. 기본적으로 debuggerPathSegment='debug'입니다. 예를 들어 이 값을 GUID로 설정할 수 있으므로 다른 사람이 검색하기 더 어렵습니다.

    디버깅에 대한 자세한 내용은 이 [링크](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html)를 확인하세요.

## 시나리오 및 권장 사항/문제 해결

### 내 노드 응용 프로그램에서 너무 많은 아웃바운드 호출을 생성합니다.

많은 응용 프로그램에서 일반 작업의 일부로 아웃바운드 연결을 생성하려고 합니다. 예를 들어 요청이 들어오면 노드 앱은 다른 위치에서 REST API에 연결하고 요청을 처리할 일부 정보를 얻습니다. http 또는 https 호출을 수행할 때 연결 유지 에이전트를 사용하려고 합니다. 예를 들어 이러한 아웃바운드 호출을 수행할 때 연결 유지 에이전트로 agentkeepalive 모듈을 사용할 수 있습니다. 이렇게 하면 azure 웹앱 VM에서 소켓이 재사용되어 모든 아웃바운드 요청에 대해 새 소켓을 만드는 오버헤드가 감소합니다. 또한 이렇게 하면 적은 수의 소켓을 사용하여 많은 아웃바운드 요청을 만들 수 있으므로 VM당 할당된 maxSockets를 초과하지 않습니다. Azure 웹앱에 대한 권장 사항은 agentKeepAlive maxSockets 값을 VM당 총 160개 소켓으로 설정합니다. VM에서 실행되는 4개의 node.exe가 있는 경우 agentKeepAlive maxSockets를 node.exe당 40(VM당 총 160)으로 설정합니다.

agentKeepALive 구성 예:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

이 예에서는 VM에서 4개의 node.exe를 실행 중이라고 가정합니다. VM에서 다른 수의 node.exe가 실행 중인 경우 maxSockets 설정을 적절하게 수정해야 합니다.

### 내 노드 응용 프로그램이 너무 많은 CPU를 사용하고 있습니다.

포털의 Azure 웹앱에서 높은 cpu 사용량에 대한 권장 사항을 받게 됩니다. 또한 특정 [메트릭](web-sites-monitor.md)을 감시하도록 모니터를 설정할 수도 있습니다. [Azure 포털 대시보드](../application-insights/app-insights-web-monitor-performance.md)에서 CPU 사용량을 확인할 때는 최고값을 놓치지 않도록 CPU에 대한 MAX 값을 확인하세요. 응용 프로그램에서 CPU를 너무 많이 사용한다고 생각되고 그 이유를 설명할 수 없는 경우 노드 응용 프로그램을 프로파일링해야 합니다.

###

#### azure 웹앱에서 V8-Profiler로 노드 응용 프로그램 프로파일링

예를 들어 아래와 같이 프로파일링할 hello world 앱이 있다고 가정해 보겠습니다.

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

scm 사이트 https://yoursite.scm.azurewebsites.net/DebugConsole로 이동합니다.

아래와 같이 명령 프롬프트가 표시됩니다. site/wwwroot 디렉터리로 이동합니다.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

"npm install v8-profiler" 명령을 실행합니다.

그러면 node\_modules 디렉터리 아래에 v8-profiler와 모든 종속성이 설치됩니다. 이제 server.js를 편집하여 응용 프로그램을 프로파일링합니다.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

위의 변경 내용은 WriteConsoleLog 함수를 프로파일링한 후 프로파일 출력을 사이트의 wwwroot 아래 'profile.cpuprofile' 파일에 기록합니다. 응용 프로그램에 요청 보내기 사이트의 wwwroot 아래에 'profile.cpuprofile' 파일이 생성된 것을 확인할 수 있습니다.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

이 파일을 다운로드하고 Chrome F12 Tools에서 이 파일을 열어야 합니다. chrome에서 F12 키를 누른 후 "Profiles 탭"을 클릭합니다. "로드" 단추를 클릭합니다. 방금 다운로드한 profile.cpuprofile 파일을 선택합니다. 방금 로드한 프로파일을 클릭합니다.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

아래와 같이 WriteConsoleLog 함수에 의해 95%의 시간이 소요되었다는 내용이 표시됩니다. 또한 이 문제를 발생시킨 정확한 줄 번호와 소스 파일도 표시됩니다.

### 내 노드 응용 프로그램이 너무 많은 메모리를 사용하고 있습니다.

포털의 Azure 웹앱에서 높은 메모리 사용량에 대한 권장 사항을 받게 됩니다. 또한 특정 [메트릭](web-sites-monitor.md)을 감시하도록 모니터를 설정할 수도 있습니다. [Azure 포털 대시보드](../application-insights/app-insights-web-monitor-performance.md)에서 메모리 사용량을 확인할 때는 최고값을 놓치지 않도록 메모리에 대한 MAX 값을 확인하세요.

#### node.js에 대한 누수 감지 및 힙 Diff 

[node-memwatch](https://github.com/lloyd/node-memwatch)를 사용하여 메모리 누수를 확인할 수 있습니다. You can install memwatch just like v8-profiler처럼 memwatch를 설치하고 힙을 캡처 및 diff하는 코드를 편집하여 응용 프로그램에서 메모리 누수를 확인할 수 있습니다.

### 내 node.exe가 임의로 중지됨 

이러한 현상이 발생하는 몇 가지 이유가 있습니다.

1.  응용 프로그램에서 catch되지 않는 예외를 throw합니다. throw된 예외에 대한 자세한 내용은 d:\\home\\LogFiles\\Application\\logging-errors.txt 파일을 확인하세요. 이 파일에는 스택 추적이 있으므로 이에 따라 응용 프로그램을 수정할 수 있습니다.

2.  응용 프로그램에서 너무 많은 메모리를 사용하여 다른 프로세스가 시작되는 데 영향을 줍니다. 총 VM 메모리가 100%에 근접한 경우 프로세스 관리자는 다른 프로세스가 작업을 수행할 수 있도록 node.exe를 중지할 수 있습니다. 이 문제를 해결하려면 응용 프로그램에서 메모리 누수가 발생하지 않는지, 응용 프로그램에서 실제로 많은 메모리를 사용해야 하는지 확인합니다. 또는 훨씬 많은 RAM이 있는 큰 VM으로 강화하세요.

### 내 노드 응용 프로그램이 시작되지 않음

응용 프로그램 시작 시 500 오류를 반환하는 경우 몇 가지 원인이 있을 수 있습니다.

1.  Node.exe가 올바른 위치에 없습니다. nodeProcessCommandLine 설정을 확인합니다.

2.  기본 스크립트 파일이 올바른 위치에 없습니다. web.config를 확인하고 처리기 섹션에서 기본 스크립트 파일의 이름이 기본 스크립트 파일과 일치하는지 확인합니다.

3.  Web.config 구성이 잘못되었습니다. 설정 이름/값을 확인하세요.

4.  콜드 부팅 – 응용 프로그램을 시작하는 데 너무 오래 걸립니다. 응용 프로그램이 (maxNamedPipeConnectionRetry * namedPipeConnectionRetryDelay) / 1000초보다 오래 걸리면 iisnode에서 500 오류를 반환합니다. 응용 프로그램 시작 시간과 일치하도록 이러한 설정 값을 늘리면 iisnode의 제한 시간 초과와 500 오류 반환을 방지할 수 있습니다.

### 내 노드 응용 프로그램에 충돌 발생

응용 프로그램에서 catch되지 않는 예외를 throw합니다. throw된 예외에 대한 자세한 내용은 d:\\home\\LogFiles\\Application\\logging-errors.txt 파일을 확인하세요. 이 파일에는 스택 추적이 있으므로 이에 따라 응용 프로그램을 수정할 수 있습니다.

### 내 노드 응용 프로그램을 시작하는 데 너무 많은 시간이 소요됨(콜드 부팅)

가장 일반적인 이유는 응용 프로그램의 node\_modules에 너무 많은 파일이 있고 응용 프로그램에서 시작 중에 이러한 파일 중 대부분을 로드하려고 시도하는 경우입니다. 기본적으로 파일은 Azure 웹앱의 네트워크 공유에 상주하므로 많은 파일을 로드하면 다소 시간이 소요될 수 있습니다. 이 작업을 빠르게 하기 위한 몇 가지 해결 방법은 다음과 같습니다.

1.  npm3을 사용하여 플랫 종속성 구조가 있고 중복 종속성이 없는지 확인하고 모듈을 설치합니다.

2.  node\_modules의 지연 로드를 시도하면 시작 시 모든 모듈을 로드하지 않습니다. 따라서 모듈을 사용하려는 함수 내에서 실제로 필요할 때 필요한 호출('모듈')이 완료됩니다.

3.  Azure 웹앱은 로컬 캐시라는 기능을 제공합니다. 이 기능은 네트워크 공유에서 VM의 로컬 디스크로 콘텐츠를 복사합니다. 파일은 로컬이므로 node\_modules의 로드 시간이 훨씬 빠릅니다. 이 [설명서](../app-service/app-service-local-cache.md)에서는 로컬 캐시를 사용하는 방법을 자세히 설명합니다.

## IISNODE http 상태 및 하위 상태

이 [소스 파일](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h)은 iisnode에서 오류 발생 시 반환할 수 있는 모든 가능한 상태/하위 상태의 조합을 나열합니다.

win32 오류 코드를 표시하도록 응용 프로그램에 대해 FREB를 사용하도록 설정합니다(성능상의 이유로 비프로덕션 사이트에서만 FREB를 사용하도록 설정하세요).

| HTTP 상태 | HTTP 하위 상태 | 가능한 원인                                                                                                                                                                                            
|-------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------
| 500 | 1000 | 요청을 IISNODE로 디스패치하는 몇 가지 문제가 있습니다. node.exe가 시작되었는지 확인하세요. Node.exe가 시작 시 충돌되었을 수 있습니다. web.config 구성에 오류가 있는지 확인하세요. |
| 500 | 1001 | - Win32Error 0x2 - 앱이 URL에 응답하지 않습니다. URL 다시 쓰기 규칙이나 Express 앱에 올바른 경로가 정의되어 있는지 확인하세요. - Win32Error 0x6d – 명명된 파이프를 사용 중 – 파이프를 사용 중이므로 Node.exe에서 요청을 수락하지 않습니다. 높은 cpu 사용량을 확인하세요. - 기타 오류 – node.exe가 충돌되었는지 확인하세요.
| 500 | 1002 | Node.exe가 충돌됨 – d:\\home\\LogFiles\\logging-errors.txt에서 스택 추적을 확인하세요. |
| 500 | 1003 | 파이프 구성 문제 – 이 내용은 표시되지 않지만 표시되는 경우 명명된 파이프 구성이 잘못되었습니다. |
| 500 | 1004-1018 | node.exe 간에 요청을 보내거나 응답을 처리하는 동안 일부 오류가 발생했습니다. Node.exe가 충돌되었는지 확인하세요. d:\\home\\LogFiles\\logging-errors.txt에서 스택 추적을 확인하세요. |
| 503 | 1000 | 메모리가 부족하여 추가 명명된 파이프 연결을 할당할 수 없습니다. 앱에서 많은 메모리를 사용하는 이유를 확인하세요. maxConcurrentRequestsPerProcess 설정 값을 확인하세요. 무한이 아니고 많은 요청이 있는 경우 이 값을 늘려야 이 오류를 피할 수 잇습니다. |
| 503 | 1001 | 응용 프로그램을 재활용 중이므로 요청을 node.exe로 디스패치할 수 없습니다. 응용 프로그램이 재활용된 후에야 요청이 정상적으로 처리됩니다. |
| 503 | 1002 | win32 오류 코드로 실제 원인을 확인하세요. – 요청을 node.exe로 디스패치할 수 없습니다. |
| 503 | 1003 | 명명된 파이프 사용량이 너무 많음 – 노드에서 많은 CPU를 사용 중인지 확인하세요.                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
NODE.exe 내에 NODE\_PENDING\_PIPE\_INSTANCES라는 설정이 있습니다. 기본적으로 azure 웹앱 이외에서는 이 값은 4입니다. 따라서 node.exe는 명명된 파이프에서 한 번에 4개의 요청만 받아들일 수 있습니다. Azure 웹앱에서 이 값은 5000으로 설정되고 이 값은 azure 웹앱에서 실행 중인 대부분의 노드 응용 프로그램에서 충분합니다. NODE\_PENDING\_PIPE\_INSTANCES에 대한 값이 높으므로 azure 웹앱에서 503.1003이 표시되지 않습니다. |

## 추가 리소스

Azure 앱 서비스에서 다음 링크를 따라 node.js 응용 프로그램에 대해 자세히 알아보세요.

* [Azure 앱 서비스에서 Node.js 웹앱 시작](app-service-web-nodejs-get-started.md)
* [Azure 앱 서비스에서 Node.js 웹 앱을 디버그하는 방법](web-sites-nodejs-debug.md)
* [Azure 응용 프로그램에 Node.js 모듈 사용](../nodejs-use-node-modules-azure-apps.md)
* [Azure 앱 서비스 웹앱: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js 개발자 센터](../nodejs-use-node-modules-azure-apps.md)
* [Super 암호 Kudu 디버그 콘솔 탐색](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

<!---HONumber=AcomDC_0629_2016-->