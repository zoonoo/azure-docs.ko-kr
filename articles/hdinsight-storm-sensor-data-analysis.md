<properties title="Storm 및 HDInsight를 사용하여 센서 데이터 분석" pageTitle="Apache Storm 및 Microsoft Azure HDInsight(Hadoop)를 사용하여 센서 데이터 분석" description="Apache Storm을 사용하여 HDInsight(Hadoop)에서 센서 데이터를 실시간으로 처리하는 방법에 대해 알아봅니다." metaKeywords="Azure hdinsight storm, Azure hdinsight realtime, azure hadoop storm, azure hadoop realtime, azure hadoop real-time, azure hdinsight real-time" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

# Storm 및 HDInsight(Hadoop)의 HBase를 사용하여 센서 데이터 분석

이 문서에서는 HDInsight Storm 클러스터를 사용하는 솔루션을 빌드하여 Azure 이벤트 허브에서 센서 데이터를 처리하는 방법을 설명합니다. 처리 중에 Storm 토폴로지는 들어오는 데이터를 HBase 클러스터에 저장합니다. 또한 이 토폴로지는 SignalR을 사용하여 Azure 웹 사이트에서 호스트되는 웹 기반 대시보드를 통해 거의 실시간 정보를 제공합니다.

> [AZURE.NOTE] 이 프로젝트의 전체 버전은 <https://github.com/Blackmist/hdinsight-eventhub-example>에서 제공됩니다.

## 필수 조건

-   Azure 구독

-   [Microsoft Azure SDK for .NET][Microsoft Azure SDK for .NET]이 포함된 Visual Studio

-   [Java 및 JDK][Java 및 JDK]

-   [Maven][Maven]

-   [Git][Git]

> [AZURE.NOTE] Java, JDK, Maven 및 Git는 [Chocolatey NuGet][Chocolatey NuGet] 패키지 관리자를 통해서도 제공됩니다.

## 대시보드 만들기

대시보드는 거의 실시간의 센서 정보를 표시하는 데 사용됩니다. 이 경우 대시보드는 Azure 웹 사이트에서 호스트되는 ASP.NET 응용 프로그램입니다. 이 응용 프로그램은 기본적으로 메시지를 처리할 때 Storm 토폴로지에서 정보를 받는 [SignalR][SignalR] 허브로 사용됩니다.

이 웹 사이트에는 정적 index.html 파일도 포함됩니다. 이 파일은 SignalR에도 연결되며 D3.js를 사용하여 Storm 토폴로지에 의해 전송된 데이터의 그래프를 만듭니다.

> [WACOM.NOTE] SignalR 대신 원시 WebSockets를 사용할 수도 있지만, 웹 사이트를 규모 확장하려는 경우 WebSockets는 확장 메커니즘을 기본적으로 제공하지 않습니다. Azure 서비스 버스(<http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus>)를 사용하면 SignalR을 확장할 수 있습니다.
>
> Storm 토폴로지를 사용하여 원시 WebSockets를 사용하는 Python 웹 사이트와 통신하는 방법의 예제는 [Storm Tweet Sentiment D3 Visualization][Storm Tweet Sentiment D3 Visualization] 프로젝트를 참조하세요.

1.  Visual Studio에서 **ASP.NET 웹 응용 프로그램** 프로젝트 템플릿을 사용하여 새 C# 응용 프로그램을 만듭니다. 새 응용 프로그램 이름을 **Dashboard**로 지정합니다.

2.  **새 ASP.NET 프로젝트** 창에서 **비어 있음** 응용 프로그램 템플릿을 선택합니다. **Microsoft Azure** 섹션에서 **클라우드의 호스트** 및 **웹 사이트**를 선택합니다. 그리고 마지막으로 **확인**을 클릭합니다.

    > [AZURE.NOTE] 해당 메시지가 표시되면 Azure 구독에 로그인합니다.

3.  **Microsoft Azure 사이트 구성** 대화 상자에서 웹 사이트의 **사이트 이름**과 **하위 지역**을 입력하고 **확인**을 클릭합니다. 그러면 대시보드를 호스트할 Azure 웹 사이트가 만들어집니다.

4.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 | SignalR 허브 클래스(v2)**를 선택합니다. 클래스의 이름을 **DashHub.cs**로 지정하여 프로젝트에 추가합니다. 그러면 HDInsight와 대시보드 웹 페이지 간에 데이터를 전달하는 데 사용되는 SignalR 허브가 포함됩니다.

    > [AZURE.NOTE] Visual Studio 2012를 사용하는 경우에는 **SignalR 허브 클래스(v2)** 템플릿을 사용할 수 없습니다. 대신 DashHub라는 일반 **Class**를 추가할 수 있습니다. 또한 **도구 | 라이브러리 패키지 관리자 | 패키지 관리자 콘솔**을 열고 다음 명령을 실행하여 SignalR 패키지를 수동으로 설치해야 합니다.
    >
    > `install-package Microsoft.AspNet.SignalR`

5.  **DashHub.cs**의 코드를 다음과 같이 바꿉니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.AspNet.SignalR;

        namespace dashboard
        {
            public class DashHub : Hub
            {
                public void Send(string message)
                {
                    // Call the broadcastMessage method to update clients.
                    Clients.All.broadcastMessage(message);
                }
            }
        }

6.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 | OWIN 시작 클래스**를 선택합니다. 새 클래스의 이름을 **Startup.cs**로 지정합니다.

    > [AZURE.NOTE] Visual Studio 2012를 사용하는 경우에는 **OWIN 시작 클래스** 템플릿을 사용할 수 없습니다. 대신 Startup이라는 **Class**를 만들 수 있습니다.

7.  **Startup.cs**의 내용을 다음과 같이 바꿉니다.

        using System;
        using System.Threading.Tasks;
        using Microsoft.Owin;
        using Owin;

        [assembly: OwinStartup(typeof(dashboard.Startup))]

        namespace dashboard
        {
            public class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
                    app.MapSignalR();
                }
            }
        }

8.  **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 | HTML 페이지**를 클릭합니다. 새 페이지의 이름을 **index.html**로 지정합니다. 이 페이지는 이 프로젝트의 실시간 대시보드를 포함하며, DashHub에서 정보를 받은 다음 D3.js를 사용하여 그래프를 표시합니다.

9.  **솔루션 탐색기**에서 **index.html**을 마우스 오른쪽 단추로 클릭하고 **시작 페이지로 설정**을 선택합니다.

