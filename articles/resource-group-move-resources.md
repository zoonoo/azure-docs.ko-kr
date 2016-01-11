<properties 
	pageTitle="새 리소스 그룹으로 리소스 이동" 
	description="Azure PowerShell 또는 REST API를 사용하여 Azure 리소스 관리자에 대한 새 리소스 그룹에 리소스를 이동합니다." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/18/2015" 
	ms.author="tomfitz"/>

# 새 리소스 그룹 또는 구독으로 리소스 이동

이 항목에서는 하나의 리소스 그룹에서 다른 리소스 그룹으로 리소스를 이동하는 방법을 보여 줍니다. 새 구독으로 리소스를 이동할 수도 있습니다. 다음과 같은 결정을 내린 경우 리소스를 이동해야 할 수 있습니다.

1. 대금 청구를 위해서는 리소스가 다른 구독 상태여야 합니다.
2. 리소스가 이전에 그룹화되었던 리소스와 동일한 수명 주기를 더 이상 공유하지 않습니다. 해당 리소스를 다른 리소스와는 별도로 관리할 수 있도록 새 리소스 그룹으로 이동하려고 합니다.
3. 이제 리소스는 다른 리소스 그룹의 다른 리소스와 동일한 수명 주기를 공유합니다. 함께 관리할 수 있도록 리소스를 다른 리소스가 있는 리소스 그룹으로 이동하려고 합니다.

리소스를 이동할 때 다음과 같은 사항을 고려해야 합니다.

1. 리소스의 위치는 변경할 수 없습니다. 리소스를 이동할 때는 새 리소스 그룹으로만 이동됩니다. 새 리소스 그룹은 다른 위치를 가질 수 있지만 리소스의 위치는 변경되지 않습니다.
2. 대상 리소스 그룹은 사용자가 이동하는 리소스와 동일한 응용 프로그램 수명 주기를 공유하는 리소스만 포함해야 합니다.
3. Azure PowerShell 또는 Azure CLI를 사용하는 경우 최신 버전을 사용하고 있는지 확인합니다. 사용 중인 버전을 업데이트하려면 Microsoft 웹 플랫폼 설치 관리자를 실행하고 새 버전을 사용할 수 있는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md) 및 [Azure CLI 설치](xplat-cli-install.md)를 참조하세요.
4. 이동 작업을 완료하는 데 다소 시간이 걸릴 수 있으며 이 시간 동안 프롬프트는 작업이 완료될 때까지 대기합니다.
5. 리소스를 이동할 때 원본 그룹과 대상 그룹은 작업 기간 동안 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 그룹에서 차단됩니다.

## 지원되는 서비스

모든 서비스가 현재 리소스 이동 기능을 지원하는 것은 아닙니다.

현재, 새 리소스 그룹 및 구독으로의 이동을 모두 지원하는 서비스는 다음과 같습니다.

- API 관리
- 자동화
- 배치
- 데이터 팩터리
- DocumentDB
- HDInsight 클러스터
- 키 자격 증명 모음
- 논리 앱
- 모바일 고객 관리
- 알림 허브
- Operational Insights
- Redis 캐시
- 검색
- SQL 데이터베이스
- 웹앱(일부 [제한 사항](app-service-web/app-service-move-resources.md) 적용)

새 리소스 그룹으로의 이동은 지원하지만 새 구독으로의 이동은 지원하지 않는 서비스는 다음과 같습니다.

- 가상 컴퓨터(기본)
- 저장소(기본)

현재 리소스 이동을 지원하지 않는 서비스는 다음과 같습니다.

- 가상 컴퓨터
- 가상 네트워크
- 저장소

웹앱으로 작업할 경우에는 앱 서비스 계획만 이동할 수 없습니다. 웹앱을 이동하려면 옵션은 다음과 같습니다.

- 대상 리소스 그룹에 아직 Microsoft.Web 리소스가 없는 경우 하나의 리소스 그룹에 있는 모든 리소스를 다른 리소스 그룹으로 이동합니다.
- 웹앱을 다른 리소스 그룹으로 이동하지만 앱 서비스는 원래 리소스 그룹에 유지합니다.

## PowerShell을 사용하여 리소스 이동

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 **Move-AzureRmResource** 명령을 사용합니다.

첫 번째 예제는 새 리소스 그룹에 하나의 리소스를 이동하는 방법을 보여 줍니다.

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

두 번째 예제는 새 리소스 그룹에 여러 리소스를 이동하는 방법을 보여 줍니다.

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

새 구독으로 이동하려면 **DestinationSubscriptionId** 매개 변수 값을 포함합니다.

## Azure CLI를 사용하여 리소스 이동

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 **azure resource move** 명령을 사용합니다. 다음 예제에서는 Redis Cache를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. **-i** 매개 변수에서 이동할 리소스 ID를 쉼표로 구분한 목록을 제공합니다.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## REST API를 사용하여 리소스 이동

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 다음을 실행합니다.

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

요청 본문에서 대상 리소스 그룹 및 이동할 리소스를 지정합니다. 이동 REST 작업에 대한 자세한 내용은 [리소스 이동](https://msdn.microsoft.com/library/azure/mt218710.aspx)을 참조하세요.

## 다음 단계
- [리소스 관리자로 Azure PowerShell 사용](./powershell-azure-resource-manager.md)
- [리소스 관리에서 Azure CLI 사용](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Azure 포털을 사용하여 리소스 관리](azure-portal/resource-group-portal.md)
- [태그를 사용하여 리소스 구성](./resource-group-using-tags.md)

<!---HONumber=AcomDC_1223_2015-->