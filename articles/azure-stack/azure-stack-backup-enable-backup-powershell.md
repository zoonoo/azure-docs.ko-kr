---
title: PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정 | Microsoft Docs
description: 오류가 발생 하는 경우 Azure Stack을 복원할 수 있도록 Windows PowerShell을 사용 하 여 인프라 Backup 서비스를 사용 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 38ab7b80e2f03176c3bedfd98a2d0e20fc02592b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865895"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Windows PowerShell을 사용 하 여 인프라 Backup 서비스 사용에 걸리는 정기적으로 백업 합니다.
 - 내부 id 서비스 및 루트 인증서
 - 사용자 계획, 제품, 구독
 - 계산, 저장소 및 네트워크 사용자 할당량
 - 사용자 키 자격 증명 모음 암호
 - RBAC 역할 사용자 및 정책
 - 사용자 저장소 계정

PowerShell cmdlet, 백업을 사용 하도록 설정 하 고, 백업, 시작 하 고, 연산자 관리 끝점을 통해 백업 정보를 가져올에 액세스할 수 있습니다.

## <a name="prepare-powershell-environment"></a>PowerShell 환경 준비

PowerShell 환경 구성에 대 한 지침을 참조 하세요 [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)합니다. Azure Stack에 로그인 하려면 참조 [운영자 환경을 구성 하 고 Azure Stack에 로그인](azure-stack-powershell-configure-admin.md)합니다.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Backup을 사용 하려면 백업 공유를 자격 증명 및 암호화 키를 제공 합니다.

동일한 PowerShell 세션에서 사용자 환경에 대 한 변수를 추가 하 여 다음 PowerShell 스크립트를 편집 합니다. 인프라 백업 서비스에 백업 공유를 자격 증명 및 암호화 키를 제공 하는 업데이트 된 스크립트를 실행 합니다.

| 변수        | 설명   |
|---              |---                                        |
| $username       | 형식 합니다 **Username** 파일 읽기 및 쓰기에 대 한 액세스 권한이 있는 공유 드라이브 위치에 대 한 도메인 및 사용자를 사용 하 여 합니다. 예: `Contoso\backupshareuser` |
| $password       | 형식 합니다 **암호** 사용자에 대 한 합니다. |
| $sharepath      | 경로를 입력 합니다 **백업 저장소 위치**합니다. 별도 장치에서 호스트 되는 파일 공유 경로 대 한 범용 명명 규칙 (UNC) 문자열을 사용 해야 합니다. UNC 문자열로 공유 파일 또는 장치와 같은 리소스의 위치를 지정합니다. 백업 데이터의 가용성을 보장 하기 장치 별도 위치에 있어야 합니다. |
| $frequencyInHours | 빈도 시간 빈도 결정 백업은 만들어집니다. 기본값은 12입니다. Scheduler는 최대 12 및 4 개를 지원합니다.|
| $retentionPeriodInDays | 일의 보존 기간 외부 위치에 유지 됩니다 백업 기간 (일)을 결정 합니다. 기본값은 7입니다. Scheduler는 최대 14 및 2 개를 지원합니다. 백업 보존 기간 보다 오래 된 가져올 외부 위치에서 자동으로 삭제 됩니다.|
| $encryptioncertpath | 암호화 인증서 경로 파일 경로를 지정 합니다. 데이터 암호화에 사용 된 공개 키를 사용 하 여 CER 파일입니다. |
|     |     |

```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
   
##  <a name="confirm-backup-settings"></a>백업 설정 확인

동일한 PowerShell 세션에서 다음 명령을 실행 합니다.

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

결과 다음 예제 출력과 같이 표시 됩니다.

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>백업 설정 업데이트
동일한 PowerShell 세션에서 보존 기간 및 백업 위한 빈도 대 한 기본값을 업데이트할 수 있습니다. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

결과 다음 예제 출력과 같이 표시 됩니다.

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

###<a name="azure-stack-powershell"></a>Azure Stack PowerShell 
인프라 백업을 구성 하는 PowerShell cmdlet 집합 AzsBackupConfiguration 됩니다. 이전 릴리스에서 cmdlet 집합 AzsBackupShare 했습니다. 이 cmdlet을 사용 하려면 인증서를 제공 합니다. 암호화 키를 사용 하 여 인프라 백업이 구성 된 경우에 암호화 키를 업데이트 하거나 속성을 볼 수 없습니다. 관리자 PowerShell의 버전 1.6을 사용 해야 합니다. 

인프라 백업 1901를 업데이트 하기 전에 구성 된 경우에 설정 하 고 암호화 키를 보려면 관리자 PowerShell의 버전 1.6을 사용할 수 있습니다. 버전 1.6은 인증서 파일에 암호화 키에서 업데이트할 수 없습니다.
가리킵니다 [Azure Stack PowerShell 설치](azure-stack-powershell-install.md) 모듈의 올바른 버전을 설치 하는 방법은 합니다. 


## <a name="next-steps"></a>다음 단계

백업, 참조를 실행 하는 방법을 알아봅니다 [Azure Stack 백업](azure-stack-backup-back-up-azure-stack.md)

백업 실행 되었는지 확인 하는 방법을 알아봅니다, 참조 [관리 포털에서 완료할 확인 백업](azure-stack-backup-back-up-azure-stack.md)
