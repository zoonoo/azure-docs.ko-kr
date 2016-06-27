<properties
	pageTitle="C# 및 Resource Manager 템플릿을 사용하여 VM 배포 | Microsoft Azure"
	description="C# 및 Resource Manager 템플릿을 사용하여 Azure VM을 배포하는 방법에 대해 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# C# 및 Resource Manager 템플릿을 사용하여 Azure 가상 컴퓨터 배포

리소스 그룹 및 템플릿을 사용하면 응용 프로그램을 함께 지원하는 모든 리소스를 관리할 수 있습니다. 이 문서는 Azure PowerShell을 사용하여 인증 및 저장소를 설정한 다음 C#을 사용하여 Azure 리소스를 만들고 배포하는 방법을 보여 줍니다.

이 자습서를 완료하려면 다음이 필요합니다.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [인증 토큰](../resource-group-authenticate-service-principal.md)

이러한 단계를 수행하려면 약 30분이 걸립니다.

## 1단계: Azure PowerShell 설치

최신 버전의 Azure PowerShell을 설치하고 사용하려는 구독을 선택하며 Azure 계정에 로그인하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.
    
## 2단계: 템플릿 저장소에 대한 리소스 그룹 만들기

모든 리소스는 리소스 그룹에 배포되어야 합니다. 자세한 내용은 [Azure Resource Manager 개요](../resource-group-overview.md)를 참조하세요.

1. 리소스를 만들 수 있는 사용 가능한 위치 목록을 가져옵니다.

	    Get-AzureLocation | sort Name | Select Name

2. **$locName** 값을 목록의 위치(예: **미국 중부**)로 바꿉니다. 변수를 만듭니다.

        $locName = "location name"
        
3. **$rgName** 값을 새 리소스 그룹 이름으로 바꿉니다. 변수 및 리소스 그룹을 만듭니다.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    다음과 유사한 결과가 표시됩니다.
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
    
## 3단계: 저장소 계정 및 템플릿 컨테이너 만들기

저장소 계정은 만들고 배포할 템플릿을 저장하기 위해 필요합니다.

1. $stName의 값을 저장소 계정의 이름(소문자와 숫자만 사용 가능)으로 바꿉니다. 이름의 고유성을 테스트합니다.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    이 명령에서 **False**가 반환되면 제안한 이름이 고유한 것입니다.
    
2. 이제 명령을 실행하여 저장소 계정을 만듭니다.
    
        New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
3. {blob-storage-endpoint}를 계정의 blob 저장소의 끝점으로 바꿉니다. {storage-account-name}을 저장소 계정 이름으로 바꿉니다. {primary-storage-key}를 기본 선택키로 바꿉니다. 파일이 저장되는 컨테이너를 만들려면 이 명령을 실행합니다. Azure 포털에서 끝점 및 키 값을 가져올 수 있습니다.

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint={blob-storage-endpoint};AccountName={storage-account-name};AccountKey={primary-storage-key}"
        $ctx = New-AzureStorageContext -ConnnectionString $ConnectionString
        New-AzureStorageContainer -Name "templates" -Permission Blob -Context $ctx

## 3단계: Visual Studio 프로젝트, 템플릿 파일 및 매개 변수 파일 만들기

### 템플릿 파일 만들기

Azure 리소스 관리자 템플릿을 사용하면 리소스와 관련 배포 매개 변수에 대한 JSON 설명을 사용하여 Azure 리소스를 함께 배포하고 관리할 수 있습니다. 이 자습서에서 작성하는 템플릿은 템플릿 갤러리에서 찾을 수 있는 템플릿과 유사합니다. 자세한 내용을 알아보려면 [미국 서부에서 간단한 Windows VM 배포](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/)를 참조하세요.

Visual Studio에서 다음을 수행:

1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.

2. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

3. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.

4. 웹을 클릭하고 JSON 파일을 선택하고 이름으로 *VirtualMachineTemplate.json*을 입력한 다음 **추가**를 클릭합니다.

5. VirtualMachineTemplate.json 파일의 대괄호 열기 및 닫기 기호에서 필요한 스키마 요소 및 필요한 contentVersion 요소를 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [매개 변수](../resource-group-authoring-templates.md#parameters)는 항상 필요한 것은 아니지만 템플릿 관리를 용이하게 합니다. 값 형식, 기본값(필요한 경우) 및 매개 변수의 허용되는 값을 설명합니다. 이 자습서에서는 가상 컴퓨터, 저장소 계정 및 가상 네트워크를 만드는 데 사용되는 매개 변수가 템플릿에 추가됩니다. 매개 변수 요소와 해당 자식 요소를 contentVersion 요소 뒤에 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
        }

7. 템플릿에서 [변수](../resource-group-authoring-templates.md#variables)를 사용하여 자주 변경되는 값 또는 매개 변수 값의 조합에서 만들어야 하는 값을 지정할 수 있습니다. 변수 요소를 매개 변수 섹션 뒤에 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" },
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdiskforwindowssimple",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"  
          },
        }

8. 가상 컴퓨터, 가상 네트워크 및 저장소 계정과 같은 [리소스](../resource-group-authoring-templates.md#resources)는 다음에 템플릿에 정의됩니다. 리소스 섹션을 변수 섹션 뒤에 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "OSDiskName": "osdiskforwindowssimple",
            "windowsOSVersion": "2012-R2-Datacenter",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "apiVersion": "2015-06-15",
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [ "[variables('addressPrefix')]" ]
                },
                "subnets": [ {
                  "name": "[variables('subnetName')]",
                  "properties": {
                    "addressPrefix": "[variables('subnetPrefix')]"
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
                    },
                    "subnet": {
                      "id": "[variables('subnetRef')]"
                    }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computerName": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]",
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku": "[variables('windowsOSVersion')]",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                  } ]
                }
              }
            } ]
          }
      
