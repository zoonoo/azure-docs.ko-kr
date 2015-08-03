<properties pageTitle="템플릿을 사용하여 Azure 리소스 배포" description="Azure 리소스 관리 라이브러리에서 사용 가능한 클라이언트 중 일부를 사용하여 가상 컴퓨터, 가상 네트워크 및 저장소 계정을 배포하는 방법을 알아봅니다." services="virtual-machines,virtual-networks,storage" documentationCenter="" authors="davidmu1" manager="timlt" editor="tysonn" tags="azure-resource-manager/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na" 
	ms.topic="article"
	ms.date="06/15/2015"
	ms.author="davidmu"/>

# .NET 라이브러리 및 템플릿을 사용하여 Azure 리소스 배포

리소스 그룹 및 템플릿을 사용하면 응용 프로그램을 함께 지원하는 모든 리소스를 관리할 수 있습니다. 이 자습서에는 Azure 리소스 관리 라이브러리에서 사용 가능한 일부 클라이언트를 사용하는 방법 및 가상 컴퓨터, 가상 네트워크 및 저장소 계정을 배포하기 위해 템플릿을 빌드하는 방법을 보여 줍니다.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

이 자습서를 완료하려면 다음이 필요합니다.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure 저장소 계정](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/ko-kr/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/ko-kr/download/details.aspx?id=40855)
- [Azure PowerShell](../powershell-install-configure.md)

이러한 단계를 수행하려면 약 30분이 걸립니다.

## 1단계: Azure AD에 응용 프로그램 추가 및 사용 권한 설정

Azure AD를 사용하여 Azure 리소스 관리자에 요청을 인증하려면, 응용 프로그램이 기본 디렉터리에 추가되어야 합니다. 응용 프로그램을 추가하려면 다음을 수행 합니다.

1. Azure PowerShell 명령 프롬프트를 열고 이 명령을 실행합니다.

        Switch-AzureMode –Name AzureResourceManager

2. 이 자습서에 사용하려는 Azure 계정을 설정합니다. 이 명령을 실행하고 메시지가 표시되면 구독에 대한 자격 증명을 입력합니다.

	    Add-AzureAccount

3. 다음 명령에서 {password}를 사용할 명령으로 바꾼 다음 실행하여 응용 프로그램을 만듭니다.

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. 이전 단계의 응답에서 ApplicationId 값을 기록합니다. 이 값은 자습서의 뒷부분에서 필요합니다.

	![AD 응용 프로그램 만들기](./media/arm-template-deployment/azureapplicationid.png)

	>[AZURE.NOTE]또한 관리 포털에서 응용 프로그램의 클라이언트 id 필드에서 응용 프로그램 ID를 찾을 수 있습니다.

5. {application-id}를 방금 기록된 식별자로 바꾼 다음 해당 응용 프로그램에 대한 서비스 사용자를 만듭니다.

        New-AzureADServicePrincipal -ApplicationId {application-id}

6. 응용 프로그램을 사용하는 사용 권한을 설정합니다.

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 2단계: Visual Studio 프로젝트, 템플릿 파일 및 매개 변수 파일 만들기

###템플릿 파일 만들기

Azure 리소스 관리자 템플릿을 사용하면 리소스와 관련 배포 매개 변수에 대한 JSON 설명을 사용하여 Azure 리소스를 함께 배포하고 관리할 수 있습니다. Visual Studio에서 다음을 수행합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.

2. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

3. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.

4.	새 항목 추가 창에서 **텍스트 파일**을 선택하고 *VirtualMachineTemplate.json*을 이름으로 입력한 후 **추가**를 클릭합니다.

5.	VirtualMachineTemplate.json 파일을 열고 여는 괄호와 닫는 괄호, 필요한 스키마 요소 및 필요한 contentVersion 요소를 추가합니다.

        {
            "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
            "contentVersion": "1.0.0.0",
        }

6. [매개 변수](https://msdn.microsoft.com/library/azure/dn835138.aspx#parameters)는 항상 필요한 것은 아니지만 템플릿 관리를 용이하게 합니다. 값 형식, 기본값(필요한 경우) 및 매개 변수의 허용되는 값을 설명합니다. 이 자습서에서는 가상 컴퓨터, 저장소 계정 및 가상 네트워크를 만드는 데 사용되는 매개 변수가 템플릿에 추가됩니다.

    매개 변수 요소와 해당 자식 요소를 contentVersion 요소 뒤에 추가합니다.

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
        }

