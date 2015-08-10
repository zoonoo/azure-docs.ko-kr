<properties 
	pageTitle="태그를 사용하여 Azure 리소스 구성" 
	description="태그를 적용하여 대금 청구 및 관리를 위해 리소스를 구성하는 방법을 보여 줍니다." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac"
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>


# 태그를 사용하여 Azure 리소스 구성

Azure 포털 및 기본 리소스 관리자는 리소스를 구성하고 사용자에게 잘 맞게 환경을 사용자 지정하는 작업에 사용됩니다.

Azure 클래식 포털에서는 리소스를 분류하고 그룹화하기 위해 반드시 구독이 필요합니다. Azure 포털에서 관련 엔터티를 그룹화할 수 있는 [리소스 그룹을 도입](./resource-group-portal.md)했습니다. 이 리소스 그룹은 [역할 기반 액세스를 도입](./role-based-access-control-configure.md)했을 때 더 유용해졌습니다. 이제 같은 맥락으로 여러 리소스 그룹에서, 포털 내에서, 구독 간에 리소스를 추가적으로 분류하고 확인할 수 있게 리소스에 키/값 쌍을 태그로 지정할 수 있습니다.

팀, 프로젝트 또는 심지어 작업 환경별로 리소스를 그룹화하여 보고 싶을 때 보려는 리소스를 정확히 확인할 수 있습니다.


## Azure 포털의 태그

포털에서 리소스 및 리소스 그룹에 쉽게 태그를 지정할 수 있습니다. 찾아보기 허브를 사용하여 태그를 지정할 리소스 또는 리소스 그룹으로 이동한 후 블레이드 위쪽의 개요 섹션에서 태그 부분을 클릭합니다.

![리소스 및 리소스 그룹 블레이드에서 태그 파트](./media/resource-group-using-tags/rgblade.png)

이렇게 하면 이미 적용된 태그의 목록이 있는 블레이드가 열립니다. 첫 번째 태그이면 목록은 비어 있습니다. 태그를 추가하려면 이름 및 값을 지정하고 Enter 키를 누르기만 하면 됩니다. 몇 개의 태그를 추가하면 기존 태그 이름 및 값에 따라 자동 완성 옵션이 제공되므로 리소스 간에 일관된 분류를 적용하기 쉬우며 맞춤법 오류와 같은 일반적인 실수를 피할 수 있습니다.

![이름/값 쌍이 있는 태그 리소스](./media/resource-group-using-tags/tag-resources.png)

여기에서 각 개별 태그를 클릭하여 동일한 태그가 있는 모든 리소스의 목록을 볼 수 있습니다. 물론 첫 번째 태그이면 목록에 흥미로운 내용이 들어 있지 않을 것입니다. 이제 PowerShell을 통해 모든 리소스에 신속하게 태그를 지정하는 방법을 살펴보겠습니다.


## PowerShell을 사용한 태그 지정

먼저 최신 [Azure PowerShell 모듈](./install-configure-powershell.md)을 구합니다. 이번에 Azure PowerShell 모듈을 처음 사용하는 경우이면 [설명서를 참조](./install-configure-powershell.md)하여 더 빠르게 작업할 수 있습니다. 이 문서의 목적에 맞게 이미 계정을 추가했고 태그를 지정하려는 리소스가 있는 구독을 선택했다고 가정합니다.

