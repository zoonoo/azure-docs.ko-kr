## <a name="set-up-azure-powershell-for-azure-dns"></a>Azure DNS를 위한 Azure PowerShell 설정

### <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

### <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인

PowerShell 콘솔을 열고 계정에 연결합니다. 자세한 내용은 [Resource Manager에서 PowerShell 사용](../articles/azure-resource-manager/powershell-azure-resource-manager.md)을 참조하세요.

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>구독 선택
 
계정에 대한 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

사용할 Azure 구독을 선택합니다.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 그러나 모든 DNS 리소스는 국가별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure DNS에 영향을 주지 않습니다.

기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛸 수 있습니다.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>리소스 공급자 등록

Azure DNS 서비스는 Microsoft.Network 리소스 공급자에 의해 관리됩니다. Azure DNS를 사용하려면 먼저 이 리소스 공급자를 사용하도록 Azure 구독을 등록해야 합니다. 이 작업은 각 구독에 대해 한 번만 수행하면 됩니다.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

<!--HONumber=Dec16_HO3-->


