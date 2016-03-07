<properties
   pageTitle="리소스 관리자 템플릿을 사용하여 리소스 배포 | Microsoft Azure"
   services="azure-resource-manager"
   description="Azure 리소스 관리자를 사용하여 Azure에 리소스를 배포합니다. 템플릿은 JSON 파일로, 포털, PowerShell, Mac/Linux/Windows용 Azure 명령줄 인터페이스 또는 REST에서 사용할 수 있습니다."
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager 템플릿으로 리소스 그룹 배포

이 항목에서는 Azure Resource Manager 템플릿을 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다. 여기서는 Azure PowerShell, Azure CLI, REST API 또는 Azure 포털을 사용하여 리소스를 배포합니다.

리소스 관리자에 대한 소개는 [Azure 리소스 관리자 개요](./resource-group-overview.md)를 참조하세요. 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.

템플릿을 사용하여 응용 프로그램 정의를 배포할 때 매개 변수 값을 제공하여 리소스가 만들어지는 방법을 사용자 지정할 수 있습니다. 이 매개 변수의 값은 인라인 또는 매개 변수 파일로 지정할 수 있습니다.

## 증분 및 전체 배포

기본적으로 리소스 관리자는 리소스 그룹에 대한 증분 업데이트로 배포를 처리합니다. 증분 배포를 통해 리소스 관리자는 다음을 수행합니다.

- 리소스 그룹에 존재하지만 템플릿에 지정되지 않는 리소스를 **변경되지 않은 상태로 유지**
- 템플릿에 지정되어 있지만 리소스 그룹에 없는 리소스를 **추가** 
- 템플릿에 정의된 동일한 조건으로 리소스 그룹에 존재하는 리소스를 **다시 프로비전하지 않음**

Azure PowerShell 또는 REST API를 통해 리소스 그룹에 대한 전체 업데이트를 지정할 수 있습니다. 현재 Azure CLI는 전체 배포를 지원하지 않습니다. 전체 배포를 통해 리소스 관리자는 다음을 수행합니다.

- 리소스 그룹에 존재하지만 템플릿에 지정되지 않는 리소스를 **삭제**
- 템플릿에 지정되어 있지만 리소스 그룹에 없는 리소스를 **추가** 
- 템플릿에 정의된 동일한 조건으로 리소스 그룹에 존재하는 리소스를 **다시 프로비전하지 않음**
 
PowerShell 및 REST API에 대한 아래 예제에 설명된 대로 **Mode** 속성을 통해 배포 유형을 지정합니다.

## PowerShell을 사용하여 배포

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


1. Azure 계정에 로그인합니다. 자격 증명을 제공하면 사용자 계정에 대한 정보가 반환됩니다.

    Azure PowerShell 1.0.

         PS C:\> Login-AzureRmAccount

         Evironment : AzureCloud
         Account    : someone@example.com
         ...


