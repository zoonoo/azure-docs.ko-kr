---
title: "C# 및 Resource Manager 템플릿을 사용하여 VM 배포 | Microsoft Docs"
description: "C# 및 Resource Manager 템플릿을 사용하여 Azure VM을 배포하는 방법에 대해 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 0782000e87bed0d881be5238c1b91f89a970682c
ms.openlocfilehash: 8424fb5d107935833e9652ef86e03933ea14c26e


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>C# 및 Resource Manager 템플릿을 사용하여 Azure 가상 컴퓨터 배포
리소스 그룹 및 템플릿을 사용하면 응용 프로그램을 함께 지원하는 모든 리소스를 관리할 수 있습니다. 이 문서에서는 Visual Studio 및 C#을 사용하여 인증을 설정하고, 템플릿을 만든 다음, 사용자가 만든 템플릿을 사용하여 Azure 리소스를 배포하는 방법을 보여 줍니다.

먼저 다음 설정 단계를 수행했는지 확인해야 합니다.

* [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) 설치 확인
* [인증 토큰](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 또는 [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)을 사용하여 리소스 그룹을 만듭니다.

이러한 단계를 수행하려면 약 30분이 걸립니다.

## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>1단계: Visual Studio 프로젝트, 템플릿 파일 및 매개 변수 파일 만들기
### <a name="create-the-template-file"></a>템플릿 파일 만들기
Azure Resource Manager 템플릿을 사용하면 Azure 리소스 배포 및 관리를 함께 수행할 수 있습니다. 템플릿은 리소스 및 관련 배포 매개 변수에 대한 JSON 설명입니다.

Visual Studio에서 다음 단계를 수행합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
2. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.
3. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**)로 바꿉니다.
4. 웹을 클릭하고 JSON 파일을 선택하고 이름으로 *VirtualMachineTemplate.json* 을 입력한 다음 **추가**를 클릭합니다.
5. VirtualMachineTemplate.json 파일의 대괄호 열기 및 닫기 기호에서 필요한 스키마 요소 및 필요한 contentVersion 요소를 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. [매개 변수](../azure-resource-manager/resource-group-authoring-templates.md#parameters)가 항상 필요한 것은 아니지만 템플릿이 배포될 때 값을 입력하는 방법을 제공합니다. 매개 변수 요소와 해당 자식 요소를 contentVersion 요소 뒤에 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. [변수](../azure-resource-manager/resource-group-authoring-templates.md#variables) 를 사용하여 자주 변경되는 값 또는 매개 변수 값의 조합에서 만들어야 하는 값을 지정할 수 있습니다. 변수 요소를 매개 변수 섹션 뒤에 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. [리소스](../azure-resource-manager/resource-group-authoring-templates.md#resources) 는 다음에 템플릿에 정의됩니다. 리소스 섹션을 변수 섹션 뒤에 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. 만든 템플릿 파일을 저장합니다.

### <a name="create-the-parameters-file"></a>매개 변수 파일 만들기
템플릿에 정의된 리소스 매개 변수의 값을 지정하려면 템플릿이 배포될 때 사용된 값이 들어 있는 매개 변수 파일을 만듭니다. Visual Studio에서 다음 단계를 수행합니다.

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**)로 바꿉니다.
2. 웹을 클릭하고 JSON 파일을 선택하고 이름으로 *Parameters.json* 을 입력한 다음 **추가**를 클릭합니다.
3. Parameters.json 파일을 열고 다음 JSON 내용을 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

   > [!NOTE]
   > 이 문서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
   >
   >
4. 만든 매개 변수 파일을 저장합니다.

