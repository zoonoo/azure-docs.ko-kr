AzureRm.Resources 모듈 버전 3.0에서는 태그 사용 방법이 크게 달라졌습니다. 계속하기 전에 버전을 확인하세요.

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

버전이 3.0 이상일 경우 이 항목의 예제가 해당 환경에서 작동합니다. 버전 3.0 이상이 없을 경우 이 항목을 진행하기 전에 PowerShell 갤러리 또는 웹 플랫폼 설치 관리자를 사용하여 [버전을 업데이트](/powershell/azureps-cmdlets-docs/)합니다.

```powershell
Version
-------
3.5.0
```

*리소스 그룹*에 대한 기존 태그를 보려면 다음을 사용합니다.

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

그러면 스크립트가 다음 형식을 반환합니다.

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*지정된 리소스 ID를 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

또는 *지정된 이름 및 리소스 그룹을 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*특정 태그가 있는 리소스 그룹*을 가져오려면 다음을 사용합니다.

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

*특정 태그가 있는 리소스*를 가져오려면 다음을 사용합니다.

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

리소스 또는 리소스 그룹에 태그를 적용할 때마다 해당 리소스 또는 리소스 그룹의 기존 태그가 덮어써집니다. 따라서 리소스 또는 리소스 그룹에 기존 태그가 있는지에 따라 다른 방법을 사용해야 합니다. 

*기존 태그를 포함하지 않는 리소스 그룹*에 태그를 추가하려면 다음을 사용합니다.

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

*기존 태그를 포함하는 리소스 그룹*에 태그를 추가하려면 기존 태그를 검색하고, 새 태그를 추가하고, 태그를 다시 적용합니다.

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

*기존 태그를 포함하지 않는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName examplevnet -ResourceGroupName examplegroup
```

*기존 태그를 포함하는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```powershell
$tags = (Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName examplevnet -ResourceGroupName examplegroup
```

*리소스의 기존 태그를 유지하지 않고* 리소스 그룹의 모든 태그를 리소스에 적용하려면 다음 스크립트를 사용합니다.

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

*리소스의 중복되지 않는 기존 태그를 유지하지 않고* 리소스 그룹의 모든 태그를 리소스에 적용하려면 다음 스크립트를 사용합니다.

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

모든 태그를 제거하려면 빈 해시 테이블을 전달합니다.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```



