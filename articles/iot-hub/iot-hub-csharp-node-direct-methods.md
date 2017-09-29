---
title: "Azure IoT Hub 직접 메서드(.NET/Node) 사용 | Microsoft Docs"
description: "Azure IoT Hub 직접 메서드를 사용하는 방법입니다. Node.js용 Azure IoT 장치 SDK를 사용하여 직접 메소드를 포함한 시뮬레이션된 장치 앱을 구현하며 .NET용 Azure IoT service SDK를 사용하여 직접 메소드를 호출하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: nberdy
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 76f1d32b4afeacae1488b4cf28be6c8cf7f4ea37
ms.contentlocale: ko-kr
ms.lasthandoff: 09/28/2017

---
# <a name="use-direct-methods-netnode"></a>직접 메서드 사용(.NET/Node)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

이 자습서에서는 .NET 및 Node.js 콘솔 앱을 개발하겠습니다.

* **CallMethodOnDevice.js**: .NET 백엔드 앱으로 시뮬레이션된 장치 앱에서 메서드를 호출하고 응답을 표시합니다.
* **SimulatedDevice.js**: 앞에서 만든 장치 ID로 IoT Hub에 연결하고 클라우드에서 호출하는 메서드에 응답하는 장치를 시뮬레이션하는 Node.js 앱입니다.

> [!NOTE]
> [Azure IoT SDKs][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* Node.js 버전 4.0.x 이상
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 솔루션 백 엔드에서 호출한 메서드에 응답하는 Node.js 콘솔 앱을 만듭니다.

1. **simulateddevice**라는 빈 폴더를 새로 만듭니다. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **simulateddevice** 폴더에 새 파일을 만들고 이름을 **SimulatedDevice.js**으로 지정합니다.
4. **SimulatedDevice.js** 파일 앞에 다음 `require` 문을 추가합니다.
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. **connectionString** 변수를 추가하고 이 변수를 사용하여 **DeviceClient** 인스턴스를 만듭니다. **{장치 연결 문자열}**을 *장치 ID 만들기* 섹션에서 생성한 장치 연결 문자열로 변경합니다.
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. 다음 함수를 추가하여 장치에서 직접 메서드를 구현합니다.
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. IoT Hub에 대한 연결을 열고 메서드 수신기를 초기화합니다.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. **SimulatedDevice.js** 파일을 저장한 후 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. [일시적인 오류 처리][lnk-transient-faults](영문) MSDN 문서에서 제시한 대로 프로덕션 코드에서 다시 시도 정책(예: 연결 다시 시도)을 구현해야 합니다.
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>장치에서 직접 메서드 호출
이 섹션에서는 시뮬레이션된 장치 앱에서 메서드를 호출하고 응답을 표시하는 .NET 콘솔 앱을 만듭니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. .NET Framework 버전이 4.5.1 이상인지 확인합니다. 프로젝트 이름을 **CallMethodOnDevice**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][10]
2. [솔루션 Explorer]에서 **CallMethodOnDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창][11]

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 만든 허브의 IoT Hub 연결 문자열로 대체합니다.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. **Program** 클래스에 다음 메서드를 추가합니다.
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    이 메서드는 `myDeviceId` 디바이스에서 `writeLine` 이름의 직접 메서드를 호출합니다. 그런 다음 장치에서 제공한 응답을 콘솔에 씁니다. 장치에서 응답할 시간 제한 값을 지정할 수 있는 방법에 주의하세요.
7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. Visual Studio 솔루션 Explorer에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정...**을 클릭합니다. **단일 시작 프로젝트**를 선택한 다음 드롭다운 메뉴에서 **CallMethodOnDevice** 프로젝트를 선택합니다.

2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 메서드 호출에 대한 대기를 시작합니다.
   
    ```
    node SimulatedDevice.js
    ```
   열려는 시뮬레이션된 장치를 기다립니다. ![][7]
3. 이제 장치가 연결되어 메서드 호출을 기다리고 있으므로 **CallMethodOnDevice** .NET 앱을 실행하여 시뮬레이션된 장치 응용 프로그램에서 메서드를 호출합니다. 콘솔에 작성된 장치 응답을 볼 수 있습니다.
   
    ![][8]
4. 장치는 다음 메시지를 표시하여 메서드에 반응합니다.
   
    ![][9]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 시뮬레이션된 장치 앱이 클라우드에서 호출한 메서드에 반응할 수 있도록 장치 ID를 사용했습니다. 장치에서 메서드를 호출하고 장치의 응답을 표시하는 앱도 만들었습니다. 

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [IoT Hub 시작]
* [여러 장치에서 jobs 예약][lnk-devguide-jobs]

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[IoT Hub 시작]: iot-hub-node-node-getstarted.md

