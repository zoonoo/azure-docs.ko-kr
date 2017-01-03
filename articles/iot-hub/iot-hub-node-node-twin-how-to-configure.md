---
title: "장치 쌍 속성 사용 | Microsoft Docs"
description: "이 자습서는 장치 쌍 속성을 사용하는 방법을 보여줍니다."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 400eab43a417980abe9df5fa75ee9f9e43b296d0
ms.openlocfilehash: cc3e2f92550b77fe837afa19f51ea7691422ac9b


---
# <a name="tutorial-use-desired-properties-to-configure-devices"></a>자습서: desired 속성을 사용하여 장치 구성
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

이 자습서의 끝 부분에 다음 두 개의 Node.js 콘솔 응용 프로그램이 제공됩니다.

* **SimulateDeviceConfiguration.js** - 원하는 구성 업데이트를 기다리고 시뮬레이션된 구성 업데이트 프로세스의 상태를 보고하는 시뮬레이션된 장치 앱.
* **SetDesiredConfigurationAndQuery.js** - 백 엔드에서 실행되도록 의도된, 장치에 원하는 구성을 설정하고 구성 업데이트 프로세스를 쿼리하는 Node.js 앱.

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 문서는 장치 및 백 엔드 응용 프로그램을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 0.10.x 이상
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[장치 쌍 시작][lnk-twin-tutorial] 자습서를 진행했다면 IoT Hub 및 **myDeviceId**라는 장치 ID가 이미 있으니, [시뮬레이션된 장치 앱 만들기][lnk-how-to-configure-createapp] 섹션으로 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 **myDeviceId**로 허브에 연결하는 Node.js 콘솔 앱을 만들고, 원하는 구성 업데이트를 기다린 다음, 시뮬레이션된 구성 업데이트 프로세스의 업데이트를 보고합니다.

1. **simulatedeviceconfiguration**라는 빈 폴더를 새로 만듭니다. **simulatedeviceconfiguration** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **simulatedeviceconfiguration** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **simulatedeviceconfiguration** 폴더에 새 **SimulateDeviceConfiguration.js** 파일을 만듭니다.
4. 다음 코드를 **SimulateDeviceConfiguration.js** 파일에 추가하고 **myDeviceId** 장치 ID를 만들 때 복사한 연결 문자열을 사용해 **{장치 연결 문자열}** 자리 표시자를 대체합니다.
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    **Client** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이전 코드는 **Client** 개체를 초기화한 다음 **myDeviceId**에 대한 장치 쌍을 검색하고, desired 속성에 업데이트에 대한 처리기를 연결합니다. 처리기는 configId를 비교하여 실제 구성 변경 요청이 있는지 확인한 후 구성 변경을 시작하는 메서드를 호출합니다.
   
    이전 코드에서는 간단히 하기 위해 초기 구성에 대한 하드 코드된 기본값을 사용합니다. 실제 앱은 아마도 로컬 저장소로부터 그 구성을 로드할 것입니다.
   
   > [!IMPORTANT]
   > Desired 속성 변경 이벤트는 항상 장치 연결시 한 번에 내보내지기 때문에, 모든 작업을 수행하기 전에 Desired 속성에 실제 변경이 있는지 확인해야 합니다.
   > 
   > 
5. `client.open()` 호출 전에 다음 메서드를 추가합니다.
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    **initConfigChange** 메서드는 구성 업데이트 요청으로 로컬 장치 쌍 개체에서 reported 속성을 업데이트하고 상태를 **Pending**으로 설정한 다음 서비스에서 장치 쌍을 업데이트합니다. 성공적으로 장치 쌍을 업데이트한 후, **completeConfigChange**의 실행 중에 종료되는 장기 실행 프로세스를 시뮬레이션합니다. 이 메서드는 상태를 **Success**로 설정하고 **pendingConfig** 개체를 삭제하여 로컬 장치 쌍의 reported 속성을 업데이트합니다. 그런 다음 서비스에서 장치 쌍을 업데이트합니다.
   
    대역폭을 절약하기 위해 보고된 속성은 문서 전체를 교체하는 대신에 수정할 속성(위의 코드에서 **patch**라고 명명됨)만 지정하여 업데이트합니다.
   
   > [!NOTE]
   > 이 자습서는 동시 구성 업데이트에 대해 어떤 동작도 시뮬레이션하지 않습니다. 일부 구성 업데이트 프로세스는 업데이트가 실행되는 중에 대상 구성의 변경을 수용할 수 있는 반면에 다른 프로세스는 대기해야 하거나 오류 조건을 사용해 거부할 수 있습니다. 특정 구성 프로세스에 대해 원하는 동작을 고려하여 구성 변경을 시작하기 전에 적절한 논리를 추가해야 합니다.
   > 
   > 
