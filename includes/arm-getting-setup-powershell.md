## 리소스 관리자 템플릿에 대한 PowerShell 설정
 
리소스 관리자에서 Azure PowerShell을 사용하려면 먼저 올바른 Windows PowerShell 및 Azure PowerShell 버전이 있어야 합니다.

### PowerShell 버전 확인

Windows PowerShell 버전 3.0 또는 4.0이 있는지 확인합니다. Windows PowerShell의 버전을 확인하려면 Windows PowerShell 명령 프롬프트에 다음 명령을 입력합니다.

	$PSVersionTable

다음과 같은 유형의 정보가 제공됩니다.

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2


**PSVersion** 값이 3.0 또는 4.0인지 확인합니다. 그렇지 않으면 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)을 참조하세요.

또한 Azure PowerShell 버전 0.9.0 이상이 있어야 합니다. Azure PowerShell을 설치 및 구성하지 않은 경우 지침을 보려면 [여기](powershell-install-configure.md)를 클릭하세요.

Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 설치한 Azure PowerShell의 버전을 확인할 수 있습니다.

	Get-Module azure | format-table version

다음과 같은 유형의 정보가 제공됩니다.

	Version
	-------
	0.9.0

0\.9.0 이상이 없는 경우 프로그램 및 기능 제어판을 사용하여 Azure PowerShell을 제거한 다음 최신 버전을 설치해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하세요.

### Azure 계정 및 구독 설정

Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

Azure PowerShell 명령 프롬프트를 열고 다음 명령으로 Azure에 로그온합니다.

	Add-AzureAccount

여러 Azure 구독이 있는 경우 다음 명령을 사용하여 Azure 구독을 나열할 수 있습니다.

	Get-AzureSubscription

다음과 같은 유형의 정보가 제공됩니다.

	SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
	SubscriptionName          : Visual Studio Ultimate with MSDN
	Environment               : AzureCloud
	SupportedModes            : AzureServiceManagement,AzureResourceManager
	DefaultAccount            : johndoe@contoso.com
	Accounts                  : {johndoe@contoso.com}
	IsDefault                 : True
	IsCurrent                 : True
	CurrentStorageAccountName : 
	TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Azure PowerShell 명령 프롬프트에서 다음 명령을 실행하여 현재 Azure 구독을 설정할 수 있습니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<SubscriptionName from the display of Get-AzureSubscription>"
	Select-AzureSubscription -SubscriptionName $subscr -Current	

Azure 구독 및 계정에 대한 자세한 내용은 [방법: 구독에 연결](powershell-install-configure.md#Connect)을 참조하세요.

### Azure 리소스 관리자 모듈로 전환

Azure 리소스 관리자 모듈을 사용하려면 기본 Azure 명령 집합에서 Azure 리소스 관리자 명령 집합으로 전환해야 합니다. 다음 명령을 실행합니다.

	Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]**Switch-AzureMode AzureServiceManagement** 명령을 사용하여 기본 명령 집합으로 다시 전환할 수 있습니다.

<!---HONumber=August15_HO6-->