10. **index.html** 파일의 코드를 다음과 같이 바꿉니다.

        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head>
            <title>Dashboard</title>
            <style>

                .x.axis line {
                    shape-rendering: auto;
                }

                .line {
                    fill: none;
                    stroke-width: 1.5px;
                }

            </style>
            <!--Script references. -->
            <!--Reference the jQuery library. -->
            <script src="Scripts/jquery-1.10.2.min.js"></script>
            <!--Reference the SignalR library. -->
            <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
            <!--Reference the autogenerated SignalR hub script. -->
            <script src="signalr/hubs"></script>
            <!--Reference d3.js.-->
            <script src="http://d3js.org/d3.v3.min.js"></script>
        </head>
        <body>
            <script>
                $(function () {
                    //Huge thanks to Mike Bostok for his Path Transitions article - http://bost.ocks.org/mike/path/
                    var n = 243,                                 //number of x coordinates in the graph
                    duration = 750,                          //duration for transitions
                    deviceValue=[0,0,0,0,0,0,0,0,0,0],       //temp holding for each device value
                    now = new Date(Date.now() - duration),   //Now
                    //fill an array of arrays with dummy data to start the chart
                    //each item in the top-level array is a line
                    //each item in the line arrays represents the X coordinate across a graph
                    //The 'value' within each line array represents the Y coordinate for that point
                    data = [                                 
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; })
                    ];

                    //Color scale for 10 items
                    var color = d3.scale.category10();
                    //The domain for color (the device IDs)
                    var devices = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
                    //This will auto-generate colors for this range of IDs
                    color.domain(devices);

                    //set margins and figure out width/height
                    var margin = {top: 6, right: 0, bottom: 20, left: 40},
                        width = 960 - margin.right,
                        height = 240 - margin.top - margin.bottom;

                    //the time scale for the X axis
                    var x = d3.time.scale()
                        .domain([now - (n - 2) * duration, now - duration])
                        .range([0, width]);

                    //the numerical scale for the Y axis
                    var y = d3.scale.linear()
                        .domain([100, 0])
                        .range([0, height]);

                    //The line, which is really just a
                    //couple functions that we can pass data to
                    //in order to get back x/y coords.
                    var line = d3.svg.line()
                        .interpolate("basis")
                        .x(function (d, i) { return x(now - (n - 1 - i) * duration); })
                        .y(function (d, i) { return y(d.value); });

                    //Find the HTML body element and add a child SVG element
                    var svg = d3.select("body").append("svg")
                        .attr("width", width + margin.left + margin.right)
                        .attr("height", height + margin.top + margin.bottom)
                      .append("g")
                        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

                    //Define a clipping path, because we need to clip
                    //the graph to render only the bits we want to see
                    //as it moves
                    svg.append("defs").append("clipPath")
                        .attr("id", "clip")
                      .append("rect")
                        .attr("width", width)
                        .attr("height", height);

                    //Append the x axis
                    var axis = svg.append("g")
                        .attr("class", "x axis")
                        .attr("transform", "translate(0," + height + ")")
                        .call(x.axis = d3.svg.axis().scale(x).orient("bottom"));

                    //append the y axis
                    var yaxis = svg.append("g")
                        .attr("class", "y axis")
                        .call(y.axis = d3.svg.axis().scale(y).orient("left").ticks(5));

                    //append the clipping path
                    var linegroup = svg.append("g")
                      .attr("clip-path", "url(#clip)");

                    //magic. Select all paths with a class of .line
                    //if they don't exist, make them.
                    //use the points in the line object to define
                    //the paths
                    //set the color to the cooresponding auto-generated coclor
                    var path = linegroup.selectAll(".line")
                      .data(data)
                      .enter().append("path")
                      .attr("class", "line")
                      .attr("d", line)
                      .style("stroke", function (d, i) { return color(i); });

                    //We need to transition the graph after all
                    //lines have been updated. There's no
                    //built-in for this, so this function
                    //does reference counting on end events
                    //for each line, then applies whatever
                    //callback when all are finished.
                    function endall(transition, callback) {
                        var n = 0;
                        transition
                            .each(function () { ++n; })
                            .each("end", function () { if (!--n) callback.apply(this, arguments); });
                    }

                    //wire up the SignalR client and listen for messages
                    var chat = $.connection.dashHub;
                    chat.client.broadcastMessage = function (message) {
                        //parse the JSON data
                        var incomingData = JSON.parse(message);
                        //stuff it in the global array slot for the device ID
                        deviceValue[incomingData.device] = incomingData.temperature;

                    };
                    //start listening
                    $.connection.hub.start();
                    //tick for D3 graphics
                    tick();

                    function tick() {
                        // update the domains
                        now = new Date();
                        x.domain([now - (n - 2) * duration, now - duration]);

                        //push the (presumably) fresh data deviceValue array onto
                        //the arrays that define the lines.
                        for (i = 0; i < 10; i++) {
                            data[i].push({ value: deviceValue[i] });
                            //data[1].push({ value: maxValue });
                        }
                        //slide the x-axis left
                        axis.transition()
                            .duration(duration)
                            .ease("linear")
                            .call(x.axis);

                        //Update the paths based on the updated line data
                        //and slide left
                        path
                            .attr("d", line)
                            .attr("transform", null)
                        .transition()
                            .duration(duration)
                            .ease("linear")
                            .attr("transform", "translate(" + x(now - (n - 1) * duration) + ",0)")
                            .call(endall, tick);

                        // pop the old data point off the front
                        // of the arrays
                        for (var i = 0; i < data.length; i++) {
                            data[i].shift();
                        };
                    };
                 })()
                </script>
            </body>
        </html>

    > [AZURE.NOTE] 패키지 관리자가 SignalR 스크립트의 최신 버전을 설치할 수 있습니다. 아래의 스크립트 참조가 프로젝트의 스크립트 파일 버전에 해당하는지 확인하세요. 허브를 추가하는 대신 NuGet을 사용하여 SignalR을 추가한 경우에는 버전이 다릅니다.

11. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 | HTML 페이지**를 클릭합니다. 새 페이지의 이름을 **test.html**로 지정합니다. 이 페이지를 사용하여 메시지를 보내고 받아 DashHub와 대시보드를 테스트할 수 있습니다.

12. **test.html** 파일의 코드를 다음과 같이 바꿉니다.

        <!DOCTYPE html>
        <html>
        <head>
            <title>Test</title>
            <style type="text/css">
                .container {
                    background-color: #99CCFF;
                    border: thick solid #808080;
                    padding: 20px;
                    margin: 20px;
                }
            </style>
        </head>
        <body>
            <div class="container">
                <input type="text" id="message" />
                <input type="button" id="sendmessage" value="Send" />
                <input type="hidden" id="displayname" />
                <ul id="discussion"></ul>
            </div>
            <!--Script references. -->
            <!--Reference the jQuery library. -->
            <script src="Scripts/jquery-1.10.2.min.js"></script>
            <!--Reference the SignalR library. -->
            <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
            <!--Reference the autogenerated SignalR hub script. -->
            <script src="signalr/hubs"></script>
            <!--Add script to update the page and send messages.-->
            <script type="text/javascript">
                $(function () {
                    // Declare a proxy to reference the hub.
                    var chat = $.connection.dashHub;
                    // Create a function that the hub can call to broadcast messages.
                    chat.client.broadcastMessage = function (message) {
                        // Html encode display the message.
                        var encodedMsg = $('<div />').text(message).html();
                        // Add the message to the page.
                        $('#discussion').append('<li>' + encodedMsg + '</li>');
                    };
                    // Set initial focus to message input box.
                    $('#message').focus();
                    // Start the connection.
                    $.connection.hub.start().done(function () {
                        $('#sendmessage').click(function () {
                            // Call the Send method on the hub.
                            chat.server.send($('#message').val());
                            // Clear text box and reset focus for next comment.
                            $('#message').val('').focus();
                        });
                    });
                });
            </script>
        </body>
        </html>

13. 프로젝트에 대해 **모두 저장**을 수행합니다.

14. **솔루션 탐색기**에서 **Dashboard** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 이 프로젝트용으로 만든 웹 사이트를 선택하고 **게시**를 클릭합니다.

15. 사이트가 게시되면 웹 페이지가 열리고 이동하는 타임라인이 표시됩니다.

### 대시보드 테스트

