---
title: "Azure IoT Hub를 사용하여 장치 펌웨어 업데이트(.NET/Node) | Microsoft Docs"
description: "장치 펌웨어 업데이트를 시작하려면 Azure IoT Hub에서 장치 관리를 사용하는 방법입니다. Node.js용 Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치 앱을 구현하고 .NET용 Azure IoT 서비스 SDK를 사용하여 펌웨어 업데이트를 트리거하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: 5b8aaa7e7b04224fd51c264822d619866e0161af


---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>장치 관리를 사용하여 장치 펌웨어 업데이트(.NET/Node)를 시작합니다.
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>소개
[장치 관리 시작][lnk-dm-getstarted] 자습서에서 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod] 기본 형식을 사용하여 장치를 원격으로 다시 부팅하는 방법을 살펴보았습니다. 이 자습서에서는 동일한 IoT Hub 기본 형식을 사용하고 시뮬레이션된 종단 간 펌웨어 업데이트를 수행하는 방법을 보여 줍니다.  이 패턴은 [Raspberry Pi 장치 구현 샘플][lnk-rpi-implementation]에 대한 펌웨어 업데이트 구현에 사용됩니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* IoT Hub를 통해 시뮬레이션된 장치 앱에서 firmwareUpdate 직접 메서드를 호출하는 .NET 콘솔 앱을 만듭니다.
* 펌웨어 이미지 다운로드를 기다리고, 펌웨어 이미지를 다운로드하고, 마지막으로 펌웨어 이미지를 적용하는 다단계 프로세스를 거치는 firmwareUpdate 직접 메소드를 구현하는 시뮬레이션된 장치 앱을 만듭니다.  각 단계를 실행하는 동안 장치에서는 reported 속성을 사용하여 진행 상황을 업데이트합니다.

이 자습서의 끝 부분에는 다음과 같은 Node.js 콘솔 장치 앱과 .NET(C#) 콘솔 백 엔드 앱이 있습니다.

**dmpatterns_fwupdate_service.js** - 시뮬레이션된 장치 앱에서 직접 메서드를 호출하고 응답을 표시하며 업데이트된 reported 속성을 주기적으로(매 500밀리초) 표시합니다.

**TriggerFWUpdate** - 앞에서 만든 장치 ID를 사용하여 IoT Hub 에 연결하고, firmwareUpdate 직접 메서드를 수신하며, 펌웨어 업데이트를 시뮬레이션하기 위한 다단계 프로세스(이미지 다운로드 대기, 새 이미지 다운로드, 마지막으로 이미지 적용 등)를 실행합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Microsoft Visual Studio 2015.
* Node.js 버전 0.12.x 이상, <br/>  Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비][lnk-dev-setup]에서 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

IoT hub 허브를 만들고 IoT Hub 연결 문자열을 확보하려면 [장치 관리 시작](iot-hub-csharp-node-device-management-get-started.md) 문서의 내용을 수행하세요.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트 트리거
이 섹션에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 시작하고, 장치 쌍 쿼리를 사용하여 해당 장치에서 활성 펌웨어 업데이트의 상태를 주기적으로 가져오는 .NET 콘솔 앱(C# 사용)을 만듭니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **TriggerFWUpdate**로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createapp]

2. [솔루션 Explorer]에서 **TriggerFWUpdate** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    ![NuGet 패키지 관리자 창][img-servicenuget]
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Devices;
        
5. **Program** 클래스에 다음 필드를 추가합니다. 여러 자리 표시자 값을 이전 섹션에서 만든 허브에 대한 IoT Hub 연결 문자열로 바꿉니다.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. **Program** 클래스에 다음 메서드를 추가합니다.
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. **Program** 클래스에 다음 메서드를 추가합니다.

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. 솔루션을 빌드하십시오.

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 Node.js 콘솔 앱 만들기
* 시뮬레이션된 펌웨어 업데이트 트리거
* reported 속성을 사용하여 장치 및 해당 장치가 마지막으로 펌웨어 업데이트를 완료한 시간을 확인하는 장치 쌍 쿼리를 사용하도록 설정

1. **manageddevice**라는 빈 폴더를 새로 만듭니다.  **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **manageddevice** 폴더에 새 **dmpatterns_fwupdate_device.js** 파일을 만듭니다.
4. 다음 'require' 문을 **dmpatterns_fwupdate_device.js** 파일의 시작 부분에 추가합니다.
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. reported 속성을 업데이트하는 데 사용되는 다음 함수를 추가합니다.
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
7. 펌웨어 이미지의 다운로드 및 적용을 시뮬레이션하는 다음 함수를 추가합니다.
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. reported 속성을 통해 펌웨어 업데이트 상태를 다운로드 대기 중 상태로 업데이트하는 다음 함수를 추가합니다.  일반적으로 장치는 사용 가능한 업데이트에 대한 알림을 받으며 관리자가 정의한 정책이 장치가 업데이트를 다운로드하고 적용하도록 유발합니다.  여기가 해당 정책을 활성화하는 논리가 실행되는 부분입니다.  간단히 하기 위해 4초를 지연시키고 펌웨어 이미지 다운로드를 진행합니다. 
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. reported 속성을 통해 펌웨어 업데이트 상태를 펌웨어 이미지 다운로드 중 상태로 업데이트하는 다음 함수를 추가합니다.  펌웨어 다운로드를 시뮬레이션하고 펌웨어 업데이트 상태를 업데이트하여 다운로드 성공 또는 실패 여부를 알려줍니다.
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. reported 속성을 통해 펌웨어 업데이트 상태를 펌웨어 이미지 적용 중 상태로 업데이트하는 다음 함수를 추가합니다.  펌웨어 이미지 적용을 시뮬레이션하고 펌웨어 업데이트 상태를 업데이트하여 적용 성공 또는 실패 여부를 알려줍니다.
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. **firmwareUpdate** 메서드를 처리하고 다단계 펌웨어 업데이트 프로세스를 시작하는 다음 함수를 추가합니다.
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. 마지막으로 IoT Hub에 장비로 연결하는 다음 코드를 추가합니다. 
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
> 
> 

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. **TriggerFWUpdate** C# 콘솔 앱 실행 - **TriggerFWUpdate** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **디버그**, **새 인스턴스 시작**을 차례로 선택합니다.

3. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 트리거하고, reported 속성을 주기적으로 사용하여 펌웨어 업데이트 프로세스의 진행 상황을 이해했습니다.  

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/


<!--HONumber=Dec16_HO1-->


