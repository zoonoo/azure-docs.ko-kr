## 이벤트 허브에 메시지 보내기
이 섹션에서는 이벤트 허브로 이벤트를 보내는 Windows 콘솔 앱을 작성합니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# 데스크톱 응용 프로그램 프로젝트를 새로 만듭니다. 프로젝트의 이름을 **Sender**로 지정합니다.

   	![][7]

2. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **Manage NuGet Packages for Solution...(솔루션에 대한 NuGet 패키지 관리...)**을 클릭합니다. 

	그러면 NuGet 패키지 관리 대화 상자가 표시됩니다.

3.  `Microsoft Azure Service Bus`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다. 

	![][8]

	그러면 <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">Azure 서비스 버스 라이브러리 NuGet 패키지</a>가 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

		using Microsoft.ServiceBus.Messaging;

5. **Program** 클래스에 다음 필드를 추가하고, 이전 섹션에서 만든 이벤트 허브 이름과 **보내기** 권한이 있는 연결 문자열로 위치 지정자 값을 대체합니다.

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. **Program** 클래스에 다음 메서드를 추가합니다.

		static async Task SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now.ToString(), message);
                    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now.ToString(), exception.Message);
                    Console.ResetColor();
                }

                await Task.Delay(200);
            }
        }

	이 메서드는 200ms 지연과 함께 이벤트 허브에 이벤트를 지속적으로 보냅니다.

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages().Wait();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png
<!--HONumber=52--> 
