---
title: "Azure IoT Hub 직접 메서드(.NET/.NET) 사용 | Microsoft Docs"
description: "Azure IoT Hub 직접 메서드를 사용하는 방법입니다. .NET용 Azure IoT 장치 SDK를 사용하여 직접 메서드를 포함한 시뮬레이트된 장치 앱을 구현하며 .NET용 Azure IoT service SDK를 사용하여 직접 메서드를 호출하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: dkshir
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9ce1fbebb6417c10618aa182e3c1d9ddf8132fb6
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="use-direct-methods-netnet"></a>직접 메서드 사용(.NET/.NET)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

이 자습서에서는 다음 2개의 .NET 콘솔 앱을 개발합니다.

* **CallMethodOnDevice**: 시뮬레이트된 장치 앱에서 메서드를 호출하고 응답을 표시하는 백 엔드 앱입니다.
* **SimulateDeviceMethods**: 앞에서 만든 장치 ID로 IoT Hub에 연결하고 클라우드에서 호출하는 메서드에 응답하는 장치를 시뮬레이트하는 콘솔 앱입니다.

> [!NOTE]
> [Azure IoT SDKs][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

프로그래밍 방식으로 장치 ID를 만들려면 [.NET을 사용하여 IoT Hub에 시뮬레이트된 장치 연결][lnk-device-identity-csharp] 문서의 해당 섹션을 참조하세요.


## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 솔루션 백 엔드에서 호출한 메서드에 응답하는 .NET 콘솔 앱을 만듭니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **SimulateDeviceMethods**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 장치 앱][img-createdeviceapp]
    
1. 솔루션 탐색기에서 **SimulateDeviceMethods** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...**를 클릭합니다.
1. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices.client**를 검색합니다. **설치**를 선택하여 **Microsoft.Azure.Devices.Client** 패키지를 설치한 후 사용 약관에 동의합니다. 이 절차에서는 [Azure IoT 장치 SDK][lnk-nuget-client-sdk] NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창 클라이언트 앱][img-clientnuget]
1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 메모한 장치 연결 문자열로 대체합니다.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. 장치에서 직접 메서드를 구현하도록 다음을 추가합니다.

        static Task<MethodResponse> WriteLineToConsole(MethodRequest methodRequest, object userContext)
        {
            Console.WriteLine();
            Console.WriteLine("\t{0}", methodRequest.DataAsJson);
            Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

            string result = "'Input was written to log.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

1. 마지막으로 **Main** 메서드에 다음 코드를 추가하여 IoT Hub에 대한 연결을 열고 메서드 수신기를 초기화합니다.
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "writeLine" method
            Client.SetMethodHandlerAsync("writeLine", WriteLineToConsole, null).Wait();
            Console.WriteLine("Waiting for direct method call\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "writeLine" handler
            Client.SetMethodHandlerAsync("writeLine", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
1. Visual Studio 솔루션 Explorer에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정...**을 클릭합니다. **단일 시작 프로젝트**를 선택한 다음 드롭다운 메뉴에서 **SimulateDeviceMethods** 프로젝트를 선택합니다.        

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. [일시적인 오류 처리][lnk-transient-faults](영문) MSDN 문서에서 제시한 대로 프로덕션 코드에서 다시 시도 정책(예: 연결 다시 시도)을 구현해야 합니다.
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>장치에서 직접 메서드 호출
이 섹션에서는 시뮬레이션된 장치 앱에서 메서드를 호출하고 응답을 표시하는 .NET 콘솔 앱을 만듭니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. .NET Framework 버전이 4.5.1 이상인지 확인합니다. 프로젝트 이름을 **CallMethodOnDevice**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createserviceapp]
2. [솔루션 Explorer]에서 **CallMethodOnDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창][img-servicenuget]

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

1. Visual Studio 솔루션 Explorer에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정...**을 클릭합니다. **단일 시작 프로젝트**를 선택한 다음 드롭다운 메뉴에서 **CallMethodOnDevice** 프로젝트를 선택합니다.

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. .NET 장치 앱 **SimulateDeviceMethods**을 실행합니다. IoT Hub의 메서드 호출에 대한 수신이 시작됩니다. 

    ![장치 앱 실행][img-deviceapprun]
1. 이제 장치가 연결되어 메서드 호출을 기다리고 있으므로 **CallMethodOnDevice** .NET 앱을 실행하여 시뮬레이션된 장치 응용 프로그램에서 메서드를 호출합니다. 콘솔에 작성된 장치 응답을 볼 수 있습니다.
   
    ![서비스 앱 실행][img-serviceapprun]
1. 장치는 다음 메시지를 표시하여 메서드에 반응합니다.
   
    ![장치에 대해 호출된 직접 메서드][img-directmethodinvoked]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 시뮬레이션된 장치 앱이 클라우드에서 호출한 메서드에 반응할 수 있도록 장치 ID를 사용했습니다. 장치에서 메서드를 호출하고 장치의 응답을 표시하는 앱도 만들었습니다. 

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [IoT Hub 시작]
* [여러 장치에서 jobs 예약][lnk-devguide-jobs]

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

<!-- Images. -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-direct-methods/device-app-nuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-service-app.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-direct-methods/service-app-nuget.png
[img-deviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-device-app.png
[img-serviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-service-app.png
[img-directmethodinvoked]: ./media/iot-hub-csharp-csharp-direct-methods/direct-method-invoked.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[IoT Hub 시작]: iot-hub-node-node-getstarted.md

