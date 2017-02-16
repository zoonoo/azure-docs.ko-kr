## <a name="how-to-deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포하는 방법
1. Azure 계정에 로그인합니다.
   
        azure login
   
   자격 증명을 제공하면 로그인 결과가 반환됩니다.
   
        ...
        info:    login command OK
2. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다.
   
        azure account set <YourSubscriptionNameOrId>
3. Azure 리소스 관리자 모듈로 전환
   
        azure config mode arm
   
   새 모드에 대한 확인이 제공됩니다.
   
        info:     New mode is arm
4. 기본 리소스 그룹이 없는 경우 새 리소스 그룹을 만듭니다. 솔루션에 필요한 위치 및 리소스 그룹의 이름을 제공합니다.
   
        azure group create -n ExampleResourceGroup -l "West US"
   
   새 리소스 그룹에 대한 요약이 반환됩니다.
   
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
   
   * 인라인 매개 변수 및 로컬 템플릿을 사용합니다.
     
             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment
   * 인라인 매개 변수 및 템플릿 링크를 사용합니다.
     
             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment
   * 매개 변수 파일을 사용합니다.
     
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



<!--HONumber=Jan17_HO3-->


