> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)
> 
> 

[시뮬레이션된 장치 클라우드 업로드 샘플] 연습은 [Azure IoT Gateway SDK][lnk-sdk]를 사용하여 시뮬레이션된 장치에서 장치-클라우드 원격 분석을 IoT Hub에 보내는 방법을 보여줍니다.

이 연습에서는 다음 내용을 다룹니다.

1. **아키텍처**: 시뮬레이션된 장치 클라우드 업로드 샘플에 대한 중요한 아키텍처 정보입니다.
2. **빌드 및 실행**: 샘플을 빌드하고 실행하는 데 필요한 단계입니다.

## <a name="architecture"></a>아키텍처
시뮬레이션된 장치 클라우드 업로드 샘플은 SDK를 사용하여 시뮬레이션된 장치의 원격 분석을 IoT Hub로 보내는 게이트웨이를 만드는 방법을 보여줍니다. 시뮬레이션된 장치는 다음과 같은 이유 때문에 IoT Hub에 직접 연결할 수 없습니다.

* 장치에서 IoT Hub가 이해하는 통신 프로토콜을 사용하지 않습니다.
* IoT Hub에 의해 할당된 ID를 기억할 정도로 장치가 지능적이지 않습니다.

게이트웨이는 다음과 같은 방법으로 시뮬레이션된 장치에 대해 이러한 문제를 해결합니다.

* 게이트웨이는 시뮬레이션된 장치가 사용하는 프로토콜을 이해하고, 장치로부터 장치-클라우드 원격 분석을 수신하고, IoT Hub가 이해하는 프로토콜을 사용하여 이 메시지를 IoT Hub에 전달합니다.
* 게이트웨이에서 시뮬레이션된 장치를 대신하여 IoT Hub ID를 저장하고, 시뮬레이션된 장치가 IoT Hub에 메시지를 보낼 때 프록시 역할을 합니다.

다음 다이어그램은 게이트웨이 모듈을 비롯한 샘플의 주요 개념을 보여줍니다.

![][1]

> [!NOTE]
> 모듈은 서로에게 직접 메시지를 전달하지 않습니다. 모듈은 아래 다이어그램에 표시된 것과 같은 구독 메커니즘을 사용하여 다른 모듈에게 메시지를 배달하는 내부 broker에 메시지를 게시합니다. 자세한 내용은 [IoT Gateway SDK 시작][lnk-gw-getstarted]을 참조하세요.
> 
> 

### <a name="protocol-ingestion-module"></a>프로토콜 수집 모듈
모듈은 게이트웨이를 통해, 장치로부터 데이터를 가져오고 클라우드로 데이터를 보내는 시작점입니다. 샘플에서 모듈은 네 가지 작업을 수행합니다.

1. 시뮬레이션된 온도 데이터를 만듭니다. 참고: 실제 장치를 사용하면, 모듈은 물리적인 장치로부터 데이터를 읽어옵니다.
2. 시뮬레이션된 온도 데이터를 메시지 콘텐츠에 배치합니다.
3. 시뮬레이션된 온도 데이터를 포함하는 메시지에 가짜 MAC 주소와 함께 속성을 추가합니다.
4. 메시지를 다음 번 순서의 모듈에서 사용할 수 있도록 만듭니다.

> [!NOTE]
> 위 다이어그램의 **Protocol X ingestion**이라는 모듈은 소스 코드에서 **시뮬레이션된 장치**라고 합니다.
> 
> 

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT Hub ID 모듈
이 모듈 시뮬레이션된 장치 앱의 프로토콜 수집 모듈에 의해 추가된 MAC 주소를 포함하는 속성을 포함하는 메시지를 찾아 스캔합니다. 모듈이 그러한 속성을 찾으면, 메시지에 IoT Hub 장치 키와 함께 또 다른 속성을 추가하고, 다음 번 순서의 모듈에서 메시지를 사용할 수 있도록 만듭니다. 이것이 샘플이 시뮬레이션된 장치와 IoT Hub 장치 ID를 연결하는 방식입니다. 개발자는 모듈 구성의 일부로 MAC 주소와 IoT Hub ID 사이의 매핑을 수동으로 설정합니다. 

> [!NOTE]
> 이 샘플은 MAC 주소를 유일한 장치 식별자로 사용하고, 이것을 IoT Hub 장치 ID와 상호 연결시킵니다. 하지만, 다른 고유 식별자를 사용하는 자신만의 모듈을 작성할 수 있습니다. 예를 들어, 고유한 일련 번호 또는 IoT Hub 장치 ID를 결정하는 데 사용할 수 있는 고유한 장치 이름이 포함된 원격 분석 데이터를 포함하는 장치가 있을 수 있습니다.
> 
> 

### <a name="iot-hub-communication-module"></a>IoT Hub 통신 모듈
이 모듈은 이전 모듈에 의해 할당된 IoT Hub 장치 ID와 함께 메시지를 가져와서, HTTP를 사용하여 IoT Hub에 메시지 콘텐츠를 보냅니다. HTTP는 IoT Hub가 이해하는 세 가지 프로토콜 중 하나입니다.

각각의 시뮬레이션된 장치 앱에 IoT Hub에 대한 연결을 여는 대신, 이 모듈은 게이트웨이에서 IoT hub로 단일 HTTP 연결을 열고, 이 연결을 통해 시뮬레이션된 모든 장치로부터 연결을 다중 송신합니다. 이렇게 하면 단일 게이트웨이가 모든 장치에 대해 고유 연결을 연 경우보다 훨씬 더 많은 장치에(시뮬레이션된 또는 그 밖의) 연결할 수 있습니다.

![][2]

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[시뮬레이션된 장치 클라우드 업로드 샘플]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md

<!--HONumber=Feb17_HO1-->


