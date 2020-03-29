---
title: Azure 백업 에이전트 문제 해결
description: 이 문서에서는 Azure Backup 에이전트의 설치 및 등록 문제를 해결하는 방법을 알아봅니다.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 24169356600c25e664221af397051bb0fec3e459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673084"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure 복구 서비스(MARS) 에이전트 문제 해결

이 문서에서는 구성, 등록, 백업 및 복원 중에 발생할 수 있는 오류를 해결하는 방법을 설명합니다.

## <a name="basic-troubleshooting"></a>기본 문제 해결

Microsoft 의 MARS(Azure 복구 서비스) 에이전트 문제 해결을 시작하기 전에 다음을 확인하는 것이 좋습니다.

- [MARS 에이전트가 최신 상태인지 확인합니다.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS 에이전트와 Azure 간에 네트워크 연결이 있는지 확인합니다.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- 서비스 콘솔에서 MARS가 실행되고 있는지 확인합니다. 필요한 경우 작업을 다시 시작하고 다시 시도하십시오.
- [스크래치 폴더 위치에서 5 %에서 10 %의 사용 가능한 볼륨 공간을 사용할 수 있는지 확인하십시오.](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup을 방해하고 있는지 확인합니다.](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- 예약된 백업이 실패했지만 수동 백업이 작동하는 경우 [백업이 일정에 따라 실행되지 않는](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)것을 참조하세요.
- OS에 최신 업데이트가 있는지 확인합니다.
- [지원되지 않는 드라이브 및 지원되지 않는 특성이 있는 파일이 백업에서 제외되도록 합니다.](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 보호된 시스템의 클럭이 올바른 표준 시간대로 구성되어 있는지 확인합니다.
- [.NET Framework 4.5.2 이상이 서버에 설치되어 있는지 확인합니다.](https://www.microsoft.com/download/details.aspx?id=30653)
- 서버를 볼트에 다시 등록하려는 경우:
  - 에이전트가 서버에 제거되고 포털에서 에이전트가 삭제되었는지 확인합니다.
  - 서버를 등록하는 데 처음 사용된 것과 동일한 암호를 사용합니다.
- 오프라인 백업의 경우 백업을 시작하기 전에 Azure PowerShell 3.7.0이 원본 및 복사 컴퓨터에 모두 설치되어 있는지 확인합니다.
- 백업 에이전트가 Azure 가상 컴퓨터에서 실행 중인 경우 [이 문서를](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)참조하십시오.

## <a name="invalid-vault-credentials-provided"></a>잘못된 자격 증명 모음이 제공되었습니다.

**오류 메시지**: 잘못된 자격 증명이 제공되었습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. (ID: 34513)

| 원인 | 권장 작업 |
| ---     | ---    |
| **자격 증명이 잘못되었습니다.** <br/> <br/> Vault 자격 증명 파일이 손상되었거나 만료되었을 수 있습니다. 예를 들어 등록 시간 48시간 전에 다운로드되었을 수 있습니다.| Azure 포털의 복구 서비스 자격 증명에서 새 자격 증명을 다운로드합니다. (MARS [에이전트 다운로드](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) 섹션에서 6단계를 참조하십시오.) 그런 다음 다음 단계를 적절히 수행합니다. <ul><li> MARS를 이미 설치하고 등록한 경우 Microsoft Azure 백업 에이전트 MMC 콘솔을 연 다음 **작업** 창에서 **서버 등록을** 선택하여 새 자격 증명으로 등록을 완료합니다. <br/> <li> 새 설치에 실패하면 새 자격 증명으로 다시 설치해 보십시오.</ul> **참고:** 여러 개의 자격 증명 자격 증명 파일이 다운로드된 경우 최신 파일만 다음 48시간 동안 유효합니다. 새 자격 증명 파일을 다운로드하는 것이 좋습니다.
| **프록시 서버/방화벽이 등록을 차단하고 있습니다.** <br/>또는 <br/>**인터넷 연결 없음** <br/><br/> 컴퓨터 또는 프록시 서버에 인터넷 연결이 제한되어 있고 필요한 URL에 대한 액세스를 보장하지 않으면 등록이 실패합니다.| 다음 단계를 수행하세요.<br/> <ul><li> IT 팀과 협력하여 시스템에 인터넷 연결이 있는지 확인합니다.<li> 프록시 서버가 없는 경우 에이전트를 등록할 때 프록시 옵션이 선택되지 않았는지 확인합니다. [프록시 설정을 확인합니다.](#verifying-proxy-settings-for-windows)<li> 방화벽/프록시 서버가 있는 경우 네트워킹 팀과 협력하여 이러한 URL 및 IP 주소에 액세스할 수 있도록 합니다.<br/> <br> **Url**<br> `www.msftncsi.com` <br> . Microsoft.com <br> . WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP 주소**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>앞의 문제 해결 단계를 완료한 후 다시 등록해 보십시오.<br></br> Azure ExpressRoute를 통해 연결하는 경우 [Azure ExpressRoute 지원에](backup-support-matrix-mars-agent.md#azure-expressroute-support)설명된 대로 설정이 구성되어 있는지 확인합니다.
| **바이러스 백신 소프트웨어가 등록을 차단하고 있습니다.** | 서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 이러한 파일 및 폴더에 대한 바이러스 백신 검사에 필요한 제외 규칙을 추가합니다. <br/><ul> <li> CBengine.exe <li> Csc.exe<li> 스크래치 폴더입니다. 기본 위치는 C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\스크래치입니다. <li> C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\빈의 빈 폴더입니다.

### <a name="additional-recommendations"></a>추가 권장 사항

- C:/Windows/Temp로 이동하여 확장명이 .tmp인 파일이 60,000 또는 65,000개 넘게 있는지 확인합니다. 있는 경우 해당 파일을 삭제합니다.
- 기기의 날짜와 시간이 현지 표준 시간대와 일치하는지 확인합니다.
- [이러한 사이트가](install-mars-agent.md#verify-internet-access) Internet Explorer의 신뢰할 수 있는 사이트에 추가되었는지 확인합니다.

### <a name="verifying-proxy-settings-for-windows"></a>Windows용 프록시 설정 확인

1. [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) 페이지에서 PsExec을 다운로드하십시오.
1. 상승된 명령 프롬프트에서 실행합니다. `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`

   이 명령은 인터넷 익스플로러를 엽니다.
1. **도구** > **인터넷 옵션으로** > 이동 LAN**설정을****연결합니다.** > 
1. 시스템 계정의 프록시 설정을 확인합니다.
1. 프록시가 구성되지 않고 프록시 세부 정보가 제공되면 세부 정보를 제거합니다.
1. 프록시가 구성되고 프록시 세부 정보가 올바르지 않으면 **프록시 IP** 및 **포트** 세부 정보가 올바른지 확인합니다.
1. Internet Explorer를 닫습니다.

## <a name="unable-to-download-vault-credential-file"></a>자격 증명 모음 파일을 다운로드할 수 없음

| Error   | 권장 작업 |
| ---     | ---    |
|자격 증명 모음 파일을 다운로드하지 못했습니다. (ID: 403) | <ul><li> 다른 브라우저를 사용하여 자격 증명 을 다운로드하거나 다음 단계를 수행하십시오. <ul><li> Internet Explorer를 시작합니다. F12를 선택합니다. </li><li> **네트워크** 탭으로 이동하여 캐시와 쿠키를 지웁습니다. </li> <li> 페이지를 새로 고칩니다.<br></li></ul> <li> 구독이 비활성화/만료되었는지 확인합니다.<br></li> <li> 방화벽 규칙이 다운로드를 차단하고 있는지 확인합니다. <br></li> <li> 볼트 에 대한 제한을 소진하지 않았는지 확인합니다(볼트당 50대의 컴퓨터).<br></li>  <li> 사용자에게 자격 증명 자격 증명을 다운로드하고 볼트에 서버를 등록하는 데 필요한 Azure Backup 권한이 있는지 확인합니다. [역할 기반 액세스 제어 를 사용하여 Azure 백업 복구 지점을 관리합니다.](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다.

| Error  | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure 복구 서비스 에이전트가 Microsoft Azure 백업에 연결할 수 없습니다. (ID: 100050) 네트워크 설정을 확인하고 인터넷에 연결할 수 있는지 확인합니다.<li>(407) 프록시 인증 필요. |프록시가 연결을 차단하고 있습니다. |  <ul><li>인터넷 익스플로러에서, 로 이동 **도구** > **인터넷 옵션** > **보안** > **인터넷**. 사용자 지정 수준을 선택하고 **파일 다운로드** 섹션으로 아래로 **스크롤합니다.** **사용**을 선택합니다.<p>인터넷 익스플로러에서 신뢰할 수 있는 사이트에 [URL과 IP 주소를](install-mars-agent.md#verify-internet-access) 추가해야 할 수도 있습니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다.<li> 컴퓨터가 인터넷 액세스가 제한된 경우 컴퓨터 또는 프록시의 방화벽 설정에서 이러한 URL 및 IP 주소를 허용해야 [합니다.](install-mars-agent.md#verify-internet-access) <li>서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 바이러스 백신 검사에서 다음 파일을 제외합니다. <ul><li>CBEngine.exe(dpmra.exe 대신)<li>CSC.exe(.NET Framework와 관련됨) 서버에 설치된 모든 .NET 프레임워크 버전에 대한 CSC.exe가 있습니다. 영향을 받는 서버의 모든 버전의 .NET Framework에 대해 CSC.exe 파일을 제외합니다. <li>스크래치 폴더 또는 캐시 위치입니다. <br>스크래치 폴더 또는 캐시 경로의 기본 위치는 C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\Scratch입니다.<li>C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\빈의 빈 폴더입니다.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>안전한 백업을 위한 암호화 키를 설정하지 못했습니다.

| Error | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| <br />보안 백업을 위해 암호화 키를 설정하지 못했습니다. 활성화가 완전히 성공한 것은 아니지만 암호화 암호가 다음 파일에 저장되었습니다. |<li>서버가 다른 자격 증명 모음에 이미 등록되어 있습니다.<li>구성하는 동안 암호가 손상되었습니다.| 볼트에서 서버를 등록 취소하고 새 암호로 다시 등록합니다.

## <a name="the-activation-did-not-complete-successfully"></a>정품 인증이 완료되지 않았습니다.

| Error  | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
|<br />정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다. 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.     | <li> 스크래치 폴더는 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.         | <li>최신 [버전의](https://aka.ms/azurebackup_agent) MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 백업 데이터의 총 크기의 5%에서 10% 사이의 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [파일 및 폴더 백업에 대한 일반적인 질문의](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)단계를 참조하십시오.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 경로의 기본 위치는 C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\스크래치 입니다.*        |

## <a name="encryption-passphrase-not-correctly-configured"></a>암호화 암호가 올바르게 구성되지 않았습니다.

| Error  | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
| <br />34506 오류입니다. 이 컴퓨터에 저장된 암호화의 암호가 제대로 구성되어 있지 않습니다.    | <li> 스크래치 폴더는 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.        | <li>[최신 버전](https://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 백업 데이터의 총 크기의 5%에서 10% 사이의 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [파일 및 폴더 백업에 대한 일반적인 질문의](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)단계를 참조하십시오.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 경로의 기본 위치는 C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\스크래치 입니다.*         |

## <a name="backups-dont-run-according-to-schedule"></a>백업은 일정에 따라 실행되지 않습니다.

예약된 백업이 자동으로 트리거되지 않지만 수동 백업이 올바르게 작동하는 경우 다음 작업을 시도하십시오.

- Windows Server 백업 일정이 Azure 파일 및 폴더 백업 일정과 충돌하지 않는지 확인합니다.

- 온라인 백업 상태가 **사용**설정으로 설정되어 있는지 확인합니다. 상태를 확인하려면 다음 단계를 수행합니다.

  1. 작업 스케줄러에서 **Microsoft를** 확장하고 **온라인 백업을**선택합니다.
  1. **Microsoft-OnlineBackup을** 두 번 클릭하고 트리거 탭으로 **이동합니다.**
  1. 상태가 **활성화로**설정되어 있는지 확인합니다. 그렇지 않은 경우 **편집**, **사용 중을**선택한 다음 **확인을**선택합니다.

- 작업을 실행하기 위해 선택한 사용자 계정이 서버의 **SYSTEM** 또는 **로컬 관리자 그룹인지** 확인합니다. 사용자 계정을 확인하려면 **일반** 탭으로 이동하여 **보안** 옵션을 확인합니다.

- PowerShell 3.0 이상이 서버에 설치되어 있는지 확인합니다. PowerShell 버전을 확인하려면 이 명령을 실행하고 `Major` 버전 번호가 3 이상인지 확인합니다.

  `$PSVersionTable.PSVersion`

- 이 경로가 `PSMODULEPATH` 환경 변수의 일부인지 확인합니다.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 에 대 `LocalMachine` 한 PowerShell 실행 `restricted`정책이 설정 된 경우 백업 작업을 트리거 하는 PowerShell cmdlet 실패 할 수 있습니다. 이러한 명령을 상승 된 모드에서 실행하여 실행 정책을 `Unrestricted` `RemoteSigned`다음 중 하나 또는 다음 으로 확인하거나 설정합니다.

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- 누락 되거나 손상 된 PowerShell 모듈 MSOnlineBackup 파일이 없는지 확인 합니다. 누락되거나 손상된 파일이 있는 경우 다음 단계를 수행합니다.

  1. 제대로 작동하는 MARS 에이전트가 있는 컴퓨터에서 C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\bin\Modules에서 MSOnlineBackup 폴더를 복사합니다.
  1. 문제가 있는 컴퓨터에서 복사된 파일을 동일한 폴더 위치에 붙여넣습니다(C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\bin\Modules).

     컴퓨터에 MSOnlineBackup 폴더가 이미 있는 경우 파일을 붙여넣거나 기존 파일을 바꿉습니다.

> [!TIP]
> 변경 내용이 일관되게 적용되도록 하려면 이전 단계를 수행한 후 서버를 다시 시작합니다.

## <a name="troubleshoot-restore-problems"></a>복원 문제 해결

몇 분 후에도 Azure Backup이 복구 볼륨을 성공적으로 탑재하지 않을 수 있습니다. 또한 프로세스 중에 오류 메시지가 발생할 수 있습니다. 정상적으로 복구를 시작하려면 다음 단계를 수행하십시오.

1. 몇 분 동안 실행 중인 경우 마운트 프로세스를 취소합니다.

2. 최신 버전의 백업 에이전트가 있는지 확인합니다. 버전을 확인하려면 MARS 콘솔의 **작업** 창에서 **Microsoft Azure 복구 서비스 에이전트 소개를**선택합니다. **버전** 번호가 [이 문서](https://go.microsoft.com/fwlink/?linkid=229525)에 언급된 버전보다 높거나 같은지 확인합니다. 이 링크를 선택하여 [최신 버전을 다운로드합니다.](https://go.microsoft.com/fwLink/?LinkID=288905)

3. 장치 **관리자** > **저장소 컨트롤러로** 이동하여 **Microsoft iSCSI 이니시에이터를**찾습니다. 당신이 그것을 찾을 경우, 7 단계로 직접 이동합니다.

4. Microsoft iSCSI 이니시에이터 서비스를 찾을 수 없는 경우 하드웨어 ID **ROOT\ISCSIPRT를**사용하여 **알 수 없는 장치라는** **장치 관리자** > **저장소 컨트롤러** 에서 항목을 찾으십시오.

5. **알 수 없는 장치를** 마우스 오른쪽 단추로 클릭하고 드라이버 소프트웨어 **업데이트를**선택합니다.

6. **업데이트된 드라이버 소프트웨어를 자동으로 검색**하는 옵션을 선택하여 드라이버를 업데이트합니다. 이 업데이트는 마이크로 **소프트 iSCSI 이니시에이터로** **알 수없는 장치를** 변경해야합니다 :

    ![스토리지 컨트롤러가 강조 표시된 Azure Backup 디바이스 관리자의 스크린샷](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. 작업 **관리자** > **서비스 (로컬)** > **Microsoft iSCSI 이니시터 서비스로**이동 :

    ![서비스(로컬)가 강조 표시된 Azure Backup 작업 관리자의 스크린샷](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Microsoft iSCSI 초기자 서비스를 다시 시작합니다. 이렇게 하려면 서비스를 마우스 오른쪽 단추로 클릭하고 **중지를**선택합니다. 그런 다음 다시 마우스 오른쪽 단추로 클릭하고 **시작을**선택합니다.

9. [인스턴트 복원](backup-instant-restore-capability.md)을 사용하여 복구를 다시 시도합니다.

복구가 여전히 실패하면 서버 또는 클라이언트를 다시 시작합니다. 다시 시작하지 않으려는 경우 또는 서버를 다시 시작한 후에도 복구가 여전히 실패하는 경우 [다른 컴퓨터에서 복구해](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)보십시오.

## <a name="troubleshoot-cache-problems"></a>캐시 문제 해결

캐시 폴더(스크래치 폴더라고도 함)가 잘못 구성되었거나 필수 구성 조건이 없거나 액세스가 제한된 경우 백업 작업이 실패할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

MARS 에이전트 작업이 성공하려면 캐시 폴더가 아래 요구 사항을 준수해야 합니다.

- [스크래치 폴더 위치에서 5%~10%의 사용 가능한 볼륨 공간을 사용할 수 있도록 보장](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [스크래치 폴더 위치가 유효하고 액세스 가능한지 확인](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [캐시 폴더의 파일 특성이 지원되도록 합니다.](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [할당된 섀도우 복사 저장 공간이 백업 프로세스에 충분한지 확인](#increase-shadow-copy-storage)
- [캐시 폴더에 대한 액세스를 제한하는 다른 프로세스(예: 바이러스 백신 소프트웨어)가 없는지 확인합니다.](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>섀도우 카피 저장 공간 증가

데이터 원본을 보호하는 데 필요한 섀도 복사본 저장 공간이 부족하면 백업 작업이 실패할 수 있습니다. 이 문제를 해결 하려면 아래와 같이 vssadmin을 사용 하 여 보호 된 볼륨에 그림자 복사 저장 공간을 증가 합니다.

- 상승된 명령 프롬프트에서 현재 그림자 저장 공간을 확인합니다.<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- 아래 명령을 사용하여 그림자 저장 공간을 늘립니다.<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>캐시 폴더에 대한 액세스를 차단하는 다른 프로세스 또는 바이러스 백신 소프트웨어

서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 이러한 파일 및 폴더에 대한 바이러스 백신 검사에 필요한 제외 규칙을 추가합니다.  

- 스크래치 폴더입니다. 기본 위치는 C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\스크래치
- C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\빈의 빈 폴더
- CBengine.exe
- Csc.exe

## <a name="common-issues"></a>일반적인 문제

이 섹션에서는 MARS 에이전트를 사용하는 동안 발생하는 일반적인 오류를 다룹니다.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

오류 메시지 | 권장 작업 |
-- | --
Microsoft Azure Recovery Services 에이전트가 스크래치 위치에 저장된 백업 체크섬에 액세스할 수 없습니다. | 이 문제를 해결하려면 아래를 수행하고 서버를 다시 시작하십시오. <br/> - [스크래치 위치 파일을 잠그는 바이러스 백신 또는 기타 프로세스가 있는지 확인합니다.](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [스크래치 위치가 유효하고 화성 에이전트가 액세스할 수 있는지 확인합니다.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

오류 메시지 | 권장 작업 |
-- | --
Microsoft Azure Recovery Services 에이전트가 VHD를 초기화하기 위해 스크래치 위치에 액세스할 수 없음 | 이 문제를 해결하려면 아래를 수행하고 서버를 다시 시작하십시오. <br/> - [스크래치 위치 파일을 잠그는 바이러스 백신 또는 기타 프로세스가 있는지 확인합니다.](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [스크래치 위치가 유효하고 화성 에이전트가 액세스할 수 있는지 확인합니다.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>살로우디스크스페이스

오류 메시지 | 권장 작업 |
-- | --
스크래치 폴더가 있는 볼륨의 저장소부족으로 인해 백업에 실패했습니다. | 이 문제를 해결하려면 아래 단계를 확인하고 작업을 다시 시도하십시오.<br/>- [MARS 에이전트가 최신인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [백업 스크래치 공간에 영향을 주는 스토리지 문제 확인 및 해결](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

오류 메시지 | 권장 작업 |
-- | --
파일에 변경 내용을 찾을 수 없습니다. 이는 다양한 이유 때문일 수 있습니다. 작업을 다시 시도하세요. | 이 문제를 해결하려면 아래 단계를 확인하고 작업을 다시 시도하십시오.<br/> - [MARS 에이전트가 최신인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [백업 스크래치 공간에 영향을 주는 스토리지 문제 확인 및 해결](#prerequisites)

## <a name="next-steps"></a>다음 단계

- Azure 백업 [에이전트를 사용하여 Windows 서버를 백업하는 방법에](tutorial-backup-windows-server-to-azure.md)대한 자세한 내용을 확인합니다.
- 백업을 복원해야 하는 경우 [Windows 컴퓨터에 파일 복원을](backup-azure-restore-windows-server.md)참조하십시오.
