> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>소개

[IoT Hub 장치 쌍 시작하기][lnk-twin-tutorial]에서는 *태그*를 사용하여 장치 메타데이터를 설정하는 방법을 학습했습니다. *보고되는 속성*을 사용하여 장치 앱에서 장치 조건을 받은 다음, 이 정보를 SQL 방식 언어를 사용하여 쿼리했습니다.

이 자습서에서는 장치 쌍의 *원하는 속성*을 *보고되는 속성*과 함께 사용하여 장치 앱을 원격으로 구성하는 방법을 알아봅니다. 장치 쌍의 보고되는 속성과 원하는 속성을 통해 장치 응용 프로그램의 다단계 구성을 구현하고 모든 장치에서 이 작업의 상태에 대한 정보를 확보합니다. 장치 구성의 역할과 관련된 자세한 내용은 [IoT Hub를 사용한 장치 관리 개요][lnk-dm-overview]에서 찾아볼 수 있습니다.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

높은 수준에서 장치 쌍을 사용하면 솔루션 백 엔드가 특정 명령을 전송하는 대신 관리되는 장치에 대해 원하는 구성을 지정할 수 있습니다. 업데이트 프로세스의 현재 상태와 잠재적인 오류 조건을 지속적으로 솔보고하는 한편 장치가 해당 구성을 업데이트하는 가장 좋은 방법을 설정하는 역할을 담당하게 됩니다. 이는 특정 장치 조건이 특정 명령을 즉시 수행하는 능력에 영향을 미치는 IoT 시나리오에서 중요합니다. 이 패턴은 솔루션 백 엔드가 모든 장치에 대한 구성 프로세스 상태의 전체 가시성을 갖도록 설정되어 있어 대량의 장치 집합을 관리하는데 중요합니다.

> [!TIP]
> 장치가 좀 더 대화형 방식으로 제어되는 시나리오(예: 사용자 제어 앱에서 팬 켜기)에서는 [직접 메서드][lnk-methods] 사용을 고려하는 것이 좋습니다.

이 자습서에서는 장치 앱이 구성 업데이트를 적용할 수 있게 솔루션 백 엔드가 대상 장치의 원격 분석 구성을 변경합니다. 예를 들어 구성 업데이트에서 소프트웨어 모듈 다시 시작이 필요할 수 있습니다. 이 자습서에서는 간단한 지연으로 시뮬레이션됩니다.

솔루션 백 엔드는 장치 쌍의 필요한 속성에 들어 있는 구성을 다음과 같은 방식으로 저장합니다.

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

구성은 복잡한 개체일 수 있으므로 고유한 ID(해시 또는 [GUID][lnk-guid])가 지정됩니다.


장치 앱은 reported 속성에 desired 속성 **telemetryConfig**을 미러링하는 현재 구성을 보고합니다.

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

reported **telemetryConfig**에 구성 업데이트 프로세스의 상태를 보고하는 데 사용되는 추가 속성 **상태**가 어떻게 들어 있는지 확인합니다.

새 원하는 구성을 받을 때, 장치 앱은 상태 변경에 따라 보류된 구성을 보고합니다.

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

일정 시간 후에 장치 앱은 속성을 업데이트하여 이 작업의 성공 또는 실패 여부를 보고합니다. 솔루션 백 엔드는 언제라도 모든 장치에 대한 구성 프로세스의 상태를 쿼리할 수 있습니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 솔루션 백 엔드에서 구성 업데이트를 받아 구성 업데이트 프로세스에 여러 업데이트를 *보고된 속성*으로 보고하는 시뮬레이션된 장치 앱을 만듭니다.
* 장치의 원하는 구성을 업데이트한 다음 구성 업데이트 프로세스를 쿼리하는 백 엔드 앱을 만듭니다.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
