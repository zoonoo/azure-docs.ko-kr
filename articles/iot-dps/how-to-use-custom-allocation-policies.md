---
title: Azure IoT Hub Device Provisioning Service에서 사용자 지정 할당 정책을 사용하는 방법 | Microsoft Docs
description: Azure IoT Hub Device Provisioning Service에서 사용자 지정 할당 정책을 사용하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: f0eb2f7358e8fb1564275e1de510f302d2eef90b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500943"
---
# <a name="how-to-use-custom-allocation-policies"></a>사용자 지정 할당 정책을 사용하는 방법


사용자 지정 할당 정책을 사용하면 IoT Hub에 디바이스를 할당하는 방법을 더 구체적으로 제어할 수 있습니다. 이 작업을 수행하려면 [Azure 함수](../azure-functions/functions-overview.md)에서 사용자 지정 코드를 사용하여 IoT Hub에 디바이스를 할당합니다. 디바이스 프로비저닝 서비스는 Azure 함수 코드를 호출하여 디바이스 및 등록과 관련된 모든 정보를 제공합니다. 실행된 함수 코드는 디바이스를 프로비전하는 데 사용된 IoT Hub 정보를 반환합니다.

사용자 지정 할당 정책을 사용하면 Device Provisioning Service에서 제공하는 정책이 시나리오의 요구 사항을 충족하지 않을 경우 사용자가 고유한 할당 정책을 정의합니다.

예를 들어 프로비전 중에 디바이스에서 사용하는 인증서를 검사하고 해당 디바이스를 인증서 속성에 따라 IoT Hub에 할당하려고 할 수 있습니다. 디바이스에 대한 정보가 데이터베이스에 저장되어 있고 데이터베이스를 쿼리하여 디바이스에 할당해야 하는 IoT Hub를 확인해야 할 수 있습니다.


이 문서에서는 C#으로 작성된 Azure 함수를 사용하는 사용자 지정 할당 정책에 대해 설명합니다. *Contoso Toasters Division* 및 *Contoso Heat Pumps Division*을 나타내는 두 개의 새로운 IoT Hub가 생성됩니다. 프로비저닝을 요청하는 디바이스에는 프로비저닝을 위해 허용되는 다음 접미사 중 하나가 포함된 등록 ID가 있어야 합니다.

- **-contoso-tstrsd-007**: Contoso Toasters Division
- **-contoso-hpsd-088**: Contoso Heat Pumps Division

디바이스는 등록 ID에 있는 이러한 필수 접미사 중 하나를 기반으로 프로비전됩니다. 이러한 디바이스는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)에 포함된 프로비저닝 샘플을 사용하여 시뮬레이트됩니다. 

이 문서에서는 다음 단계를 수행합니다.

