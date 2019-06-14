---
title: Azure Backup Agent 문제 해결
description: Azure Backup 에이전트의 설치 및 등록 문제 해결
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 2c2ed46ed6e4a5d6663387777d3425d18b50500e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060208"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트 문제 해결

다음은 구성, 등록, 백업 및 복원하는 동안 발생할 수 있는 오류 해결 방법입니다.

## <a name="basic-troubleshooting"></a>기본 문제 해결

수행 하는 것이 좋습니다는 아래 유효성 검사를 시작 하기 전에 Microsoft Azure Recovery Services (MARS) 에이전트 문제 해결:

- [Microsoft Azure Recovery Services (MARS) 에이전트를 최신 상태로 유지](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS 에이전트와 Azure 간에 네트워크 연결이 있는지 확인](https://aka.ms/AB-A4dp50)
- Microsoft Azure Recovery Services가 실행 중인지 확인(서비스 콘솔에서) 필요한 경우 다시 시작하고 작업을 다시 시도
- [스크래치 폴더 위치에서 5~10% 볼륨 여유 공간을 사용할 수 있는지 확인](https://aka.ms/AB-AA4dwtt)
- [다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup을 방해하는지 확인](https://aka.ms/AB-AA4dwtk)
- [예약 백업은 실패하지만 수동 백업은 작동](https://aka.ms/ScheduledBackupFailManualWorks)
- OS에 최신 업데이트가 설치되었는지 확인
- [백업에서 지원 되지 않는 드라이브 및 지원 되지 않는 특성을 사용 하 여 파일을 제외 하십시오](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 보호되는 시스템의 **시스템 클록**이 올바른 표준 시간대로 구성되었는지 확인 <br>
- [서버에 적어도 .Net Framework 버전 4.5.2 이상이 설치되었는지 확인](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 자격 증명 모음에 **서버를 다시 등록**하려는 경우: <br>
  - 에이전트가 서버에서 제거되고 포털에서 삭제되었는지 확인 <br>
  - 처음 서버 등록에 사용한 것과 동일한 암호 사용 <br>
- 오프 라인 백업의 경우, 오프 라인 백업 작업을 시작 하기 전에 Azure PowerShell 버전 3.7.0 소스와 복사 컴퓨터에 설치 되어 있는지 확인
- [Backup 에이전트가 Azure 가상 머신에서 실행 되 고 때 고려 사항](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>잘못된 자격 증명 모음이 제공되었습니다.

**오류 메시지**: 잘못된 자격 증명 모음이 제공되었습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. (ID: 34513)

| 원인 | 권장 작업 |
| ---     | ---    |
| **자격 증명 모음 자격 증명이 올바르지 않습니다.** <br/> <br/> 자격 증명 모음 자격 증명 파일이 손상 되었을 수 있습니다 또는 만료 되었을 (예: 다운로드 된 경우 등록 된 시간 전에 48 시간 이상)| Azure portal에서 Recovery Services 자격 증명 모음에서 새 자격 증명을 다운로드 (참조 *6 단계* 아래에서 [ **MARS 에이전트를 다운로드** ](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) 섹션)을 수행 하 고는 아래: <ul><li> 설치 된 이미 있고 Microsoft Azure Backup 에이전트를 등록 하는 경우 다음 Microsoft Azure Backup 에이전트 MMC 콘솔을 열고 선택 **서버 등록** 새로 다운로드 한 등록을 완료 하려면 작업 창에서 자격 증명 <br/> <li> 새 설치에 실패 한 경우 다음 다시 설치 하십시오 새 자격 증명을 사용 하 여</ul> **참고**: 여러 자격 증명 모음 자격 증명 파일을 이전에 다운로드 하는 경우만 최신 다운로드 한 파일이 유효한 48 시간 이내입니다. 따라서 다음 새로 새 자격 증명 모음 자격 증명 파일을 다운로드 하는 것이 좋습니다.
| **프록시 서버/방화벽 차단 <br/>또는 <br/>아니요 인터넷에 연결** <br/><br/> 컴퓨터나 프록시 서버는 인터넷 액세스를 제한 한 경우 필요한 Url을 나열 하지 않고 등록이 실패 합니다.| 이 문제를 해결 하려면 다음을 수행 합니다 아래:<br/> <ul><li> 시스템이 인터넷에 연결 하도록 IT 팀에서 작업<li> 프록시 서버를 등록 에이전트 프록시 옵션을 선택한 경우 확인 없으면 프록시 설정 단계 나열 확인 [여기](#verifying-proxy-settings-for-windows)<li> 방화벽/프록시 서버가 있는 경우 다음 Url 및 IP 아래 해결 확인 네트워킹 팀과 작업 권한이<br/> <br> **URLs**<br> - *www.msftncsi.com* <br>-  *.Microsoft.com* <br> -  *.WindowsAzure.com* <br>-  *.microsoftonline.com* <br>-  *.windows.net* <br>**IP 주소**<br> - *20.190.128.0/18* <br> - *40.126.0.0/18* <br/></ul></ul>위의 문제 해결 단계를 완료 한 후 다시 등록
| **바이러스 백신 소프트웨어 차단** | 서버에 설치 된 바이러스 백신 소프트웨어를 사용 하는 경우 다음 바이러스 백신 검사에서 다음 파일에 대 한 필요한 제외 규칙을 추가 합니다. <br/><ui> <li> *CBengine.exe* <li> *CSC.exe*<li> 스크래치 폴더가 기본 위치는 *C:\Program Files\Microsoft Azure Recovery Services agent\scratch입니다* <li> Bin 폴더에 *C:\Program Files\Microsoft Azure Recovery Services Agent\Bin*

### <a name="additional-recommendations"></a>추가 권장 사항
- 로 이동 *c: / Windows/Temp* 여 확장명이.tmp 인 파일을 60,000 또는 65,000 개 넘게 있는지 여부를 확인 합니다. 가 있는 경우 이러한 파일을 삭제 합니다.
- 현지 표준 시간대를 사용 하 여 일치 하는 컴퓨터의 날짜 및 시간 확인
- 확인 합니다 [다음](backup-configure-vault.md#verify-internet-access) 사이트가 IE 신뢰할 수 있는 사이트로 추가

### <a name="verifying-proxy-settings-for-windows"></a>Windows에 대 한 프록시 설정을 확인 하는 중

- 다운로드 **psexec** 에서 [여기](https://docs.microsoft.com/sysinternals/downloads/psexec)
- 이 실행 `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` 명령 프롬프트에서:
- 이 시작 됩니다 *Internet Explorer* 창
- 로 이동 *도구가* -> *인터넷 옵션* -> *연결* -> *LAN 설정*
- 에 대 한 프록시 설정을 확인 하십시오 *시스템* 계정
- 프록시가 구성 되어 있지 경우 프록시에 대 한 자세한 내용은 다음 세부 정보 제거
-   프록시가 구성 되어 잘못 된 프록시 세부 정보를 확인 한 다음 *프록시 IP* 하 고 *포트* 세부 정보는 정확 하 게
- 닫기 *Internet Explorer*

## <a name="unable-to-download-vault-credential-file"></a>자격 증명 모음 파일을 다운로드할 수 없음

| 오류 세부 정보 | 권장 작업 |
| ---     | ---    |
|자격 증명 모음 파일을 다운로드하지 못했습니다. (ID: 403) | <ul><li> 다른 브라우저를 사용하여 자격 증명 모음을 다운로드하거나 아래 단계를 수행합니다. <ul><li> IE를 시작하고 F12 키를 누릅니다. </li><li> **네트워크** 탭으로 이동하여 IE 캐시와 쿠키를 지웁니다. </li> <li> 페이지를 새로 고칩니다.<br>또는</li></ul> <li> 구독이 사용 안 함/만료된 상태인지 확인합니다.<br>또는</li> <li> 자격 증명 모음 파일 다운로드를 차단하는 방화벽 규칙이 있는지 확인합니다. <br>또는</li> <li> 자격 증명 모음의 한도(자격 증명 모음당 50개 머신)를 모두 사용하지 않았는지 확인합니다.<br>또는</li>  <li> 사용자에 게 필요한 Azure Backup 자격 증명 모음 자격 증명을 다운로드 하 고 자격 증명 모음에 서버 등록 내용은 권한이 확인 [문서](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| **오류** <br /><ol><li>*Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다. (ID: 100050) 네트워크 설정을 확인하고 인터넷에 연결할 수 있는지 확인하세요.*<li>*(407) 프록시 인증 필요* |프록시가 연결을 차단 중 |  <ul><li>**IE** > **설정** > **인터넷 옵션** > **보안** > **인터넷**을 시작합니다. 그런 다음 **사용자 지정 수준**을 선택하고 파일 다운로드 섹션이 표시될 때까지 스크롤합니다. **사용**을 선택합니다.<li>또한 이러한 사이트를 IE [신뢰할 수 있는 사이트](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins)에 추가해야 합니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다.<li> 컴퓨터에서 인터넷을 제한 한 경우 방화벽 설정을 컴퓨터 또는 프록시에이 허용 하는지 확인 [Url](backup-configure-vault.md#verify-internet-access) 하 고 [IP 주소](backup-configure-vault.md#verify-internet-access)합니다. <li>서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 다음 파일을 바이러스 백신 검사에서 제외합니다. <ul><li>CBEngine.exe(dpmra.exe 대신)<li>CSC.exe(.NET Framework와 관련됨) 서버에 설치된 모든 .NET 버전에 대한 CSC.exe 파일이 있습니다. 영향을 받는 서버의 모든 .NET Framework 버전에 연결된 CSC.exe 파일을 제외시킵니다. <li>폴더를 스크래치하거나 위치를 캐시합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.<li>bin 폴더는 C:\Program Files\Microsoft Azure Recovery Services Agent\Bin입니다.



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>안전한 백업을 위한 암호화 키를 설정하지 못했습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| **오류** <br />*안전한 백업을 위한 암호화 키를 설정하지 못했습니다. 활성화가 완전히 성공한 것은 아니지만 암호화 암호가 다음 파일에 저장되었습니다*. |<li>서버가 다른 자격 증명 모음에 이미 등록되어 있습니다.<li>구성하는 동안 암호가 손상되었습니다.| 자격 증명 모음에서 서버의 등록을 취소하고 새 암호로 다시 등록합니다.

## <a name="the-activation-did-not-complete-successfully"></a>정품 인증이 완료되지 않았습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
|**오류** <br />*정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다. 작업을 잠시 후에 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.*     | <li> 스크래치 폴더가 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 다른 위치로 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.         | <li>[최신 버전](https://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 백업 데이터 전체 크기의 5~10%에 달하는 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [Azure Backup 에이전트에 대한 질문](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)의 단계를 참조하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>암호화 암호가 올바르게 구성되지 않았습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
|**오류** <br />*오류 34506. 이 컴퓨터에 저장된 암호화의 암호가 제대로 구성되어 있지 않습니다*.    | <li> 스크래치 폴더가 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 다른 위치로 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.        | <li>[최신 버전](https://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 백업 데이터 전체 크기의 5~10%에 달하는 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [Azure Backup 에이전트에 대한 질문](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)의 단계를 참조하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>백업이 일정에 따라 실행되지 않음
수동 백업은 문제 없이 실행되지만 예약 백업이 자동으로 트리거되지 않으면 다음 작업을 시도해보세요.

- Azure 파일 및 폴더 백업 일정을 사용 하 여 Windows Server 백업 일정 충돌 하지 않는 것을 확인 합니다.

- 온라인 백업 상태 설정 되어 있는지 확인 **사용**합니다. 수행 하는 상태를 확인 하는 아래:

  - 열기 **작업 스케줄러** 확장 **Microsoft**를 선택 하 고 **온라인 백업**합니다.
  - **Microsoft OnlineBackup**을 두 번 클릭하고 **트리거** 탭으로 이동합니다.
  - 상태로 설정 되어 있으면 확인 **사용**합니다. 그렇지 않은 경우 선택한 **편집할** > **Enabled** 확인란을 클릭 **확인**합니다.

- 확인 작업 실행을 위해 선택한 사용자 계정 중 하나 **시스템** 하거나 **로컬 관리자 ' 그룹** 서버의 합니다. 로 사용자 계정을 확인 하려면 합니다 **일반** 탭을 확인 합니다 **보안** 옵션입니다.

- PowerShell 3.0 이상 서버에 설치 되었는지 확인 합니다. PowerShell 버전을 확인하려면 다음 명령을 실행하고 *주* 버전 번호가 3 이상인지 확인합니다.

  `$PSVersionTable.PSVersion`

- 일부인 다음 경로 확인 합니다 *PSMODULEPATH* 환경 변수

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- *LocalMachine*에 대한 PowerShell 실행 정책이 제한으로 설정된 경우 백업 작업을 트리거하는 PowerShell cmdlet이 실패할 수 있습니다. 확인 및를 실행 정책을 설정 하려면 관리자 모드에서 다음 명령을 실행 *Unrestricted* 또는 *RemoteSigned*

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- 없는지 누락 되거나 손상 **PowerShell** 모듈 **MSonlineBackup**합니다. 없거나 손상 된 파일이 있는 경우 해결 하려면이 문제를 수행 합니다 아래:

  - MARS 에이전트는 제대로 작동 하는 모든 컴퓨터에서 MSOnlineBackup 폴더를 복사 *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* 경로입니다.
  - 동일한 경로에 문제가 있는 컴퓨터에 붙여 넣어 *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* 합니다.
  - 하는 경우 **MSOnlineBackup** 폴더는 이미 저장 되어 있는 컴퓨터에서 붙여넣기 또는 내부에 있는 콘텐츠 파일을 교체 합니다.


> [!TIP]
> 변경 내용이 일관적으로 적용되도록 위의 단계를 수행한 후 서버를 다시 부팅합니다.


## <a name="troubleshoot-restore-issues"></a>복원 문제 해결

몇 분 후에도 Azure Backup이 복구 볼륨을 성공적으로 탑재하지 않을 수 있습니다. 프로세스 중에 오류 메시지가 나타날 수도 있습니다. 정상적으로 복구를 시작하려면 다음 단계를 따릅니다.

1.  탑재가 몇 분째 실행 중인 경우 진행 중인 탑재 프로세스를 취소합니다.

2.  Backup Agent가 최신 버전인지 확인합니다. 버전을 확인하려면 MARS 콘솔의 **작업** 창에서 **Microsoft Azure Recovery Services 에이전트 정보**를 선택합니다. **버전** 번호가 [이 문서](https://go.microsoft.com/fwlink/?linkid=229525)에 언급된 버전보다 높거나 같은지 확인합니다. 최신 버전은 [여기](https://go.microsoft.com/fwLink/?LinkID=288905)서 다운로드할 수 있습니다.

3.  **디바이스 관리자** > **스토리지 컨트롤러**로 이동하여 **Microsoft iSCSI 초기자**를 찾습니다. 찾을 수 있는 경우 바로 7단계로 이동합니다.

4.  Microsoft iSCSI 초기자 서비스를 찾을 수 없는 경우 하드웨어 ID **ROOT\ISCSIPRT**를 포함한 **알 수 없는 디바이스**라는 항목을 **디바이스 관리자** > **스토리지 컨트롤러**에서 찾을 수 있는지 확인합니다.

5.  **알 수 없는 디바이스**를 마우스 오른쪽 단추로 클릭하고 **드라이버 소프트웨어 업데이트**를 선택합니다.

6.  **업데이트된 드라이버 소프트웨어를 자동으로 검색**하는 옵션을 선택하여 드라이버를 업데이트합니다. 업데이트가 완료되면 다음과 같이 **알 수 없는 디바이스**가 **Microsoft iSCSI 초기자**로 변경됩니다.

    ![스토리지 컨트롤러가 강조 표시된 Azure Backup 디바이스 관리자의 스크린샷](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  **작업 관리자** > **서비스(로컬)**  > **Microsoft iSCSI 초기자 서비스**로 이동합니다.

    ![서비스(로컬)가 강조 표시된 Azure Backup 작업 관리자의 스크린샷](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Microsoft iSCSI 초기자 서비스를 다시 시작합니다. 이렇게 하려면 서비스를 마우스 오른쪽 단추로 클릭하고, **중지**를 선택하고, 다시 마우스 오른쪽 단추로 클릭하고, **시작**을 선택합니다.

9.  [**즉시 복원**](backup-instant-restore-capability.md)을 사용하여 복구를 다시 시도합니다.

복구가 계속 실패하면 서버 또는 클라이언트를 다시 부팅합니다. 다시 부팅하지 않으려는 경우 또는 서버를 다시 부팅한 후에도 복구가 계속 실패하는 경우 다른 머신에서 복구를 시도해 봅니다. [이 문서](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)의 단계를 따릅니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Backup 에이전트로 Windows Server를 백업하는 방법](tutorial-backup-windows-server-to-azure.md)에서 자세한 내용을 참조하세요.
* 백업을 복원해야 하는 경우 이 문서를 참조하여 [Windows 컴퓨터에 파일을 복원](backup-azure-restore-windows-server.md)할 수 있습니다.
