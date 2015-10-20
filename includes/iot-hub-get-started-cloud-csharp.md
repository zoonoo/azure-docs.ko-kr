## 장치 ID 만들기

이 섹션에서는 IoT Hub에서 새 장치 ID를 만드는 Windows 콘솔 앱을 작성합니다. 자세한 내용은 [IoT Hub 개발자 가이드][IoT Hub Developer Guide - Identity Registry]의 **장치 ID 레지스트리** 섹션을 참조하세요. 이 콘솔 응용 프로그램을 실행하면 장치-클라우드 메시지를 IoT Hub에 보낼 때 장치 ID로 사용하는 ID와 키를 갖게 됩니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# 데스크톱 응용 프로그램 프로젝트를 새로 만듭니다. 프로젝트 **CreateDeviceIdentity**의 이름을 지정합니다.

	![][10]

2. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **Manage NuGet Packages for Solution...(솔루션에 대한 NuGet 패키지 관리...)**을 클릭합니다.

	**NuGet 패키지 관리자** 창이 표시됩니다.

3. **시험판 포함** 옵션을 선택합니다. 그런 다음 `Microsoft Azure Devices`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.

	![][11]

4. 그러면 [Microsoft Azure 장치 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 패키지가 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. **Program** 클래스에 다음 필드를 추가하고, 자리 표시자 값을 이전 섹션에서 만든 IoT Hub 이름과 해당 연결 문자열로 대체합니다.

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. **Program** 클래스에 다음 메서드를 추가합니다.

		private async static Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	이 메서드는 ID **myFirstDevice**를 사용하여 새 장치 ID를 만듭니다(이미 동일한 ID가 있는 경우 간단히 검색됨). 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키는 IoT Hub에 연결할 시뮬레이트된 장치에서 사용됩니다.

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. 이 응용 프로그램을 실행하고 장치 키를 기록합니다.

    ![][12]

> [AZURE.NOTE]IoT Hub ID 레지스트리는 안전한 액세스를 위해 장치 ID를 저장하는 데만 사용된다는 것에 유의해야 합니다. 즉, 보안 자격 증명을 저장하고 개별 장치 액세스를 사용하거나 사용하지 않도록 설정하는 데만 사용됩니다. 장치 응용 프로그램 메타데이터는 응용 프로그램별 저장소에 저장되어야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][IoT Hub Developer Guide - Identity Registry]를 참조하세요.

## 장치-클라우드 메시지 받기

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 Windows 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브][Event Hubs Overview]와 호환되는 끝점을 노출하여 장치-클라우드 메시지를 읽습니다. 간단히 하기 위해 이 자습서에서는 간소화된 판독기를 만들어 사용하며 이는 높은 처리량 배포에는 적합하지 않습니다. IoT Hub의 장치-클라우드 메시지를 처리하는 방법에 대한 자세한 내용은 [장치-클라우드 메시지 처리] 자습서에서 찾아볼 수 있습니다. 이벤트 허브에서 읽은 메시지를 처리하는 방법에 대한 자세한 내용은 [이벤트 허브 시작] 자습서를 참조하세요.

1. 최신 Visual Studio 솔루션에서 **파일->추가->프로젝트**를 클릭하고 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# 데스크톱 앱 프로젝트를 새로 만듭니다. 프로젝트 **ReadDeviceToCloudMessages**의 이름을 지정합니다.

    ![][10]

2. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

    **NuGet 패키지 관리** 대화 상자가 나타납니다.

3. `WindowsAzure.ServiceBus`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.

    그러면 [Azure 서비스 버스](https://www.nuget.org/packages/WindowsAzure.ServiceBus)가 모든 종속 항목과 함께 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Messaging;

5. **Program** 클래스에 다음 필드를 추가하고, 자리 표시자 값을 이전 섹션에서 만든 IoT Hub 이름과 해당 연결 문자열로 대체합니다.

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. **Program** 클래스에 다음 메서드를 추가합니다.

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    이 메서드는 EventHub 클라이언트를 사용하여 IoT Hub의 모든 장치-클라우드 수신 파티션에서 받습니다. EventHubReceiver를 만들 때 `DateTime.Now` 매개 변수가 어떻게 전달되는지 확인합니다. 이를 통해 시작한 다음 전송된 메시지만 받는 수신자를 만듭니다.

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[이벤트 허브 시작]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure Preview Portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=Oct15_HO3-->