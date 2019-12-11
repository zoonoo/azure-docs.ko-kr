---
title: Azure IoT Hub 장치 프로 비전 서비스를 사용 하 여 사용자 지정 할당 정책
description: DPS (Azure IoT Hub 장치 프로 비전 서비스)에서 사용자 지정 할당 정책을 사용 하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b6b7d4614d3c63fe93e213fb830b85d0b7f9c474
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974873"
---
# <a name="how-to-use-custom-allocation-policies"></a>사용자 지정 할당 정책을 사용 하는 방법

사용자 지정 할당 정책을 통해 IoT hub에 장치를 할당 하는 방법을 보다 효과적으로 제어할 수 있습니다. 이는 [Azure Function](../azure-functions/functions-overview.md) 의 사용자 지정 코드를 사용 하 여 IoT hub에 장치를 할당 하는 방법으로 수행 됩니다. 장치 프로 비전 서비스는 장치 및 등록에 대 한 모든 관련 정보를 제공 하는 Azure 함수 코드를 호출 합니다. 함수 코드가 실행 되 고 장치를 프로 비전 하는 데 사용 되는 IoT hub 정보를 반환 합니다.

사용자 지정 할당 정책을 사용 하 여 장치 프로 비전 서비스에서 제공 하는 정책이 시나리오의 요구 사항을 충족 하지 않는 경우 고유한 할당 정책을 정의 합니다.

예를 들어 프로 비전 하는 동안 장치가 사용 하는 인증서를 확인 하 고 인증서 속성을 기반으로 IoT hub에 장치를 할당할 수 있습니다. 또는 장치에 대 한 정보를 데이터베이스에 저장 하 고, 장치를 할당 해야 하는 IoT hub를 결정 하기 위해 데이터베이스를 쿼리해야 할 수도 있습니다.

이 문서에서는에서 C#작성 된 Azure 함수를 사용 하 여 사용자 지정 할당 정책을 보여 줍니다. *Contoso Toers* 와 *Contoso 열 펌프 나누기*를 나타내는 두 개의 새 IoT 허브가 생성 됩니다. 프로 비전을 요청 하는 장치에는 프로 비전에 대해 허용할 다음 접미사 중 하나가 포함 된 등록 ID가 있어야 합니다.

* **-contoso-tstrsd-007**: Contoso toers
* **-contoso-hpsd-088**: Contoso 열 펌프 나누기

장치는 등록 ID에서 이러한 필수 접미사 중 하나를 기반으로 프로 비전 됩니다. 이러한 장치는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)에 포함 된 프로 비전 샘플을 사용 하 여 시뮬레이션 됩니다.

이 문서에서는 다음 단계를 수행 합니다.

