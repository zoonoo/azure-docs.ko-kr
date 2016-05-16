<properties
	pageTitle="ARM 템플릿 및 C#을 사용하여 IoT Hub 만들기 | Microsoft Azure"
	description="이 자습서에 따라 리소스 관리자 템플릿을 사용하여 C# 프로그램으로 IoT Hub를 만드는 작업을 시작할 수 있습니다."
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
     ms.date="05/03/2016"
     ms.author="dobett"/>

# ARM 템플릿을 사용하여 C# 프로그램에서 IoT Hub 만들기

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## 소개

ARM(Azure 리소스 관리자)을 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리합니다. 이 자습서는 리소스 관리자 템플릿을 사용하여 C# 프로그램에서 IoT Hub를 만드는 방법을 보여 줍니다.

> [AZURE.NOTE] Azure에는 리소스를 만들고 작업하기 위한 두 가지 다양한 배포 모델이 있습니다. [리소스 관리자 및 클래식](../resource-manager-deployment-model.md) 이 문서에서는 리소스 관리자 배포 모델 사용에 대해 설명합니다.

이 자습서를 완료하려면 다음이 필요합니다.

- Microsoft Visual Studio 2015.
- 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.
- 템플릿 파일을 저장할 수 있는 [Azure 저장소 계정][lnk-storage-account]입니다.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 이상

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Visual Studio 프로젝트 준비

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 새로 만듭니다. 프로젝트 이름을 **CreateIoTHub**로 지정합니다.

2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. NuGet 패키지 관리자에서 **시험판 포함**을 선택하고 **Microsoft.Azure.Management.Resources**를 검색합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**를 클릭하여 라이선스에 동의합니다.

4. NuGet 패키지 관리자에서 **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

5. NuGet 패키지 관리자에서 **Microsoft.Azure.Common**을 검색합니다. **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**를 클릭하여 라이선스에 동의합니다.

6. Program.cs에서 기존 **using** 문을 다음으로 바꿉니다.

    ```
    using System;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
7. Program.cs에서 다음 정적 변수를 추가하여 자리 표시자 값을 바꿉니다. 이 자습서의 앞부분에서 **ApplicationId**, **SubscriptionId**, **TenantId** 및 **암호**를 적어 두었습니다. **저장소 계정 이름**은 템플릿 파일을 저장할 Azure 저장소 계정의 이름입니다. **리소스 그룹 이름**은 IoT Hub를 만들 때 사용할 리소스 그룹의 이름으로, 기존의 리소스 그룹이거나 새 리소스 그룹입니다. **배포 이름**은 **Deployment\_01**과 같은 배포의 이름입니다.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## IoT hub를 만들 템플릿 제출

JSON 템플릿과 매개 변수 파일을 사용하여 리소스 그룹에 새 IoT hub를 만듭니다. 템플릿을 사용하여 기존 IoT Hub를 변경할 수도 있습니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **새 항목**을 차례로 클릭합니다. 프로젝트에 **template.json**이라는 JSON 파일을 새로 추가합니다.

2. **template.json**의 콘텐츠를 다음 리소스 정의로 바꿔서 표준 IoT hub를 **미국 동부** 지역에 새로 추가합니다.

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
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
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **새 항목**을 차례로 클릭합니다. 프로젝트에 **parameters.json**이라는 JSON 파일을 새로 추가합니다.

4. **{your initials}mynewiothub**와 같은 새 IoT Hub의 이름(이 이름은 전역적으로 고유해야 하므로 사용자의 이름 또는 이니셜을 포함해야 함)을 설정하는 다음 매개 변수 정보로 **parameters.json**의 내용을 바꿉니다.

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. **서버 탐색기**에서 Azure 구독에 연결하고 저장소 계정에서 **templates**라는 새 컨테이너를 만듭니다. **속성** 패널에서 **templates** 컨테이너의 **공용 읽기 액세스** 권한을 **Blob**로 설정합니다.

6. **서버 탐색기**에서 **templates** 컨테이너를 마우스 오른쪽 단추로 클릭한 다음 **BLob 컨테이너 보기**를 클릭합니다. **Blob 업로드** 단추를 클릭하고, **parameters.json** 파일과 **templates.json** 파일을 선택한 다음 **열기**를 클릭하여 JSON 파일을 **templates** 컨테이너에 업로드합니다. JSON 데이터를 포함하는 BLob의 URL은 다음과 같습니다.

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.windows.net/templates/template.json
    ```

7. Program.cs에 다음 메서드를 추가합니다.
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. **CreateIoTHub** 메서드에 다음 코드를 추가하여 Azure 리소스 관리자에게 템플릿 및 매개 변수 파일을 제출합니다.

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. 새 IoT Hub의 상태 및 키를 표시하는 다음 코드를 **CreateIoTHub** 메서드에 추가합니다.

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## 응용 프로그램을 완료하고 실행합니다.

이제 **CreateIoTHub** 메서드를 호출하여 응용 프로그램을 완료한 다음 빌드하고 실행합니다.

1. **Main** 메서드의 끝에 다음 코드를 추가합니다.

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. **빌드**, **솔루션 빌드**를 차례로 클릭합니다. 오류를 수정합니다.

3. **디버그**, **디버깅 시작**을 차례로 클릭하여 응용 프로그램을 실행합니다. 배포를 실행하는 데 몇 분 정도 걸릴 수 있습니다.

4. [포털][lnk-azure-portal]을 방문하여 리소스 목록을 보거나 **Get AzureRmResource** PowerShell cmdlet을 사용하여 응용 프로그램이 새 IoT Hub를 추가했는지 확인할 수 있습니다.

> [AZURE.NOTE] 이 예제 응용 프로그램은 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 완료되면 [포털][lnk-azure-portal] 또는 **Remove-AzureRmResource** PowerShell cmdlet을 사용하여 IoT Hub를 삭제할 수 있습니다.

## 다음 단계

ARM 템플릿을 사용하여 C# 프로그램에서 IoT Hub를 배포했으니 구체적인 내용을 알아볼 차례입니다.

- [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.
- Azure 리소스 관리자의 기능에 대한 자세한 내용은 [Azure 리소스 관리자 개요][lnk-azure-rm-overview]를 참조하세요.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

<!---HONumber=AcomDC_0504_2016-->