1.  SignalR이 작동하며 SignalR로 전송된 데이터에 대해 대시보드에 그래프 선이 표시되는지 확인하려면 새 웹 브라우저 창을 열어 이 웹 사이트의 **test.html** 페이지를 표시합니다. 예를 들면 **http://mydashboard.azurewebsites.net/test.html**로 이동합니다.

2.  대시보드에는 **장치 ID** 및 **온도** 값이 포함된 JSON 형식 데이터가 필요합니다. 예를 들면 **{"device":0, "temperature":80}**과 같습니다. 대시보드가 다른 페이지에서 열려 있는 상태로 0~9 사이의 장치 ID를 사용하여 **test.html** 페이지에서 테스트 값을 입력합니다. 각 장치 ID의 선은 서로 다른 색으로 그려집니다.

## 이벤트 허브 구성

이벤트 허브는 센서에서 메시지(이벤트)를 받는 데 사용됩니다. 다음 단계에 따라 새 이벤트 허브를 만듭니다.

1.  [Azure 포털][Azure 포털]에서 **새로 만들기 | 서비스 버스 | 이벤트 허브 | 사용자 지정 만들기**를 선택합니다.

2.  **새 이벤트 허브 추가** 대화 상자에서 **이벤트 허브 이름**을 입력하고 허브를 만들 **하위 지역**을 선택한 다음 새 네임스페이스를 만들거나 기존 네임스페이스를 선택합니다. 마지막으로 **화살표**를 클릭합니다.

3.  **이벤트 허브 구성** 대화 상자에서 **파티션 개수** 및 **메시지 보존** 값을 입력합니다. 이 예에서는 파티션 개수로 10을, 메시지 보존으로는 1을 사용합니다.

4.  이벤트 허브가 만들어지면 네임스페이스를 선택하고 **이벤트 허브**를 선택합니다. 그리고 마지막으로 앞에서 만든 이벤트 허브를 선택합니다.

5.  **구성**을 선택하고 다음 정보를 사용하여 새 액세스 정책 두 개를 만듭니다.

    | 이름    | 권한   |
    |---------|--------|
    | devices | 보내기 |
    | storm   | 수신   |

    권한을 만든 후 페이지 아래쪽의 **저장** 아이콘을 선택합니다. 그러면 이 허브로 메시지를 보내고 이 허브에서 메시지를 읽는 데 사용할 공유 액세스 정책이 만들어집니다.

6.  정책을 저장한 후 페이지 아래쪽의 **공유 액세스 키 생성기**를 사용하여 **devices** 및 **storm** 정책의 키를 검색합니다. 그런 다음 나중에 사용할 수 있도록 키를 저장합니다.

### 이벤트 허브에 메시지 보내기

누구나 쉽게 사용할 수 있는 표준 센서 집합은 없으므로 .NET 응용 프로그램을 사용하여 난수를 생성합니다. 아래의 단계에 따라 작성하는 .NET 응용 프로그램은 키를 눌러 중지할 때까지 1초마다 10개 장치에 대해 이벤트를 생성합니다.

1.  Visual Studio에서 새 **Windows 데스크톱** 프로젝트를 만들고 **콘솔 응용 프로그램** 프로젝트 템플릿을 선택합니다. 프로젝트의 이름을 **SendEvents**로 지정한 다음 **확인**을 클릭합니다.

2.  **솔루션 탐색기**에서 **SendEvents**를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

3.  **NuGet 패키지 관리**에서 다음 패키지를 검색하여 설치합니다.

    -   **Microsoft Azure 서비스 버스**
    -   **JSON.Net**

    패키지를 설치한 후 패키지 관리자를 **닫습니다**.

4.  **Program.cs**의 내용을 다음과 같이 바꿉니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using Newtonsoft.Json;
        using Microsoft.ServiceBus;
        using System.Threading;

        namespace SendEvents
        {
            class Program
            {

                static int numberOfDevices = 10;
                static string eventHubName = "temperature";
                static string eventHubNamespace = "namespace";
                static string sharedAccessPolicyName = "devices";
                static string sharedAccessPolicyKey = "key for devices policy";

                static void Main(string[] args)
                {
                    var settings = new MessagingFactorySettings()
                    {
                        TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(sharedAccessPolicyName, sharedAccessPolicyKey),
                        TransportType = TransportType.Amqp
                    };
                    var factory = MessagingFactory.Create(
                         ServiceBusEnvironment.CreateServiceUri("sb", eventHubNamespace, ""), settings);

                    EventHubClient client = factory.CreateEventHubClient(eventHubName);

                    try
                    {

                        List<Task> tasks = new List<Task>();
                        // Send messages to Event Hub
                        Console.WriteLine("Sending messages to Event Hub {0}", client.Path);
                        Random random = new Random();
                        //for (int i = 0; i < numberOfMessages; ++i)
                        while(!Console.KeyAvailable)
                        {
                            // One event per device
                            for(int devices = 0; devices < numberOfDevices; devices++)
                            {
                                // Create the device/temperature metric
                                Event info = new Event() { 
                                    TimeStamp = DateTime.UtcNow,
                                    DeviceId = random.Next(numberOfDevices),
                                    Temperature = random.Next(100)
                                };
                                // Serialize to JSON
                                var serializedString = JsonConvert.SerializeObject(info);
                                Console.WriteLine(serializedString);
                                EventData data = new EventData(Encoding.UTF8.GetBytes(serializedString))
                                {
                                    PartitionKey = info.DeviceId.ToString()
                                };

                                // Send the metric to Event Hub
                                tasks.Add(client.SendAsync(data));
                            }
                            // Sleep a second
                            Thread.Sleep(1000);
                        };

                        Task.WaitAll(tasks.ToArray());
                    }
                    catch (Exception exp)
                    {
                        Console.WriteLine("Error on send: " + exp.Message);
                    }

                }
            }
        }

    이 시점에서 Event 클래스를 참조하는 줄에 대한 경고가 표시됩니다. 지금은 해당 경고를 무시합니다.

5.  **Program.cs** 파일의 시작 부분에서 다음 변수의 값을 Azure 관리 포털의 이벤트 허브에서 검색된 해당 값으로 설정합니다.

    | 설정할 항목            | 설정할 값                                         |
    |------------------------|---------------------------------------------------|
    | eventHubName           | 이벤트 허브의 이름(예: **temperature**)           |
    | eventHubNamespace      | 이벤트 허브의 네임스페이스(예: **sensors-ns**)    |
    | sharedAccessPolicyName | 보내기 권한을 사용하여 만든 정책(예: **devices**) |
    | sharedAccessPolicyKey  | 보내기 권한을 사용하는 정책의 키                  |

6.  **솔루션 탐색기**에서 **SendEvents**을 마우스 오른쪽 단추로 클릭하고 **추가 | 클래스**를 클릭합니다. 새 클래스의 이름을 **Event.cs**로 지정합니다. 이 클래스는 이벤트 허브로 전송되는 메시지를 설명합니다.

7.  **Event.cs**의 내용을 다음과 같이 바꿉니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Runtime.Serialization;
        using System.Text;
        using System.Threading.Tasks;

        namespace SendEvents
        {
            [DataContract]
            public class Event
            {
                [DataMember]
                public DateTime TimeStamp { get; set; }
                [DataMember]
                public int DeviceId { get; set; }
                [DataMember]
                public int Temperature { get; set; }
            }
        }

    이 클래스는 전송하는 데이터를 설명합니다(TimeStamp, DeviceID 및 Temperature 값).

