이 항목에는 Azure PowerShell 1.0 Preview의 PowerShell cmdlet이 포함되어 있습니다. 지금 바로 이 미리 보기 릴리스를 설치하지 않으려면 Azure PowerShell 0.9.8을 계속 사용하면 됩니다. 대부분의 경우에서 두 버전 간의 유일한 차이점은 1.0 Preview cmdlet 이름이 {동사}-AzureRm{명사} 패턴을 따르는 반면 0.9.8 이름에는 **Rm**이 없습니다(예: New-AzureResourceGroup 대신 New-AzureRmResourceGroup). 버전 간의 차이가 좀 더 의미 있는 경우 이 항목에서 두 버전에 대한 예를 보여 줍니다.

Azure PowerShell 0.9.8을 사용하는 경우 먼저 **Switch-AzureMode AzureResourceManager** 명령을 실행하여 리소스 관리자 모드를 사용하도록 설정해야 합니다. 이 명령은 1.0 Preview에서는 필요하지 않습니다.

권장되는 사용에 대한 설명을 비롯한 1.0 Preview 릴리스에 대한 자세한 내용은 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)를 참조하세요. Azure PowerShell 1.0 Preview를 설치하는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 Cmdlet](https://msdn.microsoft.com/library/mt125356.aspx)을 참조하세요. 리소스 관리자 명령에서 중요한 변경 내용은 [Azure 리소스 관리자 관리 PowerShell cmdlet 변경 내용](../articles/powershell-preview-resource-manager-changes.md)을 참조하세요.

<!---HONumber=Oct15_HO3-->