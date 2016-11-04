## 시뮬레이트된 장치에서 대화형 메시지 보내기
이 섹션에서는 [IoT Hub 시작]에서 만든 시뮬레이트된 장치 응용 프로그램을 수정하여 대화형 장치-클라우드 메시지를 IoT Hub로 보냅니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.
   
    ```
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
   
        Task.Delay(10000).Wait();
      }
    }
    ```
   
    이 메서드는 **SimulatedDevice** 프로젝트의 **SendDeviceToCloudMessagesAsync** 메서드와 매우 비슷합니다. 유일한 차이점은 이제 **MessageId** 시스템 속성 및 **messageType**라는 사용자 속성을 설정한 것합니다. 이 코드는 GUID(전역 고유 식별자)를 **MessageId** 속성에 할당합니다. 서비스 버스는 이를 사용하여 받은 메시지를 중복 제거할 수 있습니다. 샘플은 **messageType** 속성을 사용하여 데이터 요소 메시지에서 대화형 메시지를 구분합니다. 응용 프로그램은 정보를 메시지 본문 대신 메시지 속성에 전달하므로 이벤트 프로세서가 메시지 라우팅을 수행하기 위해 전체 메시지를 역직렬화할 필요가 없습니다.
   
   > [!NOTE]
   > 장치 코드의 대화형 메시지를 중복 제거하는 데 사용되는 **MessageId**를 만드는 것이 중요합니다. 간헐적인 네트워크 통신 또는 기타 오류로 인해 해당 장치에서 동일한 메시지가 여러 번 재전송될 수 있습니다. 또한 GUID 대신 의미 체계 메시지 ID(예: 관련 메시지 데이터 필드의 해시)를 사용할 수 있습니다.
   > 
   > 
2. **Main** 메서드에서 `Console.ReadLine()` 줄 바로 앞에 다음 메서드를 추가합니다.
   
    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````
   
   > [!NOTE]
   > 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
   > 
   > 

<!-- Links -->
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh675232.aspx
[IoT Hub 시작]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0608_2016-->