8.  **모두 저장**을 수행한 다음 응용 프로그램을 실행하여 이벤트 허브에 메시지를 채웁니다.

## Azure 가상 네트워크 만들기

Storm 클러스터에서 실행되는 토폴로지가 HBase와 직접 통신하려면 두 서버를 모두 Azure 가상 네트워크에 프로비전해야 합니다.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.

2.  페이지 아래쪽에서 **+새로 만들기**, **네트워크 서비스**, **가상 네트워크**, **빨리 만들기**를 차례로 클릭합니다.

3.  다음 값을 입력하거나 선택합니다.

    -   **이름**: 가상 네트워크의 이름입니다.
    -   **주소 공간**: 클러스터의 모든 노드에 대한 주소를 제공하기에 충분히 큰 가상 네트워크의 주소 공간을 선택합니다. 그렇지 않으면 프로비전에 실패합니다.
    -   **최대 VM 수**: 최대 VM 수 중 하나를 선택합니다.
    -   **위치**: 위치는 만들 HBase 클러스터와 동일해야 합니다.
    -   **DNS 서버**: 이 문서에서는 Azure에서 제공하는 내부 DNS 서버를 사용하므로 **없음**을 선택하면 됩니다. 사용자 지정 DNS 서버가 포함된 고급 네트워킹 구성도 지원됩니다. 자세한 지침은 <http://msdn.microsoft.com/library/azure/jj156088.aspx>를 참조하세요.

4.  **가상 네트워크 만들기**를 클릭합니다. 새 가상 네트워크 이름이 목록에 나타납니다. 상태 열에 **생성됨**이 표시될 때까지 기다립니다.

5.  기본 창에서 방금 만든 가상 네트워크를 클릭합니다.

6.  페이지 맨 위에서 **대시보드**를 클릭합니다.

7.  **간략 상태** 아래에서 **가상 네트워크 ID**를 적어 둡니다. 이 ID는 Storm 및 HBase 클러스터를 프로비전할 때 필요합니다.

8.  페이지 맨 위에서 **구성**을 클릭합니다.

9.  페이지 맨 아래에서 기본 서브넷 이름은 **Subnet-1**입니다. **서브넷 추가** 단추를 사용하여 **Subnet-2**를 추가합니다. 이러한 서브넷에 Storm 및 HBase 클러스터가 포함됩니다.

    > [WACOM.NOTE] 이 문서에서는 노드가 하나뿐인 클러스터를 사용합니다. 다중 노드 클러스터를 만드는 경우에는 클러스터에 사용할 서브넷의 **CIDR(주소 수)**을 확인해야 합니다. 주소 수는 작업자 노드 수에 7을 더한 값보다 커야 합니다(게이트웨이: 2, 헤드 노드: 2, Zookeeper: 3). 예를 들어 10개 노드 HBase 클러스터가 필요한 경우 서브넷의 주소 수는 17(10+7)보다 커야 합니다. 그렇지 않으면 배포에 실패합니다.
    >
    > 각 클러스터에 단일 서브넷을 지정하는 것이 좋습니다.

10. 페이지 아래쪽에서 **저장**을 클릭합니다.

## HDInsight Storm 클러스터 만들기

1.  [Azure 관리 포털][azureportal]에 로그인합니다.

2.  왼쪽에서 **HDInsight**를 클릭하고 페이지 왼쪽 아래에서 **+새로 만들기**를 클릭합니다.

3.  두 번째 열에서 HDInsight 아이콘을 클릭하고 **사용자 지정**을 선택합니다.

4.  **클러스터 세부 정보** 페이지에서 새 클러스터의 이름을 입력하고 **클러스터 유형**으로 **Storm**을 선택합니다. 화살표를 선택하여 계속합니다.

5.  이 클러스터에 사용할 **데이터 노드**의 수로 1을 입력합니다. **하위 지역/가상 네트워크**에서 앞에서 만든 Azure 가상 네트워크를 선택합니다. **가상 네트워크 서브넷**으로는 **Subnet-2**을 선택합니다.

    > [WACOM.NOTE] 이 문서에서 사용하는 클러스터의 비용을 최소화하려면 **클러스터 크기**를 1로 줄이고 클러스터를 다 사용한 후에는 삭제합니다.

6.  관리자의 **사용자 이름** 및 **암호**를 입력하고 화살표를 선택하여 계속합니다.

7.  **저장소 계정**에서는 **새 저장소 만들기** 또는 기존 저장소 계정을 선택합니다. 그런 다음 사용할 **계정 이름** 및 **기본 컨테이너**를 선택하거나 입력합니다. 그리고 왼쪽 아래에 있는 확인 아이콘을 클릭하여 Storm 클러스터를 만듭니다.

## HDInsight HBase 클러스터 만들기

1.  [Azure 관리 포털][azureportal]에 로그인합니다.

2.  왼쪽에서 **HDInsight**를 클릭하고 페이지 왼쪽 아래에서 **+새로 만들기**를 클릭합니다.

3.  두 번째 열에서 HDInsight 아이콘을 클릭하고 **사용자 지정**을 선택합니다.

4.  **클러스터 세부 정보** 페이지에서 새 클러스터의 이름을 입력하고 **클러스터 유형**으로 **HBase**를 선택합니다. 화살표를 선택하여 계속합니다.

5.  이 클러스터에 사용할 **데이터 노드**의 수로 1을 입력합니다. **하위 지역/가상 네트워크**에서 앞에서 만든 Azure 가상 네트워크를 선택합니다. **가상 네트워크 서브넷**으로는 **Subnet-1**을 선택합니다.

    > [WACOM.NOTE] 이 문서에서 사용하는 클러스터의 비용을 최소화하려면 **클러스터 크기**를 1로 줄이고 클러스터를 다 사용한 후에는 삭제합니다.

6.  관리자의 **사용자 이름** 및 **암호**를 입력하고 화살표를 선택하여 계속합니다.

7.  **저장소 계정**에서는 **새 저장소 만들기** 또는 기존 저장소 계정을 선택합니다. 그런 다음 사용할 **계정 이름** 및 **기본 컨테이너**를 선택하거나 입력합니다. 그리고 왼쪽 아래에 있는 확인 아이콘을 클릭하여 Storm 클러스터를 만듭니다.

    > [WACOM.NOTE] Storm 클러스터에 사용했던 것과는 다른 컨테이너를 사용해야 합니다.

### 원격 데스크톱 사용

이 자습서에서는 원격 데스크톱을 사용하여 Storm 및 HBase 클러스터에 액세스해야 합니다. 다음 단계에 따라 Storm 및 HBase에 대해 원격 데스크톱을 사용하도록 설정합니다.

1.  [Azure 관리 포털][azureportal]에 로그인합니다.

2.  왼쪽에서 **HDInsight**를 선택하고 목록에서 Storm 클러스터를 선택합니다. 마지막으로 페이지 위쪽에서 **구성**을 선택합니다.

3.  페이지 아래쪽에서 **원격 사용**을 선택합니다. 메시지가 표시되면 사용자 이름, 암호 및 원격 데스크톱 액세스 만료 날짜를 입력합니다. 그런 후에 확인 표시를 클릭하여 원격 데스크톱을 사용하도록 설정합니다.

원격 데스크톱을 사용하도록 설정한 후 페이지 아래쪽에서 **연결**을 선택할 수 있습니다. 그런 다음 메시지에 따라 클러스터에 연결합니다.

### HBase DNS 접미사 검색

