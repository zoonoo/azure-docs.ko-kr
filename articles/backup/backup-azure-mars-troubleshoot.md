---
title: Azure Backup 에이전트 문제 해결
description: Azure Backup 에이전트의 설치 및 등록 문제 해결
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 1c4c2ed6265bdb3c29986fb0b90c3d85d32aadca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434018"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) 에이전트 문제 해결

이 문서에서는 구성, 등록, 백업 하는 동안 표시 될 수 있습니다 하는 오류를 해결 하는 방법을 설명 하 고 복원 합니다.

## <a name="basic-troubleshooting"></a>기본 문제 해결

시작 하기 전에 Microsoft 문제 해결 Azure Recovery Services (MARS) 에이전트는 다음을 확인 하는 것이 좋습니다.

- [MARS 에이전트를 최신 상태로 유지](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)합니다.
- [MARS 에이전트와 Azure 간에 네트워크 연결이 있는지](https://aka.ms/AB-A4dp50)합니다.
- MARS (서비스 콘솔)에서 실행 중인지 확인 합니다. 해야 할 경우 다시 시작 하 고 작업을 다시 시도 합니다.
- [스크래치 폴더 위치에 5% ~ 10% 사용 가능한 볼륨 공간을 사용할 수 있는지 확인](https://aka.ms/AB-AA4dwtt)합니다.
- [다른 확인 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup을 사용 하 여 방해](https://aka.ms/AB-AA4dwtk)합니다.
- 예약 된 백업 실패 하지만 수동 백업 작동 하는 경우 참조 [백업 일정에 따라 ě](https://aka.ms/ScheduledBackupFailManualWorks)합니다.
- OS에 최신 업데이트를 확인 합니다.
- [백업에서 지원 되지 않는 드라이브 및 지원 되지 않는 특성을 사용 하 여 파일을 제외 하십시오](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)합니다.
- 보호 된 시스템에 있는 시계가 올바른 표준 시간대로 구성 되었는지 확인 합니다.
- [.NET Framework 4.5.2를 확인 하거나 나중에 서버에 설치 되어](https://www.microsoft.com/download/details.aspx?id=30653)입니다.
- 자격 증명 모음에 서버 등록 중 어느 것:
  - 에이전트 서버에서 제거 되 고 포털에서 삭제 되는 확인 합니다.
  - 서버를 등록 하려면 처음에 사용한 동일한 암호를 사용 합니다.
- 오프 라인 백업의 경우 백업을 시작 하기 전에 Azure PowerShell 3.7.0 원본 데이터베이스와 복사 컴퓨터에 설치 되어를 확인 합니다.
- Backup 에이전트를 Azure 가상 머신에서 실행 되는 경우 참조 [이 문서에서는](https://aka.ms/AB-AA4dwtr)합니다.

## <a name="invalid-vault-credentials-provided"></a>잘못된 자격 증명 모음이 제공되었습니다.

**오류 메시지**: 잘못된 자격 증명 모음이 제공되었습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. (ID: 34513)

| 원인 | 권장 작업 |
| ---     | ---    |
| **자격 증명 모음 자격 증명에는 유효 하지 않습니다.** <br/> <br/> 자격 증명 모음 자격 증명 파일이 손상 되었을 수 있습니다 또는 만료 된 것 같습니다. (예를 들어 수 다운로드 된 경우 48 시간 이상 시간 이전에 등록 합니다.)| Azure portal에서 Recovery Services 자격 증명 모음에서 새 자격 증명을 다운로드 합니다. (의 6 단계를 참조 합니다 [MARS 에이전트 다운로드](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) 섹션입니다.) 그런 다음 적절 하 게 다음이 단계를 수행 합니다. <ul><li> 이미 설치를 했으므로 MARS를 등록 하는 경우 Microsoft Azure Backup 에이전트 MMC 콘솔을 열고 선택한 **서버 등록** 에 **작업** 새 등록을 완료 하는 창 자격 증명입니다. <br/> <li> 새 설치에 실패 하는 경우 새 자격 증명을 사용 하 여 다시 설치 하십시오.</ul> **참고**: 여러 자격 증명 모음 자격 증명 파일을 다운로드 한 경우 다음 48 최신 파일만 유효 합니다. 새 자격 증명 모음 자격 증명 파일을 다운로드 하는 것이 좋습니다.
| **프록시 서버/방화벽이 등록 차단** <br/>또는 <br/>**인터넷 연결 되지 않은** <br/><br/> 컴퓨터 또는 프록시 서버는 인터넷 연결이 제한 된 필요한 Url에 대 한 액세스를 보장 하지 않습니다 하 고 등록이 실패 합니다.| 다음이 단계를 수행 합니다.<br/> <ul><li> 시스템이 인터넷에 연결 하도록 IT 팀과 함께 작동 합니다.<li> 프록시 서버에 없는 경우 에이전트를 등록할 때 프록시 옵션을 선택 하지 않으면 확인 합니다. [프록시 설정을 확인](#verifying-proxy-settings-for-windows)합니다.<li> 방화벽/프록시 서버에 이러한 Url을 확인 하려면 네트워킹 팀에서 작업 하 고 IP 주소에 액세스 합니다.<br/> <br> **URLs**<br> www.msftncsi.com <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP 주소**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>위의 문제 해결 단계를 완료 한 후 다시 등록 하십시오.
| **바이러스 백신 소프트웨어가 등록 차단** | 서버에 설치 된 바이러스 백신 소프트웨어를 사용 하는 경우 이러한 파일 및 폴더에 대 한 바이러스 백신 검사에 필요한 제외 규칙을 추가 합니다. <br/><ui> <li> CBengine.exe <li> CSC.exe<li> 스크래치 폴더입니다. 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services agent\scratch입니다. <li> C:\Program Files\Microsoft Azure Recovery Services Agent\Bin에 bin 폴더입니다.

### <a name="additional-recommendations"></a>추가 권장 사항
- C:/Windows/Temp로 이동하여 확장명이 .tmp인 파일이 60,000 또는 65,000개 넘게 있는지 확인합니다. 있는 경우 해당 파일을 삭제합니다.
- 컴퓨터의 날짜 및 시간 일치 현지 표준 시간대를 확인 합니다.
- 확인 [이러한 사이트](backup-configure-vault.md#verify-internet-access) Internet Explorer에서 신뢰할 수 있는 사이트에 추가 됩니다.

### <a name="verifying-proxy-settings-for-windows"></a>Windows에 대 한 프록시 설정을 확인 하는 중

1. PsExec을 다운로드 합니다 [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) 페이지입니다.
1. 실행 `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` 관리자 권한 명령 프롬프트에서.

   이 명령은 Internet Explorer 열립니다.
1. 로 이동 **도구가** > **인터넷 옵션** > **연결** > **LAN 설정**합니다.
1. 시스템 계정에 대 한 프록시 설정을 확인 합니다.
1. 프록시가 구성 되어 있지 프록시에 대 한 자세한 내용은 세부 정보를 제거 합니다.
1. 프록시가 구성 되어 프록시 세부 정보가 올바른지를 확인 합니다 **프록시 IP** 하 고 **포트** 세부 정보가 올바른지 합니다.
1. Internet Explorer를 닫습니다.

## <a name="unable-to-download-vault-credential-file"></a>자격 증명 모음 파일을 다운로드할 수 없음

| 오류   | 권장 작업 |
| ---     | ---    |
|자격 증명 모음 파일을 다운로드하지 못했습니다. (ID: 403) | <ul><li> 다른 브라우저를 사용 하 여 자격 증명 모음 자격 증명을 다운로드 하거나 다음이 단계를 수행 합니다. <ul><li> Internet Explorer를 시작 합니다. F12 키를 선택 합니다. </li><li> 로 이동 합니다 **네트워크** 탭 하 고 캐시 및 쿠키를 지웁니다. </li> <li> 페이지를 새로 고칩니다.<br></li></ul> <li> 사용 안 함/만료 된 구독 인지 확인 합니다.<br></li> <li> 모든 방화벽 규칙은 다운로드를 차단 하는 경우를 확인 합니다. <br></li> <li> Vault (자격 증명 모음 당 50 컴퓨터)에 대 한도 소진 하지 않은 것을 확인 합니다.<br></li>  <li> 사용자에 자격 증명 모음 자격 증명을 다운로드 하 고 자격 증명 모음에 서버를 등록 하는 데 필요한 Azure Backup 사용 권한을 확인 합니다. 참조 [Azure Backup 복구 지점 관리에 관한 Access Control](backup-rbac-rs-vault.md)합니다.</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다.

| 오류  | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure 복구 서비스 에이전트를 Microsoft Azure Backup에 연결할 수 없습니다. (ID: 100050) 네트워크 설정을 확인 하 고 인터넷에 연결할 수 있는지 확인 합니다.<li>(407) 프록시 인증이 필요 합니다. |프록시 연결을 차단 합니다. |  <ul><li>Internet Explorer로 이동 **도구가** > **인터넷 옵션** > **Security** > **인터넷**. 선택 **사용자 지정 수준** 까지 아래로 스크롤하여 합니다 **파일 다운로드** 섹션입니다. **사용**을 선택합니다.<p>추가할 수도 있습니다 [Url 및 IP 주소](backup-configure-vault.md#verify-internet-access) Internet Explorer에서 신뢰할 수 있는 사이트로 합니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다.<li> 컴퓨터에서 인터넷을 제한 한 경우 방화벽 설정을 컴퓨터 또는 프록시에이 허용 하는지 확인 [Url 및 IP 주소](backup-configure-vault.md#verify-internet-access)합니다. <li>서버에 설치 된 바이러스 백신 소프트웨어를 사용 하는 경우 이러한 파일을 바이러스 백신 검사에서 제외 합니다. <ul><li>CBEngine.exe(dpmra.exe 대신)<li>CSC.exe(.NET Framework와 관련됨) 서버에 설치 된 모든.NET Framework 버전에 대 한 CSC.exe 있습니다. 영향을 받는 서버의 모든 버전의.NET Framework에 대 한 CSC.exe 파일을 제외 합니다. <li>스크래치 폴더 또는 캐시 위치입니다. <br>스크래치 폴더 또는 캐시 경로 대 한 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services agent\scratch입니다.<li>C:\Program Files\Microsoft Azure Recovery Services Agent\Bin에 bin 폴더입니다.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>안전한 백업을 위한 암호화 키를 설정하지 못했습니다.

| 오류 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| <br />안전한 백업을 위한 암호화 키를 설정하지 못했습니다. 정품 인증을 완전히 성공 하지 못했지만 하지만 암호화 암호가 다음 파일에 저장 되었습니다. |<li>서버가 다른 자격 증명 모음에 이미 등록되어 있습니다.<li>구성하는 동안 암호가 손상되었습니다.| 자격 증명 모음에서 서버의 등록을 취소 하 고 새 암호를 다시 등록 합니다.

## <a name="the-activation-did-not-complete-successfully"></a>정품 인증이 완료되지 않았습니다.

| 오류  | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
|<br />정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다. 작업을 잠시 후에 다시 시도하세요. 문제가 계속되면 Microsoft 지원에 문의하세요.     | <li> 충분 한 공간이 없는 볼륨에 스크래치 폴더가 있는 합니다. <li> 스크래치 폴더가 올바르게 이동 되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.         | <li>로 업그레이드 합니다 [최신](https://aka.ms/azurebackup_agent) 의 MARS 에이전트입니다.<li>백업 데이터의 총 크기의 10% 5% 사이는 사용 가능한 공간이 볼륨에 스크래치 폴더 또는 캐시 위치를 이동 합니다. 캐시 위치를 올바르게 이동 하려면의 단계를 참조 [파일 및 폴더를 백업 하는 방법에 대 한 일반적인 질문](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)합니다.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 경로 대 한 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services agent\scratch입니다*합니다.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>암호화 암호가 올바르게 구성되지 않았습니다.

| 오류  | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
| <br />34506 오류입니다. 이 컴퓨터에 저장 된 암호화의 암호가 제대로 구성 되지 않았습니다.    | <li> 충분 한 공간이 없는 볼륨에 스크래치 폴더가 있는 합니다. <li> 스크래치 폴더가 올바르게 이동 되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.        | <li>[최신 버전](https://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>백업 데이터의 총 크기의 10% 5% 사이는 사용 가능한 공간이 볼륨에 스크래치 폴더 또는 캐시 위치를 이동 합니다. 캐시 위치를 올바르게 이동 하려면의 단계를 참조 [파일 및 폴더를 백업 하는 방법에 대 한 일반적인 질문](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)합니다.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 경로 대 한 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services agent\scratch입니다*합니다.         |


## <a name="backups-dont-run-according-to-schedule"></a>백업 일정에 따라 실행 안 함
예약 된 백업을 자동으로 트리거되지 없는 수동 백업이 올바르게 작동 하지만 경우에 다음 작업을 시도 합니다.

- Azure 파일 및 폴더 백업 일정을 사용 하 여 Windows Server 백업 일정을 충돌 하지 확인 합니다.

- 백업 상태를 online으로 설정 되었는지 확인 **사용**합니다. 상태를 확인 하려면 다음이 단계를 수행 합니다.

  1. 작업 스케줄러에서는 확장 **Microsoft** 선택한 **온라인 백업**합니다.
  1. 두 번 클릭 **Microsoft OnlineBackup** 로 이동 합니다 **트리거** 탭 합니다.
  1. 상태 설정 되어 있는지 확인 **사용**합니다. 그렇지 않은 경우 선택 **편집**를 선택 **Enabled**를 선택한 후 **확인**.

- 확인 작업 실행을 위해 선택한 사용자 계정 중 하나 **시스템** 하거나 **로컬 관리자 ' 그룹** 서버의 합니다. 로 사용자 계정을 확인 하려면 합니다 **일반** 탭을 확인 합니다 **보안** 옵션입니다.

- PowerShell 3.0 이상 서버에 설치 되었는지 확인 합니다. PowerShell 버전을 확인 하려면이 명령을 실행 하 고 있는지 확인 합니다 `Major` 버전 번호는 3 이상:

  `$PSVersionTable.PSVersion`

- 이 경로 부분을 확인 합니다 `PSMODULEPATH` 환경 변수:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 경우에 대 한 PowerShell 실행 정책을 `LocalMachine` 는 설정으로 제한 된 PowerShell cmdlet은 백업 작업을 트리거하는 실패할 수 있습니다. 확인을 실행 정책을 설정 하는 관리자 모드에서 이러한 명령을 실행할 `Unrestricted` 또는 `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- 없거나 손상 된 PowerShell 모듈 MSOnlineBackup 파일이 있는지 확인 하십시오. 없거나 손상 된 파일의 경우 이러한 단계를 수행 합니다.

  1. MARS 에이전트가 제대로 작동 하는지 있는 모든 컴퓨터에서 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules에서 MSOnlineBackup 폴더를 복사 합니다.
  1. 문제가 있는 컴퓨터의 동일한 폴더 위치 (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)에 복사한 파일을 붙여넣습니다.

     가 이미 MSOnlineBackup 폴더를 컴퓨터에 파일을 붙여 넣습니다 아니면 기존 파일이 대체 합니다.


> [!TIP]
> 변경 내용이 일관 되 게 적용 되도록 이전 단계를 수행한 후 서버를 다시 시작 합니다.


## <a name="troubleshoot-restore-problems"></a>복원 문제 해결

몇 분 후에도 Azure Backup이 복구 볼륨을 성공적으로 탑재하지 않을 수 있습니다. 및 프로세스 중 오류 메시지가 나타날 수 있습니다. 일반적으로 복구를 시작 하려면 다음이 단계를 수행 합니다.

1.  몇 분 동안 실행 된 경우에 탑재 프로세스를 취소 합니다.

2.  Backup 에이전트의 최신 버전이 있는지 확인 합니다. 버전을 확인 하는 **동작** MARS 콘솔 창의 **에 대 한 Microsoft Azure Recovery Services 에이전트**합니다. **버전** 번호가 [이 문서](https://go.microsoft.com/fwlink/?linkid=229525)에 언급된 버전보다 높거나 같은지 확인합니다. 이 링크를 선택 [최신 버전 다운로드](https://go.microsoft.com/fwLink/?LinkID=288905)합니다.

3.  로 이동 **장치 관리자** > **저장소 컨트롤러** 찾아서 **Microsoft iSCSI 초기자**합니다. 이 찾을 경우 7 단계로 직접 이동 합니다.

4.  Microsoft iSCSI 초기자 서비스를 찾을 수 없는 경우 아래에 있는 항목을 찾을 하려고 **장치 관리자** > **저장소 컨트롤러** 라는 **알 수 없는 장치** 하드웨어 ID를 사용 하 여 **ROOT\ISCSIPRT**합니다.

5.  마우스 오른쪽 단추로 클릭 **알 수 없는 장치** 선택한 **업데이트 드라이버 소프트웨어**합니다.

6.  **업데이트된 드라이버 소프트웨어를 자동으로 검색**하는 옵션을 선택하여 드라이버를 업데이트합니다. 이 업데이트에서 변경 해야 **알 수 없는 장치** 하 **Microsoft iSCSI 초기자**:

    ![스토리지 컨트롤러가 강조 표시된 Azure Backup 디바이스 관리자의 스크린샷](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  로 이동 **작업 관리자** > **서비스 (로컬)**  > **Microsoft iSCSI 초기자 서비스**:

    ![서비스(로컬)가 강조 표시된 Azure Backup 작업 관리자의 스크린샷](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Microsoft iSCSI 초기자 서비스를 다시 시작합니다. 이렇게 하려면 서비스를 마우스 오른쪽 단추로 클릭 하 고 선택 **중지**합니다. 그런 다음 단추로 다시 클릭 하 고 선택 **시작**합니다.

9.  [인스턴트 복원](backup-instant-restore-capability.md)을 사용하여 복구를 다시 시도합니다.

복구가 계속 실패 하는 경우에 서버 또는 클라이언트 다시 시작 합니다. 를 다시 시작 하거나 복구가 계속 실패 하면 서버를 다시 시작한 후에 시도 하지 않으려면 [다른 컴퓨터에서 복구](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계
* 대해 자세히 알아보세요 [Azure Backup 에이전트를 사용 하 여 Windows Server 백업 방법](tutorial-backup-windows-server-to-azure.md)합니다.
* 백업을 복원 해야 할 경우 참조 [Windows 컴퓨터에 파일을 복원](backup-azure-restore-windows-server.md)합니다.