* Azure CLI를 사용하여 두 개의 Contoso 부서 IoT Hub(**Contoso Toasters Division** 및 **Contoso Heat Pumps Division**) 만들기
* 사용자 지정 할당 정책에 Azure 함수를 사용하여 새 그룹 등록 만들기
* 두 개의 디바이스 시뮬레이션을 위한 디바이스 키 만들기
* Azure IoT C SDK에 대한 개발 환경 준비
* 디바이스를 시뮬레이트하여 사용자 지정 할당 정책의 예제 코드에 따라 프로비전되었는지 확인합니다.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md) 빠른 시작을 완료해야 합니다.
* ['C++를 사용한 데스크톱 개발'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드가 활성화된 Visual Studio 2015 또는 [Visual Studio 2017](https://www.visualstudio.com/vs/)
* 최신 버전의 [Git](https://git-scm.com/download/) 설치

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>두 개의 부서 IoT Hub 만들기

이 섹션에서는 Azure Cloud Shell을 사용하여 **Contoso Toasters Division**과 **Contoso Heat Pumps Division**을 나타내는 두 개의 새 IoT Hub를 만듭니다.

1. Azure Cloud Shell을 사용하여 [az group create](/cli/azure/group#az-group-create) 명령으로 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

    다음 예제에서는 *eastus* 지역에 *contoso-us-resource-group*이라는 리소스 그룹을 만듭니다. 이 문서에 만든 모든 리소스에 이 그룹을 사용하는 것이 좋습니다. 이 접근 방법은 완료한 후 더 쉽게 정리됩니다.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Azure Cloud Shell을 사용하여 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 명령으로 **Contoso Toasters Division** IoT Hub를 만듭니다. IoT Hub는 *contoso-us-resource-group*에 추가됩니다.

    다음 예제에서는 *eastus* 위치에 *contoso-toasters-hub-1098*이라는 IoT Hub를 만듭니다. 고유한 허브 이름을 사용해야 합니다. 허브 이름에서 **1098** 대신 고유한 접미사를 구성합니다. 사용자 지정 할당 정책에 대한 예제 코드는 허브 이름에 `-toasters-`가 필요합니다.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

3. Azure Cloud Shell을 사용하여 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 명령으로 **Contoso Heat Pumps Division** IoT Hub를 만듭니다. 이 IoT Hub도 *contoso-us-resource-group*에 추가됩니다.

    다음 예제에서는 *eastus* 위치에 *contoso-heatpumps-hub-1098*이라는 IoT Hub를 만듭니다. 고유한 허브 이름을 사용해야 합니다. 허브 이름에서 **1098** 대신 고유한 접미사를 구성합니다. 사용자 지정 할당 정책에 대한 예제 코드는 허브 이름에 `-heatpumps-`가 필요합니다.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    이 명령을 완료하는 데도 몇 분 정도 걸릴 수 있습니다.




## <a name="create-the-enrollment"></a>등록 만들기

이 섹션에서는 사용자 지정 할당 정책을 사용하는 새 등록 그룹을 만듭니다. 간단한 설명을 위해 이 문서에서는 등록에 [대칭 키 증명](concepts-symmetric-key-attestation.md)을 사용합니다. 더 안전한 솔루션을 위해 신뢰 체인과 함께 [X.509 인증서 증명](concepts-security.md#x509-certificates)을 사용하는 것이 좋습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Device Provisioning Service 인스턴스를 엽니다.

2. **등록 관리** 탭을 선택한 다음, 페이지 위쪽에 있는 **등록 그룹 추가** 단추를 클릭합니다. 

3. **등록 그룹 추가**에서 다음 정보를 입력하고 **저장** 단추를 클릭합니다.

    **그룹 이름**: **contoso-custom-allocated-devices**를 입력합니다.

    **증명 유형**: **대칭 키**를 선택합니다.

    **키 자동 생성**: 이 확인란은 이미 선택되어 있습니다.

    **허브에 디바이스를 할당할 방법 선택**: **사용자 지정(Azure 함수 사용)** 을 선택합니다.

    ![대칭 키 증명에 대한 사용자 지정 할당 등록 그룹 추가](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. **등록 그룹 추가**에서 **새 IoT Hub 연결**을 클릭하여 새 부서 IoT Hub를 둘 다 연결합니다. 

    두 부서 IoT Hub 모두에 대해 이 단계를 실행해야 합니다.

    **구독**: 여러 구독이 있는 경우 부서 IoT 허브를 만든 구독을 선택합니다.

    **IoT 허브**: 직접 만든 부서 허브 중 하나를 선택합니다.

    **액세스 정책**: **iothubowner**를 선택합니다.

    ![부서 IoT Hub를 Provisioning Service와 연결합니다.](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. **등록 그룹 추가**에서 부서 IoT Hub가 둘 다 연결된 후에는 아래 표시된 대로 등록 그룹에서 해당 허브를 IoT Hub 그룹으로 선택해야 합니다.

    ![등록에 대한 부서 허브 그룹 만들기](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. **등록 그룹 추가**에서 **Azure 함수 선택** 섹션으로 아래로 스크롤하고 **새 함수 앱 만들기**를 클릭합니다.

7. **Function App**에서 열리는 페이지를 만들고 새 함수에 대한 다음 설정을 입력한 후 **만들기**를 클릭합니다.

    **앱 이름**: 고유한 함수 앱 이름을 입력합니다. **contoso-function-app-1098**이 예제로 표시됩니다.

    **리소스 그룹**: **기존 항목 사용** 및 **contoso-us-resource-group**을 선택하여 이 문서에서 만든 모든 리소스를 함께 유지합니다.

    **Application Insights**: 이 연습에서는 이 기능을 끌 수 있습니다.

    ![함수 앱 만들기](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. **등록 그룹 추가** 페이지로 돌아가 새 함수 앱이 선택되었는지 확인합니다. 함수 앱 목록을 새로 고치려면 구독을 다시 선택해야 할 수 있습니다.

    새 함수 앱을 선택한 후 **새 함수 만들기**를 클릭합니다.

    ![함수 앱 만들기](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    새 함수 앱이 열립니다.

9. 함수 앱에서 새 함수를 만들려면 클릭합니다.

    ![함수 앱 만들기](./media/how-to-use-custom-allocation-policies/new-function.png)

    새 함수의 경우 기본 설정을 사용하여 **CSharp** 언어로 새 **웹후크 + API**를 만듭니다. **이 함수 만들기**를 클릭합니다.

    그러면 **HttpTriggerCSharp1**이라는 새 C# 함수가 생성됩니다.

10. 새 C# 함수의 코드를 다음 코드로 바꾸고 **저장**을 클릭합니다.    

    ```C#
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. **등록 그룹 추가** 페이지로 돌아가서 새 함수가 선택되었는지 확인합니다. 함수 목록을 새로 고치려면 함수 앱을 다시 선택해야 할 수 있습니다.

    새 함수를 선택한 후 **저장**을 클릭하여 등록 그룹을 저장합니다.

    ![마지막으로 등록 그룹 저장](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. 등록을 저장한 후 등록을 다시 열고 **기본 키**를 기록해 두세요. 키를 생성하려면 먼저 등록을 저장해야 합니다. 이 키는 나중에 시뮬레이트된 디바이스에 대한 고유한 디바이스 키를 생성하는 데 사용됩니다.


## <a name="derive-unique-device-keys"></a>고유한 디바이스 키 파생

이 섹션에서는 두 개의 고유한 디바이스 키를 만듭니다. 하나의 키는 시뮬레이트된 토스터 디바이스에 사용됩니다. 다른 키는 시뮬레이트된 열 펌프 디바이스에 사용됩니다.

디바이스 키를 생성하려면 이전에 적어 둔 **기본 키**를 사용하여 각 디바이스에 대한 디바이스 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)을 계산하고 결과를 Base64 형식으로 변환합니다. 등록 그룹을 사용하여 파생된 디바이스 키를 만드는 방법에 대한 자세한 내용은 [대칭 키 증명](concepts-symmetric-key-attestation.md)의 그룹 등록 섹션을 참조하세요.

이 문서의 예제에서는 다음 두 디바이스 등록 ID를 사용하여 두 디바이스의 디바이스 키를 계산합니다. 두 등록 ID에는 모두 사용자 지정 할당 정책에 대한 예제 코드에서 작동하는 유효한 접미사가 있습니다.

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Linux 워크스테이션

Linux 워크스테이션을 사용하는 경우 openssl을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

1. **KEY**의 값을 이전에 적어 둔 **기본 키**로 바꿉니다.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


#### <a name="windows-based-workstations"></a>Windows 기반 워크스테이션

Windows 기반 워크스테이션을 사용하는 경우 PowerShell을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

1. **KEY**의 값을 이전에 적어 둔 **기본 키**로 바꿉니다.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


시뮬레이트된 디바이스는 각 등록 ID와 함께 파생된 디바이스 키를 사용하여 대칭 키 증명을 수행합니다.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK 개발 환경 준비

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용되는 개발 환경을 준비합니다. SDK에는 시뮬레이트된 디바이스의 샘플 코드가 포함되어 있습니다. 이 시뮬레이트된 디바이스는 디바이스의 부팅 시퀀스 중에 프로비저닝을 시도합니다.

이 섹션은 Windows 기반 워크스테이션에 적용됩니다. Linux 예제를 보려면 [다중 테넌트를 지원하기 위해 장치를 프로비전하는 방법](how-to-provision-multitenant.md)에서 VM 설정을 참조하세요.

1. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    `CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다.

2. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 Azure IoT C SDK GitHub 리포지토리를 복제합니다.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    이 작업을 완료하는 데 몇 분 정도가 걸립니다.


3. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. 개발 클라이언트 플랫폼에 관련된 SDK 버전을 빌드하는 다음 명령을 실행합니다. 또한 시뮬레이션된 디바이스에 대한 Visual Studio 솔루션이 `cmake` 디렉터리에서 생성됩니다. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    `cmake`에서 C++ 컴파일러를 찾지 못하면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다. 

    빌드가 성공되면 마지막 몇몇 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```




## <a name="simulate-the-devices"></a>디바이스 시뮬레이트

이 섹션에서는 이전에 설정한 Azure IoT C SDK에 있는 **prov\_dev\_client\_sample**이라는 프로비저닝 샘플을 업데이트합니다. 

이 샘플 코드는 프로비저닝 요청을 Device Provisioning Service 인스턴스에 보내는 디바이스 부팅 시퀀스를 시뮬레이트합니다. 부팅 시퀀스를 통해 토스터 디바이스가 인식되고 사용자 지정 할당 정책을 통해 IoT Hub에 할당됩니다.

1. Azure Portal에서 Device Provisioning 서비스에 대한 **개요** 탭을 선택하고 **_ID 범위_** 값을 기록해 둡니다.

    ![포털 블레이드에서 디바이스 프로비저닝 서비스 엔드포인트 정보 추출](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio에서 이전에 CMake를 실행하여 생성된 **azure_iot_sdks.sln** 솔루션 파일을 엽니다. 솔루션 파일은 다음 위치에 있습니다.

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Samples** 폴더로 이동합니다. **prov\_dev\_client\_sample**이라는 샘플 프로젝트를 확장합니다. **원본 파일**을 확장하고, **prov\_dev\_client\_sample.c**를 엽니다.

4. `id_scope` 상수를 찾고, 값을 앞에서 복사한 **ID 범위** 값으로 바꿉니다. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 동일한 파일에서 `main()` 함수에 대한 정의를 찾습니다. 아래와 같이 `hsm_type` 변수가 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`로 설정되었는지 확인합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 


#### <a name="simulate-the-contoso-toaster-device"></a>Contoso 토스터 디바이스 시뮬레이트

1. 토스터 디바이스를 시뮬레이션하려면 **prov\_dev\_client\_sample.c**에서 주석으로 처리된 `prov_dev_set_symmetric_key_info()` 호출을 찾습니다.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    함수 호출의 주석 처리를 제거하고 자리 표시자 값(꺾쇠 괄호 포함)을 이전에 생성한 토스터 등록 ID 및 파생된 디바이스 키로 바꿉니다. 아래 표시된 키 값 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 은 예제로만 제공됩니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    파일을 저장합니다.

2. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작**을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 프롬프트에서 **예**를 클릭하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 시뮬레이트된 토스터 디바이스를 성공적으로 부팅하고, 사용자 지정 정책을 통해 토스터 IoT Hub에 할당할 Provisioning Service 인스턴스에 연결하는 예제입니다.

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Contoso 열 펌프 디바이스 시뮬레이트

1. 열 펌프 디바이스를 시뮬레이션하려면 다시 **prov\_dev\_client\_sample.c**의 `prov_dev_set_symmetric_key_info()` 호출을 이전에 생성한 열 펌프 등록 ID 및 파생된 디바이스 키로 업데이트합니다. 아래 표시된 키 값 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg**도 예제로만 제공됩니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    파일을 저장합니다.

2. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작**을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 프롬프트에서 **예**를 클릭하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 시뮬레이트된 열 펌프 디바이스를 성공적으로 부팅하고, 사용자 지정 정책을 통해 Contoso 열 펌프 IoT Hub에 할당할 Provisioning Service 인스턴스에 연결하는 예제입니다.

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>사용자 지정 할당 정책 문제 해결

다음 표는 예상 시나리오와 발생할 수 있는 결과 오류 코드를 보여 줍니다. 이 표를 사용하여 Azure Functions와 관련된 사용자 지정 할당 정책 오류를 해결할 수 있습니다.


| 시나리오 | Provisioning Service의 등록 결과 | 프로비저닝 SDK 결과 |
| -------- | --------------------------------------------- | ------------------------ |
| 웹후크가 ‘iotHubHostName’이 유효한 IoT Hub 호스트 이름으로 설정된 200 OK을 반환함 | 결과 상태: 할당됨  | SDK가 허브 정보와 함께 PROV_DEVICE_RESULT_OK을 반환함 |
| 웹후크가 ‘iotHubHostName’이 응답에 있고 빈 문자열이나 Null로 설정된 200 OK을 반환함 | 결과 상태: 실패<br><br> 오류 코드: CustomAllocationIotHubNotSpecified(400208) | SDK가 PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED를 반환함 |
| 웹후크가 401 권한 없음을 반환함 | 결과 상태: 실패<br><br>오류 코드: CustomAllocationUnauthorizedAccess(400209) | SDK가 PROV_DEVICE_RESULT_UNAUTHORIZED를 반환함 |
| 디바이스를 사용하지 않도록 설정하기 위한 개별 등록이 생성됨 | 결과 상태: 사용 안 함 | SDK가 PROV_DEVICE_RESULT_DISABLED를 반환함 |
| 웹후크가 오류 코드 >= 429를 반환함 | DPS의 오케스트레이션이 여러 번 재시도함 현재 재시도 정책:<br><br>&nbsp;&nbsp;- 다시 시도 횟수: 10<br>&nbsp;&nbsp;- 초기 간격: 1초<br>&nbsp;&nbsp;- 증분: 9초 | SDK가 오류를 무시하고 지정된 시간에 다른 상태 가져오기 메시지를 제출함 |
| 웹후크가 다른 모든 상태 코드를 반환함 | 결과 상태: 실패<br><br>오류 코드: CustomAllocationFailed(400207) | SDK가 PROV_DEVICE_RESULT_DEV_AUTH_ERROR를 반환함 |


## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 생성된 리소스를 계속 사용하려는 경우 리소스를 그대로 유지할 수 있습니다. 리소스를 계속 사용하지 않으려면 다음 단계를 통해 이 문서에서 만든 모든 리소스를 삭제해야 불필요한 비용을 방지할 수 있습니다.

이러한 단계에서는 **contoso-us-resource-group**이라는 동일한 리소스 그룹에 표시된 대로 이 문서에서 모든 리소스를 만들었다고 가정합니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 보관할 리소스가 포함된 기존 리소스 그룹 내에 IoT Hub를 만든 경우 리소스 그룹을 삭제하지 말고 IoT Hub 리소스만 삭제하면 됩니다.
>

이름별로 리소스 그룹을 삭제하려면:

1. [Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

2. **이름별 필터...** 텍스트 상자에 리소스 **contoso-us-resource-group**을 포함하는 리소스 그룹의 이름을 입력합니다. 

3. 결과 목록의 리소스 그룹 오른쪽에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

4. 리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 다시 입력하여 확인한 다음, **삭제**를 클릭합니다. 잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

- 자세한 Reprovisioning에 알아보려면 [개념을 다시 프로 비전 된 IoT 허브 장치](concepts-device-reprovision.md) 
- 자세한 프로 비전 해제에 알아보려면 [이전에 자동으로 프로 비전 된 장치를 프로 비전 해제 하는 방법](how-to-unprovision-devices.md) 











