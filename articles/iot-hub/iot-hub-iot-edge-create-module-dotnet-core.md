---
title: "C#을 사용하여 Azure IoT Edge 모듈 만들기 | Microsoft Docs"
description: "이 자습서에서는 최신 Azure IoT Edge NuGet 패키지, Visual Studio Code 및 C#을 사용하여 BLE 데이터 변환기 모듈을 작성하는 방법을 소개합니다."
services: iot-hub
author: jeffreyCline
manager: sushi
keywords: "azure, iot, 자습서, 모듈, nuget, vscode, csharp, edge"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 91ff3c96e4d7928131eba83c0e8c1951782447be
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>C#을 사용하여 Azure IoT Edge 모듈 만들기

이 자습서에서는 `Visual Studio Code` 및 `C#`을 사용하여 `Azure IoT Edge`에 대한 모듈을 만드는 방법을 소개합니다.

이 자습서에서는 최신 `Azure IoT Edge NuGet` 패키지를 사용하여 환경 설정 및 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 데이터 변환기 모듈을 작성하는 방법을 보여 줍니다. 

>[!NOTE]
이 자습서는 플랫폼 간 호환성을 지원하는 `.NET Core SDK`를 사용합니다. 다음 자습서는 `Windows 10` 운영 체제를 사용하여 작성되었습니다. 이 자습서의 명령 중 일부는 사용자의 `development environment`에 따라 달라질 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

이 섹션에서는 `Azure IoT Edge` 모듈 개발을 위한 환경을 설정합니다. **64비트 Windows** 및 **64비트 Linux(Ubuntu/Debian 8)** 운영 체제 모두에 적용됩니다.

다음과 같은 소프트웨어가 필요합니다.

