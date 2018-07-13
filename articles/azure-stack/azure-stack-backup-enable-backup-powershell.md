---
title: PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정 | Microsoft Docs
description: 오류가 발생 하는 경우 Azure Stack을 복원할 수 있도록 Windows PowerShell을 사용 하 여 인프라 Backup 서비스를 사용 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4fb40904e59e78e416d4598472a6adeb498e49f4
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968887"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Windows PowerShell을 사용 하 여 인프라 Backup 서비스 사용에 걸리는 정기적으로 백업 합니다.
 - 내부 id 서비스 및 루트 인증서
 - 사용자 계획, 제품, 구독
 - Keyvault 비밀
 - RBAC 역할 사용자 및 정책

PowerShell cmdlet, 백업을 사용 하도록 설정 하 고, 백업, 시작 하 고, 연산자 관리 끝점을 통해 백업 정보를 가져올에 액세스할 수 있습니다.

## <a name="prepare-powershell-environment"></a>PowerShell 환경 준비

PowerShell 환경 구성에 대 한 지침을 참조 하세요 [Azure Stack 용 PowerShell 설치 ](azure-stack-powershell-install.md)합니다. Azure Stack에 로그인 하려면 참조 [운영자 환경을 구성 하 고 Azure Stack에 로그인](azure-stack-powershell-configure-admin.md)합니다.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Backup을 사용 하려면 백업 공유를 자격 증명 및 암호화 키를 제공 합니다.

동일한 PowerShell 세션에서 사용자 환경에 대 한 변수를 추가 하 여 다음 PowerShell 스크립트를 편집 합니다. 인프라 백업 서비스에 백업 공유를 자격 증명 및 암호화 키를 제공 하는 업데이트 된 스크립트를 실행 합니다.

| 변수        | 설명   |
|---              |---                                        |
| $username       | 형식 합니다 **Username** 파일 읽기 및 쓰기에 대 한 액세스 권한이 있는 공유 드라이브 위치에 대 한 도메인 및 사용자를 사용 하 여 합니다. 예: `Contoso\backupshareuser` |
| $key            | 형식 합니다 **암호화 키** 각 백업을 암호화 하는 데 사용 합니다. |
| $password       | 형식 합니다 **암호** 사용자에 대 한 합니다. |
| $sharepath      | 경로를 입력 합니다 **백업 저장소 위치**합니다. 별도 장치에서 호스트 되는 파일 공유 경로 대 한 범용 명명 규칙 (UNC) 문자열을 사용 해야 합니다. UNC 문자열로 공유 파일 또는 장치와 같은 리소스의 위치를 지정합니다. 백업 데이터의 가용성을 보장 하기 장치 별도 위치에 있어야 합니다. |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>백업 설정 확인

동일한 PowerShell 세션에서 다음 명령을 실행 합니다.

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

다음 출력과 유사한 결과가 표시 됩니다.

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>다음 단계

 - 백업, 참조를 실행 하는 방법을 알아봅니다 [Azure Stack 백업](azure-stack-backup-back-up-azure-stack.md )합니다.  
 - 백업 실행 되었는지 확인 하는 방법을 알아봅니다, 참조 [관리 포털에서 완료할 확인 백업](azure-stack-backup-back-up-azure-stack.md )합니다.
