<properties
	pageTitle="REST API를 사용하여 IoT Hub 만들기 | Microsoft Azure"
	description="이 자습서를 따라 REST API를 사용하기 시작하고 IoT Hub를 만듭니다."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="dobett"/>

# 자습서: C# 프로그램을 사용하여 IoT Hub 만들기

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## 소개

[IoT Hub 리소스 공급자 REST API][lnk-rest-api]를 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리합니다. 이 자습서는 리소스 공급자 REST API를 사용하여 C# 프로그램에서 IoT Hub를 만드는 방법을 보여 줍니다.

이 자습서를 완료하려면 다음이 필요합니다.

- Microsoft Visual Studio 2015.
- 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 이상

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Visual Studio 프로젝트 준비

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 새로 만듭니다. 프로젝트 이름을 **CreateIoTHubREST**로 지정합니다.

2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. NuGet 패키지 관리자에서 **시험판 포함**을 선택하고 **Microsoft.Azure.Management.Resources**를 검색합니다. **2.18.11-preview** 버전을 선택합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

4. NuGet 패키지 관리자에서 **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색합니다. 버전 **2.19.208020213**을 선택합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**를 클릭하여 라이선스에 동의합니다.

5. NuGet 패키지 관리자에서 **Microsoft.Azure.Common**을 검색합니다. 버전 **2.1.0**을 선택합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**를 클릭하여 라이선스에 동의합니다.

6. Program.cs에서 기존 **using** 문을 다음으로 바꿉니다.

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    ```
    
7. Program.cs에서 다음 정적 변수를 추가하여 자리 표시자 값을 바꿉니다. 이 자습서의 앞부분에서 **ApplicationId**, **SubscriptionId**, **TenantId** 및 **암호**를 적어 두었습니다. **리소스 그룹 이름**은 IoT Hub를 만들 때 사용할 리소스 그룹의 이름으로, 기존의 리소스 그룹이거나 새 리소스 그룹입니다. **IoT Hub 이름**은 **MyIoTHub**와 같이 만들 IoT Hub의 이름입니다. **배포 이름**은 **Deployment\_01**과 같은 배포의 이름입니다.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## REST API를 사용하여 IoT Hub 만들기

IoT Hub 리소스 공급자 REST API를 사용하여 리소스 그룹에 새 IoT Hub를 만듭니다. [REST API][lnk-rest-api]를 사용하여 기존 IoT Hub를 변경할 수도 있습니다.

1. Program.cs에 다음 메서드를 추가합니다.
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client, string token)
    {
        
    }
    ```

2. 다음 코드를 **CreateIoTHub** 메서드에 추가하여 인증 토큰을 요청에 추가합니다.

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
      new AuthenticationHeaderValue("Bearer", token);
    ```

3. 다음 코드를 **CreateIoTHub** 메서드에 추가하여 JSON 표현을 만들고 생성하기 위한 IoT Hub를 설명합니다.

    ```
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
    
    var content = new StringContent(
      JsonConvert.SerializeObject(description),
      Encoding.UTF8, "application/json");
    ```

4. 다음 코드를 **CreateIoTHub** 메서드에 추가하여 Azure에 REST 요청을 제출하고 응답을 확인합니다.

    ```
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2015-08-15-preview",
      subscriptionId, rgName, iotHubName);
    var httpsRepsonse = client.HttpClient.PutAsync(
      requestUri, content).Result;

    if (!httpsRepsonse.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", httpsRepsonse.Content.ReadAsStringAsync().Result);
      return false;
    }
    ```

5. 다음 코드를 **CreateIoTHub** 메서드 끝에 추가하여 배포가 완료되기를 기다립니다.

    ```
    ResourceGetResult resourceGetResult = null;
    do
    {
    resourceGetResult = client.Resources.GetAsync(
      rgName,
      new ResourceIdentity()
      {
        ResourceName = iotHubName,
        ResourceProviderApiVersion = "2015-08-15-preview",
        ResourceProviderNamespace = "Microsoft.Devices",
        ResourceType = "IotHubs"
      }).Result;
    Console.WriteLine("IoTHub state {0}", 
      resourceGetResult.Resource.ProvisioningState);
    } while (resourceGetResult.Resource.ProvisioningState != "Succeeded" 
          && resourceGetResult.Resource.ProvisioningState != "Failed");
    
    if (resourceGetResult.Resource.ProvisioningState != "Succeeded")
    {
        Console.WriteLine("Failed to create iothub");
        return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## 응용 프로그램을 완료하고 실행합니다.

이제 **CreateIoTHub** 및 **ShowIoTHubKeys** 메서드를 호출하여 응용 프로그램을 완료한 다음 빌드하고 실행합니다.

1. **Main** 메서드의 끝에 다음 코드를 추가합니다.

    ```
    if (CreateIoTHub(client, token.AccessToken))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. **빌드**, **솔루션 빌드**를 차례로 클릭합니다. 오류를 수정합니다.

3. **디버그**, **디버깅 시작**을 차례로 클릭하여 응용 프로그램을 실행합니다. 배포를 실행하는 데 몇 분 정도 걸릴 수 있습니다.

4. [포털][lnk-azure-portal]을 방문하여 리소스 목록을 보거나 **Get AzureRmResource** PowerShell cmdlet을 사용하여 응용 프로그램이 새 IoT Hub를 추가했는지 확인할 수 있습니다.

> [AZURE.NOTE]이 예제 응용 프로그램은 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 완료되면 [포털][lnk-azure-portal] 또는 **Remove-AzureRmResource** PowerShell cmdlet을 사용하여 IoT Hub를 삭제할 수 있습니다.

## 다음 단계

- [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 살펴봅니다.
- Azure 리소스 관리자의 기능에 대한 자세한 내용은 [Azure 리소스 관리자 개요][lnk-azure-rm-overview]를 참조하세요.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/ko-KR/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_1203_2015-->