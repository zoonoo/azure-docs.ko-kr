---
title: Azure VM에서 BitLocker 부팅 오류 해결 | Microsoft Docs
description: Azure VM에서 BitLocker 부팅 오류를 해결하는 방법을 알아봅니다
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 5ac40e3c98da613b53c06908bcde927cc2e81b08
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684751"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Azure VM의 BitLocker 부팅 오류

 이 문서에서는 Microsoft Azure에서 Windows VM(가상 머신)을 시작할 때 발생할 수 있는 BitLocker 오류에 대해 설명합니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>증상

 Windows VM이 시작되지 않습니다. [부트 진단](../windows/boot-diagnostics.md) 창의 스크린샷을 확인하면 다음 오류 메시지 중 하나가 표시됩니다.

- BitLocker 키가 있는 USB 드라이버 꽂기

- 잠겼습니다. 다시 시작하려면 복구 키를 입력하세요(자판 배열: 미국). 잘못된 로그인 정보를 너무 많이 입력했으므로 개인 정보 보호를 위해 PC가 잠겼습니다. 복구 키를 검색하려면 다른 PC 또는 모바일 디바이스에서 https://windows.microsoft.com/recoverykeyfaq로 이동합니다. 이 키가 필요한 경우 키 ID는 XXXXXXX입니다. 또는 PC를 초기화할 수 있습니다.

- 이 드라이브의 잠금을 해제하려면 암호를 입력하세요. [ ] 입력하는 암호를 표시하려면 Insert 키를 누르세요.
- USB 디바이스에서 복구 키를 로드하려면 복구 키를 입력하세요.

## <a name="cause"></a>원인

이 문제는 VM이 암호화된 디스크의 암호를 해독하기 위한 BEK(BitLocker 복구 키) 파일을 찾을 수 없는 경우에 발생할 수 있습니다.

## <a name="solution"></a>해결 방법

이 문제를 해결하려면 VM을 중지하고 할당을 취소했다가 다시 시작합니다. 이 작업을 수행하면 VM이 강제로 Azure Key Vault에서 BEK 파일을 검색한 후 암호화된 디스크에 넣습니다. 

이 방법으로 문제가 해결되지 않으면 다음 단계에 따라 BEK 파일을 수동으로 복원합니다.

1. 영향을 받는 VM의 시스템 디스크의 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.
2. BitLocker에 의해 암호화된 [복구 VM에 시스템 디스크를 연결](troubleshoot-recovery-disks-portal-windows.md)합니다. 이 작업은 BitLocker로 암호화된 VM 에서만 사용할 수 있는 [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) 명령을 실행하는 데 필요합니다.

    관리 디스크를 연결할 때 “암호화 설정이 포함되어 있으므로 데이터 디스크로 사용할 수 없습니다.” 오류 메시지가 표시될 수 있습니다. 이 경우 다음 스크립트를 실행하여 디스크를 다시 연결합니다.

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     관리 디스크를 Blob 이미지에서 복원된 VM에 연결할 수 없습니다.

3. 디스크가 연결된 후에는 일부 Azure PowerShell 스크립트를 실행할 수 있도록 복구 VM에 대해 원격 데스크톱 연결을 설정합니다. 복구 VM에 [최신 버전의 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)을 설치했는지 확인합니다.

