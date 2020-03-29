---
title: 시스템 상태 백업 문제 해결
description: 이 문서에서는 온-프레미스 Windows 서버에 대 한 시스템 상태 백업에서 문제를 해결 하는 방법을 알아봅니다.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969570"
---
# <a name="troubleshoot-system-state-backup"></a>시스템 상태 백업 문제 해결

이 문서에서는 시스템 상태 백업을 사용하는 동안 발생할 수 있는 문제에 대한 해결 에 대해 설명합니다.

## <a name="basic-troubleshooting"></a>기본 문제 해결

시스템 상태 백업 문제 해결을 시작하기 전에 아래 유효성 검사를 수행하는 것이 좋습니다.

- [Microsoft Azure 복구 서비스(MARS) 에이전트가 최신 상태인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS 에이전트와 Azure 간에 네트워크 연결이 있는지 확인](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Microsoft Azure Recovery Services가 실행 중인지 확인(서비스 콘솔에서) 필요한 경우 작업을 다시 시작하고 다시 시도하십시오.
- [스크래치 폴더 위치에서 5~10% 볼륨 여유 공간을 사용할 수 있는지 확인](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup을 방해하는지 확인](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [예약 백업은 실패하지만 수동 백업은 작동](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- OS에 최신 업데이트가 설치되었는지 확인
- [지원되지 않는 드라이브 및 지원되지 않는 특성이 있는 파일이 백업에서 제외되도록 합니다.](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 보호되는 시스템의 **시스템 클록**이 올바른 표준 시간대로 구성되었는지 확인 <br>
- [서버에 적어도 .Net Framework 버전 4.5.2 이상이 설치되었는지 확인](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 서버를 볼트에 **다시 등록하려는** 경우 다음을 수행하십시오. <br>
  - 에이전트가 서버에 제거되어 포털에서 삭제되었는지 확인합니다. <br>
  - 처음 서버 등록에 사용한 것과 동일한 암호 사용 <br>
- 오프라인 백업인 경우 오프라인 백업 작업을 시작하기 전에 Azure PowerShell 버전 3.7.0이 원본 및 복사 컴퓨터에 모두 설치되어 있는지 확인합니다.
- [Azure 가상 컴퓨터에서 백업 에이전트가 실행 중인 경우 고려 사항](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>제한 사항

- Microsoft에서는 시스템 상태 복구를 사용하여 다른 하드웨어로 복구하는 방법을 권장하지 않습니다.
- 시스템 상태 백업은 현재 "온-프레미스" Windows 서버를 지원합니다. Azure VM에는 이 기능을 사용할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 백업을 사용 하 고 시스템 상태 백업 문제를 해결 하기 전에 아래 필수 구성 조건 검사를 수행 합니다.  

### <a name="verify-windows-server-backup-is-installed"></a>Windows 서버 백업이 설치되었는지 확인

Windows 서버 백업이 서버에 설치되고 활성화되어 있는지 확인합니다. 설치 상태를 확인하려면 다음 PowerShell 명령을 실행합니다.

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

출력에 설치 **상태가** **사용 가능한**것으로 표시되면 Windows Server 백업 기능을 설치할 수 있지만 서버에 설치되지 는 않습니다. 그러나 Windows 서버 백업이 설치되지 않은 경우 아래 방법 중 하나를 사용하여 설치하십시오.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>방법 1: PowerShell을 사용하여 Windows 서버 백업 설치

PowerShell을 사용하여 Windows 서버 백업을 설치하려면 다음 명령을 실행하십시오.

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>방법 2: 서버 관리자를 사용 하 여 Windows 서버 백업 설치

서버 관리자를 사용하여 Windows 서버 백업을 설치하려면 아래 단계를 수행하십시오.

1. **서버 관리자에서** **역할 및 기능 추가를**클릭합니다. **역할 및 기능 추가 마법사가** 나타납니다.

    ![대시보드](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 설치 유형을 선택하고 다음 을 **클릭합니다.** **Next**

    ![설치 유형](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 서버 풀에서 서버를 선택하고 **다음**을 클릭합니다. 서버 역할에서 기본 선택을 그대로 두고 **다음**을 클릭합니다.
4. **기능** 탭에서 **Windows 서버 백업을** 선택하고 다음 을 **클릭합니다.**

    ![기능](./media/backup-azure-system-state-troubleshoot/features.png)

5. **확인** 탭에서 **설치를** 클릭하여 설치 프로세스를 시작합니다.
6. **결과** 탭에서 Windows 서버 백업 기능이 Windows 서버에 성공적으로 설치되어 표시됩니다.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>시스템 볼륨 정보 권한

로컬 SYSTEM이 Windows가 설치된 볼륨에 있는 **시스템 볼륨 정보** 폴더를 완전히 제어할 수 있는지 확인합니다. 일반적으로 **C:\시스템 볼륨 정보입니다.** 위의 사용 권한이 올바르게 설정되지 않으면 Windows Server 백업이 실패할 수 있습니다.

### <a name="dependent-services"></a>종속 서비스

아래 서비스가 실행 중인지 확인합니다.

**서비스 이름** | **시작 유형**
--- | ---
원격 프로시저 호출(RPC) | 자동
COM+ 이벤트 시스템(이벤트 시스템) | 자동
시스템 이벤트 알림 서비스(SENS) | 자동
볼륨 그림자 복사(VSS) | 설명서
마이크로소프트 소프트웨어 그림자 복사 공급자 (SWPRV) | 설명서

### <a name="validate-windows-server-backup-status"></a>Windows 서버 백업 상태 확인

Windows 서버 백업 상태의 유효성을 검사하려면 다음 단계를 수행합니다.

- WSB PowerShell이 실행 중인지 확인

  - 상승된 PowerShell에서 실행하고 `Get-WBJob` 다음 오류가 반환되지 않는지 확인합니다.

    > [!WARNING]
    > Get-WBJob: 'Get-WBJob'이라는 용어는 cmdlet, 함수, 스크립트 파일 또는 작동 가능한 프로그램의 이름으로 인식되지 않습니다. 이름의 철자를 확인하거나 경로가 포함되어 있으면 경로가 올바른지 확인하고 다시 시도합니다.

    - 이 오류로 실패하면 전제 조건의 1단계에서 언급한 대로 서버 컴퓨터에 Windows Server 백업 기능을 다시 설치합니다.

  - 상승된 명령 프롬프트에서 아래 명령을 실행하여 WSB 백업이 제대로 작동하는지 확인합니다.

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >시스템 상태를 백업할 볼륨의 드라이브 문자로 X를 바꿉습니다.

    - 상승된 PowerShell에서 명령을 실행하여 `Get-WBJob` 작업 상태를 주기적으로 확인합니다.
    - 백업 작업이 완료되면 명령을 실행하여 `Get-WBJob -Previous 1` 작업의 최종 상태를 확인합니다.

작업이 실패하면 MARS 에이전트 시스템 상태 백업 오류가 발생할 수 있는 WSB 문제를 나타냅니다.

## <a name="common-errors"></a>일반 오류

### <a name="vss-writer-timeout-error"></a>VSS 기록기 시간 시간 시간 오류

| 증상 | 원인 | 해결 방법
| -- | -- | --
| - MARS 에이전트가 오류 메시지와 함께 실패 : "WSB 작업은 VSS 오류로 실패했습니다. VSS 이벤트 로그를 확인하여 오류를 해결합니다."<br/><br/> - VSS 응용 프로그램 이벤트 로그에 다음 오류 로그가 있습니다: "VSS 작성기오류 0x800423f2가 있는 이벤트를 거부했으며, 기록기의 시간 시간이 동결 및 해동 이벤트 사이에 만료되었습니다."| VSS 라이터는 컴퓨터의 CPU 및 메모리 리소스 부족으로 인해 제 시간에 완료할 수 없습니다. <br/><br/> 다른 백업 소프트웨어가 이미 VSS 기록기를 사용하고 있기 때문에 이 백업에 대해 스냅샷 작업을 완료할 수 없습니다. | CPU/메모리가 시스템에서 해제될 때까지 기다리거나 너무 많은 메모리/CPU를 복용하는 프로세스를 중단하고 작업을 다시 시도하십시오. <br/><br/>  컴퓨터에서 백업이 실행되지 않는 경우 진행 중인 백업이 완료될 때까지 기다렸다가 나중에 작업을 시도합니다.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>섀도우 복사본을 증가시키기에 디스크 공간이 부족합니다.

| 증상 | 해결 방법
| -- | --
| - MARS 에이전트 오류 메시지와 함께 실패: 시스템 파일을 포함 하는 볼륨에 부족 한 디스크 공간으로 인해 그림자 복사 볼륨을 증가 할 수 없습니다 백업 실패 <br/><br/> - 다음 오류 / 경고 로그는 volsnap 시스템 이벤트 로그에 존재 : "볼륨 C에 부족한 디스크 공간이 있었다 : C의 그림자 복사본에 대한 그림자 복사 저장소를 성장 : 이 실패로 인해 볼륨 C의 모든 그림자 사본 : 삭제 될 위험이 있습니다" | - 백업이 진행되는 동안 그림자 복사본이 증가할 수 있는 충분한 공간이 있도록 이벤트 로그의 강조 표시된 볼륨에 공간을 확보합니다. <br/><br/> - 그림자 복사 공간을 구성하는 동안 그림자 복사에 사용되는 공간의 양을 제한 할 수 있습니다. 자세한 내용은 이 [문서를](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage) 참조하세요.

### <a name="efi-partition-locked"></a>EFI 파티션 잠금

| 증상 | 해결 방법
| -- | --
| MARS 에이전트는 오류 메시지와 함께 실패: "시스템 상태 백업 EFI 시스템 파티션이 잠겨 있는 동안 실패 했습니다. 이는 타사 보안또는 백업 소프트웨어에 의한 시스템 파티션 액세스 때문일 수 있습니다." | - 타사 보안 소프트웨어로 인해 문제가 발생하면 바이러스 백신 공급업체에 문의하여 MARS 에이전트를 허용할 수 있습니다. <br/><br/> - 타사 백업 소프트웨어가 실행 중인 경우 완료될 때까지 기다렸다가 다시 다시 시도하십시오.

## <a name="next-steps"></a>다음 단계

- 리소스 관리자 배포의 Windows 시스템 상태에 대한 자세한 내용은 [Windows 서버 시스템 상태 백업을](backup-azure-system-state.md) 참조하십시오.