Storm 클러스터에서 HBase에 쓰려면 HBase 클러스터의 FQDN(정규화된 도메인 이름)을 사용해야 합니다. 다음 단계에 따라 이 정보를 검색합니다.

1.  원격 데스크톱을 사용하여 HBase 클러스터에 연결

2.  클러스터에 연결한 후 Hadoop 명령줄을 열고 **ipconfig** 명령을 실행하여 DNS 접미사를 가져옵니다. **연결별 DNS 접미사**에 접미사 값이 포함되어 있습니다. 예를 들면 **mycluster.b4.internal.cloudapp.net**과 같습니다. 이 정보를 저장합니다.

## Storm 토폴로지 개발

> [WACOM.NOTE] 이 섹션의 단계는 로컬 개발 환경에서 수행해야 합니다.

### 외부 종속성 다운로드 및 빌드

이 프로젝트에서 사용되는 종속성 중 다수는 개별적으로 다운로드 및 빌드한 다음 개발 환경의 로컬 Maven 리포지토리에 설치해야 합니다. 이 섹션에서는 다음 항목을 다운로드 및 설치합니다.

-   이벤트 허브에서 메시지를 읽는 이벤트 허브 Spout

-   SignalR Java 클라이언트 SDK

#### 이벤트 허브 Spout 다운로드 및 빌드

이벤트 허브에서 데이터를 받기 위해 **eventhubs-storm-spout**를 사용합니다.

1.  원격 데스크톱을 사용하여 Storm 클러스터에 연결한 다음 **%STORM\_HOME%\\examples\\eventhubspout\\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 파일을 로컬 개발 환경에 복사합니다. 이 파일에 **events-storm-spout**가 포함되어 있습니다.

2.  다음 명령을 사용하여 패키지를 로컬 Maven 저장소에 설치합니다. 그러면 이후 단계에서 해당 패키지를 Storm 프로젝트에 참조로 쉽게 추가할 수 있습니다.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

#### SignalR 클라이언트 다운로드 및 빌드

ASP.NET 대시보드에 메시지를 보내려면 [SignalR Client SDK for Java][SignalR Client SDK for Java]를 사용합니다.

1.  명령 프롬프트를 엽니다.

2.  SignalR Client SDK 프로젝트를 다운로드 및 저장할 디렉터리로 변경합니다.

3.  다음 명령을 사용하여 GitHub에서 프로젝트를 다운로드합니다.

    git clone https://github.com/SignalR/java-client

4.  **java-client\\signalr-client-sdk** 디렉터리로 변경한 후 다음 명령을 사용하여 프로젝트를 JAR 파일로 컴파일합니다.

        cd java-client\signalr-client-sdk
        mvn package

    > [WACOM.NOTE] **gson** 종속성을 다운로드할 수 없다는 오류가 표시되면 **java-client\\signalr-client-sdk\\pom.xml** 파일에서 다음 줄을 제거합니다.
    >
    > ``` <repositories>
    > <repository>
    > <id>central</id>
    > <name>Central</name>
    > <url>http://maven.eclipse.org/build</url>
    > </repository>
    > </repositories>
    > ```
    >
    > 이 줄을 제거하면 Maven이 중앙 리포지토리에서 파일을 끌어옵니다(기본 동작). Maven이 리포지토리를 다시 시도하도록 강제 지정하려면 `-U` 명령을 사용합니다. 예를 들면 `mvn package -U`와 같습니다.

5.  다음 명령을 사용하여 패키지를 로컬 Maven 저장소에 설치합니다. 그러면 이후 단계에서 해당 패키지를 Storm 프로젝트에 참조로 쉽게 추가할 수 있습니다.

        mvn install:install-file -Dfile=target\signalr-client-sdk-1.0.jar -DgroupId=microsoft.aspnet.signalr -DartifactId=signalr-client-sdk -Dversion=1.0 -Dpackaging=jar

### Storm 토폴로지 프로젝트 스캐폴드

이벤트 허브 Spout 및 SignalR 클라이언트를 로컬 리포지토리에 설치한 후에는 Maven을 사용하여 Storm 토폴로지 프로젝트용 스캐폴딩을 만듭니다.

1.  명령 프롬프트, Bash 세션, 터미널 세션 또는 시스템에서 명령을 입력하는 데 사용하는 도구를 엽니다.

2.  디렉터리를 이 프로젝트를 만들 위치로 변경합니다. 예를 들어 모든 코드 프로젝트를 저장하는 디렉터리가 있으면 해당 디렉터리로 변경합니다.

3.  다음 Maven 명령을 사용하여 응용 프로그램에 대한 기본 스캐폴딩을 만듭니다.

        mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.examples -DartifactId=TemperatureMonitor -DinteractiveMode=false

    이 명령은 다음 작업을 수행합니다.

    -   지정한 *artifactId*를 사용하여 새 디렉터리를 만듭니다. 이 예제에서 artifactId는 **Temperature**입니다.
    -   이 프로젝트의 Maven 정보를 포함하는 **pom.xml** 파일을 만듭니다.
    -   일부 기본 코드와 테스트를 포함하는 **src** 디렉터리 구조를 만듭니다.

### 종속성 및 플러그 인 추가

다음으로 이 프로젝트의 종속성과 빌드 및 패키징 시 사용할 Maven 플러그 인을 참조하도록 **pom.xml**을 수정합니다.

1.  텍스트 편집기를 사용하여 **pom.xml** 파일을 열고 다음 코드를 **\<dependencies\>** 섹션에 추가합니다. 섹션 끝부분의 junit에 대한 종속성 뒤에 이 코드를 추가할 수 있습니다.

        <dependency>
          <groupId>org.apache.storm</groupId>
          <artifactId>storm-core</artifactId>
          <version>0.9.2-incubating</version>
          <!-- keep storm out of the jar-with-dependencies -->
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>microsoft.aspnet.signalr</groupId>
          <artifactId>signalr-client-sdk</artifactId>
          <version>1.0</version>
        </dependency>
        <dependency>
          <groupId>com.microsoft.eventhubs</groupId>
          <artifactId>eventhubs-storm-spout</artifactId>
          <version>0.9</version>
        </dependency>
        <dependency>
          <groupId>com.google.code.gson</groupId>
          <artifactId>gson</artifactId>
          <version>2.2.2</version>
        </dependency>
        <dependency>
          <groupId>com.github.ptgoetz</groupId>
          <artifactId>storm-hbase</artifactId>
          <version>0.1.2</version>
        </dependency>
        <dependency>
          <groupId>com.netflix.curator</groupId>
          <artifactId>curator-framework</artifactId>
          <version>1.3.3</version>
          <exclusions>
            <exclusion>
              <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
              </exclusion>
            <exclusion>
              <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
          </exclusions>
          <scope>provided</scope>
        </dependency>

    그러면 다음에 대한 종속성이 추가됩니다.

    -   eventhubs-storm-spout - 이벤트 허브 Spout입니다.
    -   signalr-client-sdk - SignalR 클라이언트입니다.
    -   gson - SignalR 클라이언트의 종속성으로, SignalR에 쓸 때 JSON을 만드는 데도 사용됩니다.
    -   storm-core - Storm 응용 프로그램의 핵심 기능을 제공합니다.
    -   slf4j - 로깅 기능을 제공하며 eventhubs-storm-spout에서 사용됩니다.
    -   curator-framework - eventhubs-storm-spout에서 사용됩니다.
    -   storm-core - Storm의 핵심 클래스입니다.
    -   storm-hbase - HBase에 쓰기를 허용하는 클래스입니다.

    > [WACOM.NOTE] 일부 종속성은 범위가 **provided**로 표시됩니다. 이는 해당 종속성을 Maven 리포지토리에서 다운로드해야 하며 로컬에서 응용 프로그램을 빌드 및 테스트하는 데 사용해야 하지만, 종속성을 런타임 환경에서도 사용할 수 있으며 컴파일하여 이 프로젝트에 의해 작성되는 JAR에 포함하지 않아도 됨을 나타냅니다.