* Azure CLI를 사용 하 여 두 개의 Contoso 디비전 IoT hub를 만듭니다 (**Contoso Toers** 및 **Contoso 열 펌프 나누기**).
* 사용자 지정 할당 정책에 대해 Azure 함수를 사용 하 여 새 그룹 등록 만들기
* 두 장치 시뮬레이션에 대 한 장치 키를 만듭니다.
* Azure IoT C SDK에 대 한 개발 환경 설정
* 장치를 시뮬레이션 하 고 사용자 지정 할당 정책에서 예제 코드에 따라 장치를 프로 비전 했는지 확인 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* ' 워크 로드 [를 C++사용한 데스크톱 개발 '](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 작업을 사용 하는 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 이상
* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>프로 비전 서비스 및 두 개의 디비전 IoT hub 만들기

이 섹션에서는 Azure Cloud Shell를 사용 하 여 프로 비전 서비스와 **Contoso Toers** 와 **contoso 열 펌프 나누기**를 나타내는 두 개의 IoT hub를 만듭니다.

> [!TIP]
> 이 문서에 사용 된 명령은 미국 서 부 위치에 프로 비전 서비스 및 기타 리소스를 만듭니다. 장치 프로 비전 서비스를 지 원하는 가장 가까운 지역에 리소스를 만드는 것이 좋습니다. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. 명령에서 위치는 한 단어 또는 여러 단어 형식 중 하나로 지정할 수 있습니다. 예: westus, 미국 서 부, 미국 서 부 등 값은 대/소문자를 구분 하지 않습니다. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `-- location "West US"`.
>

1. Azure Cloud Shell를 사용 하 여 [az group create](/cli/azure/group#az-group-create) 명령을 사용 하 여 리소스 그룹을 만듭니다. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

    다음 예제에서는 *westus* 지역에 *contoso-us-resource 그룹* 이라는 리소스 그룹을 만듭니다. 이 문서에서 만든 모든 리소스에 대해이 그룹을 사용 하는 것이 좋습니다. 이 접근 방식은 완료 된 후 더 쉽게 정리 됩니다.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Azure Cloud Shell를 사용 하 여 [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) 명령을 사용 하 여 장치 프로 비전 서비스를 만듭니다. 프로 비전 서비스는 *contoso-미국-리소스 그룹*에 추가 됩니다.

    다음 예제에서는 *westus* 위치에 *contoso-프로 비전-서비스-1098* 이라는 프로 비전 서비스를 만듭니다. 고유한 서비스 이름을 사용 해야 합니다. **1098**대신 서비스 이름에 고유한 접미사를 만듭니다.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    이 명령을 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

3. Azure Cloud Shell를 사용 하 여 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 명령을 사용 하 여 **Contoso toers 디비전** IoT hub를 만듭니다. IoT hub는 *contoso-미국-리소스 그룹*에 추가 됩니다.

    다음 예제에서는 *westus* 위치에 *contoso-toers-hub-1098* 라는 IoT hub를 만듭니다. 고유한 허브 이름을 사용 해야 합니다. **1098**대신 허브 이름에 고유한 접미사를 구성 합니다. 사용자 지정 할당 정책에 대 한 예제 코드에는 허브 이름에 `-toasters-` 필요 합니다.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    이 명령을 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

4. Azure Cloud Shell를 사용 하 여 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 명령을 사용 하 여 **Contoso 열 펌프 나누기** IoT hub를 만듭니다. 이 IoT hub는 *contoso-미국-리소스 그룹*에도 추가 됩니다.

    다음 예제에서는 *westus* 위치에 *contoso-heatpumps-1098* 라는 IoT hub를 만듭니다. 고유한 허브 이름을 사용 해야 합니다. **1098**대신 허브 이름에 고유한 접미사를 구성 합니다. 사용자 지정 할당 정책에 대 한 예제 코드에는 허브 이름에 `-heatpumps-` 필요 합니다.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    이 명령을 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-the-custom-allocation-function"></a>사용자 지정 할당 함수 만들기

이 섹션에서는 사용자 지정 할당 정책을 구현 하는 Azure 함수를 만듭니다. 이 함수는 등록 ID에 **-007** 또는 **-contoso-hpsd-088**문자열이 포함 되어 있는지 여부에 따라 장치를 등록 해야 하는 디비전 IoT hub를 결정 합니다. 또한 장치가 toaster 또는 열 펌프 인지 여부에 따라 장치 쌍의 초기 상태를 설정 합니다.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 홈 페이지에서 **+ 리소스 만들기**를 선택 합니다.

2. *Marketplace* 검색 검색 상자에 "함수 앱"을 입력 합니다. 드롭다운 목록에서 **함수 앱**을 선택 하 고 **만들기**를 선택 합니다.

3. **함수 앱** 만들기 페이지의 **기본 사항** 탭에서 새 함수 앱에 대해 다음 설정을 입력 하 고 **검토 + 만들기**를 선택 합니다.

    **리소스 그룹**:이 문서에서 만든 모든 리소스를 함께 유지 하려면 **contoso-미국-리소스 그룹** 을 선택 합니다.

    **함수 앱 이름**: 고유한 함수 앱 이름을 입력 합니다. 이 예제에서는 **contoso-1098-** 를 사용 합니다.

    **Publish**: **코드** 가 선택 되어 있는지 확인 합니다.

    **런타임 스택**: 드롭다운에서 **.Net Core** 를 선택 합니다.

    **지역**: 리소스 그룹과 동일한 지역을 선택 합니다. 이 예제에서는 **미국 서 부**를 사용 합니다.

    > [!NOTE]
    > 기본적으로 Application Insights 사용 하도록 설정 되어 있습니다. 이 문서에서는 Application Insights 필요 하지 않지만 사용자 지정 할당으로 발생 하는 문제를 이해 하 고 조사 하는 데 도움이 될 수 있습니다. 원하는 경우 **모니터링** 탭을 선택 하 고 **사용 Application Insights**에 대해 **아니요** 를 선택 하 여 Application Insights을 사용 하지 않도록 설정할 수 있습니다.

    ![사용자 지정 할당 함수를 호스트 하는 Azure 함수 앱 만들기](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. **요약** 페이지에서 **만들기** 를 선택 하 여 함수 앱을 만듭니다. Az üzembe helyezés eltarthat néhány percig. 완료 되 면 **리소스로 이동**을 선택 합니다.

5. 함수 앱 **개요** 페이지의 왼쪽 창에서 **함수** 옆에 **+** 를 선택 하 여 새 함수를 추가 합니다.

    ![함수 앱에 함수를 추가 합니다.](./media/how-to-use-custom-allocation-policies/create-function.png)

6. **.Net 용 Azure Functions-시작** 페이지에서 **배포 환경 선택** 단계에서 **포털 내** 타일을 선택한 다음 **계속**을 선택 합니다.

    ![포털 개발 환경 선택](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. 다음 페이지에서 **함수 만들기** 단계에 대해 웹 후크 **+ API** 타일을 선택 하 고 **만들기**를 선택 합니다. **HttpTrigger1** 라는 함수가 만들어지고, 포털에는 **실행. csx** 코드 파일의 내용이 표시 됩니다.

8. 필요한 Nuget 패키지를 참조 합니다. 초기 장치 쌍을 만들기 위해 사용자 지정 할당 함수는 호스팅 환경에 로드 되어야 하는 두 개의 Nuget 패키지에 정의 된 클래스를 사용 합니다. Azure Functions를 사용 하 여 Nuget 패키지는 *함수. 호스트* 파일을 사용 하 여 참조 됩니다. 이 단계에서는 *함수. 호스트* 파일을 저장 하 고 업로드 합니다.

    1. 다음 줄을 원하는 편집기에 복사 하 고 컴퓨터의 파일을 *함수. 호스트로*저장 합니다.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. **HttpTrigger1** 함수에서 창의 오른쪽에 있는 **파일 보기** 탭을 확장 합니다.

        ![파일 보기 열기](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. **업로드**를 선택 하 고 **함수 proj** 파일을 찾은 다음 **열기** 를 선택 하 여 파일을 업로드 합니다.

        ![파일 업로드 선택](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. **HttpTrigger1** 함수의 코드를 다음 코드로 바꾸고 **저장**을 선택 합니다.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>등록 만들기

이 섹션에서는 사용자 지정 할당 정책을 사용 하는 새 등록 그룹을 만듭니다. 간단히 하기 위해이 문서에서는 등록에 [대칭 키 증명](concepts-symmetric-key-attestation.md) 을 사용 합니다. 보다 안전한 솔루션을 위해서는 신뢰 체인에 [x.509 인증서 증명](concepts-security.md#x509-certificates) 을 사용 하는 것이 좋습니다.

1. 계속 [Azure Portal](https://portal.azure.com)에서 프로 비전 서비스를 엽니다.

2. 왼쪽 창에서 **등록 관리** 를 선택 하 고 페이지 위쪽에서 **등록 그룹 추가** 단추를 선택 합니다.

3. **등록 그룹 추가**에서 다음 정보를 입력 하 고 **저장** 단추를 선택 합니다.

    **그룹 이름**: **contoso-사용자 할당-장치**를 입력 합니다.

    **증명 형식**: **대칭 키**를 선택 합니다.

    **키 자동 생성**:이 확인란은 이미 선택 되어 있어야 합니다.

    **허브에 장치를 할당 하는 방법을 선택**합니다. **사용자 지정 (Azure 함수 사용)** 을 선택 합니다.

    ![대칭 키 증명에 대 한 사용자 지정 할당 등록 그룹 추가](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. **등록 그룹 추가**에서 새 **iot hub 연결** 을 선택 하 여 새 디비전 iot 허브를 모두 연결 합니다.

    디비전 IoT 허브 모두에 대해이 단계를 실행 합니다.

    **구독**: 구독이 여러 개 있는 경우 디비전 IoT 허브를 만든 구독을 선택 합니다.

    **IoT hub**: 만든 디비전 허브 중 하나를 선택 합니다.

    **액세스 정책**: **iothubowner**를 선택 합니다.

    ![프로 비전 서비스와 디비전 IoT hub 연결](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. **등록 그룹 추가**에서 두 디비전 IoT 허브가 모두 연결 되 면 아래와 같이 등록 그룹에 대 한 IoT Hub 그룹으로 선택 해야 합니다.

    ![등록에 대 한 디비전 허브 그룹 만들기](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. **등록 그룹 추가**에서 **Azure 함수 선택** 섹션까지 아래로 스크롤하여 이전 섹션에서 만든 함수 앱을 선택 합니다. 그런 다음 만든 기능을 선택 하 고 저장을 선택 하 여 등록 그룹을 저장 합니다.

    ![함수를 선택 하 고 등록 그룹을 저장 합니다.](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. 등록을 저장 한 후 다시 열고 **기본 키**를 적어둡니다. 키를 생성 하려면 먼저 등록을 저장 해야 합니다. 이 키는 나중에 시뮬레이션 된 장치에 대 한 고유한 장치 키를 생성 하는 데 사용 됩니다.

## <a name="derive-unique-device-keys"></a>고유한 장치 키 파생

이 섹션에서는 두 개의 고유한 장치 키를 만듭니다. 시뮬레이션 된 toaster 장치에 대해 하나의 키가 사용 됩니다. 다른 키는 시뮬레이션 된 열 펌프 장치에 사용 됩니다.

장치 키를 생성 하려면 앞에서 적어둔 **기본 키** 를 사용 하 여 각 장치에 대 한 장치 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) 을 계산 하 고 그 결과를 Base64 형식으로 변환 합니다. 등록 그룹을 사용 하 여 파생 된 장치 키를 만드는 방법에 대 한 자세한 내용은 [대칭 키 증명](concepts-symmetric-key-attestation.md)의 group 등록 섹션을 참조 하세요.

이 문서의 예제에서는 다음 두 장치 등록 Id를 사용 하 고 두 장치에 대해 장치 키를 계산 합니다. 두 등록 Id에는 모두 사용자 지정 할당 정책에 대 한 예제 코드를 사용할 수 있는 올바른 접미사가 있습니다.

* **breakroom499-007**
* **mainbuilding167-hpsd-088**

### <a name="linux-workstations"></a>Linux 워크스테이션

Linux 워크스테이션을 사용 하는 경우 다음 예제와 같이 openssl를 사용 하 여 파생 된 장치 키를 생성할 수 있습니다.

1. **키** 의 값을 앞에서 적어둔 **기본 키** 로 바꿉니다.

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

### <a name="windows-based-workstations"></a>Windows 기반 워크스테이션

Windows 기반 워크스테이션을 사용 하는 경우 다음 예제와 같이 PowerShell을 사용 하 여 파생 된 장치 키를 생성할 수 있습니다.

1. **키** 의 값을 앞에서 적어둔 **기본 키** 로 바꿉니다.

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

시뮬레이션 된 장치는 각 등록 ID로 파생 된 장치 키를 사용 하 여 대칭 키 증명을 수행 합니다.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용 되는 개발 환경을 준비 합니다. SDK에는 시뮬레이션 된 장치에 대 한 샘플 코드가 포함 되어 있습니다. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

이 섹션은 Windows 기반 워크스테이션을 중심으로 합니다. Linux 예제를 보려면 [How to 프로 비전 How to 배포할지에](how-to-provision-multitenant.md)을 참조 하세요.

1. [Cmake 빌드 시스템](https://cmake.org/download/)을 다운로드 합니다.

    `CMake` 설치를 시작 **하기 전에** visual studio 필수 구성 요소 (visual studio 및 ' 워크 C++로드를 사용한 데스크톱 개발 ')가 컴퓨터에 설치 되어 있어야 합니다. 필수 구성 요소가 준비 되 고 다운로드를 확인 한 후에는 CMake 빌드 시스템을 설치 합니다.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az Azure IoT C SDK GitHub-adattárat:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Ez a művelet várhatóan több percig is eltarthat.

3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    `cmake` C++ 컴파일러를 찾지 못하면 명령을 실행 하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 명령을 실행 해 봅니다.

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

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

## <a name="simulate-the-devices"></a>장치 시뮬레이션

이 섹션에서는 이전에 설정한 Azure IoT C SDK에 있는 **prov\_dev\_client\_sample** 이라는 프로 비전 샘플을 업데이트 합니다.

이 샘플 코드는 장치 프로 비전 서비스 인스턴스로 프로 비전 요청을 보내는 장치 부팅 시퀀스를 시뮬레이션 합니다. 부팅 시퀀스를 사용 하면 사용자 지정 할당 정책을 사용 하 여 toaster 장치를 인식 하 고 IoT hub에 할당할 수 있습니다.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio에서 CMake이를 실행 하 여 생성 된 **azure_iot_sdks .sln** 솔루션 파일을 엽니다. A megoldásfájlnak a következő helyen kell lennie:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg róla, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, mint az alább is látható:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget.

### <a name="simulate-the-contoso-toaster-device"></a>Contoso toaster 장치 시뮬레이트

1. Toaster 장치를 시뮬레이션 하려면 **prov\_dev\_client\_sample. c** 에서 `prov_dev_set_symmetric_key_info()`에 대 한 호출을 찾아 주석 처리 합니다.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    함수 호출의 주석 처리를 제거 하 고 자리 표시자 값 (꺾쇠 괄호 포함)을 이전에 생성 한 toaster 등록 ID 및 파생 된 장치 키로 바꿉니다. 아래에 표시 된 키 값 **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** 은 예제로만 제공 됩니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. 프로젝트를 다시 빌드하는 프롬프트에서 **예**를 선택 하 여를 실행 하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 사용자 지정 할당 정책에 의해 토스터 IoT hub에 할당 될 프로 비전 서비스 인스턴스를 성공적으로 부팅 및 연결 하는 시뮬레이션 된 toaster 장치의 예입니다.

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Contoso 열 펌프 장치 시뮬레이션

1. 열 펌프 장치를 시뮬레이션 하려면 이전에 생성 한 열 펌프 등록 ID 및 파생 된 장치 키를 사용 하 여 **prov\_dev\_client\_** 에서 `prov_dev_set_symmetric_key_info()`에 대 한 호출을 다시 업데이트 합니다. 아래에 표시 된 키 값 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** 예제로만 제공 됩니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. 프로젝트를 다시 빌드하는 프롬프트에서 **예** 를 선택 하 여를 실행 하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 사용자 지정 할당 정책에 따라 Contoso 열 펌프 IoT hub에 할당 될 프로 비전 서비스 인스턴스를 성공적으로 부팅 및 연결 하는 시뮬레이션 된 열 펌프 장치의 예입니다.

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>사용자 지정 할당 정책 문제 해결

다음 표에서는 예상 되는 시나리오 및 사용자가 받을 수 있는 결과 오류 코드를 보여 줍니다. 이 표를 사용 하 여 Azure Functions에 대 한 사용자 지정 할당 정책 오류 문제를 해결할 수 있습니다.

| Alkalmazási helyzet | 프로 비전 서비스의 등록 결과 | SDK 결과 프로 비전 |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook는 ' iotHubHostName '가 유효한 IoT hub 호스트 이름으로 설정 된 200 OK를 반환 합니다. | 결과 상태: 할당 됨  | SDK는 허브 정보와 함께 PROV_DEVICE_RESULT_OK를 반환 합니다. |
| 웹 후크는 응답에는 ' iotHubHostName '가 있고 빈 문자열이 나 null로 설정 된 200 OK를 반환 합니다. | 결과 상태: 실패<br><br> 오류 코드: CustomAllocationIotHubNotSpecified (400208) | SDK에서 반환 PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook에서 401 권한 없음 반환 | 결과 상태: 실패<br><br>오류 코드: CustomAllocationUnauthorizedAccess (400209) | SDK에서 반환 PROV_DEVICE_RESULT_UNAUTHORIZED |
| 장치를 사용 하지 않도록 설정 하기 위해 개별 등록을 만들었습니다. | 결과 상태: 사용 안 함 | SDK에서 반환 PROV_DEVICE_RESULT_DISABLED |
| Webhook는 오류 코드 > = 429을 반환 합니다. | DPS의 오케스트레이션은 여러 번 다시 시도 합니다. 재시도 정책은 현재 다음과 같습니다.<br><br>&nbsp;&nbsp;-다시 시도 횟수: 10<br>&nbsp;&nbsp;-초기 간격: 1<br>&nbsp;&nbsp;-증가값: 9 | SDK는 지정 된 시간 내에 오류를 무시 하 고 다른 get status 메시지를 제출 합니다. |
| Webhook는 다른 상태 코드를 반환 합니다. | 결과 상태: 실패<br><br>오류 코드: CustomAllocationFailed (400207) | SDK에서 반환 PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

이 문서에서 만든 리소스를 계속 사용 하려는 경우에는 그대로 둘 수 있습니다. 리소스를 계속 사용 하지 않으려는 경우 다음 단계를 사용 하 여이 문서에서 만든 모든 리소스를 삭제 하 여 불필요 한 요금을 방지 하세요.

여기에서 설명 하는 단계는이 문서에서 **모든 리소스를**만든 것으로 가정 합니다.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

이름으로 리소스 그룹을 삭제 하려면:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. **이름으로 필터링 ...** 텍스트 상자에 리소스를 포함 하는 리소스 그룹의 이름을 **입력 합니다.** 

3. 결과 목록에서 리소스 그룹의 오른쪽에 **있는 ...를 선택 하** 고 **리소스 그룹을 삭제**합니다.

4. 리소스 그룹의 삭제를 확인 하 라는 메시지가 표시 됩니다. 확인을 위해 리소스 그룹의 이름을 다시 입력 하 고 **삭제**를 선택 합니다. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

* 다시 프로 비전에 대 한 자세한 내용은 [IoT Hub Device 다시 프로 비전 개념](concepts-device-reprovision.md) 을 참조 하세요. 
* 프로 비전 해제에 대 한 자세한 내용은 [이전에 autoprovisioned 장치를 프로 비전 해제 하는 방법](how-to-unprovision-devices.md) 을 참조 하세요. 