태그 지정은 [리소스 관리자](http://msdn.microsoft.com/library/azure/dn790568.aspx)에서 제공되는 리소스 및 리소스 그룹에만 사용할 수 있으므로 먼저 리소스 관리자를 사용하도록 전환해야 합니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.

    Switch-AzureMode AzureResourceManager

태그는 리소스 및 리소스 그룹에 직접 존재하므로 이미 적용된 태그를 확인하기 위해서는 `Get-AzureResource` 또는 `Get-AzureResourceGroup`이 있는 리소스나 리소스 그룹을 파악하기만 하면 됩니다. 리소스 그룹부터 살펴보겠습니다.

![PowerShell에서 Get-AzureResourceGroup을 사용하여 태그 가져오기](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

이 cmdlet은 적용된 태그를 포함하여 리소스 그룹의 일부 메타데이터를 반환합니다. 리소스 그룹에 태그를 지정하기 위해 `Set-AzureResourceGroup`을 사용하고 태그 이름과 값을 지정합니다.

![PowerShell에서 Set-AzureResourceGroup을 사용하여 태그 설정](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

태그는 전체적으로 업데이트되므로 이미 태그가 지정된 리소스에 태그를 추가하려는 경우 유지하려는 모든 태그가 있는 배열을 사용해야 합니다. 태그를 제거하려는 경우에는 제거하려는 태그가 없는 배열을 저장하면 됩니다.

이 프로세스는 `Get-AzureResource` 및 `Set-AzureResource` cmdlet을 사용한다는 점을 제외하고 리소스에도 동일하게 적용됩니다. 특정 태그가 있는 리소스 또는 리소스 그룹을 가져오려면 `-Tag` 매개 변수와 함께 `Get-AzureResource` 또는 `Get-AzureResourceGroup` cmdlet을 사용합니다.

![PowerShell에서 Get-AzureResource 및 Get-AzureResourceGroup을 사용하여 태그가 지정된 리소스 및 리소스 그룹 가져오기](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## 리소스 관리자를 사용하여 태그 지정

포털 및 PowerShell 둘 다 백그라운드에서 [리소스 관리자 REST API](http://msdn.microsoft.com/library/azure/dn790568.aspx)를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 GET을 사용하여 태그를 얻고 PATCH 호출을 통해 태그 집합을 업데이트할 수 있습니다.


## 분류 관리

앞에서 자동 완성 기능이 일관성을 유지하고 실수를 방지하는 데 어떤 도움을 주는지 살펴보았습니다. 자동 완성은 구독에 대해 설정된 사용 가능한 태그의 분류에 따라 채워집니다. 리소스 또는 리소스 그룹에 추가하는 각 태그는 구독 전체의 분류에 자동으로 추가되지만 나중에 리소스에 태그를 지정할 때 사용하려는 태그 이름 및 값으로 분류를 미리 채울 수도 있습니다.

PowerShell을 사용하여 구독 내의 모든 태그 목록을 가져오려면 `Get-AzureTag` cmdlet을 사용합니다.

![PowerShell의 Get-AzureTag](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


"hidden-" 및 "link:"로 시작하는 태그도 볼 수 있습니다. 이러한 태그는 내부 태그이므로 무시해도 되며 변경하지 않도록 하세요.

`New-AzureTag` cmdlet을 사용하여 분류에 새 태그를 추가합니다. 이러한 태그는 아직 리소스 또는 리소스 그룹에 적용되지 않았더라도 자동 완성에 포함됩니다. 태그 이름/값을 제거하려면 먼저 함께 사용될 수 있는 모든 리소스에서 태그를 제거하고 `Remove-AzureTag` cmdlet을 사용하여 분류에서 제거합니다.

포털에서 태그 분류를 확인하려면 찾아보기 허브를 사용하여 모든 항목을 확인하고 태그를 선택합니다.

![찾아보기 허브를 통해 태그 찾기](./media/resource-group-using-tags/browse-tags.png)

빠른 액세스를 위해 시작 보드에 가장 중요 한 태그를 고정하고 진행 준비를 완료합니다. 즐겁게 작업하세요!

![시작 보드에 태그 고정](./media/resource-group-using-tags/pin-tags.png)

## 태그 지정 및 대금 청구

지원 되는 서비스에 대한 청구 데이터를 그룹화하는 데 태그를 사용할 수 있습니다. 예를 들어, [Azure 리소스 관리자와 통합된 가상 컴퓨터](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md)를 사용하면 가상 컴퓨터에 대한 청구 사용을 구성하는 태그를 정의하고 적용할 수 있습니다. 서로 다른 조직에 여러 VM을 실행하는 경우 비용 센터에 따라 그룹 사용에 대한 태그를 사용할 수 있습니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용과 같이 런타임 환경으로 비용을 분류하는 데 태그를 사용할 수 있습니다.

[Azure 계정 포털](https://account.windowsazure.com/) 또는 [EA 포털](https://ea.azure.com)에서 다운로드할 수 있는 [사용 api](billing-usage-rate-card-overview.md) 또는 사용 쉼표로 구분된 값(CSV) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다.

대금 청구에 태그를 지원하는 서비스용 사용 CSV를 다운로드하면 **태그** 열에 태그가 나타납니다 더 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하십시오.

![요금 청구에 대한 태그를 참조하십시오.](./media/resource-group-using-tags/billing_csv.png)

## 다음 단계
시작하기

- [Azure 리소스 관리자 개요](./resource-group-overview.md)  
- [Azure 리소스 관리자로 Azure PowerShell 사용](./powershell-azure-resource-manager.md)
- [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](./xplat-cli-azure-resource-manager.md)  
- [Azure 포털을 사용하여 Azure 리소스 관리](./resource-group-portal.md)  
  
응용 프로그램 만들기 및 배포
  
- [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)  
- [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](./resource-group-template-deploy.md)  
- [Azure에서 리소스 그룹 배포 문제 해결](./resource-group-deploy-debug.md)  
- [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)  
- [고급 템플릿 작업](./resource-group-advanced-template.md)  
  
액세스 관리 및 감사
  
- [리소스에 대한 액세스 관리 및 감사](./resource-group-rbac.md)  
- [Azure 리소스 관리자를 사용하여 서비스 사용자 인증](./resource-group-authenticate-service-principal.md)  
- [Azure 클래식 포털을 사용하여 새 Azure 서비스 사용자 만들기](./resource-group-create-service-principal-portal.md)  
  

<!---HONumber=July15_HO5-->