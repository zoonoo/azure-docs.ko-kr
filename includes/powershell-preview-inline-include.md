이 항목에는 Azure PowerShell cmdlet을 사용하는 예제가 포함되어 있습니다. Azure PowerShell은 현재 두 가지 릴리스, 즉, 1.0 Preview 및 0.9.8로 사용할 수 있습니다. 기존 스크립트가 있고 지금 변경하지 않으려는 경우 계속해서 0.9.8 릴리스를 사용할 수 있습니다. 1.0 Preview 릴리스를 사용하는 경우 프로덕션 환경에서 사용하기 전에 사전 프로덕션 환경에서 스크립트를 주의 깊게 테스트해야 해야 예기치 않는 상황을 방지할 수 있습니다.

대부분의 경우에서 두 버전 간의 유일한 차이점은 1.0 Preview cmdlet 이름이 {동사}-AzureRm{명사} 패턴을 따르는 반면 0.9.8 이름에는 **Rm**이 없습니다(예: New-AzureResourceGroup 대신 New-AzureRmResourceGroup). 버전 간의 차이가 좀 더 의미 있는 경우 이 항목에서 두 버전에 대한 예를 보여 줍니다.

Azure PowerShell 0.9.8을 사용하는 경우 먼저 **Switch-AzureMode AzureResourceManager** 명령을 실행하여 리소스 관리자 모드를 사용하도록 설정해야 합니다. 이 명령은 1.0 Preview에서는 필요하지 않습니다.

릴리스 설치 및 제거 방법을 포함하여 1.0 Preview 릴리스에 대한 자세한 내용은 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)를 참조하세요. 리소스 관리자 명령에서 중요한 변경 내용은 [Azure 리소스 관리자 관리 PowerShell cmdlet 변경 내용](../articles/powershell-preview-resource-manager-changes.md)을 참조하세요.

<!---HONumber=Oct15_HO4-->