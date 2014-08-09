<properties  linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (.NET) - Azure" metaKeywords="Get started Azure Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

# 서비스 버스 토픽/구독을 사용하는 방법

<span>이 가이드에서는 서비스 버스 토픽과 구독을 사용하는 방법을 보여 줍니다. 샘플은
C#으로 작성되었으며 .NET API를 사용합니다. 여기서 다루는 시나리오에는 **토픽 및 구독 만들기, 구독 필터 만들기,
토픽에 메시지 보내기,** **구독에서 메시지 받기,** **토픽 및 구독 삭제** 등이 포함됩니다. 토픽 및 구독에 대한 자세한
내용은 [다음 단계](#nextsteps) 섹션을 참조하십시오. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

<h2><span  class="short-header">응용 프로그램 구성</span>서비스 버스를 사용하도록 응용 프로그램 구성</h2>


서비스 버스를 사용하는 응용 프로그램을 만드는 경우 서비스 버스 어셈블리에 대한 참조를 추가하고 해당 네임스페이스를 포함해야
합니다.

<h2><span  class="short-header">NuGet 패키지 다운로드</span>서비스 버스 NuGet 패키지 다운로드</h2>


서비스 버스 **NuGet** 패키지는 서비스 버스 API를 가져오고 모든 서비스 버스 종속성으로 응용 프로그램을 구성하는 가장
쉬운 방법입니다. NuGet Visual Studio 확장을 사용하면 Visual Studio 및 Visual Studio
Express 2012 for Web에서 라이브러리와 도구를 쉽게 설치 및 업데이트할 수 있습니다. 서비스 버스 NuGet
패키지는 서비스 버스 API를 가져오고 모든 서비스 버스 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법입니다.

응용 프로그램에서 NuGet 패키지를 설치하려면 다음을 수행합니다.

1.  솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다. 2.  WindowsAzure를 검색하고 **Azure 서비스 버스** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한
    후 이 대화 상자를 닫습니다.
    
    ![](./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png)

이제 서비스 버스에 대한 코드를 작성할 준비가 되었습니다.

<h2><span  class="short-header">연결 문자열을 설정하는 방법</span>서비스 버스 연결 문자열을 설정하는 방법</h2>


서비스 버스는 연결 문자열을 사용하여 끝점과 자격 증명을 저장합니다. 코드에서 하드 코딩하는 대신 구성 파일에 연결 문자열을
저장할 수 있습니다.

* Azure 클라우드 서비스를 사용하는 경우 Azure 서비스 구성 시스템(`*.csdef` 및 `*.cscfg` 파일)을
  사용하여 연결 문자열을 저장하는 것이 좋습니다.
* Azure 웹 사이트나 Azure 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: `web.config` 파일)을
  사용하여 연결 문자열을 저장하는 것이 좋습니다.

두 경우 모두, 이 가이드의 뒷부분에 표시된 대로 `CloudConfigurationManager.GetSetting` 메서드를
사용하여 연결 문자열을 검색할 수 있습니다.

### <a name="config-connstring"> </a>클라우드 서비스를 사용하는 경우 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스 프로젝트에 고유하며, 응용 프로그램을 다시 배포하지 않고도 Azure 관리
포털에서 구성 설정을 동적으로 변경할 수 있게 해 줍니다. 예를 들어 아래 표시된 대로 서비스 정의(`*.csdef`) 파일에
설정을 추가합니다.

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

그런 다음 서비스 구성(`*.cscfg`) 파일에서 값을 지정합니다.

	<ServiceConfiguration serviceName="WindowsAzure1">
	...
		<Role name="MyRole">
			<ConfigurationSettings>
				<Setting name="Microsoft.ServiceBus.ConnectionString" 
						 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
			</ConfigurationSettings>
		</Role>
	...
	</ServiceConfiguration>

이전 섹션에서 설명한 대로 관리 포털에서 검색된 발급자 및 키 값을 사용합니다.

### 웹 사이트 또는 가상 컴퓨터를 사용하는 경우 연결 문자열 구성

