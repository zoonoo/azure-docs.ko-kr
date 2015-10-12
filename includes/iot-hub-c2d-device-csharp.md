## 시뮬레이트된 장치에서 메시지 받기

이 섹션에서는 [IoT Hub 시작]에서 만든 시뮬레이트된 장치 응용 프로그램을 수정하여 IoT Hub로부터 클라우드-장치 메시지를 수신합니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    `ReceiveAsync` 메서드는 수신 메시지를 장치에서 받은 시간에 비동기적으로 반환합니다. 지정 가능한 시간 제한 기간(이 경우 기본값 1분이 사용됨) 이후에는 *null*을 반환합니다. 이러한 경우 코드가 새 메시지를 계속 기다리게 됩니다. 이는 `if (receivedMessage == null) continue` 줄 때문입니다.

    `CompleteAsync()`에 대한 호출을 통해 메시지를 성공적으로 처리했고 장치 큐에서 안전하게 제거할 수 있다는 것을 IoT Hub에 알립니다. 장치 앱이 메시지 처리를 완료하지 못하도록 하는 경우가 발생하면 IoT Hub는 해당 메시지를 다시 배달하지 않습니다. 이때 장치 앱의 메시지 처리 논리가 *idempotent*(즉, 같은 메시지를 여러 번 받는 경우 동일한 결과 생성)이어야 함이 중요합니다. 또한 응용 프로그램은 메시지를 일시적으로 `Abandon`할 수 있습니다. 그러면 IoT Hub가 해당 메시지를 향후에 사용할 수 있도록 큐에 보존합니다. 그렇지 않고 메시지를 `Reject`하면 해당 메시지가 큐에서 영구적으로 제거됩니다. 클라우드-장치 메시지 수명 주기에 대한 자세한 내용은 [IoT Hub 개발자 가이드][IoT Hub Developer Guide - C2D]를 참조하세요.

> [AZURE.NOTE]AMQP 대신 HTTP/1을 전송으로 사용하는 경우 `ReceiveAsync`가 즉시 반환됩니다. HTTP/1에서 클라우드-장치 메시지에 대해 지원되는 패턴은 메시지를 가끔씩(즉, 25분에 한 번씩보다 적게) 확인하는 장치에 간헐적으로 연결됩니다. HTTP/1 수신을 더 많이 실행하면 IoT Hub가 요청을 제한할 수 있습니다. AMQP와 HTTP/1 지원 간의 차이점 및 IoT Hub 제한에 대한 자세한 내용은 [IoT Hub 개발자 가이드][IoT Hub Developer Guide - C2D]를 참조하세요.

2. **Main** 메서드에서 `Console.ReadLine()` 줄 바로 앞에 다음 메서드를 추가합니다.

        ReceiveC2dAsync();

> [AZURE.NOTE]간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현하는 것이 좋습니다.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=Oct15_HO1-->