---
title: "Azure IoT Hub(.NET/노드)를 사용하여 작업 예약 | Microsoft 문서"
description: "여러 장치에서 직접 메서드를 호출하여 Azure IoT Hub 작업을 예약하는 방법입니다. Node.js용 Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치 앱을 구현하고 .NET용 Azure IoT 서비스 SDK를 사용하여 작업을 실행하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2017
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: fd53e73d6a686581ea2b807ae66716fc36a99ad4
ms.lasthandoff: 12/07/2016


---
# <a name="schedule-and-broadcast-jobs"></a>작업 예약 및 브로드캐스트
[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

## <a name="introduction"></a>소개
Azure IoT Hub는 백 엔드 앱에서 수백만 개의 장치를 예약 및 업데이트하는 작업을 만들고 추적할 수 있게 하는 완전히 관리되는 서비스입니다.  작업(job)은 다음과 같은 작업(action)에 사용될 수 있습니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

개념적으로 작업(job)은 이러한 작업(action) 중 하나를 래핑하고 장치 쌍 쿼리로 정의된 장치 집합에 대해 실행 진행 상태를 추적합니다.  예를 들어 백 엔드 앱은 작업을 사용하여 장치 쌍 쿼리로 지정되고 향후에 예약된 10,000개 장치에서 다시 부팅 메서드를 호출할 수 있습니다.  그런 다음 응용 프로그램은 해당하는 각 장치에서 재부팅 메서드를 수신 및 실행함에 따라 진행 상태를 추적할 수 있습니다.

이러한 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

* 장치 쌍 및 속성: [장치 쌍 시작][lnk-get-started-twin] 및 [자습서: 장치 쌍 속성을 사용하는 방법][lnk-twin-props]
* 직접 메서드: [IoT Hub 개발자 가이드 - 직접 메서드][lnk-dev-methods] 및 [자습서: 직접 메서드 사용][lnk-c2d-methods]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 백 엔드 앱에서 호출할 수 있는 **lockDoor**를 사용하는 직접 메서드가 있는 시뮬레이션된 장치 앱 만들기
* 작업을 사용하여 시뮬레이션된 장치 앱에서 **lockDoor** 직접 메서드를 호출하고, 장치 작업을 사용하여 desired 속성을 업데이트하는 .NET 콘솔 앱을 만듭니다.

이 자습서의 끝 부분에는 다음과 같은 Node.js 콘솔 장치 앱과 .NET(C#) 콘솔 백 엔드 앱이 있습니다.

**simDevice.js** - 장치 ID로 IoT Hub에 연결하고 **lockDoor** 직접 메서드를 수신합니다.

**ScheduleJob** - 시뮬레이션된 장치 앱에서 직접 메서드를 호출하고 job을 사용하여 장치 쌍의 desired 속성을 업데이트합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Microsoft Visual Studio 2015.
* Node.js 버전 0.12.x 이상, <br/>  Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비][lnk-dev-setup]에서 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>직접 메서드를 호출하고 장치 쌍의 속성을 업데이트하기 위한 작업 예약
이 섹션에서는 직접 메서드를 사용하여 장치에서 원격 **lockDoor**를 시작하는 .NET 콘솔 앱(C# 사용)을 만들고 장치 쌍의 속성을 업데이트합니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **ScheduleJob**으로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createapp]

2. [솔루션 Explorer]에서 **ScheduleJob** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    ![NuGet 패키지 관리자 창][img-servicenuget]
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Devices;
        
5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자를 이전 섹션에서 만든 허브의 IoT Hub 연결 문자열로 대체합니다.
   
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        
6. **Program** 클래스에 다음 메서드를 추가합니다.
   
        public static async Task MonitorJob(string jobId)
        {
            JobResponse result;
            do
            {
                result = await jobClient.GetJobAsync(jobId);
                Console.WriteLine("Job Status : " + result.Status.ToString());
                Thread.Sleep(2000);
            } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
        }
                
7. **Program** 클래스에 다음 메서드를 추가합니다.

        public static async Task StartMethodJob(string jobId)
        {
            CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

            JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
                "deviceId='myDeviceId'",
                directMethod,
                DateTime.Now,
                10);

            Console.WriteLine("Started Method Job");
        }

8. **Program** 클래스에 다음 메서드를 추가합니다.

        public static async Task StartTwinUpdateJob(string jobId)
        {
            var twin = new Twin();
            twin.Properties.Desired["Building"] = "43";
            twin.Properties.Desired["Floor"] = "3";
            twin.ETag = "*";

            JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
                "deviceId='myDeviceId'",
                twin,
                DateTime.Now,
                10);

            Console.WriteLine("Started Twin Update Job");
        }
 

9. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
        jobClient = JobClient.CreateFromConnectionString(connString);

        string methodJobId = Guid.NewGuid().ToString();

        StartMethodJob(methodJobId);
        MonitorJob(methodJobId).Wait();
        Console.WriteLine("Press ENTER to run the next job.");
        Console.ReadLine();

        string twinUpdateJobId = Guid.NewGuid().ToString();

        StartTwinUpdateJob(twinUpdateJobId);
        MonitorJob(twinUpdateJobId).Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
                   
10. 솔루션을 빌드하십시오.

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 클라우드에서 호출한 직접 메서드에 응답하는 Node.js 콘솔 앱을 만듭니다. 이 메서드는 시뮬레이션된 장치 재부팅을 트리거하고, reported 속성을 사용하여 장치 및 해당 장치가 마지막으로 재부팅한 시간을 확인하는 장치 쌍 쿼리를 사용하도록 설정합니다.

1. **simDevice**라는 빈 폴더를 새로 만듭니다.  **simDevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **simDevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **simDevice** 폴더에 새 **simDevice.js** 파일을 만듭니다.
4. **simDevice.js** 파일 앞에 다음 'require' 문을 추가합니다.
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. **connectionString** 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. 다음 함수를 추가하여 **lockDoor** 메서드를 처리합니다.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. 다음 코드를 추가하여 **lockDoor** 메서드에 대한 처리기를 등록합니다.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. **simDevice.js** 파일을 저장하고 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
> 
> 

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **simDevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    node simDevice.js
    ```
2. **ScheduleJob** C# 콘솔 앱 실행 - **ScheduleJob** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **디버그**, **새 인스턴스 시작**을 차례로 선택합니다.

3. 장치 및 백 엔드 앱 모두에서 출력이 표시됩니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 장치에 대한 직접 메서드를 예약하고 장치 쌍의 속성을 업데이트하는 데 작업을 사용했습니다.

IoT Hub 및 장치 관리 패턴(예: 원격 무선 펌웨어 업데이트)을 계속 시작하려면 다음을 참조하세요

[자습서: 펌웨어 업데이트를 수행하는 방법][lnk-fwupdate]

IoT Hub 시작을 계속하려면 [IoT Gateway SDK 시작][lnk-gateway-SDK]을 참조하세요.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

