---
title: Azure Stack에서 MySQL 리소스 공급자를 유지 관리 | Microsoft Docs
description: Azure Stack에서 MySQL 리소스 공급자 서비스를 유지 관리할 수 있습니다 하는 방법에 대해 알아봅니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: e0de6b26499b4a5166db411eccee543be43c8352
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245993"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>MySQL 리소스 공급자 유지 관리 작업

MySQL 리소스 공급자 잠겨 가상 머신에서 실행 됩니다. 유지 관리 작업을 사용 하려면 가상 컴퓨터의 보안을 업데이트 해야 합니다. 이렇게 하려면 최소 권한의 원칙을 사용 하 여 DBAdapterMaintenance PowerShell 관리 JEA (Just Enough) 끝점을 사용할 수 있습니다. 리소스 공급자 설치 패키지는이 작업에 대 한 스크립트를 포함합니다.

## <a name="update-the-virtual-machine-operating-system"></a>가상 머신 운영 체제를 업데이트 합니다.

리소스 공급자에서 실행 되므로 한 *사용자* 릴리스되는 경우 필요한 패치 및 업데이트를 적용 해야 하는 가상 머신. VM에 업데이트를 적용할 패치 및 업데이트 주기의 일환으로 제공 되는 Windows 업데이트 패키지를 사용할 수 있습니다.

다음 방법 중 하나를 사용 하 여 공급자 가상 컴퓨터를 업데이트 합니다.

- 현재 패치 된 Windows Server 2016 Core 이미지를 사용 하는 최신 리소스 공급자 패키지를 설치 합니다.
- Windows 업데이트 패키지를 설치 하는 동안 설치 또는 리소스 공급자를 업데이트 합니다.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>가상 머신 Windows Defender 정의 업데이트

Defender 정의 업데이트 하려면 다음이 단계를 수행 합니다.

1. Windows Defender 정의 업데이트를 다운로드 [Windows Defender 정의](https://www.microsoft.com/en-us/wdsi/definitions)합니다.

    정의 페이지의 "수동으로 다운로드 하 여 정의 설치"까지 아래로 스크롤하십시오. "Windows 10 및 Windows 8.1 용 Windows Defender 바이러스 백신" 64 비트 파일을 다운로드 합니다.

    또는 사용 하 여 [이 직접 링크](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) fpam fe.exe 파일 다운로드/실행 합니다.

2. MySQL 리소스 공급자 어댑터 가상 머신 유지 관리 끝점에 대 한 PowerShell 세션을 엽니다.

3. 리소스 공급자 어댑터 유지 관리 끝점 세션을 사용 하 여 VM에 정의 업데이트 파일을 복사 합니다.

4. 유지 관리 PowerShell 세션에서 실행 합니다 _업데이트 DBAdapterWindowsDefenderDefinitions_ 명령입니다.

5. 정의 설치한 후 사용 하 여 정의 업데이트 파일을 삭제 하는 것이 좋습니다 합니다 _제거 ItemOnUserDrive)_ 명령입니다.

**정의 업데이트를 위한 PowerShell 스크립트 예제입니다.**

편집 하 고 Defender 정의 업데이트 하려면 다음 스크립트를 실행할 수 있습니다. 환경에서 값을 사용 하 여 스크립트의 값을 대체 합니다.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>비밀 회전

*이러한 지침은 Azure Stack 통합 시스템에만 적용 됩니다.*

SQL 및 MySQL 리소스 공급자를 사용 하 여 Azure Stack을 사용 하 여 시스템에 통합 하는 경우 Azure Stack 운영자는 만료 되지 않는 것을 확인 하려면 다음 리소스 공급자 인프라 암호를 회전 하는 일을 담당 합니다.

- 외부 SSL 인증서 [배포 중에 제공 된](azure-stack-pki-certs.md)합니다.
- 리소스 공급자 VM 로컬 관리자 계정 암호를 배포 중에 제공 합니다.
- 리소스 공급자 (dbadapterdiag) 진단 사용자 암호입니다.

