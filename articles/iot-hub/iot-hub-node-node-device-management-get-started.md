---
title: "Azure IoT Hub 장치 관리 시작(노드) | Microsoft Docs"
description: "IoT Hub 장치 관리를 사용하여 원격 장치 재부팅을 시작하는 방법입니다. Node.js용 Azure IoT 장치 SDK를 사용하여 직접 메서드를 포함한 시뮬레이션된 장치 앱 및 직접 메서드를 호출하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 198a325e02aac97d659d74b090414f7d6f074f14
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-device-management-node"></a>장치 관리 시작(노드)
## <a name="introduction"></a>소개
IoT 클라우드 응용 프로그램은 Azure IoT Hub에서 장치 쌍 및 직접 메서드라는 기본 요소를 사용하여 장치에 대한 장치 관리 작업을 원격으로 시작하고 모니터링할 수 있습니다. 이 문서에서는 IoT 클라우드 응용 프로그램 및 장치가 함께 작동하여 IoT Hub를 사용하여 원격 장치 재부팅을 시작 및 모니터링하는 방식에 대한 지침 및 코드를 제공합니다.

클라우드 기반 백 엔드 앱에서 장치에 대한 장치 관리 작업을 원격으로 시작하고 모니터링하려면 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod]와 같은 Azure IoT Hub 기본 형식을 사용합니다. 이 자습서에서는 백 엔드 앱 및 장치가 함께 작동하여 IoT Hub에서 원격 장치 다시 시작을 시작 및 모니터링하는 방식을 보여 줍니다.

직접 메서드를 사용하여 클라우드의 백 엔드 앱에서 장치 관리 작업(예: 재부팅, 공장 기본 설정으로 복원 및 펌웨어 업데이트)을 시작합니다. 장치는 다음과 같은 역할을 합니다.

* IoT Hub에서 보낸 메서드 요청 처리.
* 장치에서 해당하는 장치 특정 작업 시작.
* reported 속성을 통해 IoT Hub에 상태 업데이트 제공

클라우드에서 백 엔드 앱을 사용하여 장치 쌍 쿼리를 실행하고 장치 관리 작업의 진행 상태를 보고할 수 있습니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 장치 ID를 만듭니다.
* 장치를 다시 시작하는 직접 메서드가 포함된 시뮬레이트된 장치 앱을 만듭니다. 직접 메서드는 클라우드에서 호출됩니다.
* IoT Hub를 통해 시뮬레이션된 장치 앱에서 재부팅 직접 메서드를 호출하는 .NET 콘솔 앱을 만듭니다.

이 자습서를 마치면 두 가지 Node.js 콘솔 앱이 만들어집니다.

**dmpatterns_getstarted_device.js**, 이전에 만든 장치 ID로 IoT Hub에 연결하며 재부팅 직접 메서드를 수신하고 물리적 재부팅을 시뮬레이션하며 마지막 재부팅 시간을 보고합니다.

**dmpatterns_getstarted_service.js**, 시뮬레이션된 장치 앱에 직접 메서드를 호출하고 응답을 표시하고 업데이트된 reported 속성을 표시합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 0.12.x 이상, <br/>  Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비][lnk-dev-setup]에서 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 Node.js 콘솔 앱 만들기
* 시뮬레이션된 장치 재부팅 트리거
* reported 속성을 사용하여 장치 및 해당 장치가 마지막으로 재부팅한 시간을 확인하는 장치 쌍 쿼리를 사용하도록 설정

