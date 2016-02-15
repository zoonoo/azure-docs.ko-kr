<properties
	pageTitle="리소스 관리자 템플릿을 사용하여 IoT Hub 만들기 | Microsoft Azure"
	description="이 자습서를 통해 리소스 관리자 템플릿을 사용하여 IoT Hub 만들기를 시작할 수 있습니다."
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

Azure 리소스 관리자를 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리합니다. 이 자습서는 리소스 관리자 템플릿을 사용하여 C# 프로그램에서 IoT Hub를 만드는 방법을 보여 줍니다.

> [AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 다양한 배포 모델이 있습니다. [리소스 관리자 및 클래식](../resource-manager-deployment-model.md) 이 문서에서는 리소스 관리자 배포 모델 사용에 대해 설명합니다.

이 자습서를 완료하려면 다음이 필요합니다.

- Microsoft Visual Studio 2015.
- 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 이상

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Visual Studio 프로젝트 준비

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 새로 만듭니다. 프로젝트 이름을 **CreateIoTHub**로 지정합니다.

2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. NuGet 패키지 관리자에서 **Microsoft.Azure.Management.Resources**를 검색합니다. **2.18.11-preview** 버전을 선택합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**를 클릭하여 라이선스에 동의합니다.

4. NuGet 패키지 관리자에서 **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색합니다. **2.19.208020213** 버전을 선택합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

5. NuGet 패키지 관리자에서 **Microsoft.Azure.Common**을 검색합니다. **2.1.0** 버전을 선택합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**를 클릭하여 라이선스에 동의합니다.

6. Program.cs에서 기존 **using** 문을 다음으로 바꿉니다.

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http.Headers;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
7. Program.cs에서 다음 정적 변수를 추가하여 자리 표시자 값을 바꿉니다. 이 자습서의 앞부분에서 **ApplicationId**, **SubscriptionId**, **TenantId** 및 **암호**를 적어 두었습니다. **리소스 그룹 이름**은 IoT Hub를 만들 때 사용할 리소스 그룹의 이름으로, 기존의 리소스 그룹이거나 새 리소스 그룹입니다. **IoT Hub 이름**은 **MyIoTHub**와 같은 만들려는 IoT Hub의 이름입니다. **배포 이름**은 **Deployment\_01**과 같은 배포의 이름입니다.

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

## IoT hub를 만들 템플릿 제출

JSON 템플릿을 사용하여 리소스 그룹에 새 IoT hub를 만듭니다. 템플릿을 사용하여 기존 IoT Hub를 변경할 수도 있습니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다. 프로젝트에 **template.json**이라는 JSON 파일을 새로 추가합니다.

2. 솔루션 탐색기에서 **template.json**을 선택한 다음 **속성**에서 **출력 디렉터리로 복사**를 **항상 복사**로 설정합니다.

3. **template.json**의 콘텐츠를 다음 리소스 정의로 바꿔서 표준 IoT hub를 **미국 동부** 지역에 새로 추가합니다.

    ```
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",

    "resources": [
      {
        "apiVersion": "2015-08-15-preview",
        "type": "Microsoft.Devices/Iothubs",
        "name": "[IotHubName]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
    ]
    }
    ```

4. Program.cs에 다음 메서드를 추가합니다.
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. 다음 코드를 **CreateIoTHub** 메서드에 추가하여 템플릿 파일을 로드하고, IoT hub의 이름을 추가하고, 템플릿을 Azure 리소스 관리자에 제출합니다.

    ```
    string template = File.ReadAllText("template.json");
    template = template.Replace("[IotHubName]", iotHubName);
    var createResponse = client.Deployments.CreateOrUpdateAsync(
      rgName,
      deploymentName,
      new Deployment()
      {
        Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          Template = template
        }
      }).Result;
    ```

6. 배포가 성공적으로 완료될 때가지 기다리는 다음 코드를 **CreateIoTHub** 메서드에 추가합니다.

    ```
    string state = createResponse.Deployment.Properties.ProvisioningState;
    while (state != "Succeeded" && state != "Failed")
    {
      var getResponse = client.Deployments.GetAsync(
        rgName,
        deploymentName).Result;

      state = getResponse.Deployment.Properties.ProvisioningState;
      Console.WriteLine("Deployment state: {0}", state);
    }

    if (state != "Succeeded")
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
    if (CreateIoTHub(client))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. **빌드**, **솔루션 빌드**를 차례로 클릭합니다. 오류를 수정합니다.

3. **디버그**, **디버깅 시작**을 차례로 클릭하여 응용 프로그램을 실행합니다. 배포를 실행하는 데 몇 분 정도 걸릴 수 있습니다.

4. [포털][lnk-azure-portal]을 방문하여 리소스 목록을 보거나 **Get AzureRmResource** PowerShell cmdlet을 사용하여 응용 프로그램이 새 IoT Hub를 추가했는지 확인할 수 있습니다.

> [AZURE.NOTE] 이 예제 응용 프로그램은 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 완료되면 [포털][lnk-azure-portal] 또는 **Remove-AzureRmResource** PowerShell cmdlet을 사용하여 IoT Hub를 삭제할 수 있습니다.

## 다음 단계

- [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 살펴봅니다.
- Azure 리소스 관리자의 기능에 대한 자세한 내용은 [Azure 리소스 관리자 개요][lnk-azure-rm-overview]를 참조하세요.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/ko-KR/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_0204_2016-->