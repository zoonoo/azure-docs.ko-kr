---
title: Raspberry Pi를 Azure IoT Central 애플리케이션에 연결(C#) | Microsoft Docs
description: 장치 개발자는를 사용 하 여 C#Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 설명 합니다.
author: viv-liu
ms.author: viviali
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d15e58d53e53779b1db1369c4e698032d524f096
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067621"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Azure IoT Central 애플리케이션에 Raspberry Pi 연결(C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

이 문서에서는 디바이스 개발자로서 C# 프로그래밍 언어를 사용하여 Raspberry Pi를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서의 단계를 완료하려면 다음 구성 요소가 필요합니다.

* **샘플 Devkits** 애플리케이션 템플릿으로 만든 Azure IoT Central 애플리케이션. 자세한 내용은 참조는 [애플리케이션 빠른 시작 만들기](quick-deploy-iot-central.md)를 참조하세요.
* Raspbian 운영 체제를 실행하는 Raspberry Pi 디바이스. Raspberry Pi는 인터넷에 연결할 수 있어야 합니다. 자세한 내용은 [Raspberry Pi 설정](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)을 참조 하세요.

## <a name="sample-devkits-application"></a>**샘플 Devkits** 애플리케이션

**샘플 Devkits** 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 **Raspberry Pi** 디바이스가 포함됩니다.

* 디바이스가 수집하는 다음 측정값을 포함하는 원격 분석입니다.
  * 습도
  * 온도
  * 압력
  * 지자기 센터(X, Y, Z)
  * 가속도계(X, Y, Z)
  * 자이로스코프(X, Y, Z)
* 설정
  * 전압
  * 현재
  * 팬 속도
  * IR 토글.
* 속성
  * Die 번호 디바이스 속성
  * 위치 클라우드 속성

장치 템플릿 구성에 대 한 자세한 내용은 [Raspberry Pi 장치 템플릿 정보](#raspberry-pi-device-template-details)를 참조 하세요.

## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 응용 프로그램에서 **Raspberry Pi** 장치 템플릿의 실제 장치를 추가 합니다. 장치 연결 정보 (**범위 ID**, **장치 ID**및 **기본 키**)를 기록해 둡니다. 자세한 내용은 [Azure IoT Central 애플리케이션에 실제 디바이스 추가](tutorial-add-device.md)를 참조하세요.

### <a name="create-your-net-application"></a>.NET 애플리케이션 만들기

데스크톱 컴퓨터에서 디바이스 애플리케이션을 만들고 테스트합니다.

Visual Studio Code를 사용하여 다음 단계를 완료할 수 있습니다. 자세한 내용은 [C# 사용](https://code.visualstudio.com/docs/languages/csharp)을 참조하세요.

> [!NOTE]
> 원한다면 다른 코드 편집기를 사용하여 다음 단계를 완료해도 됩니다.

1. .NET 프로젝트를 초기화하고 필수 NuGet 패키지를 추가하려면 다음 명령을 실행합니다.

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Mqtt
   dotnet restore
   ```

1. Visual Studio Code에서 `pisample` 폴더를 엽니다. **pisample.csproj** 프로젝트 파일을 엽니다. 다음 코드 조각에 표시된 `<RuntimeIdentifiers>` 태그를 추가합니다.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RootNamespace>pisample</RootNamespace>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.21.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Client" Version="1.4.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Transport.Mqtt" Version="1.1.8" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > 패키지 버전 번호가 표시 된 버전 보다 높을 수 있습니다.

1. **pisample.csproj** 파일을 저장합니다. Visual Studio Code에서 복원 명령을 실행하라는 메시지를 표시하면 **복원**을 선택합니다.

1. **Program.cs** 를 열고 내용을 다음 코드로 바꿉니다. 이전에 `{your Scope ID}`기록해 `{your Device ID}`둔 값 `{your Device Primary Key}` 으로, 및를 업데이트 합니다.

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Microsoft.Azure.Devices.Provisioning.Client;
    using Microsoft.Azure.Devices.Provisioning.Client.Transport;

    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string ScopeID = "{your Scope ID}";
        static string DeviceID = "{your Device ID}";
        static string PrimaryKey = "{your Device Primary Key}";
        static string GlobalDeviceEndpoint = "global.azure-devices-provisioning.net";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;

        static async Task Main(string[] args)
        {
          Console.WriteLine("== Raspberry Pi Azure IoT Central example ==");

          try
          {

            using (var security = new SecurityProviderSymmetricKey(DeviceID, PrimaryKey, null))
            {
              DeviceRegistrationResult result = await RegisterDeviceAsync(security);
              if (result.Status != ProvisioningRegistrationStatusType.Assigned) {
                Console.WriteLine("Failed to register device");
                return;
              }
              IAuthenticationMethod auth = new DeviceAuthenticationWithRegistrySymmetricKey(result.DeviceId, (security as SecurityProviderSymmetricKey).GetPrimaryKey());
              Client = DeviceClient.Create(result.AssignedHub, auth, TransportType.Mqtt);
            }

            await SendDevicePropertiesAsync();

            Console.Write("Register settings changed handler...");
            await Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null);
            Console.WriteLine("Done");

            cts = new CancellationTokenSource();
            Task task = SendTelemetryAsync(cts.Token);

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
            cts.Cancel();
            await task;
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine(ex.Message);
          }
        }

        public static async Task<DeviceRegistrationResult> RegisterDeviceAsync(SecurityProviderSymmetricKey security)
        {
            Console.WriteLine("Register device...");

            using (var transport = new ProvisioningTransportHandlerMqtt(TransportFallbackType.TcpOnly))
            {
              ProvisioningDeviceClient provClient =
                        ProvisioningDeviceClient.Create(GlobalDeviceEndpoint, ScopeID, security, transport);

              Console.WriteLine($"RegistrationID = {security.GetRegistrationID()}");


              Console.Write("ProvisioningClient RegisterAsync...");
              DeviceRegistrationResult result = await provClient.RegisterAsync();

              Console.WriteLine($"{result.Status}");
              Console.WriteLine($"ProvisioningClient AssignedHub: {result.AssignedHub}; DeviceID: {result.DeviceId}");

              return result;
            }
        }

        public static async Task SendDevicePropertiesAsync()
        {
            Console.WriteLine("Send device properties...");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static async Task SendTelemetryAsync(CancellationToken token)
        {
          Random rand = new Random();

          while (true)
          {
            double currentTemperature = baseTemperature + rand.NextDouble() * 20;
            double currentPressure = basePressure + rand.NextDouble() * 100;
            double currentHumidity = baseHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
              humidity = currentHumidity,
              pressure = currentPressure,
              temp = currentTemperature
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));

            token.ThrowIfCancellationRequested();
            await Client.SendEventAsync(message);

            Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
          }
        }


        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          Console.WriteLine("Received settings change...");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

          string setting = "fanSpeed";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setVoltage";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setCurrent";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "activateIR";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          Console.WriteLine("Send settings changed acknowledgement...");
          await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static void BuildAcknowledgement(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

## <a name="run-your-net-application"></a>.NET 애플리케이션 실행

응용 프로그램 예제를 빌드하고 실행 하려면 다음을 수행 합니다.

1. 명령줄 환경에서 다음 명령을 실행합니다.

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` 폴더를 Raspberry Pi 디바이스에 복사합니다. 다음과 같이 **scp** 명령을 사용하여 파일을 추가합니다.

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    자세한 내용은 [Raspberry Pi 원격 액세스](https://www.raspberrypi.org/documentation/remote-access/)를 참조하세요.

1. Raspberry Pi 디바이스에 로그인하여 셸에서 다음 명령을 실행합니다.

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Raspberry Pi에서 다음 명령을 실행합니다.

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![프로그램 시작](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Azure IoT Central 애플리케이션에서, Raspberry Pi에서 실행되는 코드가 애플리케이션과 상호 작용하는 방식을 살펴볼 수 있습니다.

   * 실제 디바이스의 **측정값** 페이지에서 원격 분석 데이터를 볼 수 있습니다.
   * **속성** 페이지에서, 보고된 **다이 번호** 속성 값을 볼 수 있습니다.
   * **설정** 페이지에서, 전압, 팬 속도 등 Raspberry Pi의 다양한 설정을 변경할 수 있습니다.

     다음 스크린샷은 설정 변경 내용을 수신하는 Raspberry Pi를 보여줍니다.

     ![설정 변경 내용을 수신하는 Raspberry Pi](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi 장치 템플릿 정보

**샘플 Devkits** 애플리케이션 템플릿으로 만든 애플리케이션에는 다음과 같은 특징을 가진 **Raspberry Pi** 디바이스가 포함됩니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

| 필드 이름     | 단위  | 최소 | 최대값 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>설정

숫자 설정

| Display name | 필드 이름 | 단위 | 소수 자릿수 | 최소 | 최대값 | 초기 |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 전압      | setVoltage | 볼트 | 0              | 0       | 240     | 0       |
| 현재      | setCurrent | 암페어  | 0              | 0       | 100     | 0       |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

설정 전환

| Display name | 필드 이름 | 온 텍스트 | 오프 텍스트 | 초기 |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | 끄기     |

### <a name="properties"></a>속성

| 형식            | Display name | 필드 이름 | 데이터 형식                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| 디바이스 속성 | 다이 번호   | dieNumber  | number                                 |
| 위치        | 위치     | 위치   | {lat: float, long: float, alt?: float} |

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에 Raspberry Pi를 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 고유한 IoT 장치에 대 한 [사용자 지정 장치 템플릿을 설정](howto-set-up-template.md) 하는 방법을 배우는 것입니다.
