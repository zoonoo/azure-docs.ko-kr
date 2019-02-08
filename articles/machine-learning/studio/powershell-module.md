---
제목: Machine Learning Studio용 PowerShell 모듈 titleSuffix: Azure Machine Learning Studio 설명: PowerShell을 사용하여 Azure Machine Learning Studio 작업 영역, 실험, 웹 서비스 등을 만들고 관리하는 방법을 설명합니다. services: machine-learning ms.service: machine-learning ms.subservice: studio ms.topic: article

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-ms.author=haining, previous-author=hning86 ms.date: 2019/01/25
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Azure Machine Learning Studio용 PowerShell 모듈

PowerShell 모듈을 사용하면 Studio 리소스 및 작업 영역/데이터 세트/웹 서비스 등의 자산을 프로그래밍 방식으로 관리할 수 있습니다.

다음의 세 가지 PowerShell 모듈을 통해 Studio 리소스를 조작할 수 있습니다.

* [Azure PowerShell Az](#az-rm)(2018년 출시) - cmdlet 이름은 다르지만 AzureRM의 모든 기능이 포함되어 있습니다.
* [AzureRM](#az-rm)(2016년 출시)
* [Azure Machine Learning PowerShell 클래식](#classic)(2016년 출시)

이러한 모듈은 다소 비슷하기는 하지만 각각 특정 시나리오용으로 설계되었습니다. 이 문서에서는 PowerShelll 모듈 간의 차이점을 설명하고 선택해야 하는 모듈을 결정하는 데 도움이 되는 정보를 제공합니다.

## <a name="choosing-modules"></a> 모듈 선택

관리 중인 리소스 종류에 따라 사용 가능한 PowerShell 모듈 중에서 적절한 모듈을 선택해야 합니다.

아래의 [지원 표](#support-table)에서 각 모듈이 지원하는 리소스를 확인하세요. PowerShell 클래식은 Az 또는 AzureRM과 함께 설치할 수 있으므로 두 모듈(클래식+Az 또는 클래식+AzureRM)을 설치하면 모든 리소스 종류를 관리할 수 있습니다.

그러나 Az와 AzureRM은 동시에 설치하지 않는 것이 좋습니다. Az와 AzureRM 중에 설치할 모듈을 결정해야 하는 경우 향후 모든 배포에 사용할 수 있는 Az를 선택하는 것이 좋습니다. AzureRM은 환경에서 특수한 상황으로 인해 필요한 경우에만 사용하세요.

Az와 AzureRM 간의 차이점과 Microsoft에서 제공하는 마이그레이션 경로에 대해 자세히 알아보려면 [Azure PowerShell Az 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)를 참조하세요.

## <a name="az-rm"></a> Azure PowerShell Az 및 AzureRM

Az와 AzureRM은 모두 **Azure Resource Manager** 배포 모델을 사용하여 배포된 솔루션을 관리합니다. 이러한 리소스에는 Studio 작업 영역 및 Studio 새 웹 서비스가 포함됩니다. 클래식 배포 모델을 사용하여 배포된 리소스를 관리하려면 PowerShell 클래식 모듈을 사용해야 합니다. 배포 모델에 대해 자세히 알아보려면 [Azure Resource Manager와 클래식 배포 비교](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)를 참조하세요.

현재 Azure 조작용 PowerShell 모듈로 사용되고 있는 Az는 AzureRM의 모든 이전 기능을 포함합니다. AzureRM에도 버그 수정은 계속 제공되지만 신규 cmdlet이나 기능은 제공되지 않습니다. AzureRM에서 Az로 업그레이드하는 경로가 제공되기는 하지만, Studio 사용 시 Az에 문제가 발생하는 경우 문제를 보고한 후 AzureRM을 대신 사용하세요.

Az 사용을 시작하려면 [Azure Az 설치 지침](https://docs.microsoft.com/powershell/azure/install-az-ps)을 따르세요.

## <a name="classic"></a> PowerShell 클래식

Studio [PowerShell 클래식 모듈](https://aka.ms/amlps)에서는 **클래식 배포 모델**을 사용하여 배포된 리소스를 관리할 수 있습니다. 이러한 리소스에는 Studio 사용자 자산, 클래식 웹 서비스, 클래식 웹 서비스 엔드포인트가 포함됩니다.

그러나 리소스 배포와 관리를 간편하게 수행하려면 모든 새 리소스에 Resource Manager 배포 모델을 사용하는 것이 좋습니다. 배포 모델에 대해 자세히 알아보려면 [Azure Resource Manager와 클래식 배포 비교](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)를 참조하세요.

PowerShell 클래식 사용을 시작하려면 GitHub에서 [릴리스 패키지](https://github.com/hning86/azuremlps/releases)를 다운로드하고 [설치 지침](https://github.com/hning86/azuremlps/blob/master/README.md)을 따릅니다. 지침은 다운로드/압축 해제된 DLL의 차단을 해제한 다음 PowerShell 환경으로 가져오는 방법을 설명합니다.

## <a name="support-table"></a> PowerShell 지원 표

 **Studio 작업 영역** | **Az** |  **AzureRM** | **PowerShell 클래식** |
| --- | --- | --- | --- | --- |
| 작업 영역 만들기/삭제 | [Resource Manager 템플릿](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Resource Manager 템플릿](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| 작업 영역 사용자 관리 |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| 약정 플랜 관리 | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **웹 서비스** | **Az** | **AzureRM** | **PowerShell 클래식** |
| 웹 서비스 관리 | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (새 웹 서비스) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (새 웹 서비스) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (클래식 웹 서비스) |
| 엔드포인트/키 관리 |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (새 웹 서비스) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (새 웹 서비스) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (클래식 웹 서비스) |
|||
| **사용자 자산** | **Az** | **AzureRM** | **PowerShell 클래식** |
| 데이터 세트/학습된 모델 관리 |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 실험 관리 |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| 사용자 지정 모듈 관리 |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>다음 단계
다음 링크를 통해 이동하여 PowerShell 모듈용 전체 설명서를 확인합니다.
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell 클래식](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