웹 사이트나 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: `web.config`)을 사용하는 것이 좋습니다.
`<a ppSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

	<configuration>
	    <a ppSettings>
		    <a dd key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

이전 섹션에서 설명한 대로 관리 포털에서 검색된 발급자 및 키 값을 사용합니다.

<h2><span  class="short-header">토픽을 만드는 방법</span>토픽을 만드는 방법</h2>


**NamespaceManager** 클래스를 통해 서비스 버스 토픽과 구독에 대한 관리 작업을 수행할 수 있습니다.
**NamespaceManager** 클래스는 큐를 만들고 열거 및 삭제하기 위한 메서드를 제공합니다.

이 예제에서는 Azure **CloudConfigurationManager** 클래스를 사용하여
**NamespaceManager** 개체를 생성합니다. 연결 문자열은 서비스 버스 서비스 네임스페이스의 기준 주소와 관리 권한이
있는 적절한 자격 증명으로 구성됩니다. 이 연결 문자열은 다음 형식을 사용합니다.

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

예를 들어 이전 섹션의 구성 설정이 제공될 경우

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    
    var namespaceManager = 
    	NamespaceManager.CreateFromConnectionString(connectionString);
    
    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

토픽의 속성을 조정할 수 있게 해 주는 **CreateTopic** 메서드 오버로드가 있습니다. 예를 들어 토픽에 전송되는
메시지에 적용할 기본 "TTL(Time-To-Live)" 값을 설정할 수 있습니다. 이러한 설정은
**TopicDescription** 클래스를 사용하여 적용됩니다. 다음 예제에서는 최대 크기가 5GB이고 기본 메시지
TTL(Time-To-Live)이 1분인 "TestTopic"이라는 토픽을 만드는 방법을 보여 줍니다.

    // Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);
    
    // Create a new Topic with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    
    var namespaceManager = 
    	NamespaceManager.CreateFromConnectionString(connectionString);
    
    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**참고:** **NamespaceManager** 개체의 **TopicExists** 메서드를 사용하여 서비스 네임스페이스 내에
지정된 이름의 토픽이 이미 있는지 확인할 수 있습니다.

<h2><span  class="short-header">구독을 만드는 방법</span>구독을 만드는 방법</h2>


**NamespaceManager** 클래스를 사용하여 토픽 구독을 만들 수도 있습니다. 구독에는 이름이 지정되며, 구독의 가상
큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

### 기본(MatchAll) 필터를 사용하여 구독 만들기

**MatchAll** 필터는 새 구독을 만들 때 필터를 지정하지 않은 경우 사용되는 기본 필터입니다. **MatchAll**
필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 "AllMessages"라는 구독을
만들고 기본 **MatchAll** 필터를 사용합니다.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    
    var namespaceManager = 
    	NamespaceManager.CreateFromConnectionString(connectionString);
    
    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### 필터를 사용하여 구독 만들기

토픽에 전송된 메시지 중 특정 토픽 구독 내에 표시되어야 하는 메시지를 지정하는 필터를 설정할 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 **SqlFilter**입니다. SQL 필터는 토픽에
게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은
[SqlFilter.SqlExpression][] 구문을 참조하십시오.

아래 예제에서는 사용자 지정 **MessageNumber** 속성이 3보다 큰 메시지만 선택하는 **SqlFilter**를
사용하여 "HighMessages"라는 구독을 만듭니다.

    // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
    	new SqlFilter("MessageNumber > 3");
    
     namespaceManager.CreateSubscription("TestTopic", 
    	"HighMessages", 
    	highMessagesFilter);

마찬가지로, 다음 예제에서는 **MessageNumber** 속성이 3보다 작거나 같은 메시지만 선택하는
**SqlFilter**를 사용하여 "LowMessages"라는 구독을 만듭니다.

    // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
    	new SqlFilter("MessageNumber <= 3");
    
     namespaceManager.CreateSubscription("TestTopic", 
    	"LowMessages", 
    	lowMessagesFilter);

이제 "TestTopic"으로 메시지를 보내는 경우 "AllMessages" 토픽 구독을 구독하는 수신자에게는 항상 배달되고,
"HighMessages" 및 "LowMessages" 토픽 구독을 구독하는 수신자에게는 메시지 내용에 따라 선택적으로
배달됩니다.

<h2><span  class="short-header">토픽에 메시지 보내기</span>토픽에 메시지를 보내는 방법</h2>


서비스 버스 토픽에 메시지를 보내기 위해 응용 프로그램은 연결 문자열을 사용하여 **TopicClient** 개체를 만듭니다.

아래 코드는 **CreateFromConnectionString** API 호출을 사용하여 위에서 만든 "TestTopic"
토픽에 대한 **TopicClient** 개체를 만드는 방법을 보여 줍니다.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

 TopicClient Client =     	TopicClient.CreateFromConnectionString(connectionString, "TestTopic");
    
    Client.Send(new BrokeredMessage());

서비스 버스 토픽으로 전송된 메시지는 **BrokeredMessage** 클래스 인스턴스입니다.
**BrokeredMessage** 개체에는 표준 속성 집합(예: **Label** 및 **TimeToLive**), 응용
프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 응용 프로그램 데이터 본문이 있습니다. 응용 프로그램은
**BrokeredMessage** 생성자에 직렬화된 개체를 전달하여 메시지 본문을 설정할 수 있으며, 적절한
**DataContractSerializer**가 개체를 직렬화하는 데 사용됩니다. 또는 **System.IO.Stream**을
제공할 수 있습니다.

다음 예제에서는 위의 코드 조각에서 얻은 "TestTopic" **TopicClient**에 테스트 메시지 5개를 보내는 방법을
보여 줍니다. 루프가 반복될 때마다 각 메시지의 **MessageNumber** 속성 값이 어떻게 달라지는지 확인합니다(메시지를
수신하는 구독 결정).

    for (int i=0; i<5 ; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
    	new BrokeredMessage("Test message " + i);
    
       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;
    
       // Send message to the topic
       Client.Send(message);
     }

서비스 버스 토픽은 256KB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대
크기는 64KB임). 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 큐
크기는 생성 시 정의되며 상한이 5GB입니다.

## <span  class="short-header">구독에서 메시지 받기</span>구독에서 메시지를 받는 방법

구독에서 메시지를 받는 가장 쉬운 방법은 **SubscriptionClient** 개체를 사용하는 것입니다.
**SubscriptionClient** 개체는 **ReceiveAndDelete** 및 **PeekLock**의 두 가지 모드로
작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, 서비스 버스가 구독 메시지에 대한 읽기
요청을 받으면 메시지를 이용되는 것으로 표시하고 응용 프로그램에 반환합니다. **ReceiveAndDelete** 모드는 가장
단순한 모델이며, 응용 프로그램이 실패 이벤트 시 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해
소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보십시오. 서비스 버스가 메시지를 이용되는 것으로
표시했기 때문에 응용 프로그램이 다시 시작되고 메시지 이용을 다시 시작할 때 크래시 전에 이용된 메시지는 누락됩니다.

**PeekLock** 모드(기본 모드)에서는 수신 프로세스가 2단계 작업이므로 메시지 누락이 허용되지 않는 응용 프로그램을
지원할 수 있습니다. 서비스 버스가 요청을 받으면 이용할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용
프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지에 대해
**Complete**를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. 서비스 버스는 **Complete** 호출을 확인한
후 메시지를 이용되는 것으로 표시하고 구독에서 제거합니다.

아래 예제에서는 기본 **PeekLock** 모드를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 다른
**ReceiveMode** 값을 지정하기 위해 **CreateFromConnectionString**에 다른 오버로드를 사용할
수 있습니다. 이 예제는 무한 루프를 만들고 메시지가 "HighMessages" 구독에 도착하면 처리합니다.
"HighMessages" 구독 경로는 "<*topic
path*>/subscriptions/<*subscription name*>" 형식으로 제공됩니다.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    
    SubscriptionClient Client = 
    	SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");
    
    Client.Receive();
     
    // Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();
    
       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
    			message.Properties["MessageNumber"]);
    
             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
             message.Abandon();
          }
       }
    } 

<h2><span  class="short-header">응용 프로그램 크래시 및 읽을 수 없는 메시지</span>응용 프로그램 크래시 및 읽을 수 없는 메시지를 처리하는 방법</h2>


서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용
프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 **Complete** 메서드 대신 **Abandon** 메서드를
호출할 수 있습니다. 그러면 서비스 버스에서 구독 내 메시지의 잠금을 해제하여 동일한 이용 응용 프로그램이나 다른 이용 응용
프로그램이 다시 받을 수 있게 합니다.

구독 내에서 잠긴 메시지와 연결된 시간 제한도 있으며, 응용 프로그램에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는
경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **Complete** 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 응용
프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정
상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지
배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의
**MessageId** 속성을 사용합니다.

<h2><span  class="short-header">토픽 및 구독 삭제</span>토픽 및 구독을 삭제하는 방법</h2>


아래 예제에서는 **HowToSample** 서비스 네임스페이스에서 **TestTopic**이라는 토픽을 삭제하는 방법을 보여
줍니다.

    // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 다음 코드는
**TestTopic** 토픽에서 **HighMessages**라는 구독을 삭제하는 방법을 보여 줍니다.

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

<h2><span  class="short-header">다음 단계</span>다음 단계</h2>


이제 서비스 버스 토픽 및 구독의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보십시오.

* MSDN 참조: [큐, 토픽 및 구독][1]
* [SqlFilter][2]에 대한 API 참조
* 서비스 버스 큐로 메시지를 보내고 받는 응용 프로그램 빌드: [서비스 버스 조정된 메시징 .NET 자습서][3]



[1]: http://msdn.microsoft.com/ko-kr/library/hh367516.aspx
[2]: http://msdn.microsoft.com/ko-kr/library/microsoft.servicebus.messaging.sqlfilter.aspx
[3]: http://msdn.microsoft.com/ko-kr/library/hh367512.aspx