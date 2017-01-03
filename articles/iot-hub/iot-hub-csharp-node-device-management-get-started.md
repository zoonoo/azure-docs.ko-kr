---
title: "Azure IoT Hub 장치 관리 시작 | Microsoft 문서"
description: "이 자습서에서는 Azure IoT Hub에서 장치 관리를 시작하는 방법을 보여 줍니다."
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
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: cf9741e7bc30ccb5e6b8f79dad7c8ef725cd683a


---
# <a name="tutorial-get-started-with-device-management"></a>자습서: 장치 관리 시작

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]
## <a name="introduction"></a>소개
IoT 백 엔드 앱은 Azure IoT Hub의 기본 형식, 즉 장치 쌍 및 직접 메서드를 사용하여 장치에서 장치 관리 작업을 원격으로 시작하고 모니터링할 수 있습니다.  이 문서에서는 IoT Hub를 사용하여 원격 장치를 다시 부팅하고 모니터링하기 위해 IoT 백 엔드 앱과 장치가 함께 작동하는 방법에 대한 지침과 코드를 제공합니다.

클라우드 기반 백 엔드 앱에서 장치에 대한 장치 관리 작업을 원격으로 시작하고 모니터링하려면 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod]와 같은 IoT Hub 기본 형식을 사용합니다. 이 자습서에서는 백 엔드 앱 및 장치가 함께 작동하여 IoT Hub에서 원격 장치 재부팅을 시작 및 모니터링하는 방식을 보여 줍니다.

직접 메서드를 사용하여 클라우드의 백 엔드 앱에서 장치 관리 작업(예: 재부팅, 공장 기본 설정으로 복원 및 펌웨어 업데이트)을 시작합니다. 장치는 다음과 같은 역할을 합니다.

* IoT Hub에서 보낸 메서드 요청 처리.
* 장치에서 해당하는 장치 특정 작업 시작.
* reported 속성을 통해 IoT Hub에 상태 업데이트 제공

클라우드에서 백 엔드 앱을 사용하여 장치 쌍 쿼리를 실행하고 장치 관리 작업의 진행 상태를 보고할 수 있습니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 장치 ID를 만듭니다.
* 클라우드에서 재부팅을 호출할 수 있는 직접 메서드가 포함된 시뮬레이션된 장치 앱을 만듭니다.
* IoT Hub를 통해 시뮬레이션된 장치 앱에서 재부팅 직접 메서드를 호출하는 콘솔 응용 프로그램을 만듭니다.

이 자습서의 끝 부분에는 다음과 같은 Node.js 콘솔 장치 앱과 .NET(C#) 콘솔 백 엔드 앱이 있습니다.

**dmpatterns_getstarted_device.js** - 이전에 만든 장치 ID로 IoT Hub에 연결하며 재부팅 직접 메서드를 수신하고 물리적 재부팅을 시뮬레이션하며 마지막 재부팅 시간을 보고합니다.

**TriggerReboot** - 시뮬레이션된 장치 앱에서 직접 메서드를 호출하고 응답을 표시하며 업데이트된 reported 속성을 표시합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Microsoft Visual Studio 2015.
* Node.js 버전 0.12.x 이상, <br/>  Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비][lnk-dev-setup]에서 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 재부팅 트리거
이 섹션에서는 직접 메서드를 사용하여 장치에서 원격 재부팅을 시작하고, 장치 쌍 쿼리를 사용하여 해당 장치의 마지막 재부팅 시간을 찾는 .NET 콘솔 앱(C# 사용)을 만듭니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **TriggerReboot**로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createapp]

2. [솔루션 탐색기]에서 **TriggerReboot** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    ![NuGet 패키지 관리자 창][img-servicenuget]
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Devices;
        
5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 만든 IoT Hub 및 대상 장치의 연결 문자열로 바꿉니다.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. **Program** 클래스에 다음 메서드를 추가합니다.  이 코드는 재부팅 장치에 대한 장치 쌍을 가져오고 reported 속성을 출력합니다.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. **Program** 클래스에 다음 메서드를 추가합니다.  이 코드는 직접 메서드를 사용하여 장치에서 재부팅을 시작합니다.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. 솔루션을 빌드하십시오.

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 Node.js 콘솔 앱 만들기
* 시뮬레이션된 장치 재부팅 트리거
* reported 속성을 사용하여 장치 및 해당 장치가 마지막으로 재부팅한 시간을 확인하는 장치 쌍 쿼리를 사용하도록 설정

1. **manageddevice**라는 빈 폴더를 새로 만듭니다.  **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **manageddevice** 폴더에 새 **dmpatterns_getstarted_device.js** 파일을 만듭니다.
4. 다음 'require' 문을 **dmpatterns_getstarted_device.js** 파일의 시작 부분에 추가합니다.
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 변수를 추가하고 이 변수를 사용하여 장치 클라이언트를 만듭니다.  연결 문자열을 장치 연결 문자열로 바꿉니다.  
   
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
7. 다음 코드를 추가하여 IoT Hub에 대한 연결을 열고 직접 메서드 수신기를 시작합니다.
   
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
   
   [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.


## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. **TriggerReboot** C# 콘솔 앱 실행 - **TriggerReboot** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **디버그**, **새 인스턴스 시작**을 차례로 선택합니다.

3. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다.

## <a name="customize-and-extend-the-device-management-actions"></a>장치 관리 작업 사용자 지정 및 확장
IoT 솔루션에서 정의된 장치 관리 패턴 집합을 확장하거나 장치 쌍 및 클라우드-장치 메서드 기본 형식을 사용하여 사용자 지정 패턴을 활성화하도록 설정할 수 있습니다. 장치 관리 작업의 다른 예로 공장 재설정, 펌웨어 업데이트, 소프트웨어 업데이트, 전원 관리, 네트워크 및 연결 관리, 데이터 암호화가 있습니다.

## <a name="device-maintenance-windows"></a>장치 유지 관리 기간
일반적으로 서비스 중단 및 가동 중지 시간을 최소화하면서 작업을 수행하도록 장치를 구성합니다.  장치 유지 관리 기간은 장치에서 해당 구성을 업데이트해야 할 경우 시간을 정의하는 데 널리 사용되는 패턴입니다. 백 엔드 솔루션에서는 장치 쌍의 desired 속성을 사용하여 유지 관리 기간을 사용하는 장치에 대한 정책을 정의하고 활성화할 수 있습니다. 장치에서 유지 관리 기간 정책을 수신하면 장치 쌍의 reported 속성을 사용하여 정책의 상태를 보고할 수 있습니다. 그런 다음 백 엔드 앱은 장치 쌍 쿼리를 사용하여 장치 및 각 정책의 규정 준수를 입증합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 직접 메서드를 사용하여 장치에서 원격 재부팅을 트리거하고, reported 속성을 사용하여 장치에서 마지막으로 재부팅한 시간을 보고하고, 장치 쌍을 쿼리하여 장치가 클라우드에서 마지막으로 다시 부팅한 시간을 확인했습니다.

IoT Hub 및 장치 관리 패턴(예: 원격 무선 펌웨어 업데이트)을 계속 시작하려면 다음을 참조하세요

[자습서: 펌웨어 업데이트를 수행하는 방법][lnk-fwupdate]

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

IoT Hub 시작을 계속하려면 [IoT Gateway SDK 시작][lnk-gateway-SDK]을 참조하세요.

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

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
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/


<!--HONumber=Nov16_HO5-->


