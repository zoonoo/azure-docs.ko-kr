### 최신 PowerShell 버전의 태그 cmdlet 변경

[Azure PowerShell 2.0][powershell]의 2016년 8월 릴리스에서는 태그를 사용하는 방식이 크게 변경되었습니다. 계속하기 전에 AzureRm.Resources 모듈 버전을 확인합니다.

    Get-Module -ListAvailable -Name AzureRm.Resources | Select Version

2016년 8월 이전에 Azure PowerShell을 마지막으로 업데이트한 경우 결과에 3.0보다 낮은 버전이 표시됩니다.

    Version
    -------
    2.0.2

2016년 8월 이후에 Azure PowerShell을 업데이트한 경우 결과에 버전 3.0이 표시됩니다.

    Version
    -------
    3.0.1
    
모듈 버전 3.0.1 이상에는 태그를 사용하기 위한 가장 최근의 cmdlet가 있습니다. 이 버전의 Azure 리소스 모듈은 PowerShell 갤러리, PowerShellGet 또는 웹 플랫폼 설치 관리자를 사용하여 Azure PowerShell을 업그레이드할 때 자동으로 설치됩니다. 버전이 3.0.1보다 이전 버전이면 해당 버전을 사용할 수 있으나 최신 버전으로 업데이트하는 것이 좋습니다. 최신 버전은 태그를 보다 쉽게 사용할 수 있도록 변경되었습니다. 두 가지 방법은 모두 이 항목에 나와 있습니다.

### 최신 버전의 변경 내용에 맞게 스크립트 업데이트 

최신 릴리스에서 **Tags** 매개 변수가 **Tag**로, 형식이 **Hashtable**에서 **Hashtable**로 변경되었습니다. 각 항목에 대해 더 이상 **이름** 및 **값**을 제공할 필요가 없습니다. 대신 키-값 쌍을 **키 = "값"** 형식으로 제공합니다.

기존 스크립트를 업데이트하려면 **Tags** 매개 변수를 **Tag**로 변경하고 다음 예제와 같이 태그 형식을 변경합니다.

    # Old
    New-AzureRmResourceGroup -Tags @{ Name = "testtag"; Value = "testval" } -Name $resourceGroupName -Location $location

    # New
    New-AzureRmResourceGroup -Tag @{ testtag = "testval" } -Name $resourceGroupName -Location $location 

그러나 리소스 그룹 및 리소스는 해당 메타데이터에서 여전히 **Tags** 속성을 반환합니다. 이 속성은 변경되지 않습니다.

### 버전 3.0.1 이상

태그는 리소스 및 리소스 그룹에 직접 존재합니다. 기존 태그를 확인하려면 **Get-AzureRmResource**를 사용하여 리소스를, **Get-AzureRmResourceGroup**을 사용하여 리소스 그룹을 봅니다.

리소스 그룹부터 살펴보겠습니다.

    Get-AzureRmResourceGroup -Name testrg1

이 cmdlet은 적용된 태그를 포함하여 리소스 그룹의 일부 메타데이터를 반환합니다.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

태그를 포함하는 리소스 메타데이터를 검색하려면 다음 예제를 사용합니다.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

결과에서 태그 이름을 확인할 수 있습니다.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : testrg1
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {Dept, Environment}

**Tags** 속성을 사용하여 태그 이름 및 값을 가져옵니다.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1).Tags

그러면 다음 결과가 반환됩니다.

    Name                   Value
    ----                   -----
    Dept                   Finance
    Environment            Production

특정 리소스 그룹 또는 리소스에 대한 태그를 보는 대신 특정 태그 및 값이 있는 모든 리소스 또는 리소스 그룹을 검색할 수 있습니다. 특정 태그가 있는 리소스 그룹을 가져오려면 **-Tag** 매개 변수와 함께 **Find-AzureRmResourceGroup** cmdlet을 사용합니다.