6. 장치 앱 실행.
   
        node SimulateDeviceConfiguration.js
   
    메시지 `retrieved device twin`이 표시되어야 합니다. 앱이 계속 실행되게 합니다.

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기
이 섹션에서는 새로운 원격 분석 구성 개체를 사용하여 **myDeviceId**와 연결된 장치 쌍에서 *desired 속성*을 업데이트하는 Node.js 콘솔 앱을 만들 것입니다. 그런 다음 IoT Hub에 저장된 장치 쌍을 쿼리하여 장치의 원하는(desired) 구성 및 보고된(reported) 구성 사이의 차이점을 표시합니다.

1. **setdesiredandqueryapp**라는 빈 폴더를 새로 만듭니다. **setdesiredandqueryapp** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **setdesiredandqueryapp** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 패키지를 설치합니다.
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. 텍스트 편집기를 사용하여 **addtagsandqueryapp** 폴더에 새 **SetDesiredAndQuery.js** 파일을 만듭니다.
4. 다음 코드를 **SetDesiredAndQuery.js** 파일에 추가하고 허브를 만들 때 복사한 연결 문자열을 사용해 **{서비스 연결 문자열}** 자리 표시자를 대체합니다.
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{service connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    **레지스트리** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이전 코드는 **Registry** 개체를 초기화한 다음 **myDeviceId**에 대한 장치 쌍을 검색하고, 새로운 원격 분석 구성 개체를 사용해 desired 속성을 업데이트합니다. 그러면 10초간 **queryTwins** 함수 이벤트를 호출합니다.

    > [!IMPORTANT]
    > 이 응용 프로그램은 설명 목적으로 10초 마다 IoT Hub를 쿼리합니다. 변경을 감지하기 위해서가 아니라 여러 장치에서 사용자용 보고서를 생성하기 위해 쿼리를 사용합니다. 솔루션에 장치 이벤트의 실시간 알림이 필요한 경우 [장치-클라우드 메시지][lnk-d2c]를 사용합니다.
    > 
    >을 참조하세요.

1. 다음 코드를 **queryTwins** 함수를 구현하기 위해 `registry.getDeviceTwin()` 호출 앞에 추가합니다.
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    이전 코드는 IoT Hub에 저장된 장치 쌍을 쿼리하여 원하는(desired) 원격 분석 구성 및 보고된(reported) 원격 분석 구성을 출력합니다. 모든 장치에서 다양한 보고서를 생성하는 방법을 알아보려면 [IoT Hub 쿼리 언어][lnk-query]를 참조하세요.
2. **SimulateDeviceConfiguration.js**이 실행되는 가운데 응용 프로그램을 다음을 사용해 실행합니다.
   
        node SetDesiredAndQuery.js 5m
   
    24시간이 아니라 5분이란 새로운 액티브 보내기 빈도 사용해 보고된 구성이 **Success**에서 **Pending**으로 그리고 다시 **Success**로 변경 표시되어야 합니다.
   
   > [!IMPORTANT]
   > 장치 보고서 작업 및 쿼리 결과 사이에 최대 1분간 지연됩니다. 이는 쿼리 인프라를 매우 높은 규모에서 작업하도록 하기 위해서입니다. 단일 장치 쌍의 일관된 뷰를 검색하려면 **Registry** 클래스에 **getDeviceTwin** 메서드를 사용합니다.
   > 
   > 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 백 엔드 응용 프로그램에서 원하는 구성을 *desired 속성*으로 설정하고 시뮬레이션된 장치 앱을 작성하여 그 변경을 감지하고 장치 쌍에 그 상태를 *reported 속성*으로 보고하는 다단계 업데이트 프로세스를 시뮬레이션합니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작][lnk-iothub-getstarted] 자습서를 참조하여 장치에서 원격 분석을 보냅니다.
* [작업 예약 및 브로드캐스트][lnk-schedule-jobs] 자습서를 참조하여 대규모 장비 집합에 작업을 예약하거나 실행합니다.
* [직접 메서드 사용][lnk-methods-tutorial] 자습서를 참조하여 대화형으로(예: 사용자가 제어하는 앱에서 팬을 켬) 장치를 제어합니다.

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app



<!--HONumber=Nov16_HO5-->


