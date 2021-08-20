---
title: 스크립트 샘플 - 온-프레미스 Windows 서버용 백업 구성
description: 스크립트를 사용하여 온-프레미스 Windows 서버용 백업을 구성하는 방법을 알아봅니다.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 7ddf8fcca6761821f586030147276eefd364fc48
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560854"
---
# <a name="powershell-script-to-configure-backup-for-on-premises-windows-server"></a>온-프레미스 Windows 서버용 백업을 구성하는 PowerShell 스크립트

이 스크립트를 사용하면 자격 증명 모음 만들기부터 MARS 에이전트 및 정책 구성까지 온-프레미스 Windows 서버용 백업을 쉽게 구성할 수 있습니다.

## <a name="sample"></a>샘플

```azurepowershell
# Create Recovery Services Vault (RSV)
Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
New-AzResourceGroup –Name "test-rg" –Location "WestUS"
New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
$Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant

Get-AzRecoveryServicesVault

# Installing the Azure Backup agent
$MarsAURL = 'https://aka.ms/Azurebackup_Agent'
$WC = New-Object System.Net.WebClient
$WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
C:\Downloads\MARSAgentInstaller.EXE /q

MARSAgentInstaller.exe /q # Please note the commandline install options available here: https://docs.microsoft.com/en-us/azure/backup/backup-client-automation#installation-options

# Registering Windows Server or Windows client machine to a Recovery Services Vault
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false

# Networking settings
Set-OBMachineSetting -NoProxy
Set-OBMachineSetting -NoThrottle

# Encryption settings
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>" #NOTE: You must generate a security pin by selecting Generate, under Settings > Properties > Security PIN in the Recovery Services vault section of the Azure portal. 
# See: https://docs.microsoft.com/en-us/rest/api/backup/securitypins/get 
# See: https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/Add-AzureKeyVaultKey?view=azurermps-6.13.0 

# Back up files and folders
$NewPolicy = New-OBPolicy
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule

# Configuring a retention policy
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy

# Including and excluding files to be backed up
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions

# Applying the policy
Get-OBPolicy | Remove-OBPolicy
Set-OBPolicy -Policy $NewPolicy
Get-OBPolicy | Get-OBSchedule
Get-OBPolicy | Get-OBRetentionPolicy
Get-OBPolicy | Get-OBFileSpec

# Performing an on-demand backup
Get-OBPolicy | Start-OBBackup

# Remote management
Get-Service -Name WinRM
Enable-PSRemoting -Force
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force

```

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 MARS 에이전트를 사용해 온-프레미스 백업을 배포하고 관리하는 방법을 [자세히 알아봅니다](../backup-client-automation.md).