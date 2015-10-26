## 시뮬레이트된 장치에서 대화형 메시지 보내기

이 섹션에서는 대화형 장치-클라우드 메시지를 IoT Hub로 보내도록 시뮬레이트된 장치 응용 프로그램을 수정합니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    이 메서드는 `MessageId` 시스템 속성만 다르고 [IoT Hub 시작]에서 만든 `SendDeviceToCloudMessagesAsync()` 메서드와 매우 유사하며 이제 `messageType`라는 사용자 속성이 설정됩니다. `MessageId` 속성은 메시지 수신 중복 제거에 사용되는 고유 전역 ID(GUID)로 설정됩니다. `messageType` 속성은 데이터 요소 메시지에서 대화형 메시지를 구분하는 데 사용됩니다. 이 정보는 메시지 본문 대신 메시지 속성에 전달되므로 백 엔드에 있는 이벤트 프로세서가 라우팅 수행을 위해 전체 메시지를 역직렬화할 필요가 없습니다.

> [AZURE.NOTE]일시적인 네트워크 통신(또는 다른 오류)은 장치에서 동일한 메시지를 여러 번 전송할 수 있으므로 대화형 메시지 중복 제거에 사용되는 `MessageId`가 장치에서 만들어지는 것이 중요합니다. GUID가 아닌 의미 체계 메시지 ID(예: 관련 메시지 데이터 필드의 해시)를 사용할 수 있습니다.

2. **Main** 메서드에서 `Console.ReadLine()` 줄 바로 앞에 다음 메서드를 추가합니다.

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE]간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현하는 것이 좋습니다.

<!-- Links -->
[IoT Hub 시작]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

<!---HONumber=Oct15_HO3-->