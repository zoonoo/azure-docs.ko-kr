<properties
	pageTitle="Azure PowerShell 1.0 Preview 리소스 관리자 변경 내용 | Microsoft Azure"
	description="Azure PowerShell 1.0 Preview에서 리소스 관리자 cmdlet의 변경 내용을 설명합니다."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="na"
	ms.date="10/09/2015"
	ms.author="gauravbh;tomfitz"/>

#Azure 리소스 관리자 관리 PowerShell cmdlet에 대한 변경 내용

Azure PowerShell 1.0 Preview 릴리스의 일부로 관리 cmdlet의 기능을 몇 가지 개선했습니다. 이러한 개선 사항은 1.0 시험판에 포함된 cmdlet 이름 변경에 추가된 것입니다. 이러한 개선 사항 중 일부는 대규모 변경을 포함하고 있어 기존 스크립트를 중단시킬 수도 있습니다. 이러한 변경이 환경 개선에 도움이 되기를 바랍니다. 이러한 변경 사항에 대한 의견을 주시면 Azure PowerShell 1.0에 포함하겠습니다. 새 cmdlet을 사용해 보고 의견을 들려 주세요.

##리소스 그룹에서 분리된 템플릿 배포

0\.9.8 릴리스에서는 템플릿 배포 매개 변수가 모두 리소스 그룹 cmdlet에도 있었습니다. 따라서 New-AzureResourceGroup에서 새 리소스 그룹을 만들 수 있을 뿐 아니라 배포할 템플릿에 대한 세부 정보를 제공할 수 있었습니다. 동일한 템플릿 배포 기능이 New-AzureResourceGroupDeployment에도 있었습니다. 1.0 시험판에서는 이 기능을 분리했습니다. 이제 New-AzureRMResourceGroup에서 새 리소스 그룹을 만드는 기능을 제공하고 New-AzureRmResourceGroupDeployment에서 템플릿을 배포하는 기능을 제공합니다. 파이핑을 사용하면 이 둘을 함께 사용할 수 있습니다. 이 경우 cmdlet을 더 쉽게 이해하고 사용할 수 있습니다.

##통합된 감사 로그 cmdlet

감사 로그의 경우 다양한 범위의 로그를 가져오는 많은 cmdlet이 있었습니다(예: Get-AzureResourceProviderLog, Get-AzureResourceGroupLog, Get-AzureSubscriptionIdLog 및 Get-AzureResourceLog). 로그를 가져오려면 종종 로그 cmdlet을 조합해서 실행해야 했습니다. 이는 최적의 환경이 아니었습니다. 이 기능을 매개 변수 사용을 통해 여러 범위에서 호출할 수 있는 단일 cmdlet으로 통합했습니다. 이제 범위를 지정하는 적절한 매개 변수를 사용하여 Get-AzureRmLog를 호출할 수 있습니다.

##리소스 및 리소스 그룹의 Get cmdlet에 대한 변경 내용

AzureRmResource 및 Get-AzureRmResourceGroup cmdlet에 대한 변경 내용을 통합했으므로 이러한 cmdlet은 이제 리소스 공급자에만 직접 쿼리합니다. 캐시에 쿼리하는 기능을 Find-AzureRmResource*라는 새 cmdlet으로 분리했습니다. 특정 태그가 있는 리소스 그룹을 찾으려면 새 Find-AzureRmResourceGroup cmdlet을 사용할 수 있습니다. 이 변경 사항으로 태그를 쿼리하는 매개 변수가 Get-AzureRmResource 및 Get-AzureRmResourceGroup cmdlet에서 제거되었습니다.

##제거된 Test-AzureResource 및 Test-AzureResourceGroup

이러한 cmdlet은 일부 시나리오 및 리소스 유형에 대해 안정적으로 작동하지 않았습니다. 이 기능에 대한 더 나은 솔루션을 찾기 위해 노력하고 있습니다. 그 동안 안정적이지 않은 cmdlet을 계속 사용하지 않았으면 합니다. 따라서 이 릴리스에서 이러한 cmdlet을 제거했으며 향후 릴리스에 안정적인 솔루션을 추가할 예정입니다.

##Get-AzureRmResourceProvider 개선 사항

이제 Get-AzureRmResourceProvider cmdlet을 사용하여 리소스 공급자에 대한 위치 정보를 가져올 수 있습니다. 이 정보를 통해 특정 지역에서 사용할 수 있는 공급자와 유형을 알 수 있습니다. 또한 이 cmdlet을 사용하여 특정 공급자에 대해 사용 가능한 위치 목록도 얻을 수 있습니다. Get-AzureRmResourceProvider cmdlet을 통해 모든 위치 정보를 얻을 수 있으므로 Get-AzureLocation cmdlet은 제거했습니다.

##정책 cmdlet

Azure 리소스 관리자에 정책 지원을 추가했습니다. 이 릴리스에는 정책을 관리하는 PowerShell cmdlet이 추가되었습니다. 정책에 대한 자세한 내용은 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)를 참조하세요.

<!---HONumber=Oct15_HO3-->