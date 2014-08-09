<properties  linkid="dev-net-how-to-service-bus-relay" urlDisplayName="Service Bus Relay" pageTitle="How to use Service Bus relay (.NET) - Azure" metaKeywords="get started azure Service Bus Relay C# " description="Learn how to use the Azure Service Bus relay service to connect two applications hosted in different locations." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

# 서비스 버스 릴레이 서비스를 사용하는 방법

이 가이드에서는 서비스 버스 릴레이 서비스를 사용하는 방법을 보여 줍니다. 샘플은 C#으로 작성되었으며 Azure용 .NET
라이브러리의 일부인 서비스 버스 어셈블리에 포함된 확장과 함께 Windows Communication Foundation API를
사용합니다. 서비스 버스 릴레이에 대한 자세한 내용은 [다음 단계](#next_steps) 섹션을 참조하십시오.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

<h2><span  class="short-header">서비스 버스 릴레이 정의</span>서비스 버스 릴레이 정의</h2>


서비스 버스 **릴레이** 서비스를 사용하면 Azure 데이터 센터와 고유한 온-프레미스 엔터프라이즈 환경 둘 다에서 실행되는
**하이브리드 응용 프로그램**을 빌드할 수 있습니다. 서비스 버스 릴레이는 방화벽 연결을 열거나 회사 네트워크 인프라를
주입식으로 변경하지 않고도 회사 엔터프라이즈 네트워크 내에 있는 WCF(Windows Communication
Foundation) 서비스를 공용 클라우드에 안전하게 노출할 수 있게 함으로써 이 작업을 도와줍니다.

![릴레이 개념](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

서비스 버스 릴레이를 사용하면 기존 엔터프라이즈 환경 내에 WCF 서비스를 호스트할 수 있습니다. 그런 다음 이러한 WCF
서비스로 들어오는 세션 및 요청의 수신 대기를 Azure 내에서 실행되는 서비스 버스로 위임할 수 있습니다. 이렇게 하면
Azure에서 실행되는 응용 프로그램 코드나 모바일 작업자 또는 엑스트라넷 파트너 환경에 이러한 서비스를 노출할 수 있습니다.
서비스 버스를 사용하면 이러한 서비스에 액세스할 수 있는 사람을 세부적으로 안전하게 제어할 수 있습니다. 서비스 버스는 기존
엔터프라이즈 솔루션의 응용 프로그램 기능과 데이터를 노출하고 클라우드에서 이용하는 강력하고 안전한 방법을 제공합니다.

이 가이드에서는 서비스 버스 릴레이를 사용하여 TCP 채널 바인딩을 통해 노출되고 두 당사자 간의 보안 통신을 구현하는 WCF 웹
서비스를 만드는 방법을 보여 줍니다.

<h2><span  class="short-header">서비스 네임스페이스 만들기</span>서비스 네임스페이스 만들기</h2>


Azure에서 서비스 버스 릴레이 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 서비스 네임스페이스는 응용
프로그램 내에서 서비스 버스 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다.

서비스 네임스페이스를 만들려면

1.  [Azure 관리 포털][1]에 로그온합니다.

2.  관리 포털의 왼쪽 탐색 창에서 **Service Bus**를 클릭합니다.

3.  관리 포털의 아래쪽 창에서 **만들기**를 클릭합니다.
    
    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  **새 네임스페이스 추가** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시
    확인합니다.
    
    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)

5.  네임스페이스 이름이 사용 가능한지 확인한 후 해당 네임스페이스를 호스트할 국가 또는 지역을 선택합니다(계산 리소스를 배포할
    국가/지역과 같아야 함).
    
    중요: 응용 프로그램을 배포하도록 선택할 지역과 **같은 지역**을 선택합니다. 그러면 최상의 성능을 얻을 수 있습니다.

6.  확인 표시를 클릭합니다. 이제 시스템이 서비스 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를
    프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.
    
    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)
    
    만든 네임스페이스가 관리 포털에 표시되며, 활성화되는 데 약간의 시간이 걸립니다. 계속하기 전에 **활성** 상태가 될
    때까지 기다리십시오.

<h2><span  class="short-header">관리 자격 증명 얻기</span>네임스페이스에 대한 기본 관리 자격 증명 얻기</h2>


새 네임스페이스에 대해 릴레이 연결 만들기 등의 관리 작업을 수행하려면 네임스페이스에 대한 관리 자격 증명을 얻어야 합니다.

1.  왼쪽 탐색 창에서 **Service Bus** 노드를 클릭하여 사용 가능한 네임스페이스 목록을 표시합니다.   
     ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

2.  표시된 목록에서 방금 만든 네임스페이스를 선택합니다.   
     ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)

3.  **연결 정보**를 클릭합니다.   
     ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-06.png)

4.  **Access 연결 정보** 대화 상자에서 **기본 발급자** 및 **기본 키** 항목을 찾습니다. 아래에서 이 정보를
    사용하여 네임스페이스 관련 작업을 수행할 것이므로 이 값을 기록해 둡니다.

