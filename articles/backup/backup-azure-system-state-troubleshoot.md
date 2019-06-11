---
title: Azure Backup 사용 하 여 시스템 상태 백업 문제 해결
description: 시스템 상태 백업에는 문제를 해결 합니다.
services: backup
author: srinathvasireddy
manager: sivan
keywords: 백업으로 하는 방법 백업 시스템 상태
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathvasireddy
ms.openlocfilehash: 53b9f8fb58a6e70a4bd2cd02adb9ce824466d7de
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481596"
---
# <a name="troubleshoot-system-state-backup"></a>시스템 상태 백업 문제 해결

이 문서에서는 시스템 상태 백업을 사용 하는 동안 발생할 수 있는 문제에 대 한 솔루션을 설명 합니다.

## <a name="pre-requisite"></a>필수 구성 요소

있습니다 수행 하기 전에 Azure Backup을 사용 하 여 시스템 상태 백업 문제를 해결 했습니다는 필수 조건 아래를 확인 합니다.  

### <a name="verify-windows-server-backup-is-installed"></a>Windows Server Backup이 설치 되었는지 확인

Windows Server Backup을 설치 하 고 서버에서 사용할 수 있는지 확인 합니다. 설치 상태를 확인 하려면는 아래 PowerShell 명령을:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
출력에 표시 되는 경우는 **설치 상태** 으로 **사용 가능한**, Windows Server 백업 기능을 설치에 사용할 수 있지만 서버에 설치 되지 않은 것입니다. 그러나 Windows Server Backup을 설치 하지 않은 경우 다음 중 하나를 사용 된 다음 설치 하는 방법입니다.

**방법 1: PowerShell을 사용 하 여 Windows Server 백업을 설치합니다**

PowerShell을 사용 하 여 Windows Server Backup을 설치 하려면는 아래 명령을:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**방법 2: 서버 관리자를 사용 하 여 Windows Server 백업을 설치합니다**

서버 관리자를 사용 하 여 Windows Server Backup을 설치 하려면 다음을 수행 합니다 아래:

1. 에 **Sever Manger** 클릭 **역할 및 기능 추가**합니다. 합니다 **추가 역할 및 기능 마법사** 나타납니다.

    ![대시보드](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 선택 **설치 유형을** 누릅니다 **다음**합니다.

    ![설치 유형](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 서버 풀에서 서버를 선택 하 고 클릭 **다음**합니다. 서버 역할의 기본 선택 항목을 유지 하 고 클릭 **다음**합니다.
4. 선택 **Windows Server Backup** 에 **기능** 탭을 클릭 **다음**합니다.

    ![기능](./media/backup-azure-system-state-troubleshoot/features.png)

5. 에 **확인** 탭을 클릭 **설치** 설치 프로세스를 시작 합니다.
6. 에 **결과** 탭, Windows Server Backup 기능 Windows Server에 제대로 설치 되었는지 표시 됩니다.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>시스템 볼륨 정보 사용 권한

로컬 시스템 모든 권한을 갖고 있는지 확인 하십시오 **시스템 볼륨 정보** windows가 설치 된 볼륨의 폴더입니다. 일반적으로 이것이 **C:\System Volume Information**합니다. 위의 사용 권한이 올바르게 설정 되지 않은 경우 Windows Server 백업이 실패할 수 있습니다.

### <a name="dependent-services"></a>종속 서비스

확인을 아래 서비스 실행 중 상태가 됩니다.

**서비스 이름** | **시작 유형**
--- | ---
원격 프로시저 Call(RPC) | 자동
COM + 이벤트 System(EventSystem) | 자동
시스템 이벤트 알림 Service(SENS) | 자동
볼륨 섀도 Copy(VSS) | 수동
Microsoft 소프트웨어 섀도 복사본 Provider(SWPRV) | 수동

### <a name="validate-windows-server-backup-status"></a>Windows Server 백업 상태 확인

Windows Server 백업 상태 확인을 위해 수행 된 아래:

  * WSB PowerShell 실행 중인지 확인

    -   실행 `Get-WBJob` 오류로 반환 하지 상승 된 PowerShell 및 있는지 확인 합니다.

    > [!WARNING]
    > Get-WBJob: ' Get-WBJob' 용어는 cmdlet, 함수, 스크립트 파일 또는 실행 프로그램의 이름으로 인식 되지 않습니다. 이름의 철자를 확인하거나 경로가 포함되어 있으면 경로가 올바른지 확인하고 다시 시도합니다.

    -   이 오류로 인해 실패 한 후 다시 설치 필수 구성 요소를 1 단계에서 설명한 것 처럼 서버 컴퓨터에서 Windows Server Backup 기능 합니다.

  * WSB 백업 실행 하 여 제대로 작동 하는지 확인 합니다 아래 명령을 관리자 권한 명령 프롬프트에서:

      ` wbadmin start systemstatebackup -backuptarget:X: -quiet `

      > [!NOTE]
      >시스템 상태를 저장 하려는 볼륨의 드라이브 문자를 사용 하 여 X 대체 이미지를 백업 합니다.

    - 주기적으로 실행 하 여 작업의 상태를 확인할 `Get-WBJob` 관리자 권한 PowerShell 명령        
    - 백업 작업이 완료 된 후 실행 하 여 작업의 최종 상태를 확인할 `Get-WBJob -Previous 1` 명령

작업이 실패 한 경우에 MARS 에이전트 시스템 상태 백업 실패를 초래 하는 WSB 문제를 나타냅니다.

## <a name="common-errors"></a>일반적인 오류

### <a name="vss-writer-timeout-error"></a>VSS 기록기 시간 초과 오류

| 증상 | 원인 | 해결 방법
| -- | -- | --
| -MARS 에이전트 오류 메시지와 함께 실패합니다. "VSS 오류를 사용 하 여 WSB 작업이 실패 했습니다. 이 문제를 해결 하는 VSS 이벤트 로그 확인 "<br/><br/> -다음 오류 로그는 VSS 응용 프로그램 이벤트 로그에 존재 합니다. "VSS 기록기가 오류 0x800423f2 사용 하 여 이벤트를 거부 하는, 중지 및 재개 이벤트 간의 기록기의 제한 시간이 만료 되었습니다."| VSS 기록기를 컴퓨터의 CPU 및 메모리 리소스가 부족 하 여 시간에 완료할 수 됩니다. <br/><br/> 다른 백업 소프트웨어는 VSS 기록기를 사용 하 여 이미, 결과적으로 스냅숏 작업을 완료할 수 없습니다이 백업에 대 한 | 시스템에서 확보할 수 또는 메모리/CPU를 너무 많이 수행 하는 프로세스를 중단 하 고 작업을 다시 시도 하려면 p U/메모리 대기 <br/><br/>  진행 중인 백업을 완료 하 고 백업이 없습니다 머신에서 실행 하는 경우 나중에 시도 대기


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>섀도 복사본을 증가 부족 한 디스크 공간

| 증상 | 해결 방법
| -- | --
| -MARS 에이전트 오류 메시지와 함께 실패합니다. 시스템 파일이 들어 있는 볼륨의 섀도 복사본 볼륨 디스크 공간이 부족으로 인해 확장 되지 않아서 백업에 실패 했습니다. <br/><br/> -오류/경고 로그를 수행 하는 것이 volsnap 시스템 이벤트 로그에 있습니다. "했습니다. 디스크 공간이 부족이 오류로 인해 c: 드라이브의 섀도 복사본에 대 한 섀도 복사본 저장소 증가를 c: 볼륨에서 모든 섀도 복사본 볼륨은 삭제할 위험이 c:" | -공간을 확보 하기 이벤트 로그에 강조 표시 된 볼륨의 섀도 복사본 백업이 진행 되는 동안 증가 대 한 충분 한 공간이 있도록 <br/><br/> -구성 하는 동안 섀도 복사에 사용 되는 공간의 크기를 제한할 수에서는 섀도 복사본 공간이, 자세한 내용은 참조 [문서](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>잠긴 EFI 파티션

| 증상 | 해결 방법
| -- | --
| MARS 에이전트 오류 메시지와 함께 실패합니다. "시스템 상태를 다시 최대 실패 EFI 시스템 파티션이 잠겨. 백업 소프트웨어 또는 타사 보안 시스템 파티션에 액세스 있기 때문일 수 있습니다 " | -문제 뿐만 아니라 타사 보안 소프트웨어 때문 인 경우 다음 해야 MARS 에이전트를 허용할 수 있습니다 있도록 바이러스 백신 공급 업체에 문의 <br/><br/> -타사 백업 소프트웨어를 실행 하는 경우 완료 하 고 등록 다시 시도 하려면 기다립니다


## <a name="next-steps"></a>다음 단계

- Resource Manager 배포에서 Windows 시스템 상태에 대 한 자세한 내용은 참조 하세요. [Windows Server 시스템 상태 백업](backup-azure-system-state.md)
