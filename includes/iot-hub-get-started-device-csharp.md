## 시뮬레이션된 장치 앱 만들기

이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 Windows 콘솔 앱을 작성합니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 새로운 Visual C# Windows 클래식 데스크톱을 최신 솔루션에 추가합니다. 프로젝트 **SimulatedDevice**의 이름을 지정합니다.

   	![][30]

2. 솔루션 탐색기에서 **SimulatedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.

3. **NuGet 패키지 관리자** 창에서 **시험판 포함** 옵션이 선택되었는지 확인합니다. **Microsoft Azure 장치 클라이언트**를 검색한 후, **설치**를 클릭하고 사용 약관에 동의합니다.

	그러면 [Azure IoT - 장치 SDK NuGet 패키지][lnk-device-nuget]가 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. 다음 필드를 **프로그램** 클래스에 추가하여, 자리 표시자 값을 *IoT Hub 만들기* 섹션에서 가져온 IoT Hub 호스트 이름과 *장치 ID 만들기* 섹션에서 가져온 장치 키로 대체합니다.

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

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

	이 메서드는 1초마다 새로운 장치-클라우드 메시지를 보냅니다. 메시지에는 deviceId가 있는 JSON 직렬화된 개체와 풍속 센서를 시뮬레이션하기 위해 임의로 생성된 숫자가 있습니다.

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  기본적으로 **Create** 메서드는 AMQP 프로토콜을 사용하여 IoT Hub와 통신하는 **DeviceClient**를 만듭니다. HTTPS 프로토콜을 사용하려면 프로토콜을 지정할 수 있도록 해주는 **Create** 메서드의 재정의를 사용합니다. HTTPS 프로토콜을 사용하려면 **Microsoft.AspNet.WebApi.Client** NuGet 패키지를 프로젝트에 추가하여 **System.Net.Http.Formatting** 네임스페이스를 포함해야 합니다.


> [AZURE.NOTE]간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/ko-KR/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_1217_2015-->