<h2><span  class="short-header">NuGet 패키지 다운로드</span>서비스 버스 NuGet 패키지 다운로드</h2>


서비스 버스 **NuGet** 패키지는 서비스 버스 API를 가져오고 모든 서비스 버스 종속성으로 응용 프로그램을 구성하는 가장
쉬운 방법입니다. NuGet Visual Studio 확장을 사용하면 Visual Studio 및 Visual Studio
Express 2012 for Web에서 라이브러리와 도구를 쉽게 설치 및 업데이트할 수 있습니다. 서비스 버스 NuGet
패키지는 서비스 버스 API를 가져오고 모든 서비스 버스 종속성으로 응용 프로그램을 구성하는 가장 쉬운 방법입니다.

응용 프로그램에서 NuGet 패키지를 설치하려면 다음을 수행합니다.

1.  솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다. 2.  WindowsAzure를 검색하고 **Azure 서비스 버스** 항목을 선택합니다. **설치**를 클릭하여 설치를 완료한
    후 이 대화 상자를 닫습니다.
    
    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

<h2><span  class="short-header">SOAP 웹 서비스 노출 및 이용</span>서비스 버스를 사용하여 SOAP 웹 서비스를 노출하고 TCP와 함께 이용하는 방법</h2>


기존 WCF SOAP 웹 서비스를 외부에서 이용할 수 있도록 노출하려면 서비스 바인딩과 주소를 변경해야 합니다. 이 경우 WCF
서비스를 설정 및 구성한 방법에 따라 구성 파일이나 코드를 변경해야 할 수도 있습니다. WCF를 사용하면 동일한 서비스에 대해
여러 개의 네트워크 끝점을 가질 수 있으므로 외부 액세스를 위한 서비스 버스 끝점을 추가하는 동시에 기존 내부 끝점을 유지할 수
있습니다.

이 작업에서는 간단한 WCF 서비스를 빌드하고 서비스 버스 수신기를 해당 서비스에 추가합니다. 이 예제에서는 Visual
Studio 2012에 대한 기본적인 지식이 있다고 가정하므로 프로젝트를 만드는 방법을 자세히 설명하지는 않으며, 대신 코드에
중점을 둡니다.

아래 단계를 시작하기 전에 다음 절차를 완료하여 환경을 설정합니다.

1.  Visual Studio 내에서 솔루션에 "Client" 및 "Service"의 두 프로젝트가 포함된 콘솔 응용 프로그램을
    만듭니다.
2.  두 프로젝트의 대상 프레임워크를 .NET Framework 4로 설정합니다. 3.  두 프로젝트에 **Azure Service Bus NuGet** 패키지를 추가합니다. 그러면 필요한 모든 어셈블리 참조가
    프로젝트에 추가됩니다.

### 서비스를 만드는 방법

먼저 서비스 자체를 만듭니다. 모든 WCF 서비스는 적어도 다음 세 가지 요소로 구성되어 있습니다.

* 교환되는 메시지 및 호출해야 하는 작업을 설명하는 계약 정의
* 위 계약의 구현
* 해당 서비스를 호스트하고 많은 끝점을 노출하는 호스트

이 섹션의 코드 예제에서는 이러한 각 구성 요소를 다룹니다.

계약은 두 개의 숫자를 더하고 결과를 반환하는 단일 작업 **AddNumbers**를 정의합니다.
**IProblemSolverChannel** 인터페이스를 사용하면 클라이언트에서 프록시 수명을 더 쉽게 관리할 수 있습니다.
이러한 인터페이스 생성은 모범 사례로 간주됩니다. 이 계약 정의를 별도의 파일에 저장하여 "Client" 및 "Service"
프로젝트 둘 다에서 해당 파일을 참조할 수 있게 하는 것이 좋지만 두 프로젝트에 코드를 복사할 수도 있습니다.

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

계약이 준비되면 구현은 어렵지 않습니다.

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

**프로그래밍 방식으로 서비스 호스트를 구성하는 방법**

계약과 구현이 준비되었으면 이제 서비스를 호스트할 수 있습니다. 호스팅은 서비스 인스턴스를 관리하고 메시지를 수신 대기하는 끝점을
호스트하는 **System.ServiceModel.ServiceHost** 개체 내부에서 수행됩니다. 아래 코드는 일반적인 로컬
끝점과 서비스 버스 끝점 둘 다로 서비스를 구성하여 내부 및 외부 끝점의 모양을 나란히 보여 줍니다.
"\*\*namespace\*\*" 문자열을 해당 네임스페이스 이름으로 바꾸고 "\*\*key\*\*"를 위의 설정 단계에서 얻은
발급자 키로 바꿉니다.

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    
    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");
    
    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});
    
    sh.Open();
    
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    
    sh.Close();