2.  **pom.xml** 파일 끝부분의 **\</project\>** 항목 바로 앞에 다음 코드를 추가합니다.

          <build>
            <plugins>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                  <source>1.7</source>
                  <target>1.7</target>
                </configuration>
              </plugin>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                  <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                  </transformers>
                </configuration>
                <executions>
                  <execution>
                    <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                  </execution>
                </executions>
              </plugin>
              <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.2.1</version>
                <executions>
                  <execution>
                  <goals>
                    <goal>exec</goal>
                  </goals>
                  </execution>
                </executions>
                <configuration>
                  <executable>java</executable>
                  <includeProjectDependencies>true</includeProjectDependencies>
                  <includePluginDependencies>false</includePluginDependencies>
                  <classpathScope>compile</classpathScope>
                  <mainClass>${storm.topology}</mainClass>
                </configuration>
              </plugin>
            </plugins>
            <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>Config.properties</include>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          </build>

    이 코드는 프로젝트 빌드 시 Maven에서 다음 작업을 수행하도록 명령합니다.

    -   **/conf/Config.properties** 리소스 파일을 포함합니다. 이 파일은 나중에 만들어지며 Azure 이벤트 허브에 연결하기 위한 구성 정보를 포함합니다.
    -   **/conf/hbase-site.xml** 리소스 파일을 포함합니다. 이 파일은 나중에 만들어지며 HBase에 연결하는 방법에 대한 정보를 포함합니다.
    -   **maven-compiler-plugin**을 사용하여 응용 프로그램을 컴파일합니다.
    -   **maven-shade-plugin**을 사용하여 이 프로젝트 및 필수 종속성을 포함하는 uberjar 또는 FAT jar을 빌드합니다.
    -   **exec-maven-plugin**을 사용합니다. 그러면 Hadoop 클러스터 없이도 로컬에서 응용 프로그램을 실행할 수 있습니다.

### 구성 파일 추가

**eventhubs-storm-spout**는 **Config.properties** 파일에서 구성 정보를 읽어 연결 대상 이벤트 허브를 확인합니다. 클러스터에서 토폴로지를 시작할 때 구성 파일을 지정할 수도 있지만 프로젝트에 구성 파일을 포함하면 알려진 기본 구성이 제공됩니다.

1.  **Temperature** 디렉터리에서 **conf**라는 새 디렉터리를 만듭니다.

2.  **conf** 디렉터리에서 새 파일 두 개를 만듭니다.

    -   **Config.properties** - 이벤트 허브의 설정을 포함합니다.
    -   **hbase-site.xml** - HBase 연결을 위한 설정을 포함합니다.

3.  **Config.properties** 파일의 내용으로 다음을 사용합니다.

        eventhubspout.username = storm

        eventhubspout.password = <the key of the 'storm' policy>

        eventhubspout.namespace = <the event hub namespace>

        eventhubspout.entitypath = temperature

        eventhubspout.partitions.count = <the number of partitions for the event hub>

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 1024

    **password**는 앞에서 이벤트 허브에 대해 만든 **storm** 정책의 키로 바꿉니다. **namespace**는 이벤트 허브의 네임스페이스로 바꿉니다.

4.  **hbase-site.xml** 파일의 내용으로 다음을 사용합니다.

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
         * Copyright 2010 The Apache Software Foundation
         *
         * Licensed to the Apache Software Foundation (ASF) under one
         * or more contributor license agreements.  See the NOTICE file
         * distributed with this work for additional information
         * regarding copyright ownership.  The ASF licenses this file
         * to you under the Apache License, Version 2.0 (the
         * "License"); you may not use this file except in compliance
         * with the License.  You may obtain a copy of the License at
         *
         *     http://www.apache.org/licenses/LICENSE-2.0
         *
         * Unless required by applicable law or agreed to in writing, software
         * distributed under the License is distributed on an "AS IS" BASIS,
         * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
         * See the License for the specific language governing permissions and
         * limitations under the License.
         */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

5.  **hbase-site.xml** 파일에서 ZooKeeper 항목의 **suffix** 값을 앞에서 HBase에 대해 검색한 DNS 접미사로 바꿉니다. 예를 들면 **zookeeper0.mycluster.b4.internal.cloudapp.net, zookeeper1.mycluster.b4.internal.cloudapp.net, zookeeper2.mycluster.b4.internal.cloudapp.net**과 같습니다.

6.  파일을 저장합니다.

### 도우미 추가

JSON에서의/으로의 직렬화를 지원하려면 개체 구조를 정의하는 몇 가지 도우미 클래스가 필요합니다.

1.  **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples** 디렉터리에서 **helpers**라는 새 디렉터리를 만듭니다.

2.  **helpers** 디렉터리에서 새 파일 두 개를 만듭니다.

    -   **EventHubMessage.java** - 이벤트 허브 메시지 형식을 정의합니다.

    -   **SignalRMessage.java** - SignalR로 전송되는 메시지의 형식을 정의합니다.

3.  **EventHubMessage.java** 파일의 내용으로 다음을 사용합니다.

        package com.microsoft.examples;

        public class EventHubMessage {
          String TimeStamp;
          int DeviceId;
          int Temperature;
        }

4.  **SignalRMessage.java** 파일의 내용으로 다음을 사용합니다.

        package com.microsoft.examples;

        public class SignalRMessage {
          int device;
          int temperature;
        }

5.  이 두 파일을 저장하고 닫습니다.

### Bolt 추가

Bolt는 토폴로지에서 기본 처리를 수행합니다. 이 토폴로지에는 Bolt가 3개 있습니다. 그 중 HBase Bolt는 하나이며, 프로젝트를 빌드할 때 자동으로 다운로드됩니다.

1.  **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples** 디렉터리에서 **bolts**라는 새 디렉터리를 만듭니다.

2.  **bolts** 디렉터리에서 새 파일 두 개를 만듭니다.

    -   **ParserBolt.java** - 이벤트 허브에서 들어오는 메시지를 개별 필드로 구문 분석한 다음 두 개의 스트림을 내보냅니다.
    -   **DashboardBolt.java** - SignalR을 통해 정보를 웹 대시보드에 기록합니다.

3.  **ParserBolt.java** 파일의 내용으로 다음을 사용합니다.

        package com.microsoft.examples;

        import backtype.storm.topology.base.BaseBasicBolt;
        import backtype.storm.topology.BasicOutputCollector;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.tuple.Tuple;
        import backtype.storm.tuple.Fields;
        import backtype.storm.tuple.Values;

        import com.google.gson.Gson;
        import com.google.gson.GsonBuilder;

        public class ParserBolt extends BaseBasicBolt {

          //Declare output fields & streams
          //hbasestream is all fields, and goes to hbase
          //dashstream is just the device and temperature, and goes to the dashboard
          @Override
          public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declareStream("hbasestream", new Fields("timestamp", "deviceid", "temperature"));
            declarer.declareStream("dashstream", new Fields("deviceid", "temperature"));
          }

          //Process tuples
          @Override
          public void execute(Tuple tuple, BasicOutputCollector collector) {
            Gson gson = new Gson();
            //Should only be one tuple, which is the JSON message from the spout
            String value = tuple.getString(0);

            //Convert it from JSON to an object
            EventHubMessage evMessage = gson.fromJson(value, EventHubMessage.class);

            //Pull out the values and emit as a stream
            String timestamp = evMessage.TimeStamp;
            int deviceid = evMessage.DeviceId;
            int temperature = evMessage.Temperature;
            collector.emit("hbasestream", new Values(timestamp, deviceid, temperature));
            collector.emit("dashstream", new Values(deviceid, temperature));
          }
        }