- [Git 클라이언트](https://git-scm.com/downloads)
- [.NET Core SDK](https://www.microsoft.com/net/core#windowscmd)
- [Contact.java](https://code.visualstudio.com/)

이 샘플의 리포지토리를 복제할 필요는 없지만, 이 자습서에서 언급된 모든 샘플 코드는 다음 리포지토리에 있습니다.

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>시작

1. `.NET Core SDK`를 설치합니다.
2. Visual Studio Code Marketplace에서 `Visual Studio Code` 및 `C# extension`을 설치합니다.

이 [간략한 비디오 자습서](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows)를 통해 `Visual Studio Code` 및 `.NET Core SDK`를 사용하여 시작하는 방법을 알아보세요.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Azure IoT Edge 변환기 모듈 만들기

1. 새 `.NET Core` 클래스 라이브러리 C# 프로젝트를 초기화합니다.
    - 명령 프롬프트(`Windows + R` -> `cmd` -> `enter`)를 엽니다.
    - 만들려는 폴더로 이동하여 `C#` 프로젝트를 만듭니다.
    - **dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**을 입력합니다. 
    - 이 명령은 프로젝트 디렉터리에 `Class1.cs`라고 하는 빈 클래스를 만듭니다.
2. **cd IoTEdgeConverterModule**을 입력하여 방금 클래스 라이브러리 프로젝트를 만든 폴더로 이동합니다.
3. **code**를 입력하여 `Visual Studio Code`에서 프로젝트를 엽니다.
4. 프로젝트가 `Visual Studio Code`에서 열리면 **IoTEdgeConverterModule.csproj**를 클릭하여 다음 이미지에 나와 있는 것처럼 파일을 엽니다.

    ![Visual Studio Code 편집 창](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. 닫는 `PropertyGroup` 태그와 닫는 `Project` 태그(이전 이미지에서 6번 줄) 사이 다음 코드 조각에 표시된 `XML` blob을 삽입하고 `Ctrl` + `S` 키를 눌러 파일을 저장합니다.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. `.csproj` 파일을 저장한 후 `Visual Studio Code`에서 다음 이미지와 같이 `unresolved dependencies` 대화 상자가 있는 메시지가 표시됩니다. 

    ![Visual Studio Code 복원 종속성 대화 상자](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) `Restore`를 클릭하여 추가했던 `PackageReferences`를 포함하는 프로젝트 `.csproj` 파일의 모든 참조를 복원합니다. 

    b) `Visual Studio Code`는 자동으로 프로젝트 `obj` 폴더의 `project.assets.json` 파일을 만듭니다. 이 파일에는 더 신속한 후속 복원을 위한 프로젝트의 종속성에 대한 정보가 포함되어 있습니다.
 
    >[!NOTE]
    `.NET Core Tools`는 MSBuild 기반입니다. 즉 `.csproj` 프로젝트 파일이 `project.json` 대신 만들어짐을 의미합니다.

    - `Visual Studio Code`에서 이에 대한 확인 메시지가 표시되지 않으면 수동으로 수행할 수 있습니다. `Ctrl` + `backtick` 키 또는 `View` -> `Integrated Terminal` 메뉴를 사용하여 `Visual Studio Code` 통합 터미널 창을 엽니다.
    - `Integrated Terminal` 창에서 **dotnet restore**를 입력합니다.
    
7. `Class1.cs` 파일의 이름을 `BleConverterModule.cs`로 변경합니다. 

    a) 파일의 이름을 바꾸려면 먼저 파일을 클릭한 다음 `F2` 키를 누릅니다.
    
    b) 다음 이미지에 표시된 것처럼 새 이름 **BleConverterModule**을 입력합니다.

    ![Visual Studio Code 클래스 이름 바꾸기](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. 다음 코드 조각을 `BleConverterModule.cs` 파일로 복사하여 붙여넣어 `BleConverterModule.cs` 파일의 기존 코드를 바꿉니다.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. `Ctrl` + `S` 키를 눌러 파일을 저장합니다.

10. 다음 이미지에 표시된 것처럼 `Ctrl` + `N` 키를 눌러 `Untitled-1`이라는 새 파일을 만듭니다.

    ![Visual Studio Code 새 파일](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. 시뮬레이션된 `BLE` 장치에서 수신하는 `JSON` 개체를 deserialize하려면 다음 코드를 `Untitled-1` 파일 코드 편집기 창에 복사합니다. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. `Ctrl` + `Shift` + `S` 키를 눌러 파일을 `BleData.cs`로 저장합니다.
    - 다음 이미지에 표시된 대로 다른 이름으로 저장 대화 상자의 `Save as Type` 드롭다운 메뉴에서 `C# (*.cs;*.csx)`을 선택합니다.

    ![Visual Studio Code 다른 이름으로 저장 대화 상자](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. `Ctrl` + `N` 키를 눌러 `Untitled-1`이라는 새 파일을 만듭니다.

14. 다음 코드 조각을 복사하여 `Untitled-1` 파일에 붙여넣습니다. 이 클래스는 `BleConverterModule`에서 수신한 데이터를 출력하는 데 사용하는 `Azure IoT Edge` 모듈입니다.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. `Ctrl` + `Shift` + `S` 키를 눌러 파일을 `DotNetPrinterModule.cs`로 저장합니다.
    - 다른 이름으로 저장 대화 상자의 `Save as Type` 드롭다운 메뉴에서 `C# (*.cs;*.csx)`을 선택합니다.

16. `Ctrl` + `N` 키를 눌러 `Untitled-1`이라는 새 파일을 만듭니다.

17. `BleConverterModule`에서 수신하는 `JSON` 개체를 deserialize하려면 다음 코드 조각을 `Untitled-1` 파일로 복사하여 붙여넣습니다. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. `Ctrl` + `Shift` + `S` 키를 눌러 파일을 `BleConverterData.cs`로 저장합니다.
    - 다른 이름으로 저장 대화 상자의 `Save as Type` 드롭다운 메뉴에서 `C# (*.cs;*.csx)`을 선택합니다.

19. `Ctrl` + `N` 키를 눌러 `Untitled-1`이라는 새 파일을 만듭니다.

20. 다음 코드 조각을 복사하여 `Untitled-1` 파일에 붙여넣습니다.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. `Ctrl` + `Shift` + `S` 키를 눌러 파일을 `gw-config.json`로 저장합니다.
    - 다른 이름으로 저장 대화 상자의 `Save as Type` 드롭다운 메뉴에서 `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`을 선택합니다.

22. 출력 디렉터리로 구성 파일을 복사하도록 설정하려면 다음 XML blob으로 `IoTEdgeConverterModule.csproj`를 업데이트합니다.

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - 업데이트된 `IoTEdgeConverterModule.csproj`는 다음 이미지와 같아야 합니다.

    ![Visual Studio Code 업데이트된 .csproj 파일](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. `Ctrl` + `N` 키를 눌러 `Untitled-1`이라는 새 파일을 만듭니다.

24. 다음 코드 조각을 복사하여 `Untitled-1` 파일에 붙여넣습니다.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. `Ctrl` + `Shift` + `S` 키를 눌러 파일을 `binplace.ps1`로 저장합니다.
    - 다른 이름으로 저장 대화 상자의 `Save as Type` 드롭다운 메뉴에서 `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`을 선택합니다.

26. `Ctrl` + `Shift` + `B` 키를 눌러 프로젝트를 빌드합니다. 처음으로 프로젝트를 빌드할 때 `Visual Studio Code`에서는 다음 이미지에 표시된 대로 `No build task defined.` 대화 상자를 표시합니다.

    ![Visual Studio Code 빌드 작업 대화 상자](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) `Configure Build Task` 단추를 클릭합니다.

    b) `Select a Task Runner` 대화 상자 드롭다운 메뉴에서 다음 이미지에 표시된 대로 `.NET Core`를 선택합니다. 

    ![Visual Studio Code 작업 선택 대화 상자](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) `.NET Core` 항목을 클릭하면 `tasks.json` 파일이 `.vscode` 디렉터리에 생성되고 `code editor` 창에서 파일이 열립니다. 이 파일은 수정할 필요가 없으므로 탭을 닫습니다.

27.  `Ctrl` + `backtick` 키 또는 메뉴 `View` -> `Integrated Terminal`를 사용하여 `Visual Studio Code` 통합 터미널 창을 열고 **.\binplace.ps1**을 `PowerShell` 명령 프롬프트에 입력합니다. 이 명령은 모든 종속성을 출력 디렉터리에 복사합니다.

28. **cd .\bin\Debug\netstandard1.3**을 입력하여 `Integrated Terminal` 창의 프로젝트 출력 디렉터리로 이동합니다.

29. **.\gw.exe gw-config.json**을 `Integrated Terminal` 창 프롬프트에 입력하여 샘플 프로젝트를 실행합니다. 
    - 이 자습서의 단계를 자세히 수행했다면 이제 다음 이미지에 표시된 대로 `Azure IoT Edge BLE Data Converter Module` 샘플 프로젝트를 실행할 수 있습니다.
    
        ![Visual Studio Code에서 실행하는 시뮬레이션된 장치 예제](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - 응용 프로그램을 종료하려면 `<Enter>` 키를 누릅니다.

>[!IMPORTANT]
`Ctrl` + `C`를 사용하여 `IoT Edge` 게이트웨이 응용 프로그램을 종료하는 것이 좋습니다(즉, **gw.exe**). 이 작업은 프로세스를 비정상적으로 종료할 수 있기 때문입니다.


