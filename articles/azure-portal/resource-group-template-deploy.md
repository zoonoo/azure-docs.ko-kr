<properties
   pageTitle="Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포"
   services="azure-resource-manager"
   description="Azure 리소스 관리자를 사용하여 Azure에 응용 프로그램 배포 템플릿은 JSON 파일로, 포털, PowerShell, Mac/Linux/Windows용 Azure 명령줄 인터페이스 또는 REST에서 사용할 수 있습니다."
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
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포

이 항목에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure에 응용 프로그램을 배포하는 방법을 설명합니다. 여기서는 Azure PowerShell, Azure CLI, REST API 또는 Microsoft Azure Preview 포털을 사용하여 응용 프로그램을 배포합니다.

Azure 리소스 관리자 서식 파일을 사용 하 쉽고 빠르게 선언적 JSON 통해 Azure에서 응용 프로그램을 프로 비전 할 수 있습니다. 단일 JSON 템플릿에서 가상 컴퓨터, 가상 네트워크, 저장소, 앱 서비스, 데이터베이스 등과 같은 여러 서비스를 배포할 수 있습니다. 동일한 템플릿을 사용하여 응용 프로그램 수명 주기의 각 단계 중에 응용 프로그램을 반복해서 일관되게 배포합니다.

응용 프로그램 관리를 단순화하기 위해, 공통 수명 주기를 공유하는 모든 리소스 그룹을 단일 리소스 그룹으로 정리할 수 있습니다. 리소스 그룹을 사용하면 쉽게 모든 관련 리소스를 한꺼번에 배포, 업데이트 및 삭제할 수 있습니다. 대부분의 경우 리소스 그룹은 단일 응용 프로그램이나 응용 프로그램 계층(대규모 응용 프로그램의 경우)에 매핑됩니다. 템플릿을 통해 배포한 리소스는 단일 리소스 그룹 안에 상주하지만 다른 리소스 그룹과의 종속성을 포함할 수 있습니다.

리소스 그룹 안에서는 배포 실행을 추적하고, 배포 상태와 템플릿 실행의 출력을 확인할 수 있습니다.

템플릿을 사용하여 응용 프로그램을 배포할 때는 매개 변수 값을 제공하여 리소스가 만들어지는 방식을 사용자 지정할 수 있습니다. 이 매개 변수의 값은 인라인 또는 매개 변수 파일로 지정할 수 있습니다.

## 개념

- 리소스 그룹 - 공통 수명 주기를 공유하는 엔터티 모음
- 리소스 관리자 템플릿 - 배포의 목표 상태를 정의하는 선언 JSON 파일
- 배포 - 리소스 관리자 템플릿의 실행을 추적하는 작업
- 매개 변수 - 배포된 리소스를 사용자 지정하기 위해 배포를 실행하는 사용자가 제공한 값
- 매개 변수 파일 - 매개 변수 이름과 값을 저장하는 JSON 파일 

## 시나리오

리소스 관리자 템플릿을 사용하면 다음이 가능합니다.

- 복잡한 다계층 응용 프로그램 배포(예: Microsoft SharePoint)
- 일관되고 반복적인 응용 프로그램 배포
- 개발, 테스트 및 프로덕션 환경 지원
- 배포 상태 확인
- 배포 감사 로그를 통한 배포 실패 문제 해결

## 미리 보기 포털로 배포

어떤 혜택이 있을까요? Azure 리소스 관리자 템플릿은 [Preview 포털](https://portal.azure.com/)을 통해 만든 모든 응용 프로그램을 지원합니다. 포털을 통해 가상 컴퓨터, 가상 네트워크, 저장소 계정, 앱 서비스 또는 데이터베이스를 만들기만 하면 추가적인 작업 없이도 Azure 리소스 관리자의 혜택을 만끽할 수 있습니다. **새로 만들기** 아이콘을 선택하기만 하면 Azure 리소스 관리자를 통해 응용 프로그램을 배포하는 과정이 진행됩니다.

![새로 만들기](./media/resource-group-template-deploy/new.png)

Azure 리소스 관리자와 포털 사용에 대한 자세한 내용은 [Azure Preview 포털을 사용하여 Azure 리소스 관리](resource-group-portal.md)를 참조하세요.


## PowerShell을 사용하여 배포

이전에 리소스 관리자에서 Azure PowerShell을 사용하지 않은 경우 [Azure 리소스 관리자와 함께 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

1. Azure 계정에 로그인합니다. 자격 증명을 제공하면 사용자 계정에 대한 정보가 반환됩니다.

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Azure 리소스 관리자 모듈로 전환합니다.

        PS C:\> Switch-AzureMode AzureResourceManager

4. 기본 리소스 그룹이 없는 경우 새 리소스 그룹을 만듭니다. 솔루션에 필요한 위치 및 리소스 그룹의 이름을 제공합니다. 새 리소스 그룹에 대한 요약이 반환됩니다.

        PS C:\> New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. 리소스 그룹에 대한 새 배포를 만들려면 **New-azureresourcegroupdeployment** 명령을 실행하고 필요한 매개 변수를 제공합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름, 만든 템플릿의 경로 또는 URL 및 시나리오에 필요한 기타 매개 변수가 포함됩니다.
   
     다음과 같은 방법으로 매개 변수 값을 제공할 수 있습니다.
   
     - 인라인 매개 변수를 사용합니다.

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - 매개 변수 개체를 사용합니다.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - 매개 변수 파일을 사용합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](./#parameter-file)을 참조하세요.

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     리소스 그룹을 배포한 경우 배포에 대한 요약이 표시됩니다.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. 배포 오류에 대한 정보를 가져오려면 다음을 실행합니다.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. 배포 오류에 대한 자세한 정보를 가져오려면 다음을 실행합니다.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
        
### 비디오

다음은 PowerShell에서 리소스 관리자 템플릿으로 작업을 보여주는 비디오입니다.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Mac, Linux 및 Windows용 Azure CLI로 배포

이전에 리소스 관리자에서 Azure CLI를 사용하지 않은 경우 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](../xplat-cli-azure-resource-manager.md)을 참조하세요.

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

     - 인라인 매개 변수 및 로컬 템플릿을 사용합니다.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - 인라인 매개 변수 및 템플릿 링크를 사용합니다.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

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
   
3. 새 리소스 그룹을 만듭니다. 템플릿의 구독 ID, 배포할 리소스 그룹 이름, 배포 이름 및 템플릿 위치를 제공합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](./#parameter-file)을 참조하세요. 리소스 그룹을 만드는 REST API에 대한 정보는 [템플릿 배포 만들기](https://msdn.microsoft.com/library/azure/dn790564.aspx)를 참조하세요.
    
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
            }
       }
    }

## 다음 단계
- .NET 클라이언트 라이브러리를 통한 리소스 배포의 예를 보려면 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](../arm-template-deployment.md)를 참조하세요.
- 응용 프로그램 배포에 대한 자세한 예제는 [Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포](../app-service-web/app-service-deploy-complex-application-predictably.md)를 참조하세요.
- Azure 리소스 관리자 템플릿 섹션에 대한 자세한 내용은 [템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요.
- Azure 리소스 관리자 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](../resource-group-template-functions.md)를 참조하세요.

 

<!---HONumber=August15_HO8-->