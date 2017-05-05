> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> 
> 

## <a name="introduction"></a>소개
Azure IoT Hub는 수백만의 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 이전 자습서([IoT Hub 시작] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기])는 IoT Hub의 기본적인 장치-클라우드 및 클라우드-장치 메시징 기능을 보여줍니다. IoT Hub는 클라우드의 장치에 비지속성 메서드를 호출할 수 있는 기능을 제공합니다. 메서드는 장치와의 request-reply 상호 작용을 나타내며, 사용자가 호출의 상태를 알 수 있도록 즉시(사용자가 지정한 제한 시간이 지난 후에) 성공하거나 실패한다는 점에서 HTTP 호출과 유사합니다. [장치에 직접 메서드 호출][lnk-devguide-methods]은 메서드를 보다 자세히 설명하고 언제 메서드를 사용하고 언제 클라우드-장치 메시지를 사용할지에 대한 지침을 제공합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 장치 ID를 만듭니다.
* 클라우드에서 호출할 수 있는 직접 메서드가 포함된 시뮬레이션된 장치 앱을 만듭니다.
* IoT Hub를 통해 시뮬레이션된 장치 앱에 직접 메서드를 호출하는 콘솔 앱을 만듭니다.

> [!NOTE]
> 현재 직접 메서드는 MQTT 프로토콜을 사용하여 IoT Hub에 연결하는 장치에서만 액세스할 수 있습니다. 기존 장치 앱이 MQTT를 사용하도록 변환하는 방법에 관한 설명은 [MQTT 지원][lnk-devguide-mqtt] 문서를 참조하세요.
> 
> 




[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[IoT Hub를 사용하여 클라우드-장치 메시지 보내기]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[IoT Hub 시작]: ../articles/iot-hub/iot-hub-node-node-getstarted.md