태그 값을 가진 리소스 그룹을 검색하려면 다음 형식을 사용합니다.

    (Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 

특정 태그 및 값이 있는 모든 리소스를 가져오려면 **Find-AzureRmResource** cmdlet을 사용합니다.

    (Find-AzureRmResource -TagName Dept -TagValue Finance).Name
    
기존 태그가 없는 리소스 그룹에 태그를 추가하려면 **Set-AzureRmResourceGroup** 명령을 사용하여 태그 개체를 지정합니다.

    Set-AzureRmResourceGroup -Name test-group -Tag @{ Dept="IT"; Environment="Test" }

새로운 태그 값이 있는 리소스 그룹이 반환됩니다.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
**Set-AzureRmResource** 명령을 사용하여 기존 태그가 없는 리소스에 태그를 추가할 수 있습니다.

    Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

태그는 전체적으로 업데이트됩니다. 다른 태그가 있는 리소스에 하나의 태그를 추가하려면 유지하려는 모든 태그가 포함된 배열을 사용합니다. 먼저, 기존 태그를 선택하고 해당 집합에 태그를 추가한 후 모든 태그를 다시 적용합니다.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Status="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

태그를 하나 이상 제거하려는 경우에는 제거하려는 태그가 없는 배열을 저장하면 됩니다.

이 프로세스는 **Get-AzureRmResource** 및 **Set-AzureRmResource** cmdlet을 사용한다는 점을 제외하고 리소스에 대한 프로세스와 동일합니다.

PowerShell을 사용하여 구독 내의 모든 태그 목록을 가져오려면 **Get-AzureRmTag** cmdlet을 사용합니다.

    Get-AzureRmTag
    
그러면 태그 이름과 태그가 있는 리소스 및 리소스 그룹 수가 반환됩니다.

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

"hidden-" 및 "link:"로 시작하는 태그도 볼 수 있습니다. 이러한 태그는 내부 태그이므로 무시해도 되며 변경하지 않도록 하세요.

**New-AzureRmTag** cmdlet을 사용하여 분류에 새 태그를 추가합니다. 이러한 태그는 아직 리소스 또는 리소스 그룹에 적용되지 않았더라도 자동 완성에 포함됩니다. 태그 이름/값을 제거하려면 먼저 함께 사용될 수 있는 모든 리소스에서 태그를 제거하고 **Remove-AzureRmTag** cmdlet을 사용하여 분류에서 제거합니다.

### 3\.0.1 이전 버전

태그는 리소스 및 리소스 그룹에 직접 존재합니다. 기존 태그를 확인하려면 **Get-AzureRmResource**를 사용하여 리소스를, **Get-AzureRmResourceGroup**을 사용하여 리소스 그룹을 봅니다.

리소스 그룹부터 살펴보겠습니다.

    Get-AzureRmResourceGroup -Name testrg1

이 cmdlet은 적용된 태그를 포함하여 리소스 그룹의 일부 메타데이터를 반환합니다.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production
                    
리소스 메타데이터를 검색하려면 다음 예제를 사용합니다. 리소스 메타데이터는 태그를 직접 표시하지 않습니다.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

태그는 결과와 같이 Hashtable 개체로만 표시됩니다.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

실제 태그를 보려면 **Tags** 속성을 검색합니다.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
   
서식이 지정된 결과가 반환됩니다.
    
    Dept: Finance
    Environment: Production
    
특정 리소스 그룹 또는 리소스에 대한 태그를 보는 대신 특정 태그 및 값이 있는 모든 리소스 또는 리소스 그룹을 검색할 수 있습니다. 특정 태그가 있는 리소스 그룹을 가져오려면 **-Tag** 매개 변수와 함께 **Find-AzureRmResourceGroup** cmdlet을 사용합니다.

태그 값을 가진 리소스 그룹을 검색하려면 다음 형식을 사용합니다.

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    
특정 태그 및 값이 있는 모든 리소스를 가져오려면 Find-AzureRmResource cmdlet를 사용합니다.

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }

기존 태그가 없는 리소스 그룹에 태그를 추가하려면 Set-AzureRmResourceGroup 명령을 사용하여 태그 개체를 지정합니다.

    Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )
    
새로운 태그 값이 있는 리소스 그룹이 반환됩니다.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                Name          Value
                =======       =====
                Dept          IT
                Environment   Test

Set-AzureRmResource 명령을 사용하여 기존 태그가 없는 리소스에 태그를 추가할 수 있습니다.

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

태그는 전체적으로 업데이트됩니다. 다른 태그가 있는 리소스에 하나의 태그를 추가하려면 유지하려는 모든 태그가 포함된 배열을 사용합니다. 먼저, 기존 태그를 선택하고 해당 집합에 태그를 추가한 후 모든 태그를 다시 적용합니다.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

태그를 하나 이상 제거하려는 경우에는 제거하려는 태그가 없는 배열을 저장하면 됩니다.

이 프로세스는 Get-AzureRmResource 및 Set-AzureRmResource cmdlet를 사용한다는 점을 제외하고 리소스에 대한 프로세스와 동일합니다.

PowerShell을 사용하여 구독 내의 모든 태그 목록을 가져오려면 **Get-AzureRmTag** cmdlet을 사용합니다.

    Get-AzureRmTag
    
그러면 태그 이름과 태그가 있는 리소스 및 리소스 그룹 수가 반환됩니다.

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

"hidden-" 및 "link:"로 시작하는 태그도 볼 수 있습니다. 이러한 태그는 내부 태그이므로 무시해도 되며 변경하지 않도록 하세요.

**New-AzureRmTag** cmdlet을 사용하여 분류에 새 태그를 추가합니다. 이러한 태그는 아직 리소스 또는 리소스 그룹에 적용되지 않았더라도 자동 완성에 포함됩니다. 태그 이름/값을 제거하려면 먼저 함께 사용될 수 있는 모든 리소스에서 태그를 제거하고 **Remove-AzureRmTag** cmdlet을 사용하여 분류에서 제거합니다.


[powershell]: https://msdn.microsoft.com/library/mt619274(v=azure.200).aspx

<!---HONumber=AcomDC_0907_2016-->