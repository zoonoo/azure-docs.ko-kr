## 시뮬레이션된 장치 앱 만들기

이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 Windows 콘솔 앱을 작성합니다.

1. 최신 Visual Studio 솔루션에서 **파일->추가->프로젝트**를 클릭하고 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# 데스크톱 앱 프로젝트를 새로 만듭니다. 프로젝트 이름을 **SimulatedDevice**로 지정합니다.

   	![][30]

2. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **Manage NuGet Packages for Solution...(솔루션에 대한 NuGet 패키지 관리...)**을 클릭합니다.

	그러면 NuGet 패키지 관리 창이 표시됩니다.

3. `Microsoft Azure Devices Client`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.

	그러면 [Azure IoT - 장치 SDK NuGet 패키지]가 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. 다음 필드를 **프로그램** 클래스에 추가하고 자리 표시자 값을 각각 **IoT Hub 만들기** 및 **장치 ID 만들기** 섹션에서 가져온 IoT Hub URI 및 장치 키로 대체합니다.

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

6. **Program** 클래스에 다음 메서드를 추가합니다.

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Thread.Sleep(1000);
            }
        }

	이 메서드는 시뮬레이션된 풍속 센서를 나타내는 deviceId와 임의로 생성된 숫자와 함께 JSON 직렬화된 개체를 포함하여 새 장치-클라우드 메시지를 초 단위로 전송합니다.

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

> [AZURE.NOTE]간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현하는 것이 좋습니다.

<!-- Links -->

[Azure IoT - 장치 SDK NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[일시적인 오류 처리]: https://msdn.microsoft.com/ko-KR/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Oct15_HO3-->