<properties pageTitle="Azure WebJobs SDK 정의" metaKeywords="Azure Azure WebJobs SDK, Azure storage, Azure queues, Azure tables" description="An introduction to the Azure WebJobs SDK. Explains what the SDK is, typical scenarios it is useful for, and code samples." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="What is the Azure WebJobs SDK" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="tdykstra" />

# Azure WebJobs SDK 정의

이 문서에서는 WebJobs SDK의 정의에 대해 설명하고 이 SDK가 유용한 몇 가지 일반적인 시나리오를 검토하며 코드에서 SDK를 사용하는 방법을 간략하게 제공합니다.

## 목차

- [개요](#overview)
- [시나리오](#scenarios)
- [코드 샘플](#code)
- [WebJobs 외부에서 WebJobs SDK 사용](#workerrole)
- [WebJobs SDK를 사용하여 함수 호출](#nostorage)
- [다음 단계](#nextsteps)

## <a id="overview"></a>개요

[WebJobs](/ko-kr/documentation/articles/web-sites-create-web-jobs/)는 웹 사이트와 동일한 컨텍스트에서 프로그램이나 스크립트를 실행할 수 있도록 하는 Azure 웹 사이트의 기능입니다. WebJobs SDK는 WebJob으로 실행되며 Azure 저장소 큐, Blob 및 테이블과 서비스 버스 큐에 사용 가능한 코드를 작성하는 작업을 간소화하기 위한 도구입니다.

WebJob SDK에는 다음 구성 요소가 포함되어 있습니다.

* **NuGet 패키지**. Visual Studio 콘솔 응용 프로그램 프로젝트에 추가하는 NuGet 패키지는 코드가 Azure 저장소 서비스 또는 서비스 버스 큐를 작동하기 위해 사용하는 프레임워크를 제공합니다.   
  
* **대시보드**. WebJobs SDK의 일부분은 Azure 웹 사이트에 포함되어 있으며 NuGet 패키지를 사용하여 작성하는 프로그램에 풍부한 모니터링 및 진단을 제공합니다. 이러한 모니터링 및 진단 기능을 사용하기 위해 코드를 작성할 필요는 없습니다.

## <a id="scenarios"></a>시나리오

다음은 Azure WebJob SDK로 보다 쉽게 처리할 수 있는 일반적인 시나리오입니다.

* 이미지 처리 또는 기타 CPU 집중 작업. 웹 사이트의 일반적인 기능은 이미지나 비디오를 업로드하는 것입니다. 업로드한 후에 콘텐츠를 조작하려는 경우도 많지만 이 작업을 수행하기 위해 사용자를 기다리게 하고 싶지는 않을 것입니다.

* 큐 처리. 웹 프런트 엔드가 백 엔드 서비스와 통신하는 일반적인 방법은 큐를 사용하는 것입니다. 웹 사이트가 작업을 끝내야 할 경우 큐에 메시지를 푸시합니다. 백 엔드 서비스는 큐에서 메시지를 풀한 후 해당 작업을 수행합니다. 이미지 처리에 큐를 사용할 수 있습니다. 예를 들어 사용자가 많은 수의 파일을 업로드하면 백 엔드에서 처리를 위해 선택할 수 있게 파일 이름을 큐 메시지에 넣습니다. 또는 큐를 사용하여 사이트 응답성을 향상시킬 수 있습니다. 예를 들어 SQL 데이터베이스에 직접 쓰는 대신, 큐에 쓰고 사용자에게 작업이 완료되었다고 알린 후에 백 엔드 서비스가 지연 시간이 긴 관계형 데이터베이스 작업을 처리할 수 있도록 합니다. 이미지 프로세스가 포함된 큐 처리의 예제는 [WebJobs SDK 시작 자습서](../websites-dotnet-webjobs-sdk-get-started/)를 참조하세요.

* RSS 집계. RSS 피드 목록을 유지 관리하는 사이트가 있는 경우 백그라운드 프로세스에서 피드의 모든 문서를 풀할 수 있습니다.

* 로그 파일 집계 또는 정리와 같은 파일 유지 관리.  분석 작업을 실행하기 위해 조합하려는 여러 사이트에서 또는 별도의 시간 범위 동안 로그 파일이 만들어지도록 할 수 있습니다. 또는 오래된 로그 파일을 정리하는 작업이 매주 실행되도록 예약할 수도 있습니다.

* Azure 테이블로 수신. 파일을 Blob에 저장한 다음 구문 분석하여 테이블에 데이터를 저장할 수 있습니다. 수신 기능은 많은 행(경우에 따라 수백만 개)을 쓸 수 있는데 WebJobs SDK를 사용하면 이 기능을 쉽게 구현할 수 있습니다. 또한 SDK는 테이블에 작성되는 행의 수와 같은 진행률 표시기의 실시간 모니터링 기능도 제공합니다.

* 백그라운드 스레드에서 실행할 기타 장기 실행 작업(예: [전자 메일 전송](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). )

## <a id="code"></a> 코드 샘플

Azure 저장소를 사용하는 일반적인 작업을 처리하기 위한 코드는 간단합니다. 콘솔 응용 프로그램에서 실행하려는 백그라운드 작업에 대한 메서드를 작성한 후 WebJob SDK의 특성으로 데코레이트합니다. `Main` 메서드는 작성하는 메서드에 대한 호출을 조정하는 `JobHost` 개체를 만듭니다. WebJob SDK 프레임워크는 사용하는 WebJob SDK 특성을 토대로 메서드 호출 시기를 파악합니다. 

아래에는 큐를 폴링하고 수신된 각 큐 메시지에 대해 Blob를 만드는 간단한 프로그램이 나와 있습니다.

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

`JobHost` 개체는 백그라운드 함수 집합에 대한 컨테이너입니다. `JobHost` 개체는 함수를 모니터링하고, 이러한 함수를 트리거하는 이벤트를 조사하고, 트리거 이벤트가 발생할 때 함수를 실행합니다. `JobHost` 메서드를 호출하여 컨테이너 프로세스를 현재 스레드에서 실행할지 또는 백그라운드 스레드에서 실행할지를 지정합니다. 이 예제에서 `RunAndBlock` 메서드는 현재 스레드에서 해당 프로세스를 계속 실행합니다.

이 예제의 `ProcessQueueMessage` 메서드는 `QueueTrigger` 특성을 가지므로 새 큐 메시지가 수신되면 해당 함수가 트리거됩니다. `JobHost` 개체는 지정된 큐에서 새 큐 메시지를 찾고(이 예에서는 "webjobsqueue") 찾은 후에는 `ProcessQueueMessage`를 호출합니다. `QueueTrigger` 특성은 `inputText` 매개 변수를 큐 메시지의 값에 바인딩하도록 프레임워크에 전달합니다. 

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] <mark>string inputText</mark>, 
    [Blob("containername/blobname")]TextWriter writer)</pre>

또한 프레임워크는 `TextWriter` 개체를 "containername" 컨테이너의 "blobname" Blob에 바인딩합니다.

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    <mark>[Blob("containername/blobname")]TextWriter writer</mark>)</pre>

그런 후 함수는 이러한 매개 변수를 사용하여 큐 메시지의 값을 Blob에 씁니다.

		writer.WriteLine(inputText);

WebJobs SDK의 트리거 및 바인더 기능은 Azure 저장소 개체 및 서비스 버스 큐를 사용하기 위해 작성해야 하는 코드를 획기적으로 간소화합니다. 큐 및 Blob 처리에 필요한 하위 수준 코드는 WebJob SDK 프레임워크에 의해 작성됩니다. 이 프레임워크는 아직 존재하지 않는 큐를 만들고, 큐를 열고, 큐 메시지를 읽고, 처리가 완료되면 큐 메시지를 삭제하고, 아직 존재하지 않는 Blob 컨테이너를 만들고, Blob를 작성하는 등, 다양한 작업을 수행합니다.

WebJob SDK는 Azure 저장소를 사용하는 다양한 방법을 제공합니다. 예를 들어 `QueueTrigger` 특성으로 데코레이트하는 매개 변수가 바이트 배열이거나 사용자 지정 형식인 경우 JSON에서 자동으로 역직렬화됩니다. 또한 Azure 저장소 계정에서 새 Blob을 만들 때마다 `BlobTrigger` 특성을 사용하여 프로세스를 트리거할 수 있습니다. `QueueTrigger`는 몇 초 안에 새 큐 메시지를 찾지만 `BlobTrigger`는 새 Blob을 검색하는 데 최대 20분이 걸릴 수 있습니다. `BlobTrigger`는 `JobHost`가 시작될 때마다 Blob을 검색한 다음 Azure 저장소 로그를 주기적으로 확인하여 새 Blob을 검색합니다.

## <a id="workerrole"></a>WebJobs 외부에서 WebJobs SDK 사용

WebJobs SDK를 사용하는 프로그램은 표준 콘솔 응용 프로그램이며 WebJob으로 실행할 필요 없이 어디서나 실행할 수 있습니다. 개발 컴퓨터에서는 로컬로 프로그램을 테스트하고, 프로덕션에서는 클라우드 서비스 작업자 역할 또는 Windows 서비스 중 원하는 환경에서 프로그램을 실행할 수 있습니다. 

그렇지만 대시보드는 Azure 웹 사이트에 대한 사이트 확장으로만 사용할 수 있습니다. WebJob 외부에서 실행하되 대시보드는 계속 사용하려는 경우 WebJobs SDK 대시보드 연결 문자열이 참조하는 것과 같은 저장소 계정을 사용하도록 Azure 웹 사이트를 구성할 수 있습니다. 그러면 해당 사이트의 WebJobs 대시보드에 다른 위치에서 실행 중인 프로그램의 함수 실행에 대한 데이터가 표시됩니다. URL https://*{websitename}*.scm.azurewebsites.net/azurejobs/#/functions를 사용하여 대시보드로 이동할 수 있습니다. 자세한 내용은 [WebJob SDK를 사용한 로컬 개발을 위해 대시보드 가져오기](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)를 참조하세요. 단, 이 블로그 게시물에는 이전 연결 문자열 이름이 표시됩니다. 

## <a id="nostorage"></a>WebJobs SDK를 사용하여 함수 호출

Azure 저장소 큐, 테이블, Blob 또는 서비스 버스 큐를 직접 사용하지 않는 경우에도 WebJobs SDK는 다음과 같은 여러 가지 이점을 제공합니다.

* 대시보드에서 함수를 호출할 수 있습니다.
* 대시보드에서 함수를 재생할 수 있습니다.
* 특정 WebJob에 연결된 로그(응용 프로그램 로그) 또는 로그가 생성된 특정 함수 호출에 연결된 로그(`TextWriter` 매개 변수 로그)를 대시보드에서 확인할 수 있습니다. 

* 자세한 내용은 [함수를 수동으로 호출하는 방법](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual) 및 [로그를 작성하는 방법](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs) 을 참조하세요.

## <a id="nextsteps"></a>다음 단계

WebJobs SDK에 대한 자세한 내용은 [Azure WebJobs 권장 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.
