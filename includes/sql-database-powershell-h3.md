
### <a name="start-your-powershell-session"></a>PowerShell 세션 시작
우선 최신 [Azure PowerShell](https://msdn.microsoft.com/library/mt619274\(v=azure.300\).aspx)을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

> [!NOTE]
> SQL Database의 여러 새로운 기능은 [Azure Resource Manager 배포 모델](../articles/azure-resource-manager/resource-group-overview.md)을 사용할 때 지원되므로 예제는 Resource Manager에 [Azure SQL Database PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)을 사용합니다. 이전 버전과 호환성을 위해 서비스 관리(클래식) 배포 모델 [Azure SQL Database 서비스 관리 cmdlet](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx)이 지원되지만 Resource Manager cmdlet을 사용하는 것이 좋습니다.
> 
> 

[**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) cmdlet을 실행하면 자격 증명을 입력할 수 있는 로그인 화면이 나타납니다. Azure 포털에 로그인하는 데 사용하는 동일한 자격 증명을 사용합니다.

    Add-AzureRmAccount

여러 구독이 있는 경우 [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) cmdlet을 사용하여 PowerShell 세션이 사용해야 하는 구독을 선택합니다. 현재 PowerShell 세션이 사용 중인 구독을 보려면 [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx)를 실행합니다. 모든 구독을 보려면 [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx)을 실행합니다.

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Dec16_HO2-->


