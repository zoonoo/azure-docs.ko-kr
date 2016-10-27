<properties
    pageTitle="리소스 관리자 템플릿을 사용하여 VM 만들기 | Microsoft Azure"
    description="리소스 관리자 템플릿 및 PowerShell을 사용하여 새 Windows 가상 컴퓨터를 쉽게 만들 수 있습니다."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 Windows 가상 컴퓨터 만들기

이 문서에서는 Azure Resource Manager 템플릿을 소개하고 PowerShell을 사용하여 배포하는 방법을 보여 줍니다. 템플릿은 단일 서브넷을 사용하는 새 가상 네트워크에서 Windows Server를 실행하는 단일 가상 컴퓨터를 배포합니다.

이 문서의 단계를 수행하려면 약 20분이 걸립니다.

> [AZURE.IMPORTANT] 가용성 집합의 일부가 되도록 하려면 VM을 만들 때 해당 집합에 추가합니다. 현재는 VM을 만든 이후에 가용성 집합에 추가하는 방법이 없습니다.

## <a name="step-1:-create-the-template-file"></a>1단계: 템플릿 파일 만들기

[Azure Resource Manager 템플릿 작성](../resource-group-authoring-templates.md)에 있는 정보를 사용하여 고유의 템플릿을 만들 수 있습니다. [Azure Quickstarts 템플릿](https://azure.microsoft.com/documentation/templates/)으로 만든 고유의 템플릿을 배포할 수도 있습니다.

1. 자주 사용하는 텍스트 편집기를 열고 필요한 스키마 요소 및 필요한 contentVersion 요소를 추가합니다.

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [매개 변수](../resource-group-authoring-templates.md#parameters)가 항상 필요한 것은 아니지만 템플릿이 배포될 때 값을 입력하는 방법을 제공합니다. 매개 변수 요소와 해당 자식 요소를 contentVersion 요소 뒤에 추가합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [변수](../resource-group-authoring-templates.md#variables) 를 사용하여 자주 변경되는 값 또는 매개 변수 값의 조합에서 만들어야 하는 값을 지정할 수 있습니다. 변수 요소를 매개 변수 섹션 뒤에 추가합니다.

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
        
4. [리소스](../resource-group-authoring-templates.md#resources) 는 다음에 템플릿에 정의됩니다. 리소스 섹션을 변수 섹션 뒤에 추가합니다.

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
          
    >[AZURE.NOTE] 이 문서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md)을 참조하세요.  
            
2. 템플릿 파일을 *VirtualMachineTemplate.json*으로 저장합니다.

## <a name="step-2:-create-the-parameters-file"></a>2단계: 매개 변수 파일 만들기

템플릿에 정의된 리소스 매개 변수의 값을 지정하려면 템플릿이 배포될 때 사용된 값이 들어 있는 매개 변수 파일을 만듭니다.

1. 텍스트 편집기에서 이 JSON 콘텐츠를 *Parameters.json*이라는 새 파일에 복사합니다.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] 자세한 내용은 [사용자 이름 및 암호 요구 사항](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)을 참조하세요.

2. 매개 변수 파일을 저장합니다.

## <a name="step-3:-install-azure-powershell"></a>3단계: Azure PowerShell 설치

최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## <a name="step-4:-create-a-resource-group"></a>4단계: 리소스 그룹 만들기

모든 리소스는 [리소스 그룹](../resource-group-overview.md)에 배포되어야 합니다.

1. 리소스를 만들 수 있는 사용 가능한 위치 목록을 가져옵니다.

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. **$locName** 값을 목록의 위치(예: **미국 중부**)로 바꿉니다. 변수를 만듭니다.

        $locName = "location name"
        
3. **$rgName** 값을 새 리소스 그룹 이름으로 바꿉니다. 변수 및 리소스 그룹을 만듭니다.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    다음 예제와 유사한 결과가 표시됩니다.
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5:-create-the-resources-with-the-template-and-parameters"></a>5단계: 템플릿 및 매개 변수를 사용하여 리소스 만들기

**$templateFile** 값을 템플릿 파일의 경로 및 이름으로 바꿉니다. **$parameterFile** 값을 매개 변수 파일의 경로 및 이름으로 바꿉니다. 변수를 만들고 템플릿을 배포합니다. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] Azure 저장소 계정에서 템플릿 및 매개 변수를 배포할 수도 있습니다. 자세한 내용은 [Azure Storage와 함께 Azure PowerShell 사용](../storage/storage-powershell-guide-full.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 배포에 문제가 있는 경우 다음 단계로서 [Azure Portal을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)
- [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 검토하여 자신이 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.



<!--HONumber=Oct16_HO2-->


