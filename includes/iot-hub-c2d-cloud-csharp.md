## 앱 백 엔드에서 클라우드-장치 메시지 보내기

이 섹션에서는 클라우드-장치 메시지를 시뮬레이트된 장치 앱으로 보내는 Windows 콘솔 응용 프로그램을 작성합니다.

1. 최신 Visual Studio 솔루션에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# 데스크톱 앱 프로젝트를 새로 만듭니다. 프로젝트 **SendCloudToDevice**의 이름을 지정합니다.

   	![][20]

2. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **Manage NuGet Packages for Solution...(솔루션에 대한 NuGet 패키지 관리...)**을 클릭합니다.

	그러면 NuGet 패키지 관리 창이 표시됩니다.

3. `Microsoft Azure Devices`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.

	그러면 [Azure IoT - 서비스 SDK NuGet 패키지]가 다운로드 및 설치되고 해당 참조가 추가됩니다.

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

		using Microsoft.Azure.Devices;

5. **Program** 클래스에 다음 필드를 추가하고 자리 표시자 값을 [IoT Hub 시작]에서 만든 IoT Hub 연결 문자열로 대체합니다.

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. **Program** 클래스에 다음 메서드를 추가합니다.

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	이 메서드는 새 클라우드-장치 메시지를 ID `myFirstDevice`를 사용하여 장치에 보냅니다. [IoT Hub 시작]에서 사용한 값을 수정한 경우 그에 따라 이 매개 변수를 변경합니다.

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Visual Studio 내에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정...**을 선택합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ProcessDeviceToCloudMessages**, **SimulatedDevice** 및 **SendCloudToDevice**에 대한 **시작** 동작을 선택합니다.

9.  **F5** 키를 누르고 세 응용 프로그램이 모두 시작하는지 확인해야 합니다. **SendCloudToDevice** 창을 선택하고 **Enter** 키를 눌러서 시뮬레이트된 앱에서 메시지를 받는지 확인해야 합니다.

    ![][21]

## 배달 피드백 받기
각 클라우드-장치 메시지에 대해 배달(또는 만료) 승인을 IoT Hub에 요청할 수 있습니다. 이를 통해 클라우드 백 엔드가 다시 시도 또는 보정 논리를 쉽게 알릴 수 있습니다. 클라우드-장치 피드백에 대한 자세한 내용은 [IoT Hub 개발자 가이드][IoT Hub Developer Guide - C2D]를 참조하세요.

이 섹션에서는 **SendCloudToDevice** 앱을 수정하여 피드백을 요청하고 IoT Hub에서 수신합니다.

1. Visual Studio의 **SendCloudToDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    수신 패턴은 장치 앱으로부터 클라우드-장치 메시지를 받는 데 사용되는 방식과 동일합니다.

2. **Main** 메서드에서 `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` 줄 바로 뒤에 다음 메서드를 추가합니다.

        ReceiveFeedbackAsync();

3. 클라우드-장치 메시지 배달에 대한 피드백을 요청하려면 **SendCloudToDeviceMessageAsync** 메서드에서 속성을 지정해야 합니다. `var commandMessage = new Message(...);` 줄 바로 뒤에 다음 줄을 추가합니다.

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  **F5** 키를 눌러서 앱을 실행하고 세 응용 프로그램이 모두 시작하는지 확인해야 합니다. **SendCloudToDevice** 창을 선택하고 **Enter** 키를 눌러서 시뮬레이트된 앱에서 메시지를 받는지 확인하고 몇 초 후에 **SendCloudToDevice** 앱에서 피드백 메시지를 받는지 확인해야 합니다.

    ![][22]

> [AZURE.NOTE]간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현하는 것이 좋습니다.

<!-- Links -->

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - 서비스 SDK NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[일시적인 오류 처리]: https://msdn.microsoft.com/ko-KR/library/hh680901(v=pandp.50).aspx
[IoT Hub 시작]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=AcomDC_1203_2015-->