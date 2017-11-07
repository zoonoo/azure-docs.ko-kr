> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

[시뮬레이션된 장치 클라우드 업로드 샘플](영문)의 이 연습에서는 [Azure IoT Edge][lnk-sdk]를 사용하여 시뮬레이션된 장치에서 IoT Hub로 장치-클라우드 원격 분석을 보내는 방법을 보여 줍니다.

이 연습에서는 다음 내용을 다룹니다.

* **아키텍처**: [시뮬레이션된 장치 클라우드 업로드 샘플]에 대한 아키텍처 정보입니다.
* **빌드 및 실행**: 샘플을 빌드하고 실행하는 데 필요한 단계입니다.

## <a name="architecture"></a>아키텍처

[시뮬레이션된 장치 클라우드 업로드 샘플]은 시뮬레이션된 장치의 원격 분석을 IoT Hub로 보내는 게이트웨이를 만드는 방법을 보여줍니다. 장치는 다음과 같은 이유 때문에 IoT Hub에 직접 연결할 수 없습니다.

* IoT Hub가 이해하는 통신 프로토콜을 사용하지 않습니다.
* IoT Hub에 의해 할당된 ID를 기억할 정도로 장치가 지능적이지 않습니다.

IoT Edge 게이트웨이는 다음과 같은 방법으로 이러한 문제를 해결할 수 있습니다.

* 게이트웨이는 장치가 사용하는 프로토콜을 이해하고, 장치로부터 장치-클라우드 원격 분석을 수신하고, IoT Hub가 이해하는 프로토콜을 사용하여 이 메시지를 IoT Hub에 전달합니다.

* 게이트웨이에서 IoT Hub ID를 매핑하고, 장치가 IoT Hub에 메시지를 보낼 때 프록시 역할을 합니다.

다음 다이어그램에서는 IoT Edge 모듈을 포함하여 샘플의 주요 구성 요소를 보여 줍니다.

![다이어그램 - 시뮬레이션된 장치 메시지가 게이트웨이를 통과하여 IoT Hub로 이동][1]

이 샘플에는 게이트웨이를 구성하는 3개의 모듈이 있습니다.
1. 프로토콜 수집 모듈
1. MAC &lt;-&gt; IoT Hub ID 모듈
1. IoT Hub 통신 모듈

모듈은 서로에게 직접 메시지를 전달하지 않습니다. 모듈은 구독 메커니즘을 사용하여 다른 모듈에게 메시지를 배달하는 내부 broker에 메시지를 게시합니다. 자세한 내용은 [Azure IoT Edge 시작][lnk-gw-getstarted]을 참조하세요.

![다이어그램 - 브로커와 통신할 게이트웨이 모듈][2]

### <a name="protocol-ingestion-module"></a>프로토콜 수집 모듈

프로토콜 수집 모듈은 게이트웨이를 통해, 장치로부터 데이터를 수신하고 클라우드로 데이터를 보내는 시작점입니다. 

샘플에서 이 모듈은 다음을 수행합니다.

1. 시뮬레이션된 온도 데이터를 만듭니다. 물리적 장치를 사용하면, 모듈은 물리적인 장치로부터 데이터를 읽어옵니다.
1. 메시지를 만듭니다.
1. 시뮬레이션된 온도 데이터를 메시지 콘텐츠에 배치합니다.
1. 메시지에 가짜 MAC 주소와 함께 속성을 추가합니다.
1. 메시지를 다음 번 순서의 모듈에서 사용할 수 있도록 만듭니다.

프로토콜 수집 모듈은 **simulated_device.c** 소스 코드에 있습니다.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT Hub ID 모듈

MAC &lt;-&gt; IoT Hub ID 모듈이 번역기로 작동합니다. 이 샘플은 MAC 주소를 유일한 장치 식별자로 사용하고, 이것을 IoT Hub 장치 ID와 상호 연결시킵니다. 하지만, 다른 고유 식별자를 사용하는 자신만의 모듈을 작성할 수 있습니다. 예를 들어 장치가 고유한 일련 번호를 포함하거나 원격 분석 데이터가 고유한 임베디드 장치 이름을 포함할 수 있습니다.

샘플에서 이 모듈은 다음을 수행합니다.

1. MAC 주소 속성이 있는 메시지를 검색합니다.
1. MAC 주소가 있으면, 메시지에 IoT Hub 장치 키와 함께 또 다른 속성을 추가합니다. 
1. 메시지를 다음 번 순서의 모듈에서 사용할 수 있도록 만듭니다.

개발자는 MAC 주소와 IoT Hub ID 사이의 매핑을 설정하여 시뮬레이션된 장치를 IoT Hub 장치 ID와 연결합니다. 개발자는 모듈 구성의 일부로 매핑을 수동으로 추가합니다.

The MAC &lt;-&gt; IoT Hub ID 모듈은 **identitymap.c** 소스 코드에 있습니다. 

### <a name="iot-hub-communication-module"></a>IoT Hub 통신 모듈

IoT Hub 통신 모듈은 게이트웨이에서 IoT Hub로의 단일 HTTPS 연결을 엽니다. HTTPS는 IoT Hub가 이해하는 세 가지 프로토콜 중 하나입니다. 이 모듈을 사용하면 하나의 연결을 통해 모든 장치의 연결을 멀티플렉싱하여 각 장치에 대한 연결을 열지 않아도 됩니다. 이 방법을 사용하면 단일 게이트웨이로 많은 장치를 연결할 수 있습니다. 

샘플에서 이 모듈은 다음을 수행합니다.

1. 이전 모듈에 의해 할당된 IoT Hub 장치 키 속성과 함께 메시지를 가져옵니다. 
1. HTTPS 프로토콜을 사용하여 메시지 콘텐츠를 IoT Hub로 보냅니다. 

IoT Hub 통신 모듈은 **iothub.c** 소스 코드에 있습니다.

## <a name="before-you-get-started"></a>시작하기 전에

시작하기 전에 다음을 수행해야 합니다.

* Azure 구독에서 [IoT Hub를 만듭니다][lnk-create-hub]. 이 샘플 연습에 대한 허브 이름이 필요합니다. 계정이 없는 경우 몇 분 내에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.
* IoT Hub에 장치 두 개를 추가하고 해당 ID와 장치 키를 적어 둡니다. [장치 탐색기][lnk-device-explorer] 또는 [iothub-explorer][lnk-iothub-explorer] 도구를 사용하여 IoT Hub에 장치를 추가하고 해당 키를 검색할 수 있습니다.


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[시뮬레이션된 장치 클라우드 업로드 샘플]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md