4.  **DashboardBolt.java** 파일의 내용으로 다음을 사용합니다.

        package com.microsoft.examples;

        import backtype.storm.topology.BasicOutputCollector;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseBasicBolt;
        import backtype.storm.tuple.Tuple;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.Config;
        import backtype.storm.Constants;

        import microsoft.aspnet.signalr.client.Action;
        import microsoft.aspnet.signalr.client.ErrorCallback;
        import microsoft.aspnet.signalr.client.LogLevel;
        import microsoft.aspnet.signalr.client.Logger;
        import microsoft.aspnet.signalr.client.MessageReceivedHandler;
        import microsoft.aspnet.signalr.client.hubs.HubConnection;
        import microsoft.aspnet.signalr.client.hubs.HubProxy;

        import com.google.gson.Gson;
        import com.google.gson.GsonBuilder;

        import java.util.Map;

        public class DashboardBolt extends BaseBasicBolt {
          //Connection and proxy for SignalR hub
          private HubConnection conn;
          private HubProxy proxy;

          //Declare output fields
          @Override
          public void declareOutputFields(OutputFieldsDeclarer declarer) {
            //no stream output - we talk directly to SignalR
          }

          @Override
          public void prepare(Map config, TopologyContext context) {

            // Connect to the DashHub SignalR server
            conn = new HubConnection("http://dashboard.azurewebsites.net/");
            // Create the hub proxy
            proxy = conn.createHubProxy("DashHub");
            // Subscribe to the error event
            conn.error(new ErrorCallback() {
              @Override
              public void onError(Throwable error) {
                error.printStackTrace();
              }
            });
            // Subscribe to the connected event
            conn.connected(new Runnable() {
              @Override
              public void run() {
                System.out.println("CONNECTED");
              }
            });
            // Subscribe to the closed event
            conn.closed(new Runnable() {
              @Override
              public void run() {
                System.out.println("DISCONNECTED");
              }
            });
            // Start the connection
            conn.start()
              .done(new Action<Void>() {
                @Override
                public void run(Void obj) throws Exception {
                  System.out.println("Done Connecting!");
                }
            });
          }

          //Process tuples
          @Override
          public void execute(Tuple tuple, BasicOutputCollector collector) {
            Gson gson = new Gson();
            try {
              //Get the deviceid and temperature by field name
              int deviceid = tuple.getIntegerByField("deviceid");
              int temperature = tuple.getIntegerByField("temperature");
              //Construct the SignalR message
              SignalRMessage srMessage = new SignalRMessage();
              srMessage.device = deviceid;
              srMessage.temperature = temperature;
              // send it as JSON
              proxy.invoke("send", gson.toJson(srMessage));
            } catch (Exception e) {
               // LOG.error("Bolt execute error: {}", e);
               collector.reportError(e);
            }
          }
        }

    `http://yourwebsiteaddress`를 앞에서 대시보드를 게시한 Azure 웹 사이트의 주소로 바꿉니다. 예를 들면 http://mydashboard.azurewebsites.net과 같습니다.

5.  파일을 저장하고 닫습니다.

### 토폴로지 정의

토폴로지는 토폴로지 내의 Spout와 Bolt 간 데이터 흐름 방식과 토폴로지 및 토폴로지 내 구성 요소의 병렬 처리 수준을 설명합니다.

1.  **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples** 디렉터리에서 **Temperature.java**라는 새 파일을 만듭니다.

2.  **Temperature.java** 파일을 열고 내용으로 다음을 사용합니다.

        package com.microsoft.examples;

        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import backtype.storm.tuple.Fields;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        import java.io.FileReader;
        import java.util.Properties;

        //hbase
        import org.apache.storm.hbase.bolt.mapper.SimpleHBaseMapper;
        import org.apache.storm.hbase.bolt.HBaseBolt;
        import java.util.Map;
        import java.util.HashMap;
        import backtype.storm.tuple.Fields;

        public class Temperature
        {
          protected EventHubSpoutConfig spoutConfig;
          protected int numWorkers;

          // Reads the configuration information for the Event Hub spout
          protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if(args.length > 1) {
              properties.load(new FileReader(args[1]));
            }
            else {
              properties.load(Temperature.class.getClassLoader().getResourceAsStream(
                "Config.properties"));
            }

            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties.getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties.getProperty("zookeeper.connectionstring");
            int partitionCount = Integer.parseInt(properties.getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties.getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties.getProperty("eventhub.receiver.credits"));
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: " + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
            spoutConfig = new EventHubSpoutConfig(username, password,
              namespaceName, entityPath, partitionCount, zkEndpointAddress,
              checkpointIntervalInSeconds, receiverCredits);

            //set the number of workers to be the same as partition number.
            //the idea is to have a spout and a partial count bolt co-exist in one
            //worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();

            if(args.length > 0) {
              //set topology name so that sample Trident topology can use it as stream name.
              spoutConfig.setTopologyName(args[0]);
            }
          }

          // Create the spout using the configuration
          protected EventHubSpout createEventHubSpout() {
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            return eventHubSpout;
          }

          // Build the topology
          protected StormTopology buildTopology(EventHubSpout eventHubSpout, SimpleHBaseMapper mapper) {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
            // Name the spout 'EventHubsSpout', and set it to create
            // as many as we have partition counts in the config file
            topologyBuilder.setSpout("EventHub", eventHubSpout, spoutConfig.getPartitionCount())
              .setNumTasks(spoutConfig.getPartitionCount());
            // Create the parser bolt, which subscribes to the stream from EventHub
            topologyBuilder.setBolt("Parser", new ParserBolt(), spoutConfig.getPartitionCount())
              .localOrShuffleGrouping("EventHub").setNumTasks(spoutConfig.getPartitionCount());
            // Create the dashboard bolt, which subscribes to the stream from Parser
            topologyBuilder.setBolt("Dashboard", new DashboardBolt(), spoutConfig.getPartitionCount())
              .fieldsGrouping("Parser", "dashstream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
            // Create the HBase bolt, which subscribes to the stream from Parser
            // WARNING - uncomment the following two lines when deploying
            // leave commented when testing locally
            // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
            //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
          }

          protected void submitTopology(String[] args, StormTopology topology, Config config) throws Exception {
            // Config config = new Config();
            config.setDebug(false);

            //Enable metrics
            config.registerMetricsConsumer(backtype.storm.metric.LoggingMetricsConsumer.class, 1);

            // Is this running locally, or on an HDInsight cluster?
            if (args != null && args.length > 0) {
              config.setNumWorkers(numWorkers);
              StormSubmitter.submitTopology(args[0], config, topology);
            } else {
              config.setMaxTaskParallelism(2);

              LocalCluster localCluster = new LocalCluster();
              localCluster.submitTopology("test", config, topology);

              Thread.sleep(5000000);

              localCluster.shutdown();
            }
          }

          // Loads the configuration, creates the spout, builds the topology,
          // and then submits it
          protected void runScenario(String[] args) throws Exception{
            readEHConfig(args);
            Config config = new Config();

            //hbase configuration
            Map<String, Object> hbConf = new HashMap<String, Object>();
            if(args.length > 0) {
              hbConf.put("hbase.rootdir", args[0]);
            }
            config.put("hbase.conf", hbConf);
            SimpleHBaseMapper mapper = new SimpleHBaseMapper()
                  .withRowKeyField("deviceid")
                  .withColumnFields(new Fields("timestamp", "temperature"))
                  .withColumnFamily("cf");

            EventHubSpout eventHubSpout = createEventHubSpout();
            StormTopology topology = buildTopology(eventHubSpout, mapper);
            submitTopology(args, topology, config);
          }

          public static void main(String[] args) throws Exception {
            Temperature scenario = new Temperature();
            scenario.runScenario(args);
          }
        }

    > [AZURE.NOTE] **HBaseBolt**의 줄은 주석 처리됩니다. 다음 단계에서는 토폴로지를 로컬로 실행하기 때문입니다. HBaseBolt는 HBase와 직접 통신하므로 HBaseBolt를 사용하도록 설정하면 오류가 반환됩니다. 단, DNS 서버를 사용하여 가상 네트워크를 구성했으며 로컬 컴퓨터도 가상 네트워크에 가입시킨 경우는 예외입니다.

