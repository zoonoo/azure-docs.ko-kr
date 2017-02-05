---
title: "Azure IoT Hub를 사용하여 장치 펌웨어 업데이트(노드) | Microsoft Docs"
description: "장치 펌웨어 업데이트를 시작하려면 Azure IoT Hub에서 장치 관리를 사용하는 방법입니다. Node.js용 Azure IoT SDK를 사용하여 시뮬레이션된 장치 앱 및 펌웨어 업데이트를 트리거하는 서비스 앱을 구현합니다."
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
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: fdc8dca46f5bd0feb8e6ce24af32327be4c8ebb6


---
# <a name="use-device-management-to-initiate-a-device-firmware-update-node"></a>장치 관리를 사용하여 장치 펌웨어 업데이트(노드)를 시작합니다.
## <a name="introduction"></a>소개
[장치 관리 시작][lnk-dm-getstarted] 자습서에서 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod] 기본 형식을 사용하여 장치를 원격으로 다시 부팅하는 방법을 보았습니다. 이 자습서는 동일한 IoT Hub 기본 형식을 사용하며 종단 간 시뮬레이션된 펌웨어 업데이트를 수행하는 방법을 보여주고 지침을 제공합니다.  이러한 패턴이 Intel Edison 장치 샘플에 대한 펌웨어 업데이트 구현에 사용됩니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* IoT Hub를 통해 시뮬레이션된 장치 앱에서 firmwareUpdate 직접 메서드를 호출하는 Node.js 콘솔 앱을 만듭니다.
* firmwareUpdate 직접 메서드를 구현하는 시뮬레이션된 장치 앱을 만든 후에 펌웨어 이미지 다운로드를 위해 대기하는 다단계 프로세스를 거치고, 펌웨어 이미지를 다운로드하고, 마지막으로 펌웨어 이미지를 적용합니다.  각 단계를 실행하는 동안 장치에서는 reported 속성을 사용하여 진행 상황을 업데이트합니다.

이 자습서를 마치면 두 가지 Node.js 콘솔 앱이 만들어집니다.

**dmpatterns_fwupdate_service.js** - 시뮬레이션된 장치 앱에서 직접 메서드를 호출하고 응답을 표시하며 업데이트된 reported 속성을 주기적으로(매 500밀리초) 표시합니다.

**dmpatterns_fwupdate_device.js**, 이전에 생성한 장치 ID를 사용하여 IoT hub 에 연결하고, firmwareUpdate 직접 메서드를 수신하고, 펌웨어 업데이트를 시뮬레이션하기 위한 다단계 프로세스를 실행합니다. 여기에는 이미지 다운로드를 위한 대기, 새 이미지 다운로드, 마지막으로 이미지 적용이 포함됩니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 0.12.x 이상, <br/>  Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비][lnk-dev-setup]에서 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

IoT hub 허브를 만들고 IoT Hub 연결 문자열을 확보하려면 [장치 관리 시작](iot-hub-node-node-device-management-get-started.md) 문서의 내용을 수행하세요.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

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
11. firmwareUpdate 메서드를 처리하고 다단계 펌웨어 업데이트 프로세스를 초기화하는 다음 함수를 추가합니다.
    
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
12. 마지막으로 IoT hub를 장비로 연결하는 다음 코드를 추가합니다. 
    
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

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트 트리거
이 섹션에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 초기화하는 Node.js 콘솔 앱을 만들고 장치 쌍 쿼리를 사용하여 해당 장치에서 활성 펌웨어 업데이트의 상태를 주기적으로 가져옵니다.

1. **triggerfwupdateondevice**라는 빈 폴더를 새로 만듭니다.  **triggerfwupdateondevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **triggerfwupdateondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-hub --save
    ```
3. 텍스트 편집기를 사용하여 **triggerfwupdateondevice** 폴더에 새 **dmpatterns_getstarted_service.js** 파일을 만듭니다.
4. 다음 'require' 문을 **dmpatterns_getstarted_service.js** 파일의 시작 부분에 추가합니다.
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 다음 변수 선언을 추가하고 자리 표시자 값을 바꿉니다.
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. firmwareUpdate 보고된(reported) 속성의 값을 찾아서 표시하도록 다음 함수를 추가합니다.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. 대상 장치를 다시 시작하기 위해 firmwareUpdate 메서드를 호출하도록 다음 함수를 추가합니다.
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. 마지막으로 펌웨어 업데이트 시퀀스를 시작하고 reported 속성에 대한 주기적 표시를 시작하기 위해 다음 함수를 코드에 추가합니다.
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. **dmpatterns_fwupdate_service.js** 파일을 저장하고 닫습니다.

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. **triggerfwupdateondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 원격 다시 시작을 트리거하고 마지막 다시 시작 시간을 찾기 위해 장치 쌍에 대한 쿼리를 수행합니다.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 트리거하고, reported 속성을 주기적으로 사용하여 펌웨어 업데이트 프로세스의 진행 상황을 이해했습니다.  

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Dec16_HO1-->