7.	템플릿에서 [변수](https://msdn.microsoft.com/library/azure/dn835138.aspx#variables)를 사용하여 자주 변경되는 값 또는 매개 변수 값의 조합에서 만들어야 하는 값을 지정할 수 있습니다.

    변수 요소를 매개 변수 섹션 뒤에 추가합니다.

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
        }

8.	가상 컴퓨터, 가상 네트워크 및 저장소 계정과 같은 [리소스](https://msdn.microsoft.com/library/azure/dn835138.aspx#resources)는 다음에 템플릿에 정의됩니다.

    리소스 섹션을 변수 섹션 뒤에 추가합니다.

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
          "resources": [ {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "location": "[parameters('location')]",
            "properties": { "accountType": "[parameters('storageAccountType')]" }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
              "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
              "dnsSettings": { "domainNameLabel": "[parameters('dnsName')]" }
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
              "addressSpace": { "addressPrefixes": [ "[parameters('addressPrefix')]" ] },
              "subnets": [ {
                "name": "[parameters('subnet1Name')]",
                "properties": { "addressPrefix": "[parameters('subnet1Prefix')]" }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties": { "addressPrefix": "[parameters('subnet2Prefix')]" }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
              "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
              "ipConfigurations": [ {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddressName'))]"
                  },
                  "subnet": { "id": "[variables('subnet1Ref')]" }
                }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
              "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
              "hardwareProfile": { "vmSize": "[parameters('vmSize')]" },
              "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]",
                "windowsProfile": { "provisionVMAgent": "true" }
              },
              "storageProfile": {
                "sourceImage": { "id": "[variables('sourceImageName')]" },
                "destinationVhdsContainer" : "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmStorageAccountContainerName'),'/')]"
              },
              "networkProfile": {
                "networkInterfaces" : [ {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                } ]
              }
            }
          } ]
        }

9.	만든 템플릿 파일을 저장합니다.

###매개 변수 파일 만들기

템플릿에 정의된 리소스 매개 변수의 값을 지정하려면 값이 포함된 매개 변수 파일을 만들고 템플릿과 함께 리소스 관리자에 제출합니다. Visual Studio에서 다음을 수행합니다.

1.	솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.

2.	새 항목 추가 창에서 **텍스트 파일**을 선택하고 *Parameters.json*을 이름으로 입력한 후 **추가**를 클릭합니다.

