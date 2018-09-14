---
title: Azure Backup Agent 문제 해결
description: Azure Backup 에이전트의 설치 및 등록 문제 해결
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: 2c8978cfba8fc56d4dbc565cb3a91c75d9d54679
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700198"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>MARS(Microsoft Azure Recovery Services) 에이전트 문제 해결
## <a name="recommended-steps"></a>권장되는 단계
MARS 에이전트를 사용하여 구성, 등록, 백업 및 복원 중 오류를 해결하려면 이 문서를 참조하세요.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>잘못된 자격 증명 모음이 제공되었습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다.
| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| **오류** </br> *잘못된 보관 자격 증명이 제공되었습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. (ID: 34513)* | <ul><li> 자격 증명 모음 자격 증명이 유효하지 않습니다. (즉 등록되기 전 다운로드된지 48시간 이상이 지났습니다.)<li>MARS 에이전트는 Windows Temp 디렉터리에 파일을 다운로드할 수 없습니다. <li>자격 증명 모음 자격 증명이 네트워크 위치에 있습니다. <li>TLS 1.0을 사용하지 않도록 설정되어 있습니다.<li> 구성된 프록시 서버가 연결을 차단하고 있습니다. <br> |  <ul><li>새 자격 증명 모음 자격 증명을 다운로드합니다.<li>**인터넷 옵션** > **보안** > **인터넷**으로 이동합니다. 다음으로 **사용자 지정 수준**을 선택하고 파일 다운로드 섹션이 표시될 때까지 스크롤합니다. 그런 다음 **사용**을 선택합니다.<li>또한 사이트를 [신뢰할 수 있는 사이트](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)에 추가해야 합니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다. <li> 컴퓨터와 날짜 및 시간을 일치시킵니다.<li>C:/Windows/Temp로 이동하여 확장명이 .tmp인 파일이 60,000 또는 65,000개 넘게 있는지 확인합니다. 있는 경우 해당 파일을 삭제합니다.<li>서버에서 이 SDP 패키지를 실행하여 확인할 수 있습니다. 파일 다운로드가 허용되지 않는다는 오류가 발생하면 C:/Windows/Temp 디렉터리에 많은 수의 파일이 있을 가능성이 있습니다.<li>.NET Framework 4.6.2가 설치되어 있는지 확인합니다. <li>PCI 준수로 인해 TLS 1.0을 사용하지 않도록 설정한 경우 이 [문제 해결 페이지](https://support.microsoft.com/help/4022913)를 참조하세요. <li>서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 다음 파일을 바이러스 백신 검사에서 제외합니다. <ul><li>CBengine.exe<li>.NET Framework와 관련되어 있는 CSC.exe 서버에 설치된 모든 .NET 버전에 대한 CSC.exe 파일이 있습니다. 영향을 받는 서버의 모든 .NET Framework 버전에 연결된 CSC.exe 파일을 제외시킵니다. <li>폴더를 스크래치하거나 위치를 캐시합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| **오류** </br><ol><li>*Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다. (ID: 100050) 네트워크 설정을 확인하고 인터넷에 연결할 수 있는지 확인하세요.*<li>*(407) 프록시 인증 필요* |프록시가 연결을 차단 중 |  <ul><li>**인터넷 옵션** > **보안** > **인터넷**으로 이동합니다. 그런 다음 **사용자 지정 수준**을 선택하고 파일 다운로드 섹션이 표시될 때까지 스크롤합니다. **사용**을 선택합니다.<li>또한 사이트를 [신뢰할 수 있는 사이트](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)에 추가해야 합니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다. <li>서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 다음 파일을 바이러스 백신 검사에서 제외합니다. <ul><li>CBEngine.exe(dpmra.exe 대신)<li>CSC.exe(.NET Framework와 관련됨) 서버에 설치된 모든 .NET 버전에 대한 CSC.exe 파일이 있습니다. 영향을 받는 서버의 모든 .NET Framework 버전에 연결된 CSC.exe 파일을 제외시킵니다. <li>폴더를 스크래치하거나 위치를 캐시합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>안전한 백업을 위한 암호화 키를 설정하지 못했습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |      
| **오류** </br>*보안 백업에 대한 암호화 키를 설정하지 못했습니다. 활성화가 완전하게 성공되지 않았지만 암호화 암호가 다음 파일에 저장되었습니다*. |<li>서버가 다른 자격 증명 모음에 이미 등록되어 있습니다.<li>구성하는 동안 암호가 손상되었습니다.| 자격 증명 모음에서 서버의 등록을 취소하고 새 암호로 다시 등록합니다.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
|**오류** </br><ol>*정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다. 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.*     | <li> 스크래치 폴더가 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 다른 위치로 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.         | <li>[최신 버전](http://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 백업 데이터 전체 크기의 5~10%에 달하는 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [Azure Backup 에이전트에 대한 질문](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)의 단계를 참조하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>34506 오류입니다. 이 컴퓨터에 저장된 암호화 암호가 제대로 구성되지 않았습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
|**오류** </br><ol>*오류 34506. 이 컴퓨터에 저장된 암호화의 암호가 제대로 구성되어 있지 않습니다*.    | <li> 스크래치 폴더가 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 다른 위치로 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.        | <li>[최신 버전](http://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 백업 데이터 전체 크기의 5~10%에 달하는 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [Azure Backup 에이전트에 대한 질문](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)의 단계를 참조하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.         |

## <a name="backups-do-not-run-as-per-schedule"></a>백업이 일정에 따라 실행되지 않습니다.
수동 백업은 문제 없이 실행되나 예약 백업은 자동으로 트리거되지 않으면 다음 단계를 수행합니다. 
 
<li>PowerShell 3.0 이상이 서버에 설치되었는지 확인합니다. PowerShell 버전을 확인하려면 다음 명령을 실행하고 *주요* 버전 번호가 3 이상인지 확인합니다.

`$PSVersionTable.PSVersion`
<li>다음 경로가 *PSMODULEPATH* 환경 변수에 포함되었는지 확인합니다.

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>*LocalMachine*에 대한 powershell 실행이 제한으로 설정된 경우 백업 작업을 트리거하는 powershell cmdlet이 실패할 수 있습니다. 평가 모드에서 다음 명령을 실행하고 실행 정책을 *Unrestricted* 또는 *RemoteSigned*로 설정합니다.

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>제어판 -> 관리 도구 -> 작업 스케줄러로 이동하고 Microsoft를 확장한 다음 온라인 백업을 선택합니다.
<li>'Microsoft OnlineBackup' 작업을 두 번 클릭하고 '트리거' 탭으로 이동합니다.
<li>작업의 '상태'가 '사용'으로 설정되어 있는지 확인합니다. 그렇지 않은 경우 '편집'을 클릭하고 '사용' 확인란을 선택합니다.
<li>*일반* 탭의 *보안 옵션* 섹션으로 이동합니다. 
<li>작업 실행을 위해 선택한 사용자 계정이 *시스템* 또는 서버의 로컬 관리자 그룹 중 하나인지 확인합니다.

> [!TIP]
> 변경 내용이 일관되게 적용되었는지 확인하려면 위의 단계를 수행한 후 서버를 다시 부팅하는 것이 좋습니다.


## <a name="troubleshooting-restore-issues"></a>복원 문제 해결

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>파일 및 폴더 복구 중 복구 볼륨을 탑재하지 못함
Azure Backup이 **탑재**를 클릭하고 몇 분 후에 복구 볼륨을 성공적으로 탑재하지 않거나 하나 이상의 오류가 발생하여 복구 볼륨을 탑재하는 데 실패하면 일반적으로 복구를 시작하려면 아래 단계를 따릅니다.

1.  몇 분 동안 실행된 경우 진행 중인 탑재 프로세스를 취소합니다.

2.  최신 버전의 Azure Backup 에이전트가 설치되어 있는지 확인합니다. Azure Backup 에이전트의 버전 정보를 확인하려면 Microsoft Azure Backup 콘솔의 **작업** 창에서 **Microsoft Azure Recovery Services 에이전트 정보**를 클릭하고 **버전**이 [이 문서](https://go.microsoft.com/fwlink/?linkid=229525)에 언급된 버전 이상인지 확인합니다. 최신 버전은 [여기](https://go.microsoft.com/fwLink/?LinkID=288905)에서 다운로드할 수 있습니다.

3.  **장치 관리자** -> **저장소 컨트롤러**로 이동하고 **Microsoft iSCSI 초기자**를 찾을 수 있는지 확인합니다. 찾을 수 있는 경우 바로 아래의 7단계로 이동합니다. 

4.  3단계에서 설명한 대로 Microsoft iSCSI 초기자 서비스를 찾을 수 없는 경우 하드웨어 ID와 **ROOT\ISCSIPRT**를 포함한 **알 수 없는 장치**라는 항목을 **장치 관리자** -> **저장소 컨트롤러**에서 찾을 수 있는지 확인합니다.

5.  **알 수 없는 장치**를 마우스 오른쪽 단추로 클릭하고 **업데이트 드라이버 소프트웨어**를 선택합니다.

6.  **업데이트된 드라이버 소프트웨어를 자동으로 검색**하는 옵션을 선택하여 드라이버를 업데이트합니다. 업데이트가 완료되면 다음과 같이 **알 수 없는 장치**를 **Microsoft iSCSI 초기자**로 변경해야 합니다. 

    ![암호화](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  **작업 관리자** -> **서비스(로컬)** -> **Microsoft iSCSI 초기자 서비스**로 이동합니다. 

    ![암호화](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  서비스를 마우스 오른쪽 단추로 클릭하고 중지를 클릭한 다음 마우스 오른쪽 단추로 다시 클릭하고 **시작**을 클릭하여 Microsoft iSCSI 초기자 서비스를 다시 시작합니다.

9.  인스턴트 복원을 사용하여 복구를 다시 시도하세요. 

복구가 계속 실패하면 서버/클라이언트를 다시 부팅합니다. 다시 부팅이 바람직하지 않거나 서버를 다시 부팅한 후에도 복구가 계속 실패하는 경우 [이 문서](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)의 단계에 따라 다른 컴퓨터에서 복구를 시도해 봅니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Backup 에이전트로 Windows Server를 백업하는 방법](tutorial-backup-windows-server-to-azure.md)에서 자세한 내용을 참조하세요.
* 백업을 복원해야 하는 경우 이 문서를 참조하여 [Windows 컴퓨터에 파일을 복원](backup-azure-restore-windows-server.md)할 수 있습니다.