예제에서는 동일한 계약 구현에 있는 두 개의 끝점을 만듭니다. 하나는 로컬이고 다른 하나는 서비스 버스를 통해 프로젝트됩니다. 두
끝점의 주요 차이점은 바인딩입니다. 로컬 끝점에는 **NetTcpBinding**이 사용되고 서비스 버스 끝점과 주소에는
**NetTcpRelayBinding**이 사용됩니다. 로컬 끝점에는 특정 포트가 포함된 로컬 네트워크 주소가 있습니다. 서비스
버스 끝점에는 문자열 "sb", 해당 네임스페이스 이름 및 경로 "solver"로 구성된 끝점 주소가 있습니다. 이렇게 하면
URI "sb://[serviceNamespace].servicebus.windows.net/solver"가 생성되며,
정규화된 외부 DNS 이름을 사용하여 서비스 끝점을 서비스 버스 TCP 끝점으로 식별합니다. 위에서 설명한 대로 자리 표시자를
바꾸는 코드를 "Service" 응용 프로그램의 **Main** 함수에 배치하면 서비스가 정상적으로 작동합니다. 서비스가 서비스
버스에서만 수신 대기하도록 하려는 경우 로컬 끝점 선언을 제거합니다.

**App.config 파일에서 서비스 호스트를 구성하는 방법**

App.config 파일을 사용하여 호스트를 구성할 수도 있습니다. 이 경우 서비스 호스팅 코드는 다음과 같습니다.

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

끝점 정의가 App.config 파일로 이동합니다. **NuGet** 패키지에서 서비스 버스의 필수 구성 확장인
App.config 파일에 다양한 정의를 이미 추가했습니다. 위에 나열된 코드와 동등한 다음 코드 조각이
**system.serviceModel** 요소 바로 아래에 표시되어야 합니다. 이 코드 조각에서는 프로젝트 C# 네임스페이스의
이름이 "Service"라고 가정합니다. 자리 표시자를 해당 서비스 버스 서비스 네임스페이스와 키로 바꿉니다.

    <services>
        <service  name="Service.ProblemSolver">
            <endpoint  contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint  contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior  name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret  issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

이렇게 변경하면 서비스가 이전처럼 시작되지만 두 개의 라이브 끝점이 있습니다. 하나는 로컬 끝점이고 다른 하나는 클라우드에서 수신
대기합니다.

### 클라이언트를 만드는 방법

**프로그래밍 방식으로 클라이언트를 구성하는 방법**

서비스를 이용하기 위해 **ChannelFactory** 개체를 사용하여 WCF 클라이언트를 구성할 수 있습니다. 서비스 버스는
ACS(액세스 제어 서비스)를 사용하여 구현된 클레임 기반 보안 모델을 사용합니다. **TokenProvider** 클래스는 잘
알려진 일부 토큰 공급자를 반환하는 기본 제공 팩터리 메서드를 사용하여 보안 토큰 공급자를 나타냅니다. 아래 예제에서는
**SharedSecretTokenProvider**를 사용하여 공유 암호 자격 증명을 유지하고 액세스 제어 서비스에서 적절한
토큰을 얻는 작업을 처리합니다. 이전 섹션에서 설명한 대로 이름과 키는 포털에서 얻은 것입니다.

먼저 서비스의 **IProblemSolver** 계약 코드를 클라이언트 프로젝트에 참조하거나 복사합니다.

그런 다음 자리 표시자 텍스트를 해당 서비스 버스 서비스 네임스페이스와 키로 바꾸어 클라이언트 **Main** 메서드의 코드를
바꿉니다.

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));
    
    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

이제 클라이언트와 서비스를 컴파일한 후 실행할 수 있으며(서비스 먼저 실행), 클라이언트가 서비스를 호출하고 "9"를 인쇄합니다.
클라이언트와 서버를 서로 다른 컴퓨터 또는 네트워크에서도 실행할 수 있으며, 통신이 제대로 작동합니다. 클라이언트 코드가
클라우드나 로컬에서 실행될 수도 있습니다.

**App.config 파일에서 클라이언트를 구성하는 방법**

App.config 파일을 사용하여 클라이언트를 구성할 수도 있습니다. 이 경우 클라이언트 코드는 다음과 같습니다.

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

끝점 정의가 App.config 파일로 이동합니다. 위에 나열된 코드와 같은 다음 코드 조각은
**system.serviceModel** 요소 바로 아래에 표시되어야 합니다. 이전처럼 여기서 자리 표시자를 해당 서비스 버스
서비스 네임스페이스와 키로 바꾸어야 합니다.

    <client>
        <endpoint  name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior  name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret  issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

<h2><span  class="short-header">다음 단계</span>다음 단계</h2>


이제 서비스 버스 **릴레이** 서비스의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보십시오.

* 서비스 빌드: [Service Bus용 서비스 빌드][2]
* 클라이언트 빌드: [Service Bus Client 응용 프로그램 빌드][3]
* 서비스 버스 샘플: [Azure 샘플][4](영문)에서 다운로드



[1]: http://manage.windowsazure.com
[2]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee173564.aspx
[3]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee173543.aspx
[4]: http://code.msdn.microsoft.com/windowsazure