## <a name="step-2-install-the-libraries"></a>2단계: 라이브러리 설치
NuGet 패키지는 이 자습서를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. 리소스를 만들려면 Azure 리소스 관리 라이브러리 및 Azure Active Directory 인증 라이브러리가 필요합니다. Visual Studio에서 이러한 라이브러리를 가져오려면 다음 단계를 수행합니다.

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭하고 찾아보기를 클릭합니다.
2. 검색 상자에 *Active Directory* 를 입력하고 Active Directory 인증 라이브러리 패키지에 대해 **설치** 를 클릭한 다음 지침에 따라 패키지를 설치합니다.
3. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.Management.ResourceManager* 를 입력하고 Microsoft Azure 리소스 관리 라이브러리에 대해 **설치** 를 클릭한 다음 지침에 따라 패키지를 설치합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>3단계: 요청을 인증하는데 사용되는 자격 증명 만들기
Azure Active Directory 응용 프로그램이 생성되고 인증 라이브러리가 설치됩니다. 이제 응용 프로그램 정보의 서식을 Azure Resource Manager에 요청을 인증하는 데 사용되는 자격 증명으로 지정합니다.

1. 만들었던 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. Program 클래스에 다음 메서드를 추가하여 자격 증명을 만드는 데 필요한 토큰을 가져옵니다.

   ```
   private static async Task<AuthenticationResult> GetAccessTokenAsync()
   {
     var cc = new ClientCredential("{client-id}", "{client-secret}");
     var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
     var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
     if (token == null)
     {
       throw new InvalidOperationException("Could not get the token.");
     }
     return token;
   }
   ```

   {client-id}를 Azure Active Directory의 식별자로 바꾸고 {client-secret}을 AD 응용 프로그램의 선택키로 바꾸고, {tenant-id}를 구독의 테넌트 식별자로 바꿉니다. Get-AzureRmSubscription을 실행하여 테넌트 ID를 찾을 수 있습니다. 선택키는 Azure 포털을 사용하여 찾을 수 있습니다.
3. 자격 증명을 만들려면 Program.cs 파일에서 Main 메서드에 다음 코드를 추가합니다.

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Program.cs 파일을 저장합니다.

## <a name="step-4-deploy-the-template"></a>4단계: 템플릿 배포
이 단계에서는 앞서 만든 리소스 그룹을 사용하지만 [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 및 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 클래스를 사용하여 리소스 그룹을 만들 수도 있습니다.

1. 변수를 Program 클래스의 Main 메서드에 추가하여 앞서 만든 리소스의 이름, 배포 이름 및 구독 식별자를 지정합니다.

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    groupName 값을 리소스 그룹의 이름으로 바꿉니다. deploymentName 값을 배포에 사용하려는 이름으로 바꿉니다. Get-AzureRmSubscription을 실행하여 구독 식별자를 찾을 수 있습니다.
2. Program 클래스에 이 메서드를 추가하여 사용자가 정의한 템플릿으로 리소스 그룹에 리소스를 배포합니다.

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    저장소 계정에서 템플릿을 배포하려는 경우 Template 속성을 TemplateLink 속성으로 바꿀 수 있습니다.
3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>5단계: 리소스 삭제
Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 리소스 그룹에서 각 리소스를 개별적으로 삭제할 필요가 없습니다. 리소스 그룹을 삭제하면 모든 해당 리소스가 자동으로 삭제됩니다.

1. 리소스 그룹을 삭제하려면 Program 클래스에 다음 메서드를 추가합니다.

   ```
   public static async void DeleteResourceGroupAsync(
     TokenCredentials credential,
     string groupName,
     string subscriptionId)
   {
     Console.WriteLine("Deleting resource group...");
     var resourceManagementClient = new ResourceManagementClient(credential)
       { SubscriptionId = subscriptionId };
     await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
   }
   ```

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-6-run-the-console-application"></a>6단계: 콘솔 응용 프로그램 실행
1. 콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 자격 증명을 사용하여 Azure AD에 로그인합니다.
2. 수락됨 상태가 표시된 후에 **Enter** 키를 누릅니다.

   이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약&5;분이 필요합니다. Enter를 눌러 리소스를 삭제하기 전에 Azure 포털에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다.
3. 리소스의 상태를 보려면 Azure 포털에서 감사 로그로 이동합니다.

    ![Azure 포털에서 감사 로그 찾아보기](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>다음 단계
* 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)을 살펴봅니다.
* [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 검토하여 자신이 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.



<!--HONumber=Jan17_HO2-->


