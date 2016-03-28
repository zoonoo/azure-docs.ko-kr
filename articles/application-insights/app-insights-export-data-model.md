<properties 
	pageTitle="Application Insights 데이터 모델" 
	description="JSON의 연속 내보내기에서 내보내고 필터로 사용하는 속성을 설명합니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="awills"/>

# Application Insights 데이터 모델 내보내기

이 테이블은 [Application Insights](app-insights-overview.md) SDK에서 포털로 전송된 원격 분석의 속성을 나열합니다. 이러한 속성이 [연속 내보내기](app-insights-export-telemetry.md)에서 데이터 출력에 표시됩니다 또한 [메트릭 탐색기](app-insights-metrics-explorer.md) 및 [진단 검색](app-insights-diagnostic-search.md)의 속성 필터에 나타납니다.

사용하는 방법을 설명하는 몇 가지 [샘플](app-insights-export-telemetry.md#code-samples)이 있습니다.

첫번째 섹션의 "&lt;telemetryType&gt;"는 보기, 요청 등 원격 분석 형식 이름에 대한 자리 표시자입니다.


## &lt;telemetryType&gt;

**<measurement>**로 바꿉니다.

    KVPs <string, double> <telemetryType>.measurement      Max: 100
* 
    사용자 지정 메트릭을 추가하기 위해 AppInsights 원격 분석 항목에 대해 확장성을 제공하는 KVP(키/값 쌍) 속성 모음입니다. 

    *파생:* 측정 이름의 최대 크기는 100입니다.

    *기본값:* 키는 있고 값이 누락된 경우 count = 1, value = 0, min/max = 0이 됩니다.

**<property>**로 바꿉니다.

    KVPs <string, string> <telemetryType>.properties      Max: 100
* 
    사용자 지정 속성을 추가하기 위해 AppInsights 원격 분석 항목에 대해 확장성을 제공하는 KVP(키/값 쌍) 속성 모음입니다. 개발자는 원격 분석 항목과 연결된 KVP 목록을 제공할 수 있습니다. 각 키가 추적되고 AppInsights ikey(앱)당 최대 200개의 고유 키가 제공될 수 있습니다. 키의 최대 길이는 100자일 수 있습니다. 모든 값은 문자열로 처리되고 최대 1000자가 제공될 수 있습니다. 각 속성은 처음에 차원으로 분류되며, 각 속성의 값 집합에 따라 구분 기능을 사용하도록 설정합니다. 해당 카디널리티에 대한 속성 키를 기준으로 각 값 집합이 추적됩니다. 키의 카디널리티가 100개의 고유한 값을 초과하면 속성은 특성으로 분류됩니다. 특성은 검색할 수 있지만 구분 대상(집계 또는 그룹화 기준)이 될 수 없습니다. 

    *파생:* 속성 이름의 최대 크기는 100이고, 속성 값의 최대 크기는 1024입니다.

    *기본:* 키는 있고 값이 누락된 경우 값은 null입니다.

**count**

    long <telemetryType>.count      
* 
    원격 분석 항목의 개수입니다.   

    *파생:* null인 경우 count = 1입니다.

**duration**

    simpleMetric <telemetryType>.duration   ticks   
* 
    원격 분석 항목의 기간입니다. 요청의 경우 요청의 실행 시간입니다. 

    *기본값:* R1: 보기의 경우 이 필드는 선택 사항입니다.

**message**

    string <telemetrytype>.message      Max: 10240
* 
    원격 분석 형식에 대한 텍스트 메시지입니다. 이 문자열은 전체 텍스트 검색에 사용할 수 있습니다. 

**name**

    string <telemetrytype>.name      Max: 250
* 
    원격 분석 항목의 이름입니다. 이 이름은 인스턴스 간에 고유하지 않으며 원격 분석 유형의 그룹화를 나타냅니다. 보기의 경우 기본적으로 URLData.base입니다. 이벤트의 경우 개발자가 제공한 레이블입니다. 요청의 경우 controller\\action과 같은 요청의 읽을 수 있는 형식입니다. 

    *예제*<br/> 이름 보기<br/>70 486 시험 질문 1<br/>-내 ASP.NET 응용 프로그램에 대해<br/><br/>예제 요청 이름:<br/>POST /Components/WebHandlers/ItemCompare.ashx<br/>GET /signalr/poll<br/>GET /signalr/negotiate

**severity**

    string <telemetryType>.severity      Max: 100
* 
    원격 분석 항목의 심각도입니다. 추적 및 예외 원격 분석 항목에 유효합니다. 

**url**

    string <telemetrytype>.url      Max: 2048
* 
    페이지 보기, 이벤트, 요청 또는 RDD의 URL입니다. 전체 URL이며, 전체 텍스트 검색 및 내보내기에서 지원됩니다. 이 필드는 매우 높은 카디널리티를 가질 수 있으며 특성에 해당합니다. 메트릭 탐색기에서 집계에 대해 사용할 수 있는 urlData 데이터 항목 집합으로 구문 분석됩니다. 

    *기본* :R2: remotedepencyType에서 dependencyType = HTTP이면 이 필드는 필수입니다.<br/>clientperformanceType에 이 필드는 필수입니다.

    *예제*<br/> https://icecream.contoso.com/main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>http://fabrikam-oats.azurewebsites.net/index.htm

**urlData.base**

    string <telemetrytype>.urldata.base      Max: 2048
* 
    호스트, 쿼리 매개 변수를 제외한 URL 데이터 항목의 부분입니다. 루트 URI입니다. 이 값은 구분/집계에 사용할 수 있습니다. 

    *파생:* URL 변환에 대한 부록 참조

    *예제*<br/> /main.aspx?etc=3&extraqs=%3fetc%3d3%26formid%3dc40d07a7-1cf1-4e1d-b00e-e61876d1284e&pagemode=iframe&pagetype=entityrecord<br/>/default.aspx<br/>/Patients/Search/<br/>

**urldata.hashTag**

    string <telemetrytype>.urldata.hashtag      Max: 100
* 
    URL 데이터 항목의 해시 태그 텍스트 

    *파생:* URL 변환에 대한 부록 참조

**urlData.host**

    string <telemetrytype>.urldata.host      Max: 200
* 
    URL 데이터 항목의 호스트. URL 데이터 항목이 로컬 URI이면 빈 상태로 표시됩니다. 

    *파생:* URL 변환에 대한 부록 참조

    *예제*<br/> www.fabrikam.com<br/>www.contoso.com<br/>bretwpc711.azurewebsites.net<br/>



## availability

**availability**

    simpleMetric availability.availability      
* 
    가용성 테스트의 성공 여부를 나타내는 표시기 

**dataSize**

    simpleMetric availability.datasize      
* 
    &lt;telemetry&gt;.message 텍스트 메시지의 크기 

**result**

    enum (pass/fail) availability.result      
* 
    가용성 웹 테스트의 세부 정보를 검색하기 위한 포인터(HTTP 호출) 

**runLocation**

    string availability.runlocation      Max: 100
* 
    가용성 테스트의 실행 위치 

**testName**

    string availability.testname      Max: 1024
* 
    가용성 테스트의 이름 

**testRunId**

    string availability.testrunid      Max: 100
* 
    가용성 테스트 실행의 인스턴스에 대한 고유 ID 

**testTimeStamp**

    datetime availability.testtimestamp      
* 
    가용성 테스트의 실행 인스턴스 시작 부분의 타임스탬프 


## basicexception

**assembly**

    string basicexception.assembly      Max: 100
**exceptionGroup**

    string basicException.exceptionGroup      
**exceptionType**

    string basicexception.exceptiontype      Max: 100
**failedUserCodeAssembly**

    string basicException.failedUserCodeAssembly      
**failedUserCodeMethod**

    string basicException.failedUserCodeMethod      
**handledAt**

    string basicexception.handledat      Max: 100
**innerMostExceptionMessage**

    string basicException.innermostExceptionMessage      
**innerMostExceptionThrownAtAssembly**

    string basicException.innermostExceptionThrownAtAssembly      
**innerMostExceptionThrownAtMethod**

    string basicException.innermostExceptionThrownAtMethod      
**innerMostExceptionType**

    string basicException.innermostExceptionType      
**메서드**

    string basicexception.method      Max: 100
**outerMostExceptionMessage**

    string basicException.outerExceptionMessage      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtAssembly      
**outerMostExceptionThrownAtAssembly**

    string basicException.outerExceptionThrownAtMethod      
**outerMostExceptionType**

    string basicException.outerExceptionType      
**problemid**

    string basicexception.problemid      Max: 100
* 
    *파생:* 호출 스택 구문 분석에 대한 부록 참조 

**Exceptions.Assembly**

    string basicexception.exceptions.assembly      Max: 100
**Exceptions.fileName**

    string basicexception.exceptions.filename      Max: 100
**Exceptions.hasFullStack**

    boolean basicexception.exceptions.hasfullstack      
**Exceptions.Level**

    string basicexception.exceptions.level      Max: 100
**Exceptions.Line**

    long basicexception.exceptions.line      
**exceptions.message**

    string basicexception.exceptions.message      Max: 10240
**Exceptions.Method**

    string basicexception.exceptions.method      Max: 100
**Exceptions.outerId**

    long basicexception.exceptions.outerid      
**Exceptions.parsedStack**

    List<StackFrame> basicexception.exceptions.parsedstack      
**Exceptions.Stack**

    string basicexception.exceptions.stack      Max: 10240
**Exceptions.typeName**

    string basicexception.exceptions.typename      Max: 100
**parsedStackAssembly**

    string basicException.parsedStack.assembly      
**parsedStackFilename**

    string basicException.parsedStack.fileName      
**parsedStackLevel**

    string basicException.parsedStack.level      
**parsedStackLine**

    string basicException.parsedStack.line      
**paseStackMethod**

    string basicException.parsedStack.method      

## clientperformance

**domProcessing**

    simpleMetric clientperformance.domprocessing   ms   
* 
    perTotal 시간의 부분입니다. 이 부분은 앱이 응답을 처리하는 데 걸린 시간을 나타냅니다. 웹 클라이언트의 경우 DOM(문서 개체 모델) 처리 시간입니다. 이 타이밍은 최신 브라우저의 perfTiming API를 사용하여 캡처됩니다. 

**networkConnect**

    simpleMetric clientperformance.networkconnect   ms   
* 
    perTotal 시간의 부분입니다. 이 부분은 앱이 네트워크 연결을 설정하는 데 걸린 시간을 나타냅니다. 이 타이밍은 최신 브라우저의 perfTiming API를 사용하여 캡처됩니다. 

**perfTotal**

    simpleMetric clientperformance.perftotal   ms   
* 
    보기를 로드하기 위한 총 시간입니다. 웹 클라이언트에의 경우 ""페이지 로드 시간""과 같습니다. 이 타이밍은 최신 브라우저의 perfTiming API를 사용하여 캡처됩니다. 

**receiveResponse**

    simpleMetric clientperformance.receiveresponse   ms   
* 
    perTotal 시간의 부분입니다. 이 부분은 앱이 요청 응답을 받는 데 걸린 시간을 나타냅니다. 이 타이밍은 최신 브라우저의 perfTiming API를 사용하여 캡처됩니다. 

**sendRequest**

    simpleMetric clientperformance.sendrequest   ms   
* 
    perTotal 시간의 부분입니다. 이 부분은 앱이 서버로 요청을 보내는 데 걸린 시간을 나타냅니다. 이 타이밍은 최신 브라우저의 perfTiming API를 사용하여 캡처됩니다. 


## context

**applicationBuild**

    string context.application.build      Max: 100
* 
    앱의 응용 프로그램 빌드 번호 

**applicationVersion**

    string context.application.version      Max: 100
* 
    클라이언트 응용 프로그램의 응용 프로그램 버전 알 수 없음 상태로 설정된 경우 사용할 수 없습니다. 

    *예제*<br/> 2015.5.21.3<br/>NokiaMailBye\_CD\_20150227.4

**telemetryType**

    string context.billing.telemetrytype      Max: 100
* 
    대금 청구 기록에 대한 원격 분석 유형을 나타내며, 앱의 청구 가능한 원격 분석 항목을 구분하기 위해 내부적으로 사용됩니다. 

**dataId**

    string context.data.id      Max: 100
* 
    원격 분석 항목의 고유 식별자입니다. 데이터 컬렉션 끝점에 할당됩니다. 

    *파생:* 생성된 UUID4

    *예제*<br/> edc6eaf3-3459-46a0-bb81-bedc24913864

**eventTime**

    datetime context.data.eventtime      
* 
    UTC로 기록된 원격 분석 이벤트의 시간입니다. 일반적으로 클라이언트에서 채워집니다. 이 필드가 누락되면 데이터 컬렉션 끝점에서 채워집니다. YYYY-MM-DDTHH:MM:SS.sssZ 필드 형식을 갖습니다.    

    *예제*<br/> 2015-05-20T04:00:46.8338283Z

**samplingRate**

    string context.data.samplerate      Max: 100
* 
    데이터 생산자(SDK)의 샘플링 속도입니다. 여기서 1이 아닌 값은 이 원격 분석 항목과 관련된 메트릭이 샘플링된 값을 표시함을 나타냅니다. 따라서 샘플링 속도가 0.05이면 개수가 20인 1개의 원격 분석 항목이 반환됩니다. 

**만들기**

    string context.device.browser      Max: 100
* 
    클라이언트의 브라우저 

    *기본값:* null인 경우 사용자 에이전트 처리에 따라 설정됩니다. 사용자 에이전트 구문 분석에 대한 부록 참조

    *예제*<br/> Opera<br/>Mobile Safari<br/>Ovi Browser<br/>Chrome<br/>Firefox<br/>Internet Explorer

**browserVersion**

    string context.device.browserversion      Max: 100
* 
    클라이언트의 브라우저 버전 

    *기본값:* null인 경우 사용자 에이전트 처리에 따라 설정됩니다. 사용자 에이전트 구문 분석에 대한 부록 참조

    *예제*<br/> Opera 12.17<br/>Mobile Safari 8.0<br/>Ovi Browser 5.5<br/>Chrome 37.0<br/>Firefox 21.0<br/>Internet Explorer 7.0

**deploymentId**

    string context.device.deploymentid      Max: 100
* 
    서버의 배포 ID 


**deviceName**

    string context.device.name      Max: 100
* 
    앱이 실행 중인 장치의 이름 

**locale**

    string context.device.locale      Max: 100
* 
    클라이언트의 앱 로캘입니다. 원격 분석 항목에 명시적으로 제공되지 않으면 사용자 에이전트 필드의 처리에 따라 제공됩니다. 

    *예제*<br/> ru<br/>ko-KR<br/>de-DE<br/>unknown

**machineName**

    string context.device.vmname      Max: 100
* 
    서버의 컴퓨터 이름입니다. 가상화된 계산의 경우 이 데이터 항목이 기본 호스트와 같습니다. 전용 계산의 경우 컴퓨터 이름입니다. 


**operatingSystem**

    string context.device.os      Max: 100
* 
    클라이언트의 운영 체제 

    *기본값:* null인 경우 사용자 에이전트 처리에 따라 설정됩니다. 사용자 에이전트 구문 분석에 대한 부록 참조

    *예제*<br/> Windows<br/>iOS iPad<br/>Nokia

**operatingSystemVersion**

    string context.device.osversion      Max: 100
* 
    클라이언트의 운영 체제 버전 

    *기본값:* null인 경우 사용자 에이전트 처리에 따라 설정됩니다. 사용자 에이전트 구문 분석에 대한 부록 참조

    *예제*<br/> Windows XP<br/>iOS 8.3<br/>Nokia Series 40<br/>Windows 7<br/>Windows 8

**roleInstance**

    string context.device.roleinstance      Max: 100
* 
    서버 계산의 인스턴스입니다. 클라우드/가상화 시나리오에서는 계산 인스턴스의 가상 이름입니다. 전용 계산 인스턴스에서는 컴퓨터 이름입니다. Azure 클라우드 서비스의 경우 기본적으로 PaaS 역할 인스턴스 이름 또는 IaaS 가상 컴퓨터 이름입니다.  

**roleName**

    string context.device.rolename      Max: 100
* 
    서버 계산 인스턴스를 그룹화하기 위한 논리적 네임스페이스입니다. Azure에서 호스트되는 서비스의 경우 PaaS 역할은 기본적으로 PaaS 역할 이름입니다. IaaS 역할은 기본작으로 가상 컴퓨터 이름입니다.  

**screenHeight**

    string context.device.screenresolution.height      Max: 100
* 
    원격 분석 항목이 기록되었을 때의 클라이언트 하드웨어에서의 앱 화면 높이입니다. 명시적으로 지정하지 않으면 screenresolution 데이터 항목의 변환에 따라 제공됩니다. 

    *파생:* 있는 경우 context.device.screenresolution에서 구문 분석됩니다.

    *예제*<br/> 360<br/>1280<br/>1920

**screenResolution**

    string context.device.screenresolution      Max: 100
* 
    앱에서 원격 분석 항목이 캡처되었을 때의 화면 해상도입니다. 이 값은 세션 과정 동안 세로와 가로 간을 전환할 수 있습니다. 이 특성이 세션 수준으로 전달되면 전체 세션을 나타내기 위해 캡처된 첫 번째 화면 해상도가 됩니다. 

    *예제*<br/> Screen Resolution Height Width<br/>360X640<br/>1280X800<br/>1920x1080

**screenWidth**

    string context.device.screenresolution.width      Max: 100
* 
    원격 분석 항목이 기록되었을 때의 클라이언트 하드웨어에서의 앱 화면 너비입니다. 명시적으로 지정하지 않으면 screenresolution 데이터 항목의 변환에 따라 제공됩니다. 

    *파생:* 있는 경우 context.device.screenresolution에서 구문 분석됩니다.

    *예제*<br/> 640<br/>800<br/>1080


**aiAgentVersion**

    string context.internal.agentversion      Max: 100
* 
    원격 분석 항목을 생성한 SDK 에이전트 버전을 나타내는 내부 데이터 항목 

**city**

    string context.location.city      Max: 100
* 
    앱 세션의 구/군/시입니다. 원격 분석 항목에 직접 제공될 수 있습니다. 없는 경우 원격 분석 항목의 한 IPv4에 따라 채워집니다. IPv4가 제공되지 않으면 이 필드는 비어 있습니다. 

    *예제*<br/> 민스크<br/>하를렘

**clientIpAddress**

    ipv4 context.location.clientip      
* 
    Xxx.xxx. xxx.xxx 형식의 클라이언트 IPv4 주소입니다.

     개인 정보 문제를 해결하기 위해 마지막 옥텟은 항상 0으로 설정됩니다.

    *기본:* null인 경우 데이터 컬렉션 끝점에서 캡처된 HTTP IP로 설정됩니다.

    *예*<br/> 186.123.63.0<br/>123.203.131.0

**continent**

    string context.location.continent      Max: 100
* 
    앱 세션의 대륙입니다. 원격 분석 항목에 직접 제공될 수 있습니다. 없는 경우 원격 분석 항목의 한 IPv4에 따라 채워집니다. IPv4가 제공되지 않으면 이 필드는 비어 있습니다. 

    *예제*<br/> 유럽<br/>북아메리카

**country**

    string context.location.country      Max: 100
* 
    앱 세션의 국가입니다. 원격 분석 항목에 직접 제공될 수 있습니다. 없는 경우 원격 분석 항목의 한 IPv4에 따라 채워집니다. IPv4가 제공되지 않으면 이 필드는 비어 있습니다. 

    *예제*<br/> 벨로루시<br/>네덜란드<br/>독일


**state**

    string context.location.province      Max: 100
* 
    앱 세션의 시/도입니다. 원격 분석 항목에 직접 제공될 수 있습니다. 없는 경우 원격 분석 항목의 한 IPv4에 따라 채워집니다. IPv4가 제공되지 않으면 이 필드는 비어 있습니다. 

    *예제*<br/> 민스크<br/>오리건<br/>중앙 세르비아<br/>Provincia di Oristano

**operationId**

    string context.operation.id      Max: 100
* 
    operation.id는 원격 분석 항목에서 사용할 수 있는 상관 관계 ID입니다. 예를 들어 요청 ID가 모든 관련 원격 분석 항목의 operation.id에 채워져 RDD, 예외, 이벤트 등의 원격 분석 항목 간에 상관 관계가 설정될 수 있습니다.  

**operationName**

    string context.operation.name      Max: 100
* 
    사람이 읽을 수 있는 작업 이름입니다. 작업 ID를 참조하세요. 이를 사용하여 구매 완료와 같은 유사한 작업 ID를 그룹화할 수 있습니다.   

**operationParentId**

     context.operation.parentid      
* 
    operation.id에 대한 상위 ID로, 원격 분석 상관 관계에 대한 ID를 중첩할 수 있도록 합니다.   

**syntheticSource**

    string context.data.syntheticsource      Max: 100
* 
    issynthetic=true이면 이 데이터 항목은 가상 데이터의 소스를 나타냅니다. 

    *기본값:* null인 경우 사용자 에이전트가 알려진 가상 원본(webcrawlers 등)인지 검사되고 이에 따라 원본이 설정될 수 있습니다.

**syntheticTransaction**

    boolean context.data.issynthetic      
* 
    원격 분석 항목이 실제 사용자 동작이 아닌 가상 테스트로 인해 생성되었음을 나타내는 표시입니다. 

    *기본값:* null인 경우 가상 에이전트의 알려진 목록에서 사용자 에이전트가 검사됩니다. 일치하는 항목이 있는 경우 값이 true로 설정됩니다.<br/>사용자 에이전트가 null이면 false로 설정됩니다.

**session.Id**

    String context.session.id      Max: 100
* 
    응용 프로그램과의 실시간 사용자 상호 작용의 고유 식별자입니다. 이 상호 작용은 ""세션""입니다. 동일한 iKey의 응용 프로그램에 의해 생성된 모든 원격 분석에는 이 고유 식별자가 포함되어야 합니다. <br/><br/>세션은 같은 사용자 상호 작용 내의 연속 이벤트로 정의됩니다. 원격 분석 이벤트 없이 30분 넘게 지속되면 세션 끝 신호가 발생합니다.   

    *기본값:* MetricType, BillingType에는 유효하지 않습니다.

    *예제*<br/> CFFC8B21-9828-4F56-AD7C-B6B5AC26B133

**accountAcquisitionDate**

    datetime context.user.accountAcquisitionDate  
    
**anonUserId**

    string context.user.anonId      Max: 100
* 
    앱 내에서 익명 사용자를 정의하는 고유 식별자입니다. SDK를 사용하면 자동으로 생성되며 클라이언트에 지속됩니다. 동일한 로그인에서 장치를 공유할 경우 실제 사용자 간을 구분하지는 않습니다. 다른 로그인을 사용하고 OS가 프로필을 지원하는 경우 실제 사용자 간을 구분합니다. 인증 받은 경험이 없는 고유 사용자에게 가장 적합한 프록시입니다. 

    *예제*<br/> f23854a1b01c4b1fa79fa2d9a5768526

**anonymousUserAcquisitionDate**

    datetime context.user.anonAcquisitionDate      

**authenticatedUserAcquisitionDate**

    datetime context.user.authAcquisitionDate     
 
**authUserId**

    string context.user.authId      Max: 100
* 
    앱 내에서 인증된 사용자를 정의하는 고유 식별자입니다. 개발자가 제공합니다. 인증된 사용자의 이점은 앱 내의 장치 간에 식별자를 로밍할 수 있으며 고유성이 유지된다는 것입니다. 

**storeRegion**

    string context.user.storeregion      Max: 100
* 
    앱이 제공된 원본 저장소 영역입니다. 

**userAcountId**

    string context.user.accountId      Max: 100
* 
    앱 내 계정을 정의하는 고유한 식별자입니다. 개발자가 제공합니다. 

### 사용자 지정 메트릭

    context.custom.metrics.<metric-name>

      double value
      double count
      double min
      double max
      double stdDev
      double sampledValue
      double sum


## remotedependency

**async**

    boolean remotedependency.async      
* 
    원격 종속성 호출이 비동기 상태인지를 나타내는 표시 

**commandName**

    string remotedependency.commandname      Max: 2048
* 
    원격 종속성이 SQL경우 원격 종속성의 SQL 명령 이름 

**dependencyTypeName**

    string remotedependency.dependencytypename      Max: 2048
* 
    원격 종속성의 원격 종속성 유형 이름 

**remoteDependencyName**

    string remotedependency.remotedependencyname      Max: 2048
* 
    원격 종속성의 이름 nf 

    *파생:* &lt;telemetryType.name&gt;으로 표준화됩니다.

**type**

    string remotedependency.remotedependencytype      Max: 100
* 
    원격 종속성의 유형입니다. 지원되는 유형은 SQL, AZURE 리소스(예: 저장소, 큐, 등) 및 HTTP입니다. 

**성공**

    boolean remotedependency.success      
* 
    원격 종속성 호출의 성공 여부를 나타내는 표시 


## request

**hasdetaileddata**

    boolean request.hasdetaileddata      
* 
    operation.id를 기반으로 관련된 원격 분석 항목이 있는지를 식별하는 표시 

**httpMethod**

    string request.httpmethod      Max: 100
* 
    요청에 사용되는 HTTP 메서드입니다.   

**id**

    string request.id      Max: 100
* 
    요청의 고유 식별자로, SDK에서 생성됩니다. 이 ID를 작업 ID 속성에 추가로 채워 동일한 요청에서 발생하는 원격 분석 항목 간 상관 관계를 설정할 수 있습니다. 

**responseCode**

    long request.responsecode      
* 
    요청의 응답 코드 

**성공**

    boolean request.success      
* 
    요청의 성공 여부에 대한 표시입니다. 200s의 응답 코드는 성공으로 간주됩니다. 

    *기본값:* null인 경우 true로 설정됩니다.


## sessionmetric

**anonymousUserDurationSinceLastVisit**

    Long sessionmetric.anonymoususerdurationsincelastvisit      
* 
    이 익명 사용자 식별자가 마지막으로 방문한 이후의 시간입니다. 처음 방문할 때 이 값은 비어 있습니다. 각 후속 방문에서는 방문 간 시간(일)입니다. 값이 3인 경우 이전 세션 인스턴스에서 이 세션 인스턴스까지의 기간이 3일인 것입니다. 

**anonymousUserSessionCount**

    Long sessionmetric.anonymoususersessioncount      
* 
    익명 사용자의 방문 수입니다. 이 고유한 익명 사용자 식별자에 대한 총 기록 세션의 증분 카운터입니다. 이 식별자의 세션마다 카운터가 증가합니다. 이 카운터는 해당 사용자 식별자가 30일 기간 내에 표시되지 않으면 지워집니다. 이때 카운터가 다시 설정되며 해당 사용자 식별자가 다음에 방문할 때는 새 사용자로 간주됩니다. 

**authenticatedAccountDurationSinceLastVisit**

    Long sessionmetric.authenticatedaccountdurationsincelastvisit      
* 
    이 계정 식별자가 마지막으로 방문한 이후의 시간입니다. 처음 방문할 때 이 값은 비어 있습니다. 각 후속 방문에서는 방문 간 시간(일)입니다. 값이 3인 경우 이전 세션 인스턴스에서 이 세션 인스턴스까지의 기간이 3일인 것입니다. 

**authenticatedAccountSessionCount**

    Long sessionmetric.authenticatedaccountsessioncount      
* 
    인증된 계정 식별자의 방문 수입니다. 이 고유한 계정 식별자에 대한 총 기록 세션의 증분 카운터입니다. 이 식별자의 세션마다 카운터가 증가합니다. 이 카운터는 해당 사용자 식별자가 30일 기간 내에 표시되지 않으면 지워집니다. 이때 카운터가 다시 설정되며 해당 사용자 식별자가 다음에 방문할 때는 새 사용자로 간주됩니다. 

**authenticatedUserDurationSinceLastVisit**

    Long sessionmetric.authenticateduserdurationsincelastvisit      
* 
    이 인증된 사용자 식별자가 마지막으로 방문한 이후의 시간입니다. 처음 방문할 때 이 값은 비어 있습니다. 각 후속 방문에서는 방문 간 시간(일)입니다. 값이 3인 경우 이전 세션 인스턴스에서 이 세션 인스턴스까지의 기간이 3일인 것입니다. 

**authenticatedUserSessionCount**

    Long sessionmetric.authenticatedusersessioncount      
* 
    인증된 사용자 식별자의 방문 수입니다. 이 고유한 인증된 사용자 식별자에 대한 총 기록 세션의 증분 카운터입니다. 이 식별자의 세션마다 카운터가 증가합니다. 이 카운터는 해당 사용자 식별자가 30일 기간 내에 표시되지 않으면 지워집니다. 이때 카운터가 다시 설정되며 해당 사용자 식별자가 다음에 방문할 때는 새 사용자로 간주됩니다. 

**crashCount**

    Long sessionmetric.crashcount      
* 
    이 세션 인스턴스 중에 발생한 충돌 횟수입니다. 

**duration**

    timespan sessionmetric.duration      
* 
    세션 인스턴스의 지속 시간 

**entryEvent**

    string sessionmetric.entryevent      Max: 200
* 
    세션의 첫 번째 이벤트입니다. event.name에서 공급되며 sessionMetric 메트릭에 대한 구분/집계로 사용할 수 있습니다. 

    *파생:* 원본 위치 event.name입니다.

**entryUrl**

    string sessionmetric.entryurl      Max: 2048
* 
    세션의 첫 번째 URL입니다. urlData.base에서 공급되며 sessionMetric 메트릭에 대한 구분/집계로 사용할 수 있습니다. 

    *파생:* 원본 위치 &lt;telemetryType&gt;.Url입니다.

**eventCount**

    Long sessionmetric.eventcount      
* 
    이 세션 인스턴스 중에 발생한 이벤트 수입니다. 

**exceptionCount**

    Long sessionmetric.exceptioncount      
* 
    이 세션 인스턴스 중에 발생한 예외 수입니다. 

**exitEvent**

    string sessionmetric.exitevent      Max: 200
* 
    세션의 마지막 이벤트입니다. event.name에서 공급되며 sessionMetric 메트릭에 대한 구분/집계로 사용할 수 있습니다. 

    *파생:* 원본 위치 event.name입니다.

**exitUrl**

    string sessionmetric.exiturl      Max: 2048
* 
    세션의 마지막 URL입니다. urlData.base에서 공급되며 sessionMetric 메트릭에 대한 구분/집계로 사용할 수 있습니다. 

    *파생:* 원본 위치 &lt;telemetryType&gt;.Url입니다.

**pageBounceCount**

    boolean sessionmetric.pagebouncecount      
* 
    이 sessionMetric 원격 분석 항목이 나타내는 반송 세션의 수입니다. 반송 세션은 단일 보기 원격 분석 항목을 기반으로 만든 세션입니다. 

    *파생:* sessionMetric.viewCount + sessionMetric.requestCount = 1이면 1이고, 그렇지 않으면 0입니다.

**pageCount**

    Long sessionmetric.pagecount      
* 
    이 세션 인스턴스 중에 발생한 보기 수 

**requestCount**

    Long sessionmetric.requestcount      
* 
    이 세션 인스턴스 중에 발생한 요청 수 

**sessionCount**

    Long sessionmetric.sessioncount      
* 
    이 원격 분석 sessionMetric 인스턴스가 나타내는 세션의 수 


## 추적

**context**

    string trace.context      Max: 100
* 
    추적/예외 발생 시 앱의 컨텍스트 

**exception**

    string trace.exception      Max: 10240
* 
    추적 원격 분석 항목에 연결된 예외 

**excerpt**

    string trace.excerpt      Max: 100
* 
    추적 원격 분석 항목의 간단한 텍스트 메시지 

**formatMessage**

    string trace.formatmessage      Max: 100
* 
    추적 원격 분석 항목에 대한 형식 메시지 

**formatProvider**

    string trace.formatprovider      Max: 100
* 
    추적 원격 분석 항목에 대한 형식 공급자 

**hasStackTrace**

    boolean trace.hasstacktrace      
* 
    추적 원격 분석 항목에 스택 추적이 포함되는지 여부를 나타내는 표시 

**level**

    string trace.level      Max: 100
* 
    추적 메시지의 수준 

**loggerName**

    string trace.loggername      Max: 100
* 
    추적 로거 이름 

**loggerShortName**

    string trace.loggershortname      Max: 100
* 
    로거의 간단한 이름 

**message**

    string trace.message      Max: 10240
* 
    추적 원격 분석 항목의 전체 텍스트 메시지 

**messageId**

    string trace.messageId      Max: 100
* 
    추적 원격 분석 항목의 고유 식별자 

**매개 변수**

    string trace.parameters      Max: 100
* 
    다음은 추적 원격 분석 항목에 대한 추적 레코더에 제공되는 매개 변수입니다. 

**processId**

    string trace.processId      Max: 100
* 
    원격 분석 항목 기록에서 앱의 프로세스 ID 

**sourceType**

    string trace.sourceType      Max: 100
* 
    추적 원격 분석 항목의 소스 형식 

**sqquenceId**

    string trace.sequenceid      Max: 100
* 
    추적 공급자가 추적 원격 분석 항목의 시퀀스를 기록하는 데 사용할 수 있는 시퀀스 식별자 

**stacktrace**

    string trace.stacktrace      Max: 100
* 
    추적 원격 분석 항목에 대해 캡처된 스택 추적 

**threadId**

    string trace.threadId      Max: 100
* 
    원격 분석 항목을 기록할 때 앱의 스레드 ID 

**userStackframe**

    string trace.userstackframe      Max: 100
* 
    추적 원격 분석 항목에 대한 사용자 스택 프레임 

**userStackframNum**

    string trace.userstackframenum      Max: 100
* 
    추적 원격 분석 항목에 대한 사용자 스택 프레임 


## view

**referrerDataUrl**

    string view.referralurl      Max: 2048
* 
    페이지 보기의 참조 URL입니다. 전체 URL이며, 전체 텍스트 검색 및 내보내기에서 지원됩니다. 이 필드는 매우 높은 카디널리티를 가질 수 있으며 특성에 해당합니다. 메트릭 탐색기에서 집계에 대해 사용할 수 있는 referralData 데이터 항목 집합으로 구문 분석됩니다. 

**referrerData.base**

    string view.referrerdata.base      Max: 2048
* 
    호스트, 쿼리 매개 변수를 제외한 참조 URL의 부분입니다. 루트 URI입니다. 이 값은 구분/집계에 사용할 수 있습니다. 

    *파생:* URL 변환에 대한 부록 참조

**referrerData.hashTag**

    string view.referrerdata.hashtag      Max: 100
* 
    참조 URL의 해시 태그 텍스트 

    *파생:* URL 변환에 대한 부록 참조

**referrerData.host**

    string view.referrerdata.host      Max: 200
* 
    참조 URL의 호스트입니다. URL이 로컬 URI이면 빈 상태로 표시됩니다. 

    *파생:* URL 변환에 대한 부록 참조

**referrerData.port**

    string view.referrerdata.port      Max: 100
* 
    전체 URL에 표시되는 경우 참조 URL의 포트입니다. 그렇지 않으면 비어 있습니다. 

    *파생:* URL 변환에 대한 부록 참조

**referrerData.protocol**

    string view.referrerdata.protocol      Max: 100
* 
    참조 URL의 프로토콜(HTTP, FTP 등) 

    *파생:* URL 변환에 대한 부록 참조

    *예제*<br/> http<br/>https

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.parameter      Max: 100
* 
    참조 URL의 쿼리 매개 변수 이름 배열 

    *파생:* URL 변환에 대한 부록 참조

**referrerData.queryParameters.parameter**

    string view.referrerdata.queryparameters.value      Max: 100
* 
    referringData URL에서 구문 분석되는 쿼리 매개 변수 값 배열 

    *파생:* URL 변환에 대한 부록 참조



## 참고 항목

* [Application Insights](app-insights-overview.md) 
* [연속 내보내기](app-insights-export-telemetry.md)
* [코드 샘플](app-insights-export-telemetry.md#code-samples)

<!----HONumber=AcomDC_0302_2016-->