### <a name="powershell-examples-for-rotating-secrets"></a>암호를 회전 하는 것에 대 한 PowerShell 예제

**동시에 모든 암호를 변경 합니다.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**진단 사용자 암호를 변경 합니다.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**VM 로컬 관리자 계정 암호를 변경 합니다.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**SSL 인증서 암호를 변경 합니다.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 매개 변수

|매개 변수|설명|
|-----|-----|
|AzCredential|Azure Stack 서비스 관리자 계정 자격 증명입니다.|
|CloudAdminCredential|Azure Stack 클라우드 관리자 도메인 계정 자격 증명입니다.|
|PrivilegedEndpoint|Get-AzureStackStampInformation를 액세스 하기 위해 권한 있는 끝점입니다.|
|DiagnosticsUserPassword|진단 사용자 계정 암호입니다.|
|VMLocalCredential|MySQLAdapter VM에서 로컬 관리자 계정입니다.|
|DefaultSSLCertificatePassword|기본 SSL 인증서 (* pfx) 암호.|
|DependencyFilesLocalPath|종속성 파일 로컬 경로입니다.|
|     |     |

### <a name="known-issues"></a>알려진 문제

**문제:**<br>
비밀 회전에 대 한 로그 되지 비밀 회전 스크립트를 실행할 때 실패 하는 경우 자동으로 수집 됩니다.

**해결 방법:**<br>
로그를 수집 하도록 모든 리소스 공급자를 AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, C:\Logs에 저장을 포함 하 여 Get AzsDBAdapterLogs cmdlet을 사용 합니다.

## <a name="collect-diagnostic-logs"></a>진단 로그를 수집 합니다.

잠긴된 가상 컴퓨터에서 로그를 수집 하려면 DBAdapterDiagnostics PowerShell 관리 JEA (Just Enough) 끝점을 사용할 수 있습니다. 이 끝점에는 다음 명령을 제공합니다.

- **Get-AzsDBAdapterLog**합니다. 이 명령은 리소스 공급자 진단 로그의 zip 패키지를 만들고 세션의 사용자 드라이브에 파일을 저장 합니다. 매개 변수 없이이 명령을 실행할 수 있습니다 하 고 로그의 마지막 4 시간 수집 됩니다.

- **제거-AzsDBAdapterLog**합니다. 이 명령은 리소스 공급자 VM에서 기존 로그 패키지를 제거합니다.

### <a name="endpoint-requirements-and-process"></a>끝점의 요구 사항 및 프로세스

리소스 공급자를 설치 하거나 업데이트 dbadapterdiag 사용자 계정이 생성 됩니다. 에서는 진단 로그를 수집 하려면이 계정을 사용 합니다.

>[!NOTE]
>Dbadapterdiag 계정 암호를 사용 하는 공급자 배포 또는 업데이트 중에 만든 가상 컴퓨터의 로컬 관리자 암호와 같습니다.

사용 하는 _DBAdapterDiagnostics_ 명령을, 리소스 공급자 가상 컴퓨터에 원격 PowerShell 세션을 만들고 실행 합니다 **Get AzsDBAdapterLog** 명령입니다.

사용 하 여 로그 컬렉션에 대 한 시간 범위를 설정 합니다 **FromDate** 하 고 **ToDate** 매개 변수입니다. 이러한 매개 변수 중 하나 또는 모두를 지정 하지 않으면 다음 기본값이 사용 됩니다.

* FromDate는 현재 시간 이전 4 시간입니다.
* ToDate 현재 시간입니다.

**로그를 수집 하기 위한 PowerShell 스크립트 예제입니다.**

다음 스크립트는 리소스 공급자 VM에서에서 진단 로그를 수집 하는 방법을 보여줍니다.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>다음 단계

[MySQL 리소스 공급자를 제거 합니다.](azure-stack-mysql-resource-provider-remove.md)
