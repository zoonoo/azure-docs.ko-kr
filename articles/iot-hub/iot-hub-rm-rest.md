---
title: 리소스 공급자 REST API를 사용하여 Azure IoT Hub 만들기 | Microsoft Docs
description: 리소스 공급자 REST API를 사용하여 IoT Hub를 만드는 방법입니다.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: ca893670e01f4bccc211cd08064b6638e9224a9a
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606789"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>리소스 공급자 REST API(.NET)를 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

사용할 수는 [IoT Hub 리소스 공급자 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) 를 만들고 Azure IoT hub를 프로그래밍 방식으로 관리 합니다. 이 자습서는 IoT Hub 리소스 공급자 REST API를 사용하여 C# 프로그램에서 IoT Hub를 만드는 방법을 보여 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* [Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps) 이상.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Visual Studio 프로젝트 준비

1. Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 바탕 화면 프로젝트를 만듭니다. 프로젝트 이름을 **CreateIoTHubREST**로 지정합니다.

2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. NuGet 패키지 관리자에서 **시험판 포함**을 선택하고 **찾아보기** 페이지에서 **Microsoft.Azure.Management.ResourceManager**를 검색합니다. 패키지를 선택하고 **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

4. NuGet 패키지 관리자에서 **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색합니다.  **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

5. Program.cs에서 기존 **using** 문을 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Program.cs에서 다음 정적 변수를 추가하여 자리 표시자 값을 바꿉니다. 이 자습서의 앞부분에서 **ApplicationId**, **SubscriptionId**, **TenantId** 및 **암호**를 적어 두었습니다. **리소스 그룹 이름**은 IoT Hub를 만들 때 사용할 리소스 그룹의 이름입니다. 기존 또는 새 리소스 그룹을 사용할 수 있습니다. **IoT Hub 이름**은 **MyIoTHub**와 같이 사용자가 만든 IoT Hub의 이름입니다. IoT hub의 이름은 전역적으로 고유해야 합니다. **배포 이름**은 **Deployment_01**과 같은 배포의 이름입니다.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>리소스 공급자 REST API를 사용하여 IoT Hub 만들기

사용 된 [IoT Hub 리소스 공급자 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) 리소스 그룹에서 IoT hub를 만들려면. 리소스 공급자 REST API를 사용하여 기존 IoT Hub를 변경할 수도 있습니다.

1. Program.cs에 다음 메서드를 추가합니다.

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. **CreateIoTHub** 메서드에 다음 코드를 추가합니다. 이 코드에서는 헤더에서 인증 토큰을 사용하여 **HttpClient** 개체를 만듭니다.

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. **CreateIoTHub** 메서드에 다음 코드를 추가합니다. 이 코드는 JSON 표현을 만들고 생성하는 IoT Hub를 설명합니다. IoT Hub를 지 원하는 위치의 현재 목록은 참조 하세요 [Azure 상태](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. **CreateIoTHub** 메서드에 다음 코드를 추가합니다. 이 코드는 Azure에 REST 요청을 제출합니다. 그런 다음, 코드는 응답을 확인하며 배포 작업의 상태를 모니터링하는 데 사용할 수 있는 URL을 검색합니다.

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. **CreateIoTHub** 메서드의 끝에 다음 코드를 추가합니다. 이 코드는 배포가 완료되기를 대기하는 이전 단계에서 검색된 **asyncStatusUri** 주소를 사용합니다.

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. **CreateIoTHub** 메서드의 끝에 다음 코드를 추가합니다. 이 코드는 사용자가 만들고 콘솔에 출력한 IoT Hub의 키를 검색합니다.

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>애플리케이션을 완료하고 실행합니다.

이제 애플리케이션을 빌드하고 실행하기 전에 **CreateIoTHub** 메서드를 호출하여 완료합니다.

1. **Main** 메서드의 끝에 다음 코드를 추가합니다.

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. **빌드**, **솔루션 빌드**를 차례로 클릭합니다. 오류를 수정합니다.

3. **디버그**, **디버깅 시작**을 차례로 클릭하여 애플리케이션을 실행합니다. 배포를 실행하는 데 몇 분 정도 걸릴 수 있습니다.

4. 응용 프로그램이 새 IoT hub에 추가 했는지를 확인 하려면 다음을 방문 합니다 [Azure portal](https://portal.azure.com/) 리소스 목록을 확인 하 고 있습니다. 또는 사용 하 여 합니다 **Get AzResource** PowerShell cmdlet.

> [!NOTE]
> 이 예제 애플리케이션은 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 완료 했으면 IoT hub를 삭제할 수 있습니다 합니다 [Azure portal](https://portal.azure.com/) 또는 사용 하 여 합니다 **제거 AzResource** 했으면 PowerShell cmdlet.

## <a name="next-steps"></a>다음 단계

리소스 공급자 REST API를 사용하여 IoT Hub를 배포했으면 구체적인 내용을 알아볼 차례입니다.

* 기능에 대 한 읽기를 [IoT Hub 리소스 공급자 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)합니다.

* 읽기 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md) Azure Resource Manager의 기능에 자세히 알아보려면 합니다.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)