2. 여러 구독이 있는 경우 **Select-AzureRmSubscription** 명령을 사용하여 배포에 사용할 구독 ID를 제공합니다.

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. 기본 리소스 그룹이 없는 경우 **New-AzureRmResourceGroup**을 사용하여 새 리소스 그룹을 만듭니다. 솔루션에 필요한 위치 및 리소스 그룹의 이름을 제공합니다. 새 리소스 그룹에 대한 요약이 반환됩니다.

        PS C:\> New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. 리소스 그룹에 대한 새 배포를 만들려면 **New-AzureRmResourceGroupDeployment** 명령을 실행하고 필요한 매개 변수를 제공합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름, 만든 템플릿의 경로 또는 URL 및 시나리오에 필요한 기타 매개 변수가 포함됩니다. **Mode** 매개 변수가 지정되지 않으면 기본값 **Incremental**이 사용됩니다.
   
     다음과 같은 방법으로 매개 변수 값을 제공할 수 있습니다.
   
     - 인라인 매개 변수를 사용합니다.

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - 매개 변수 개체를 사용합니다.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - 매개 변수 파일을 사용합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](./#parameter-file)을 참조하세요.

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     리소스 그룹을 배포한 경우 배포에 대한 요약이 표시됩니다.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

     전체 배포를 실행하려면 **Mode**를 **Complete**로 설정합니다. 리소스 삭제 작업을 포함할 수 있는 Complete 모드를 사용할 것인지 확인하는 메시지가 표시됩니다.

          PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -Mode Complete -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> 
          Confirm
          Are you sure you want to use the complete deployment mode? Resources in the resource group 'ExampleResourceGroup' which are not
          included in the template will be deleted.
          [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

     템플릿에 템플릿을 배포하는 명령의 매개 변수 중 하나와 일치하는 이름을 가진 매개 변수를 포함하는 경우(예: 템플릿에 [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) cmdlet의 **ResourceGroupName** 매개 변수와 동일한 **ResourceGroupName**이라는 매개 변수 포함) **FromTemplate** 후위가 있는 매개 변수(예: **ResourceGroupNameFromTemplate**)에 대한 값을 제공하라는 메시지가 표시됩니다. 일반적으로 배포 작업에 사용되는 매개 변수와 동일한 이름을 가진 매개 변수를 명명하지 않음으로써 이러한 혼동이 발생하지 않도록 해야 합니다.

6. 배포 오류에 대한 정보를 가져오려면 다음을 실행합니다.

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment
        
        
### 비디오

다음은 PowerShell에서 리소스 관리자 템플릿으로 작업을 보여주는 비디오입니다.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Mac, Linux 및 Windows용 Azure CLI로 배포

이전에 리소스 관리자에서 Azure CLI를 사용하지 않은 경우 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.

1. Azure 계정에 로그인합니다. 자격 증명을 제공하면 로그인 결과가 반환됩니다.

        azure login
  
        ...
        info:    login command OK

2. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다.

        azure account set <YourSubscriptionNameOrId>

3. Azure 리소스 관리자 모듈로 전환합니다. 새 모드에 대한 확인이 제공됩니다.

        azure config mode arm
   
        info:     New mode is arm

4. 기본 리소스 그룹이 없는 경우 새 리소스 그룹을 만듭니다. 솔루션에 필요한 위치 및 리소스 그룹의 이름을 제공합니다. 새 리소스 그룹에 대한 요약이 반환됩니다.

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. 리소스 그룹에 대한 새 배포를 만들려면 다음 명령을 실행하고 필요한 매개 변수를 제공합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름, 만든 템플릿의 경로 또는 URL 및 시나리오에 필요한 기타 매개 변수가 포함됩니다.
   
     다음과 같은 방법으로 매개 변수 값을 제공할 수 있습니다.

     - 인라인 매개 변수 및 로컬 템플릿을 사용합니다. 각 매개 변수는 `"ParameterName": { "value": "ParameterValue" }` 형식을 사용합니다. 다음 예제에서는 이스케이프 문자를 사용한 매개 변수를 보여줍니다.

             azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - 인라인 매개 변수 및 템플릿 링크를 사용합니다.

             azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - 매개 변수 파일을 사용합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](./#parameter-file)을 참조하세요.
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     리소스 그룹을 배포한 경우 배포에 대한 요약이 표시됩니다.
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. 최신 배포에 대한 정보를 가져오려면 다음을 실행합니다.

         azure group log show -l ExampleResourceGroup

7. 배포 오류에 대한 자세한 정보를 가져오려면 다음을 실행합니다.
      
         azure group log show -l -v ExampleResourceGroup

## REST API를 사용하여 배포
1. 인증 토큰을 포함하여 [공통 매개 변수 및 헤더](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common)를 설정합니다.
2. 기본 리소스 그룹이 없는 경우 새 리소스 그룹을 만듭니다. 솔루션에 필요한 구독 ID, 새 리소스 그룹 이름 및 위치를 제공합니다. 자세한 내용은 [리소스 그룹 만들기](https://msdn.microsoft.com/library/azure/dn790525.aspx)를 참조하세요.

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }
   
3. 새 리소스 그룹을 만듭니다. 템플릿의 구독 ID, 배포할 리소스 그룹 이름, 배포 이름 및 템플릿 위치를 제공합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](./#parameter-file)을 참조하세요. 리소스 그룹을 만드는 REST API에 대한 정보는 [템플릿 배포 만들기](https://msdn.microsoft.com/library/azure/dn790564.aspx)를 참조하세요. **mode**가 **Incremental**로 설정되어 있습니다. 전체 배포를 실행하려면 **mode**를 **Complete**로 설정합니다.
    
         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }
   
4. 템플릿 배포의 상태를 가져옵니다. 자세한 내용은 [템플릿 배포에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn790565.aspx)를 참조하세요.

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## Visual Studio를 사용하여 배포

Visual Studio를 사용하여 리소스 그룹 프로젝트를 만들고 사용자 인터페이스를 통해 Azure에 배포할 수 있습니다. 프로젝트에 포함할 리소스 유형을 선택하면 해당 리소스가 리소스 관리자 템플릿에 자동으로 추가됩니다. 또한 프로젝트에서는 템플릿을 배포할 수 있는 PowerShell 스크립트를 제공합니다.

Visual Studio를 리소스 그룹과 함께 사용하는 방법은 [Visual Studio를 통해 Azure 리소스 그룹 생성 및 배포](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요.

## 포털로 배포

어떤 혜택이 있을까요? Azure 리소스 관리자 템플릿은 [포털](https://portal.azure.com/)을 통해 만든 모든 응용 프로그램을 지원합니다. 포털을 통해 가상 컴퓨터, 가상 네트워크, 저장소 계정, 앱 서비스 또는 데이터베이스를 만들기만 하면 추가적인 작업 없이도 Azure 리소스 관리자의 혜택을 만끽할 수 있습니다. **새로 만들기** 아이콘을 선택하기만 하면 Azure 리소스 관리자를 통해 응용 프로그램을 배포하는 과정이 진행됩니다.

![새로 만들기](./media/resource-group-template-deploy/new.png)

Azure 리소스 관리자와 포털 사용에 대한 자세한 내용은 [Azure 포털을 사용하여 Azure 리소스 관리](azure-portal/resource-group-portal.md)를 참조하세요.


## 매개 변수 파일

배포 중에 매개 변수 값을 템플릿에 전달하는 데 매개 변수 파일을 사용하려면 다음 예와 유사한 형식의 JSON 파일을 만들어야 합니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            },
            "adminPassword": {
                "reference": {
                   "keyVault": {
                      "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                   }, 
                   "secretName": "sqlAdminPassword" 
                }   
            }
       }
    }

매개 변수 파일 크기는 64KB보다 클 수 없습니다.

템플릿에서 매개 변수를 정의하는 방법은 [템플릿 작성](../resource-group-authoring-templates/#parameters)을 참조하세요. 보안 값을 전달하기 위한 주요 자격 증명 모음 참조에 대한 자세한 내용은 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요.

## 다음 단계
- .NET 클라이언트 라이브러리를 통한 리소스 배포의 예를 보려면 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](./virtual-machines/arm-template-deployment.md)를 참조하세요.
- 응용 프로그램 배포에 대한 자세한 예제는 [Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포](app-service-web/app-service-deploy-complex-application-predictably.md)를 참조하세요.
- 다른 환경에 솔루션 배포에 관한 지침은 [Microsoft Azure의 개발 및 테스트 환경](solution-dev-test-environments.md)을 참조하세요.
- Azure 리소스 관리자 템플릿 섹션에 대한 자세한 내용은 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- Azure 리소스 관리자 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.

 

<!---HONumber=AcomDC_0224_2016-->