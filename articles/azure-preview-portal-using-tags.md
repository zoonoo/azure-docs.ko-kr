<properties 
	pageTitle="태그를 사용하여 Azure 리소스 구성" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="flanakin" 
	writer="" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="micflan"/>


# 태그를 사용하여 Azure 리소스 구성

Azure 미리 보기 포털 및 기본 리소스 관리자는 리소스를 구성하고 사용자에게 잘 맞게 환경을 사용자 지정하는 작업에 사용됩니다. 

전체 Azure 포털에서는 리소스를 분류하고 그룹화하기 위해 반드시 구독이 필요합니다. 미리 보기 포털에서 관련 엔터티를 그룹화할 수 있는 [리소스 그룹을 도입했습니다](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups). 이 리소스 그룹은 [역할 기반 액세스를 도입](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure)했을 때 더 유용해졌습니다. 이제 같은 맥락으로 여러 리소스 그룹에서, 포털 내에서, 구독 간에 리소스를 추가적으로 분류하고 확인할 수 있게 리소스에 키/값 쌍을 태그로 지정할 수 있습니다.

팀, 프로젝트 또는 심지어 작업 환경별로 리소스를 그룹화하여 보고 싶을 때 보려는 리소스를 정확히 확인할 수 있습니다. 


## Azure 미리 보기 포털의 태그

미리 보기 포털에서 리소스 및 리소스 그룹에 쉽게 태그를 지정할 수 있습니다. 찾아보기 허브를 사용하여 태그를 지정할 리소스 또는 리소스 그룹으로 이동한 후 블레이드 위쪽의 개요 섹션에서 태그 부분을 클릭합니다. 

![Tags part on resource and resource group blades](./media/azure-preview-portal-using-tags/rgblade.png)

이렇게 하면 이미 적용된 태그의 목록이 있는 블레이드가 열립니다. 첫 번째 태그이면 목록은 비어 있습니다. 태그를 추가하려면 이름 및 값을 지정하고 Enter 키를 누르기만 하면 됩니다. 몇 개의 태그를 추가하면 기존 태그 이름 및 값에 따라 자동 완성 옵션이 제공되므로 리소스 간에 일관된 분류를 적용하기 쉬우며 맞춤법 오류와 같은 일반적인 실수를 피할 수 있습니다.

![Tag resources with name/value pairs](./media/azure-preview-portal-using-tags/tag-resources.png)

여기에서 각 개별 태그를 클릭하여 동일한 태그가 있는 모든 리소스의 목록을 볼 수 있습니다. 물론 첫 번째 태그이면 목록에 흥미로운 내용이 들어 있지 않을 것입니다. 이제 PowerShell을 통해 모든 리소스에 신속하게 태그를 지정하는 방법을 살펴보겠습니다.


## PowerShell을 사용한 태그 지정

먼저 최신 [Azure PowerShell 모듈](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)을 구합니다. 이번에 Azure PowerShell 모듈을 처음 사용하는 경우이면 [설명서를 참조](http://azure.microsoft.com/documentation/articles/install-configure-powershell)하여 더 빠르게 작업할 수 있습니다. 이 문서의 목적에 맞게 이미 계정을 추가했고 태그를 지정하려는 리소스가 있는 구독을 선택했다고 가정합니다.

태그 지정은 [리소스 관리자](http://msdn.microsoft.com/library/azure/dn790568.aspx)에서 제공되는 리소스 및 리소스 그룹에만 사용할 수 있으므로 먼저 리소스 관리자를 사용하도록 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/)을 참조하세요.

  Switch-AzureMode AzureResourceManager

태그는 리소스 및 리소스 그룹에 직접 존재하므로 이미 적용된 태그를 확인하기 위해서는 `Get-AzureResource` 또는 `Get-AzureResourceGroup`가 있는 리소스나 리소스 그룹을 파악하기만 하면 됩니다. 리소스 그룹부터 살펴보겠습니다.

![Getting tags with Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

이 cmdlet은 적용된 태그를 포함하여 리소스 그룹의 일부 메타데이터를 반환합니다. 리소스 그룹에 태그를 지정하기 위해 `Set-AzureResourceGroup`을 사용하고 태그 이름과 값을 지정합니다.

![Setting tags with Set-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

태그는 전체적으로 업데이트되므로 이미 태그가 지정된 리소스에 태그를 추가하려는 경우 유지하려는 모든 태그가 있는 배열을 저장해야 합니다. 태그를 제거하려는 경우에는 제거하려는 태그가 없는 배열을 저장하면 됩니다. 

이 프로세스는 `Get-AzureResource` 및 `Set-AzureResource` cmdlet을 사용한다는 점을 제외하고 리소스에도 동일하게 적용됩니다. 특정 태그가 있는 리소스 또는 리소스 그룹을 가져오려면 `-Tag` 매개 변수와 함께 `Get-AzureResource` 또는 `Get-AzureResourceGroup` cmdlet을 사용합니다.

![Getting tagged resources and resource groups with Get-AzureResource and Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## 리소스 관리자를 사용하여 태그 지정

미리 보기 포털 및 PowerShell 둘 다 백그라운드에서 [리소스 관리자 REST API](http://msdn.microsoft.com/library/azure/dn790568.aspx)를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 GET을 사용하여 태그를 얻고 PATCH 호출을 통해 태그 집합을 업데이트할 수 있습니다.


## 분류 관리

앞에서 자동 완성 기능이 일관성을 유지하고 실수를 방지하는 데 어떤 도움을 주는지 살펴보았습니다. 자동 완성은 구독에 대해 설정된 사용 가능한 태그의 분류에 따라 채워집니다. 리소스 또는 리소스 그룹에 추가하는 각 태그는 구독 전체의 분류에 자동으로 추가되지만 나중에 리소스에 태그를 지정할 때 사용하려는 태그 이름 및 값으로 분류를 미리 채울 수도 있습니다.

PowerShell을 사용하여 구독 내의 모든 태그 목록을 가져오려면 `Get-AzureTag` cmdlet을 사용합니다.

![Get-AzureTag in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureTag-in-PowerShell.png)


"hidden-" 및 "link:"로 시작하는 태그도 볼 수 있습니다. 이러한 태그는 내부 태그이므로 무시해도 되며 변경하지 않도록 하세요. 

`New-AzureTag` cmdlet을 사용하여 분류에 새 태그를 추가합니다. 이러한 태그는 아직 리소스 또는 리소스 그룹에 적용되지 않았더라도 자동 완성에 포함됩니다. 태그 이름/값을 제거하려면 먼저 함께 사용될 수 있는 모든 리소스에서 태그를 제거하고 `Remove-AzureTag` cmdlet을 사용하여 분류에서 제거합니다.

포털에서 태그 분류를 확인하려면 찾아보기 허브를 사용하여 모든 항목을 확인하고 태그를 선택합니다.

![Find tags via the Browse hub](./media/azure-preview-portal-using-tags/browse-tags.png)

빠른 액세스를 위해 시작 보드에 가장 중요 한 태그를 고정하고 진행 준비를 완료합니다. 즐겁게 작업하세요!

![Pin tags to the Startboard](./media/azure-preview-portal-using-tags/pin-tags.png)


<!--HONumber=46--> 
