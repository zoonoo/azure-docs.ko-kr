---
title: ASDK를 사용 하는 Azure Stack 백업 유효성 검사 | Microsoft Docs
description: ASDK를 사용 하는 Azure Stack 통합된 시스템 백업 유효성을 검사 하는 방법입니다.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 027d4a9f93032bfdd0f4cda96df74c92b5679540
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251574"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Azure Stack 백업 유효성을 검사 하 여 ASDK 사용
Azure Stack을 배포 하 고 제품, 계획, 할당량 및 구독 같은 사용자 리소스를 프로 비전을 수행 해야 [Azure Stack 인프라 백업 사용](../azure-stack-backup-enable-backup-console.md)합니다. 예약 및 일반 인프라 백업을 실행 하는 인프라 관리 데이터가 손실 되지 치명적인 하드웨어 또는 서비스 오류 이면 해야 합니다.

> [!TIP]
> 하는 것이 좋습니다 있습니다 [주문형 백업을 실행할](../azure-stack-backup-back-up-azure-stack.md) 사용 가능한 최신 인프라 데이터의 복사본을 확인 하기 위해이 절차를 시작 하기 전에 합니다. 백업이 완료 된 후 백업 ID를 캡처할 수 있는지 확인 합니다. 이 ID는 클라우드 복구 중 해야 합니다. 

Azure Stack 인프라 백업에는 Azure Stack 다시 배포 하는 동안 복원할 수 있는 클라우드에 대 한 중요 한 데이터가 포함 됩니다. 이러한 백업은 프로덕션 클라우드 영향을 주지 않고 유효성을 검사 하 여 ASDK를 사용할 수 있습니다. 

ASDK 백업 유효성을 검사 하는 다음과 같은 시나리오에서 지원 됩니다.

|시나리오|목적|
|-----|-----|
|통합된 솔루션에서 인프라 백업 유효성을 검사 합니다.|백업에 대 한 데이터가 유효한 지 짧은 수명이 유효성 검사|
|종단 간 복구 워크플로를 알아봅니다.|전체 백업 유효성을 검사 하 고 환경을 복원 하려면 ASDK를 사용 합니다.|
|     |     |

다음 시나리오 **아닙니다** 는 ASDK 백업 유효성을 검사할 때 지원:

|시나리오|목적|
|-----|-----|
|ASDK 빌드가 빌드 백업 및 복원 합니다.|최신 버전으로는 ASDK의 이전 버전에서 백업 데이터를 복원 합니다.|
|     |     |


## <a name="cloud-recovery-deployment"></a>클라우드 복구 배포
ASDK의 클라우드 복구 배포를 수행 하 여 통합된 시스템 배포에서 인프라 백업 유효성을 검사할 수 있습니다. 이 유형의 배포는 ASDK 호스트 컴퓨터에 설치 된 후에 특정 서비스 데이터 백업에서 복원 됩니다.



### <a name="cloud-recovery-prerequisites"></a>클라우드 복구 필수 구성 요소
ASDK 클라우드 복구 배포를 시작 하기 전에 다음 정보가 있는지 확인 합니다.

|필수 요소|설명|
|-----|-----|
|백업 공유 경로입니다.|Azure Stack 인프라 정보를 복구 하는 데 사용할 수 있는 최신 Azure Stack 백업의 UNC 파일 공유 경로입니다. 클라우드 복구 배포 중이 로컬 공유 만들어질 수 있습니다.|
|백업 암호화 키입니다.|Azure Stack 관리 포털을 사용 하 여 실행할 인프라 백업을 예약 하는 암호화 키입니다.|
|복원할 백업 ID입니다.|클라우드 복구 하는 동안 복원할 백업을 식별 하는 영숫자 형식의 "xxxxxxxx xxxx-xxxx-자", 백업 ID를 합니다.|
|시간 서버 IP입니다.|132.163.97.2와 같은 유효한 시간 서버 IP를 Azure Stack 배포에 필요 합니다.|
|외부 인증서 암호입니다.|Azure Stack에서 사용 하는 외부 인증서에 대 한 암호입니다. CA 백업이이 암호를 사용 하 여 복원 해야 하는 외부 인증서를 포함 합니다.|
|     |     | 

## <a name="prepare-the-host-computer"></a>호스트 컴퓨터를 준비 합니다. 
일반 ASDK 배포와 같이 ASDK 호스트 시스템 환경 설치에 대 한 준비 되어야 합니다. 개발 키트 호스트 컴퓨터 준비 되었으므로 ASDK 배포를 시작 하려면 CloudBuilder.vhdx 가상 컴퓨터 하드 드라이브에서 부팅 됩니다.

ASDK 호스트 컴퓨터에서 백업 된 Azure Stack의 동일한 버전에 해당 하는 새 cloudbuilder.vhdx를 다운로드 하 고 지침을 따르세요 [ASDK 호스트 컴퓨터를 준비 하 고](asdk-prepare-host.md)입니다.

호스트 서버는 cloudbuilder.vhdx에서 다시 시작 되 면 파일 공유를 만들고 백업 데이터를 복사 해야 합니다. 파일 공유는; 설치 프로그램을 실행 하는 계정에 액세스할 수 있어야 합니다. 이러한 예제에서는 PowerShell 명령에 대 한 관리자: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

다음으로, 새로 만든된 공유에 최신 Azure Stack 백업 파일을 복사 합니다. 공유 내의 폴더 구조: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`합니다.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>클라우드 복구 모드에서 ASDK 배포
합니다 **InstallAzureStackPOC.ps1** 스크립트는 클라우드 복구를 시작 하는 데 사용 됩니다. 

> [!IMPORTANT]
> ASDK 설치 네트워킹에 대 한 하나의 네트워크 인터페이스 카드 (NIC)를 지원합니다. 여러 Nic를 만든 경우는 하나만 사용 하도록 설정 (및 다른 모든 사용 하지 않는) 배포 스크립트를 실행 하기 전에 있는지 확인 합니다.

사용자 환경에 대 한 다음 PowerShell 명령을 수정 하 고 클라우드 복구 모드에서 ASDK 배포 하도록를 실행 합니다.

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>인프라 데이터 백업에서 복원
성공적인 클라우드 recovery 배포 하는 다음을 사용 하 여 복원 완료 해야 합니다 **복원을 AzureStack** cmdlet. 

Azure Stack 운영자도 로그인 한 후 [Azure Stack PowerShell 설치](asdk-post-deploy.md#install-azure-stack-powershell) 그런 다음에 대 한 사용자의 백업 ID를 대체 하 고는 `Name` 매개 변수를 다음 명령을 실행 합니다.

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
ASDK 복구 클라우드에 백업 데이터의 확인을 시작 하려면이 cmdlet을 호출 후 60 분 동안 기다립니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack 등록](asdk-register.md)

