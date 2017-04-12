## <a name="delete-a-recovery-services-vault-powershell"></a>Recovery Services 자격 증명 모음 삭제(PowerShell)

1. Recovery Services 자격 증명 모음 가져오기

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. 자격 증명 모음 삭제

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> 실수로 자격 증명 모음을 삭제하면 모든 데이터가 손실되므로 위 명령을 사용할 때는 최대한 주의합니다. 이는 영구적인 작업이며, 이 작업을 되돌릴 방법이 없습니다.  


