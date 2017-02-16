## <a name="how-to-deploy-with-powershell"></a>PowerShell을 사용하여 배포하는 방법
1. Azure 계정에 로그인합니다.
   
          Add-AzureAccount
   
   자격 증명을 제공하면 사용자 계정에 대한 정보가 반환됩니다.
   
          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   
2. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다. 
   
          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>
3. Azure 리소스 관리자 모듈로 전환합니다.
   
          Switch-AzureMode AzureResourceManager
4. 기본 리소스 그룹이 없는 경우 새 리소스 그룹을 만듭니다. 솔루션에 필요한 위치 및 리소스 그룹의 이름을 제공합니다.
   
        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
   새 리소스 그룹에 대한 요약이 반환됩니다.
   
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
   
   * 인라인 매개 변수를 사용합니다.
     
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"
   * 매개 변수 개체를 사용합니다.
     
            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters
   * 매개 변수 파일을 사용합니다.
     
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>
   
   리소스 그룹을 배포한 경우 배포에 대한 요약이 표시됩니다.
   
             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...
6. 배포 오류에 대한 정보를 가져오려면 다음을 실행합니다.
   
        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed
7. 배포 오류에 대한 자세한 정보를 가져오려면 다음을 실행합니다.
   
        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput



<!--HONumber=Jan17_HO3-->


