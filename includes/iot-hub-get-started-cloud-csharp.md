## 장치 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에서 새 장치 ID를 만드는 Windows 콘솔 앱을 작성합니다. 장치 ID 레지스트리에 항목이 없는 경우 장치를 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 **장치 ID 레지스트리** 섹션을 참조하세요. 이 콘솔 응용 프로그램을 실행하면 장치-클라우드 메시지를 IoT Hub로 보낼 때 장치가 자체적으로 ID를 식별할 수 있는 고유한 장치 ID와 키를 생성합니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 새로운 Visual C# Windows 클래식 데스크톱을 최신 솔루션에 추가합니다. 프로젝트 **CreateDeviceIdentity**의 이름을 지정합니다.

	![][10]

2. 솔루션 탐색기에서 **CreateDeviceIdentity** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.

3. **NuGet 패키지 관리자** 창에서 **시험판 포함** 옵션이 선택되었는지 확인합니다. 그런 다음 **Microsoft Azure 장치**를 검색하고, **설치**를 클릭하여 **Microsoft.Azure.Devices** 패키지를 설치하고, 사용 약관에 동의합니다.

	![][11]

4. 그러면 [Microsoft Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지가 다운로드 및 설치되고 참조가 추가됩니다.

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. **Program** 클래스에 다음 필드를 추가하고, 자리 표시자 값을 이전 섹션에서 만든 IoT Hub의 연결 문자열로 대체합니다.

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

	이 메서드는 ID **myFirstDevice**(레지스트리에 해당 장치 ID가 이미 있는 경우 코드는 기존 장치 정보만 검색)가 있는 새로운 장치 ID를 만듭니다. 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키는 IoT Hub에 연결할 시뮬레이트된 장치에서 사용합니다.

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. 이 응용 프로그램을 실행하고 장치 키를 기록합니다.

    ![][12]

> [AZURE.NOTE]IoT Hub ID 레지스트리는 장치 ID만 저장하여 허브에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 응용 프로그램이 다른 장치별 메타데이터를 저장해야 하는 경우 응용 프로그램별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.

## 장치-클라우드 메시지 받기

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 Windows 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브][lnk-event-hubs-overview]와 호환되는 끝점을 노출하여 장치-클라우드 메시지를 읽을 수 있습니다. 작업을 단순화하기 위해 이 자습서에서는 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. [장치-클라우드 메시지 처리][lnk-processd2c-tutorial] 자습서에서는 규모로 장치-클라우드 메시지를 처리하는 방법을 보여 줍니다. [이벤트 허브 시작][lnk-eventhubs-tutorial] 자습서는 이벤트 허브에서 메시지를 처리하는 방법에 대한 추가 정보를 제공하고 IoT Hub 이벤트 허브 호환 끝점에 적용됩니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 새로운 Visual C# Windows 클래식 데스크톱을 최신 솔루션에 추가합니다. 프로젝트 **ReadDeviceToCloudMessages**의 이름을 지정합니다.

    ![][10]

2. 솔루션 탐색기에서 **ReadDeviceToCloudMessages** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. **NuGet 패키지 관리자** 창에서 **시험판 포함** 옵션이 선택되었는지 확인합니다. 그런 다음 **WindowsAzure.ServiceBus**를 검색하고 **설치**를 클릭하여 사용 약관에 동의합니다.

    그러면 [Azure 서비스 버스][lnk-servicebus-nuget]가 모든 종속 항목과 함께 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Messaging;

5. **Program** 클래스에 다음 필드를 추가하여, 자리 표시자 값을 *IoT Hub 만들기* 섹션에서 만든 IoT Hub의 연결 문자열로 대체합니다.

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

    이 메서드는 **EventHubReceiver** 인스턴스를 사용하여 모든 IoT Hub 장치-클라우드 수신 파티션의 메시지를 수신합니다. 시작된 후 보낸 메시지만 수신하도록 **EventHubReceiver** 개체를 만든 경우 `DateTime.Now` 매개 변수를 전달하는 방법을 참조하세요. 테스트 환경에서 유용하므로 현재 메시지 집합을 볼 수 있지만 프로덕션 환경에서 코드가 모든 메시지를 처리하고 있는지 확인해야 합니다. 자세한 정보는 [IoT Hub가 장치-클라우드 메시지를 처리하는 방법][lnk-processd2c-tutorial] 자습서를 참조하세요.

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

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=AcomDC_0114_2016-->