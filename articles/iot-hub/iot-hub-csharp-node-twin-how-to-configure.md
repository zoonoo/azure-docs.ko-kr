---
title: "Azure IoT Hub 장치 쌍 속성 사용(.NET/노드) | Microsoft Docs"
description: "Azure IoT Hub 장치 쌍을 사용하여 장치를 구성하는 방법입니다. Node.js용 Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치 앱을 구현하고 .NET용 Azure IoT 서비스 SDK를 사용하여 장치 쌍을 사용하여 장치 구성을 수정하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 8f9626fc47e7d32cb104b960bea9b7de9efa6f3e
ms.contentlocale: ko-kr
ms.lasthandoff: 09/28/2017

---
# <a name="use-desired-properties-to-configure-devices"></a>desired 속성을 사용하여 장치 구성
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

이 자습서의 끝 부분에 다음 두 개의 콘솔 앱이 제공됩니다.

* **SimulateDeviceConfiguration.js** - 원하는 구성 업데이트를 기다리고 시뮬레이션된 구성 업데이트 프로세스의 상태를 보고하는 시뮬레이션된 장치 앱.
* **SetDesiredConfigurationAndQuery**, 장치에 원하는 구성을 설정하고 구성 업데이트 프로세스를 쿼리하는 Node.js 백 엔드 앱입니다.

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 문서는 장치 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* Node.js 버전 4.0.x 이상
* 활성 Azure 계정. 계정이 없는 경우 몇 분 내에 [계정][lnk-free-trial]을 만들 수 있습니다.

[장치 쌍 시작][lnk-twin-tutorial] 자습서를 수행했으면 이미 IoT Hub 및 **myDeviceId**라는 장치 ID가 있습니다. 이 경우 [시뮬레이션된 장치 앱 만들기][lnk-how-to-configure-createapp] 섹션으로 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 **myDeviceId**로 허브에 연결하는 Node.js 콘솔 앱을 만들고, 원하는 구성 업데이트를 기다린 다음, 시뮬레이션된 구성 업데이트 프로세스의 업데이트를 보고합니다.

1. **simulatedeviceconfiguration**라는 빈 폴더를 새로 만듭니다. **simulatedeviceconfiguration** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
1. **simulatedeviceconfiguration** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. 텍스트 편집기를 사용하여 **simulatedeviceconfiguration** 폴더에 새 **SimulateDeviceConfiguration.js** 파일을 만듭니다.
1. 다음 코드를 **SimulateDeviceConfiguration.js** 파일에 추가하고 **myDeviceId** 장치 ID를 만들 때 복사한 장치 연결 문자열을 사용해 **{장치 연결 문자열}** 자리 표시자를 대체합니다.
   
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
   
    **Client** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이 코드는 **Client** 개체를 초기화한 후 **myDeviceId**에 대한 장치 쌍을 검색하고, *원하는 속성*에서 업데이트를 위한 처리기를 연결합니다. 처리기는 configId를 비교하여 실제 구성 변경 요청이 있는지 확인한 후 구성 변경을 시작하는 메서드를 호출합니다.
   
    이 코드에서는 간단히 하기 위해 초기 구성에 대한 하드 코드된 기본값을 사용합니다. 실제 앱은 아마도 로컬 저장소로부터 그 구성을 로드할 것입니다.
   
   > [!IMPORTANT]
   > 원하는 속성 변경 이벤트는 장치 연결 시 한 번만 내보내집니다. 작업을 수행하기 전에 원하는 속성이 실제로 변경되었는지 확인해야 합니다.
   > 
   > 
1. `client.open()` 호출 전에 다음 메서드를 추가합니다.
   
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
   
    **initConfigChange** 메서드는 구성 업데이트 요청으로 로컬 장치 쌍 개체에서 reported 속성을 업데이트하고 상태를 **Pending**으로 설정한 다음 서비스에서 장치 쌍을 업데이트합니다. 성공적으로 장치 쌍을 업데이트한 후 **completeConfigChange** 실행 중에 종료되는 장기 실행 프로세스를 시뮬레이션합니다. 이 메서드는 로컬 reported 속성을 업데이트하여 상태를 **Success**로 설정하고 **pendingConfig** 개체를 제거합니다. 그런 다음 서비스에서 장치 쌍을 업데이트합니다.
   
    대역폭을 절약하기 위해 보고된 속성은 문서 전체를 교체하는 대신에 수정할 속성(위의 코드에서 **patch**라고 명명됨)만 지정하여 업데이트합니다.
   
   > [!NOTE]
   > 이 자습서는 동시 구성 업데이트에 대해 어떤 동작도 시뮬레이션하지 않습니다. 어떤 구성 업데이트 프로세스는 업데이트가 실행되는 중에 대상 구성의 변경을 수용할 수 있는 반면에 어떤 프로세스는 대기해야 하거나 오류 조건을 사용해 거부할 수 있습니다. 특정 구성 프로세스에 대해 원하는 동작을 고려하여 구성 변경을 시작하기 전에 적절한 논리를 추가해야 합니다.
   > 
   > 