9. 만든 템플릿 파일을 저장합니다.

### 매개 변수 파일 만들기

템플릿에 정의된 리소스 매개 변수의 값을 지정하려면 값이 포함된 매개 변수 파일을 만들고 템플릿과 함께 리소스 관리자에 제출합니다. Visual Studio에서 다음을 수행:

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.

2. 웹을 클릭하고 JSON 파일을 선택하고 이름으로 *Parameters.json*을 입력한 다음 **추가**를 클릭합니다.

3. Parameters.json 파일을 열고 다음 JSON 내용을 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] 이 문서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md)을 참조하세요.

4. 만든 매개 변수 파일을 저장합니다.

### 파일 업로드 및 해당 팔이르 사용하기 위한 사용 권한 설정 

템플릿 파일과 매개 변수 파일은 Azure 저장소 계정에서 Azure 리소스 관리자를 통해 액세스할 수 있습니다. 만든 첫 번째 저장소의 파일을 배치하려면 다음을 수행합니다.

1. 클라우드 탐색기를 연 다음 이전에 만든 저장소 계정의 템플릿 컨테이너로 이동합니다.

2. 템플릿 컨테이너 창의 오른쪽 위에서 Blob 업로드 아이콘을 클릭하고 만들었던 VirtualMachineTemplate.json 파일을 찾아서 **열기**를 클릭합니다.

3. Blob 업로드 아이콘을 다시 클릭하고 사용자가 만든 Parameters.json 파일을 찾아서 **열기**를 클릭합니다.

## 4단계: 라이브러리 설치

NuGet 패키지는 이 자습서를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Azure 리소스 관리 라이브러리 및 Azure Active Directory 인증 라이브러리를 설치해야 합니다. Visual Studio에서 이 라이브러리를 가져오려면 다음을 수행합니다.

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

2. 검색 상자에 *Active Directory*를 입력하고 Active Directory 인증 라이브러리 패키지에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

4. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.ResourceManager*를 입력하고 Microsoft Azure 리소스 관리 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

##5단계: 요청을 인증하는데 사용되는 자격 증명 만들기

Azure Active Directory 응용 프로그램이 생성되고 인증 라이브러리가 설치되므로 이제 Azure Resource Manager에 요청을 인증하는 데 사용되는 자격 증명에 대한 응용 프로그램 정보의 서식을 지정합니다. 방법:

1. 만들었던 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;
        using Microsoft.Rest;

2.	Program 클래스에 다음 메서드를 추가하여 자격 증명을 만드는 데 필요한 토큰을 가져옵니다.

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }

    {application-id}를 이전에 기록한 응용 프로그램 ID로 바꾸고, {password}를 AD 응용 프로그램에 대해 선택한 암호로 바꾸고, {tenant-id}를 구독을 위한 테넌트 ID로 바꿉니다. Get-AzureRmSubscription을 실행하여 테넌트 ID를 찾을 수 있습니다.

3. Program.cs 파일에서 Main 메서드에 다음 코드를 추가하여 자격 증명을 만듭니다.

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Program.cs 파일을 저장합니다.

## 6단계: 템플릿을 배포하는 코드 추가

이 단계에서는 [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 및 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 클래스를 사용하여 리소스가 배포되는 리소스 그룹을 만듭니다.

1. 변수를 Program 클래스의 Main 메서드에 추가하여 리소스, "미국 중부"와 같은 리소스의 위치, 관리자 계정 정보 및 구독 식별자에 사용하려는 이름을 지정합니다.

        var groupName = "resource group name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var deploymentName = "deployment name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    모든 변수 값을 사용하려는 이름 및 식별자로 바꿉니다. Get-AzureRmSubscription을 실행하여 구독 식별자를 찾을 수 있습니다. StorageName 변수의 값은 템플릿이 저장된 저장소 계정의 이름입니다.
    
2. Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 만듭니다.

        public static void CreateResourceGroup(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var resourceGroup = new ResourceGroup {
            Location = location
          };
          var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
          Console.WriteLine(rgResult.Properties.ProvisioningState);
        }

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

3. Program 클래스에 이 메서드를 추가하여 사용자가 정의한 템플릿으로 리소스 그룹에 리소스를 배포합니다.

        public static void CreateTemplateDeployment(
          TokenCredentials credential,
          string groupName,
          string storageName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/VirtualMachineTemplate.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/Parameters.json"
            }
          };
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
            groupName,
            deploymentName,
            deployment);
          Console.WriteLine(dpResult.Properties.ProvisioningState);
        }

4. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        CreateTemplateDeployment(
          credential,
          groupName",
          storageName,
          deploymentName,
          subscriptionId);
        Console.ReadLine();

##7단계: 리소스를 삭제하는 코드 추가

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 리소스 그룹에서 각 리소스를 개별적으로 삭제할 필요가 없습니다. 리소스 그룹을 삭제하면 모든 해당 리소스가 자동으로 삭제됩니다.

1.	Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 삭제합니다.

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceGroupClient = new ResourceManagementClient(credential);
          resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

##8단계: 콘솔 응용 프로그램 실행

1.	콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

2.	수락됨 상태가 표시된 후에 **Enter** 키를 누릅니다.

	이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. Enter를 눌러 리소스를 삭제하기 전에 Azure 포털에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다.

3. 리소스의 상태를 보려면 Azure 포털에서 감사 로그로 이동합니다.

	![Azure 포털에서 감사 로그 찾아보기](./media/virtual-machines-windows-csharp-template/crpportal.png)

## 다음 단계

- 배포에 문제가 있는 경우 다음 단계로서 [Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)을 살펴보세요.
- [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)에서 방금 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0615_2016-->