4. 관리자 권한 Azure PowerShell 세션(관리자 권한으로 실행)을 엽니다. 다음 명령을 실행하여 Azure 구독에 로그인합니다.

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. 다음 스크립트를 실행하여 BEK 파일의 이름을 확인합니다.

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    다음은 출력의 샘플입니다. 연결된 디스크의 BEK 파일 이름을 찾습니다. 이 경우 연결된 디스크의 드라이브 문자를 F로, BEK 파일을 EF7B2F5A-50 C 6-4637-9F13-7F599C12F85C.BEK로 가정합니다.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    두 개의 복제된 볼륨이 표시되는 경우 최신 타임스탬프가 있는 볼륨이 복구 VM에서 사용되는 현재 BEK 파일입니다.

    **콘텐츠 형식** 값이 **래핑된 BEK**이면 [KEK(키 암호화 키) 시나리오](#key-encryption-key-scenario)로 이동합니다.

    드라이브의 BEK 파일 이름을 확인했으므로 secret-file-name.BEK 파일을 만들어 드라이브의 잠금을 해제해야 합니다. 

6.  복구 디스크에 BEK 파일을 다운로드합니다. 다음 샘플은 BEK 파일을 C:\BEK 폴더에 저장합니다. 스크립트를 실행하기 전에 `C:\BEK\` 경로가 있는지 확인합니다.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  BEK 파일을 사용하여 연결된 디스크의 잠금을 해제하려면 다음 명령을 실행합니다.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    이 샘플에서 연결된 OS 디스크는 드라이브 F입니다. 올바른 드라이브 문자를 사용하는지 확인합니다. 

    - 디스크 BEK 키를 사용하여 성공적으로 잠금 해제된 경우 BItLocker 문제가 해결된 것으로 생각할 수 있습니다. 

    - BEK 키를 사용해도 디스크의 잠금이 해제되지 않으면 다음 명령으로 보호 일시 중단을 사용하여 일시적으로 BitLocker를 끌 수 있습니다.
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - dytem 디스크를 사용하여 VM을 다시 작성하려는 경우 드라이브를 완전히 암호 해독해야 합니다. 이렇게 하려면 다음 명령을 실행합니다.

        ```powershell
        manage-bde -off F:
        ```
8.  복구 VM에서 디스크를 분리했다가 영향을 받는 VM에 시스템 디스크로서 다시 연결합니다. 자세한 내용은 [OS 디스크를 복구 VM에 연결함으로써 Windows VM 문제 해결](troubleshoot-recovery-disks-windows.md)을 참조하세요.

### <a name="key-encryption-key-scenario"></a>키 암호화 키 시나리오

키 암호화 키 시나리오의 경우 다음 단계를 수행합니다.

1. **사용자|키 권한|암호화 작업 |키 래핑 해제**의 Key Vault 액세스 정책에서 로그인한 사용자 계정에 “래핑 해제된” 권한이 필요한지 확인합니다.
2. 다음 스크립트를 .PS1 파일에 저장합니다.

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. 매개 변수를 설정합니다. 스크립트는 KEK 암호를 처리하여 BEK 키를 만든 다음, 복구 VM의 로컬 폴더에 저장합니다.

4. 스크립트가 시작되면 다음 출력이 표시됩니다.

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    스크립트가 완료되면 다음과 같은 출력이 표시됩니다.

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. BEK 파일을 사용하여 연결된 디스크의 잠금을 해제하려면 다음 명령을 실행합니다.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    이 샘플에서 연결된 OS 디스크는 드라이브 F입니다. 올바른 드라이브 문자를 사용하는지 확인합니다. 

    - 디스크 BEK 키를 사용하여 성공적으로 잠금 해제된 경우 BItLocker 문제가 해결된 것으로 생각할 수 있습니다. 

    - BEK 키를 사용해도 디스크의 잠금이 해제되지 않으면 다음 명령으로 보호 일시 중단을 사용하여 일시적으로 BitLocker를 끌 수 있습니다.
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - dytem 디스크를 사용하여 VM을 다시 작성하려는 경우 드라이브를 완전히 암호 해독해야 합니다. 이렇게 하려면 다음 명령을 실행합니다.

        ```powershell
        manage-bde -off F:
        ```

6. 복구 VM에서 디스크를 분리했다가 영향을 받는 VM에 시스템 디스크로서 다시 연결합니다. 자세한 내용은 [OS 디스크를 복구 VM에 연결함으로써 Windows VM 문제 해결](troubleshoot-recovery-disks-windows.md)을 참조하세요.