3.	Parameters.json 파일을 열고 다음 JSON 내용을 추가합니다.

        {
          "contentVersion": "1.0.0.0",
          "parameters": {
            "vmName": { "value": "mytestvm1" },
            "newStorageAccountName": { "value": "mytestsa1" },
            "storageAccountType": { "value": "Standard_LRS" },
            "publicIPaddressName": { "value": "mytestip1" },
            "location": { "value": "West US" },
            "vmStorageAccountContainerName": { "value": "vhds" },
            "vmSize": { "value": "Standard_A1" },
            "subscriptionId": { "value": "{subscription-id}" },
            "vmSourceImageName": { "value": "{source-image-name}" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "virtualNetworkName": { "value": "mytestvn1" },
            "dnsName": { "value": "mytestdns1" },
            "nicName": { "value": "mytestnic1" }
          }
        }

    >[AZURE.NOTE]이미지 vhd 이름은 이미지 갤러리에서 정기적으로 변경되므로, 가상 컴퓨터를 배포하는 데 현재 이미지 이름을 가져와야 합니다. 이렇게 하려면 [Windows PowerShell을 사용하여 이미지 Windows 관리](https://msdn.microsoft.com/library/azure/dn790330.aspx)를 참조한 다음 , {source-image-name}을 사용 하려는 vhd 파일의 이름으로 바꿉니다. 예를 들면 "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"입니다. {subscription-id}를 사용자의 구독 ID로 바꿉니다.


4.	만든 매개 변수 파일을 저장합니다.

템플릿 파일과 매개 변수 파일은 Azure 저장소 계정에서 Azure 리소스 관리자를 통해 액세스할 수 있습니다. 저장소에 파일을 저장하려면 다음을 수행합니다.

1.	서버 탐색기를 열고 파일을 배치할 저장소 계정의 컨테이너로 이동합니다. 이 자습서에서는 템플릿이 있는 컨테이너의 이름을 templates로 지정합니다.

2.	templates 컨테이너 창의 오른쪽 위에서 Blob 업로드 아이콘을 클릭하고 사용자가 만든 VirtualMachineTemplate.json 파일을 찾아서 **열기**를 클릭합니다.

3. Blob 업로드 아이콘을 다시 클릭하고 사용자가 만든 Parameters.json 파일을 찾아서 **열기**를 클릭합니다.

##3단계: 라이브러리 설치

NuGet 패키지는 이 자습서를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Azure 리소스 관리 라이브러리 및 Azure Active Directory 인증 라이브러리를 설치해야 합니다. Visual Studio에서 이 라이브러리를 가져오려면 다음을 수행합니다.

1.	솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

2.	검색 상자에 *Active Directory*를 입력하고 Active Directory 인증 라이브러리 패키지에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

3.	페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Azure 리소스 관리*를 입력하고 Microsoft Azure 리소스 관리 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

##4단계: 요청을 인증하는데 사용되는 자격 증명 만들기

Azure Active Directory 응용 프로그램이 생성되고 인증 라이브러리가 설치되었으므로 Azure 리소스 관리자에 요청을 인증하는데 사용되는 자격 증명에 대한 응용 프로그램 정보의 서식을 지정합니다. 다음을 수행합니다.

1.	사용자가 만든 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;

2.	자격 증명을 만드는 데 필요한 토큰을 가져올 프로그램 클래스에 다음 메서드를 추가합니다.

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	{application-id}를 이전에 기록한 응용 프로그램 ID로 바꾸고, {password}를 AD 응용 프로그램에 대해 선택한 암호로 바꾸고, {tenant-id}를 구독을 위한 테넌트 ID로 바꿉니다. Get-AzureSubscription을 실행하여 테넌트 ID를 찾을 수 있습니다.

3.	Program.cs 파일에서 Main 메서드에 다음 코드를 추가하여 자격 증명을 만듭니다.

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

4.	Program.cs 파일을 저장합니다.


##5단계: 템플릿을 배포하는 코드 추가

리소스는 항상 템플릿에서 리소스 그룹에 배포됩니다. [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 및 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 클래스를 사용하여 리소스가 배포되는 리소스 그룹을 만듭니다.

1.	Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 만듭니다.

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  var resourceGroup = new ResourceGroup { Location = "West US" };
		  using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", resourceGroup);
		    Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

		CreateResourceGroup(credential);
		Console.ReadLine();

3.	Program 클래스에 다음 메서드를 추가하여 사용자가 정의한 템플릿으로 리소스 그룹에 리소스를 배포합니다.

		public async static void CreateTemplateDeployment(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the template deployment...");
		  var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
		  {
		    Mode = DeploymentMode.Incremental,
		    TemplateLink = new TemplateLink
		    {
		      Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json")
			},
			ParametersLink = new ParametersLink
			{
			  Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/Parameters.json")
			}
		  };
		  using (var templateDeploymentClient = new ResourceManagementClient(credential))
		  {
		    var dpResult = await templateDeploymentClient.Deployments.CreateOrUpdateAsync("mytestrg1", "mytestdp1", deployment);
			Console.WriteLine(dpResult.StatusCode);
		  }
		}

	{storage-account-name}을 이전에 파일을 저장한 계정의 이름으로 바꿉니다.

4.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

		CreateTemplateDeployment(credential);
		Console.ReadLine();

##6단계: 리소스를 삭제하는 코드 추가

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 리소스 그룹에서 각 리소스를 개별적으로 삭제할 필요가 없습니다. 리소스 그룹을 삭제하면 모든 해당 리소스가 자동으로 삭제됩니다.

1.	Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 삭제합니다.

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
		{
		  using (var resourceGroupClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
			Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

		DeleteResourceGroup(credential);
		Console.ReadLine();

##7단계: 콘솔 응용 프로그램 실행

1.	콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

2.	각 리소스를 생성하도록 각 상태 코드가 반환된 후 **Enter**를 누릅니다. 가상 컴퓨터를 만든 후 Enter 키를 눌러 모든 리소스를 삭제하기 전에 다음 단계를 수행합니다.

	이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. Enter를 눌러 리소스 삭제를 시작하기 전에, 삭제하기 전 Azure 미리 보기 포털에서 리소스의 생성을 확인하려면 몇 분이 걸릴 수 있습니다.

3. 리소스의 상태를 보려면 Azure 미리 보기 포털에서 감사 로그를 찾습니다.

	![AD 응용 프로그램 만들기](./media/arm-template-deployment/crpportal.png)

<!---HONumber=July15_HO4-->