### 로컬에서 토폴로지 테스트

개발 컴퓨터에서 파일을 컴파일하고 테스트하려면 다음 단계를 수행합니다.

1.  **SendEvent** .NET 응용 프로그램을 시작한 다음 이벤트 허브에서 이벤트를 읽을 수 있도록 이벤트 전송을 시작합니다.

2.  웹 브라우저를 열고 앞에서 Azure 웹 사이트에 배포한 웹 대시보드를 표시합니다. 그러면 토폴로지를 통과하는 값이 그려진 그래프를 확인할 수 있습니다.

3.  다음 명령을 사용하여 로컬에서 토폴로지를 시작합니다.

    mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

    그러면 토폴로지가 시작되고 이벤트 허브에서 파일을 읽은 후 Azure 웹 사이트에서 실행되는 대시보드로 파일을 보냅니다. 그리고 웹 대시보드에 선이 표시됩니다.

4.  이 작업이 수행됨을 확인한 후 Ctrl+C를 눌러 토폴로지를 중지합니다. SendEvent 앱을 중지하려면 창을 선택하고 아무 키나 누릅니다.

### HBaseBolt 사용 및 HBase 준비

1.  **Temperature.java** 파일을 열고 다음 줄에서 주석 기호(//)를 제거합니다.

        //topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
        //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

    그러면 HBase Bolt가 사용하도록 설정됩니다.

2.  **Temperature.java**를 저장합니다.

3.  원격 데스크톱을 사용하여 HBase 클러스터에 연결합니다.

4.  데스크톱에서 HDInsight 명령줄을 시작하고 다음 명령을 입력합니다.

        cd %hbase_home%
        bin\hbase shell

5.  HBase 셸에서 다음 명령을 입력하여 센서 데이터를 저장할 테이블을 만듭니다.

        create 'SensorData', 'cf'

6.  다음 명령을 입력하여 테이블에 데이터가 없음을 확인합니다.

        scan 'SensorData'

HBase 셸에서 이 프롬프트를 열어 둡니다.

## 토폴로지를 패키지하여 HDInsight에 배포

개발 환경에서 다음 단계를 수행하여 HDInsight Storm 클러스터에서 Temperature 토폴로지를 실행합니다.

1.  다음 명령을 사용하여 프로젝트에서 JAR 패키지를 만듭니다.

        mvn package

    그러면 프로젝트의 **target** 디렉터리에 **TemperatureMonitor-1.0-SNAPSHOT.jar** 파일이 만들어집니다.

2.  읽을 수 있는 이벤트가 전송되도록 로컬 개발 컴퓨터에서 **SendEvents** .NET 응용 프로그램을 시작합니다.

3.  원격 데스크톱을 사용하여 HDInsight Storm 클러스터에 연결한 다음 **TemperatureMonitor-1.0-SNAPSHOT.jar** 파일을 **c:\\apps\\dist\\storm\<version number\>** 디렉터리에 복사합니다.

4.  클러스터 바탕 화면의 **HDInsight 명령줄** 아이콘을 사용하여 새 명령 프롬프트를 열고 다음 명령을 사용하여 토폴로지를 실행합니다.

        cd %storm_home%
        bin\storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature Temperature

5.  토폴로지가 시작되고 웹 대시보드에 항목이 표시될 때까지는 몇 초 정도 걸릴 수 있습니다.

6.  항목이 대시보드에 표시되면 HBase 클러스터의 원격 데스크톱 세션으로 전환합니다.

7.  HBase 셸에서 다음 명령을 입력합니다.

        scan 'SensorData'

    이제 이 명령은 Storm 토폴로지에 의해 기록된 여러 데이터 행을 반환합니다.

8.  토폴로지를 중지하려면 Storm 클러스터가 포함된 원격 데스크톱 세션으로 이동한 다음 HDInsight 명령줄에 다음을 입력합니다.

        bin\storm kill Temperature

    몇 초 후에 토폴로지가 중지됩니다.

## 요약

지금까지 이벤트 허브에서 데이터를 읽고, HBase에서 데이터를 저장하고, SignalR 및 D3.js를 사용하여 외부 대시보드에서 Storm의 정보를 표시하는 방법에 대해 알아보았습니다.

-   Apache Storm에 대한 자세한 내용은 <https://storm.incubator.apache.org/>를 참조하세요.

-   HDInsight HBase에 대한 자세한 내용은 [HDInsight HBase 개요][HDInsight HBase 개요]를 참조하세요.

-   SignalR에 대한 자세한 내용은 [ASP.NET SignalR][ASP.NET SignalR]을 참조하세요.

-   D3.js에 대한 자세한 내용은 [D3.js - 데이터 기반 문서][D3.js - 데이터 기반 문서]를 참조하세요.

-   .NET에서 토폴로지를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Storm에 대해 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발][HDInsight에서 Storm에 대해 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발]을 참조하세요.

  [Microsoft Azure SDK for .NET]: http://azure.microsoft.com/ko-kr/downloads/archive-net-downloads/
  [Java 및 JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Maven]: http://maven.apache.org/what-is-maven.html
  [Git]: http://git-scm.com/
  [Chocolatey NuGet]: http://chocolatey.org/
  [SignalR]: http://www.asp.net/signalr/overview/getting-started/introduction-to-signalr
  [Storm Tweet Sentiment D3 Visualization]: https://github.com/P7h/StormTweetsSentimentD3Viz
  [Azure 포털]: https://manage.windowsazure.com
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [SignalR Client SDK for Java]: https://github.com/SignalR/java-client
  [HDInsight HBase 개요]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-hbase-overview/
  [ASP.NET SignalR]: http://signalr.net/
  [D3.js - 데이터 기반 문서]: http://d3js.org/
  [HDInsight에서 Storm에 대해 SCP.NET 및 C#을 사용하여 스트리밍 데이터 처리 응용 프로그램 개발]: /ko-kr/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