1. **manageddevice**라는 빈 폴더를 만듭니다.  **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **manageddevice** 폴더에 **dmpatterns_getstarted_device.js** 파일을 만듭니다.
4. 다음 'require' 문을 **dmpatterns_getstarted_device.js** 파일의 시작 부분에 추가합니다.
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  연결 문자열을 장치 연결 문자열로 바꿉니다.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 장치에서 직접 메서드를 구현하도록 다음 함수를 추가합니다.
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. IoT Hub에 대한 연결을 열고 직접 메서드 수신기를 시작합니다.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. **dmpatterns_getstarted_device.js** 파일을 저장 후 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 재부팅 트리거
이 섹션에서는 장치에서 직접 메서드를 사용하여 원격 다시 시작을 시작하는 .NET 콘솔 앱(C# 사용)을 만듭니다. 앱은 장치 쌍 쿼리를 사용하여 해당 장치에 대한 마지막 다시 시작 시간을 검색합니다.

1. **triggerrebootondevice**라는 빈 폴더를 만듭니다.  **triggerrebootondevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **triggerrebootondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iothub --save
    ```
3. 텍스트 편집기를 사용하여 **triggerrebootondevice** 폴더에 **dmpatterns_getstarted_service.js** 파일을 만듭니다.
4. 다음 'require' 문을 **dmpatterns_getstarted_service.js** 파일의 시작 부분에 추가합니다.
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 다음 변수 선언을 추가하고 자리 표시자 값을 바꿉니다.
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. 대상 장치를 재부팅하기 위해 장치 메서드를 호출하도록 다음 함수를 추가합니다.
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```
7. 장치를 쿼리하고 마지막 재부팅 시간을 가져오도록 다음 함수를 추가합니다.
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
8. 다시 시작 직접 메서드를 트리거하고 마지막 다시 시작 시간을 쿼리하는 함수를 호출하도록 다음 코드를 추가합니다.
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. **dmpatterns_getstarted_service.js** 파일을 저장 후 닫습니다.

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. **triggerrebootondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 원격 재부팅을 트리거하고 마지막 재부팅 시간을 찾기 위해 장치 쌍에 대한 쿼리를 수행합니다.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다.

## <a name="customize-and-extend-the-device-management-actions"></a>장치 관리 작업 사용자 지정 및 확장
IoT 솔루션에서 정의된 장치 관리 패턴 집합을 확장하거나 장치 쌍 및 클라우드-장치 메서드 기본 형식을 사용하여 사용자 지정 패턴을 활성화하도록 설정할 수 있습니다. 장치 관리 작업의 다른 예로 공장 재설정, 펌웨어 업데이트, 소프트웨어 업데이트, 전원 관리, 네트워크 및 연결 관리, 데이터 암호화가 있습니다.

## <a name="device-maintenance-windows"></a>장치 유지 관리 기간
일반적으로 서비스 중단 및 가동 중지 시간을 최소화하면서 작업을 수행하도록 장치를 구성합니다.  장치 유지 관리 기간은 장치에서 해당 구성을 업데이트해야 할 경우 시간을 정의하는 데 널리 사용되는 패턴입니다. 백 엔드 솔루션에서는 장치 쌍의 desired 속성을 사용하여 유지 관리 기간을 사용하는 장치에 대한 정책을 정의하고 활성화할 수 있습니다. 장치에서 유지 관리 기간 정책을 수신하면 장치 쌍의 reported 속성을 사용하여 정책의 상태를 보고할 수 있습니다. 그런 다음 백 엔드 앱은 장치 쌍 쿼리를 사용하여 장치 및 각 정책의 규정 준수를 입증합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 장치에서 원격 다시 시작을 트리거하는 데 직접 메서드를 사용했습니다. 보고된 속성을 사용하여 장치에서 마지막으로 다시 시작한 시간을 보고하고, 장치 쌍을 쿼리하여 장치가 클라우드에서 마지막으로 다시 시작한 시간을 확인했습니다.

IoT Hub 및 장치 관리 패턴(예: 원격 무선 펌웨어 업데이트)을 계속 시작하려면 다음을 참조하세요

[자습서: 펌웨어 업데이트를 수행하는 방법][lnk-fwupdate]

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

계속해서 IoT Hub를 시작하려면 [Azure IoT Edge 시작][lnk-gateway-SDK]을 참조하세요.

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

