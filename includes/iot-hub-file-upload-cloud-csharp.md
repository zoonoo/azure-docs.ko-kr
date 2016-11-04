## 파일 업로드 알림 수신
이 섹션에서는 IoT Hub에서 파일 업로드 알림 메시지를 수신하는 Windows 콘솔 앱을 작성합니다.

1. 최신 Visual Studio 솔루션에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 새로 만듭니다. 프로젝트 이름을 **ReadFileUploadNotification**으로 지정합니다.
   
    ![Visual Studio의 새 프로젝트][2]
2. 솔루션 탐색기에서 **ReadFileUploadNotification** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
   
    그러면 NuGet 패키지 관리 창이 표시됩니다.
3. `Microsoft.Azure.Devices`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.
   
    그러면 **ReadFileUploadNotification** 프로젝트에서 [Azure IoT - 서비스 SDK NuGet 패키지]가 다운로드 및 설치되고 해당 참조가 추가됩니다.
4. **Program.cs** 파일 맨 위에 다음 문을 추가합니다.
   
        using Microsoft.Azure.Devices;
5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 [IoT Hub 시작]에서 만든 IoT Hub 연결 문자열로 대체합니다.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. **Program** 클래스에 다음 메서드를 추가합니다.
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();
   
            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();
   
                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }
   
    수신 패턴은 장치 앱으로부터 클라우드-장치 메시지를 받는 데 사용되는 방식과 동일합니다.
7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[Azure IoT - 서비스 SDK NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[IoT Hub 시작]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[2]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0622_2016-->