1. 장치 앱 실행.
   
        node SimulateDeviceConfiguration.js
   
    메시지 `retrieved device twin`이 표시되어야 합니다. 앱이 계속 실행되게 합니다.

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기
이 섹션에서는 새로운 원격 분석 구성 개체를 사용하여 **myDeviceId**와 연결된 장치 쌍에서 *desired 속성*을 업데이트하는 .NET 콘솔 앱을 만듭니다. 그런 다음 IoT Hub에 저장된 장치 쌍을 쿼리하고 장치의 desired 구성과 reported 구성 사이의 차이점을 표시합니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **SetDesiredConfigurationAndQuery**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createapp]
1. 솔루션 탐색기에서 **SetDesiredConfigurationAndQuery** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리...**를 클릭합니다.
1. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창][img-servicenuget]
1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 만든 허브의 IoT Hub 연결 문자열로 대체합니다.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. **Program** 클래스에 다음 메서드를 추가합니다.
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    **레지스트리** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이 코드에서는 **Registry** 개체를 초기화한 후 **myDeviceId**에 대한 장치 쌍을 검색하고, 새로운 원격 분석 구성 개체를 사용하여 desired 속성을 업데이트합니다.
    그런 다음 10초마다 IoT Hub에 저장된 장치 쌍을 쿼리하고, 원하는 보고된 원격 분석 구성을 출력합니다. 모든 장치에서 다양한 보고서를 생성하는 방법을 알아보려면 [IoT Hub 쿼리 언어][lnk-query]를 참조하세요.
   
   > [!IMPORTANT]
   > 이 응용 프로그램은 설명 목적으로 10초 마다 IoT Hub를 쿼리합니다. 변경을 감지하기 위해서가 아니라 여러 장치에서 사용자용 보고서를 생성하기 위해 쿼리를 사용합니다. 솔루션에 장치 이벤트의 실시간 알림이 필요한 경우 [쌍 알림][lnk-twin-notifications]을 사용합니다.
   > 
   > 
1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. 솔루션 탐색기에서 **시작 프로젝트 설정...**을 열고 **SetDesiredConfigurationAndQuery** 프로젝트의 **작업**이 **시작**인지 확인합니다. 솔루션을 빌드하십시오.
1. **SimulateDeviceConfiguration.js**가 실행 중일 때 **F5** 키를 사용하여 Visual Studio에서 .NET 응용 프로그램을 실행하고 24시간이 아니라 5분이란 새로운 액티브 보내기 빈도 사용해 보고된 구성이 **Success**에서 **Pending**으로 그리고 다시 **Success**로 변경 표시되어야 합니다.

 ![장치가 성공적으로 구성됨][img-deviceconfigured]
   
   > [!IMPORTANT]
   > 장치 보고서 작업 및 쿼리 결과 사이에 최대 1분간 지연됩니다. 이는 쿼리 인프라를 매우 높은 규모에서 작업하도록 하기 위해서입니다. 단일 장치 쌍의 일관된 보기를 검색하려면 **Registry** 클래스의 **getDeviceTwin** 메서드를 사용합니다.
   > 
   > 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 솔루션 백 엔드에서 원하는 구성을 *desired 속성*으로 설정하고, 해당 변경 사항을 감지하고 reported 속성을 통해 상태를 보고하는 다단계 업데이트 프로세스를 시뮬레이션하는 장치 앱을 작성했습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작][lnk-iothub-getstarted] 자습서를 참조하여 장치에서 원격 분석을 보냅니다.
* [jobs 예약 및 브로드캐스트][lnk-schedule-jobs] 자습서를 참조하여 대규모 장비 집합에 대한 작업을 예약하거나 수행합니다.
* [직접 메서드 사용][lnk-methods-tutorial] 자습서를 참조하여 대화형으로(예: 사용자 제어 앱에서 팬 작동) 장치를 제어합니다.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app

