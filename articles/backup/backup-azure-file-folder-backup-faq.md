---
title: 파일 및 Azure Backup을 사용 하 여 폴더를 백업 하는 경우 관련 된 일반적인 질문
description: 파일 및 Azure Backup을 사용 하 여 폴더를 백업 하는 방법에 대 한 일반적인 질문을 다룹니다.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: dacurwin
ms.openlocfilehash: 1ac3cdecc79cafb9ea2697cca3c87b2ebe083d40
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254859"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>파일 및 폴더를 백업 하는 방법에 대 한 일반적인 질문 

이 문서에서는 파일 및 폴더에서 Microsoft Azure Recovery Services (MARS) 에이전트를 사용 하 여 백업 이어지고, 일반적인 질문에 답 합니다 [Azure Backup](backup-overview.md) 서비스입니다.

## <a name="general"></a>일반

### <a name="why-does-the-mars-agent-need-net-framework-452-or-higher"></a>MARS 에이전트에서는.NET framework 4.5.2를 필요한 이유는 이상?

사용할 수 있는 새로운 기능 [인스턴트 복원](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) 필요한.NET Framework 4.5.2 이상.

## <a name="configure-backups"></a>백업 구성

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>최신 버전의 MARS 에이전트를 어디서 다운로드할 수 있나요? 
Windows Server 컴퓨터, System Center DPM 및 Microsoft Azure Backup server를 백업할 때 사용 되는 최신 MARS 에이전트에 대 한 제품은 [다운로드](https://aka.ms/azurebackup_agent)합니다. 

### <a name="how-long-are-vault-credentials-valid"></a>유효한 자격 증명 모음 자격 증명 얼마나 걸리나요?
자격 증명 모음 자격 증명은 48시간이 지나면 만료됩니다. 자격 증명 파일이 만료 되 면 파일 다시는 Azure portal에서 다운로드 합니다.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>어떤 드라이브에서 백업할 수 있는 I 파일 및 폴더 

다음과 같은 유형의 드라이브 및 볼륨을 백업할 수 없습니다.

* 이동식 미디어: 모든 백업 항목 원본은 고정된 것으로 보고되어야 합니다.
* 읽기 전용 볼륨: VSS(볼륨 섀도 복사본 서비스)가 작동하려면 볼륨에 데이터 쓰기가 가능해야 합니다.
* 오프 라인 볼륨: VSS가 작동하려면 볼륨이 온라인 상태여야 합니다.
* 네트워크 공유: 온라인 백업을 사용하여 백업할 서버의 로컬 볼륨이어야 합니다.
* BitLocker로 보호된 볼륨: 볼륨의 잠금을 해제해야 백업이 실행될 수 있습니다.
* 파일 시스템 식별: NTFS는 지원되는 유일한 파일 시스템입니다.

### <a name="what-file-and-folder-types-are-supported"></a>파일 및 폴더 유형은 지원 되나요?

다음과 같은 형식이 지원됩니다.

* 암호화
* 압축
* 스파스
* 압축 + 스파스
* 하드 링크: 지원되지 않음, 건너뜀
* 재분석 지점: 지원되지 않음, 건너뜀
* 암호화됨 + 스파스: 지원되지 않음, 건너뜀
* 압축된 스트림: 지원되지 않음, 건너뜀
* DFS 링크 및 연결 지점과 같은 재분석


### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Azure VM에서 파일 및 폴더를 백업 하려면 MARS 에이전트를 사용할 수 있습니까?  
예. Azure Backup VM 수준 백업을 제공 Azure Vm에 대 한 Azure VM 에이전트에 대 한 VM 확장을 사용 합니다. VM의 Windows 운영 체제를 게스트에 파일 및 폴더를 백업 하려는 경우에 이렇게 하려면 MARS 에이전트를 설치할 수 있습니다. 

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Azure VM에 대 한 임시 저장소에 파일 및 폴더를 백업 하려면 MARS 에이전트를 사용할 수 있습니까? 
예. MARS 에이전트를 설치 하 고 임시 저장소에 게스트 Windows 운영 체제의 파일 및 폴더를 백업 합니다. -임시 저장소 데이터가 초기화 된에 대 한 백업 작업이 실패 합니다.
- 임시 저장소 데이터가 삭제 되 면 비휘발성 저장소에만 복원할 수 있습니다.

### <a name="how-do-i-register-a-server-to-another-region"></a>다른 지역에 서버를 등록 하는 방법

백업 데이터는 서버가 등록 된 자격 증명 모음의 데이터 센터로 전송 됩니다. 데이터 센터를 변경 하는 가장 쉬운 방법은 에이전트를 제거 했다가를 이며 다음 필요한 지역에 새 자격 증명 모음에 컴퓨터를 등록

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 에이전트 지원 Windows Server 2012 중복 제거는?
예. MARS 에이전트는 백업 작업을 준비할 때 중복 제거 된 데이터를 일반 데이터로 변환 합니다. 그런 다음 백업에 대 한 데이터를 최적화, 데이터를 암호화 하 고 자격 증명 모음에 암호화 된 데이터를 보냅니다.

## <a name="manage-backups"></a>백업 관리

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>백업을 위해 구성 된 Windows 컴퓨터의 이름을 바꿀 있습니까 경우 어떻게 되나요?

Windows 컴퓨터의 이름을 바꾸면 현재 구성 된 모든 백업이 중지 됩니다.

- Backup 자격 증명 모음을 사용 하 여 새 컴퓨터 이름을 등록 해야 합니다.
- 자격 증명 모음을 사용 하 여 새 이름으로 등록 하면 첫 번째 작업은는 *전체* 백업 합니다.
- 이전 서버 이름으로 자격 증명 모음에 백업 된 데이터를 복구 해야 할 경우 데이터 복구 마법사에서 대체 위치로 복원 하는 옵션을 사용 합니다. [자세히 알아보기](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine). 

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>백업에 대 한 최대 파일 경로 길이 무엇 인가요?
MARS 에이전트 NTFS를 사용 하 여 제한 된 파일 경로 길이 사양은 사용 하 여 [Windows API](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths)합니다. 보호 하려는 파일에 허용 되는 값을 초과할 경우 부모 폴더 또는 디스크 드라이브를 백업 합니다.  

### <a name="what-characters-are-allowed-in-file-paths"></a>파일 경로에 어떤 문자가 허용 되나요?

MARS 에이전트 NTFS에 의존 하며 [문자를 지원](/windows/desktop/FileIO/naming-a-file#naming_conventions) 파일 이름/경로입니다.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>"Azure Backup 구성 되지 않았습니다이 서버에 대 한" 경고가 표시 됩니다.
이 경고는 로컬 서버에 저장 된 백업 일정 설정과 백업 자격 증명 모음에 저장 된 설정과 동일 하지 않은 경우 백업 정책을 구성한 경우에 나타날 수 있습니다.
- 알려진 좋은 상태로 서버 또는 설정이 복구 된 경우 백업 일정 하지 않을 수 있습니다.
- 이 경고 메시지가 나타나면 [구성](backup-azure-manage-windows-server.md) 다시 백업 정책 및 다음 Azure 사용 하 여 로컬 서버를 다시 동기화에 대 한 실행 요청 시 백업 합니다.


## <a name="manage-the-backup-cache-folder"></a>백업 캐시 폴더 관리

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>캐시 폴더의 최소 크기 요구 사항은 무엇인가요?
캐시 폴더의 크기는 백업하는 데이터의 양에 따라 결정됩니다.
- 캐시 폴더 볼륨에 백업 데이터의 총 크기의 5 ~ 10% 이상에 해당 하는 사용 가능한 공간이 있어야 합니다.
- 볼륨에 여유 공간이 5% 미만인 경우 볼륨 크기를 늘리거나 캐시 폴더를 충분 한 공간이 있는 볼륨으로 이동 합니다.
- Windows 시스템 상태를 백업 하는 경우 캐시 폴더를 포함 하는 볼륨의 사용 가능한 공간을 추가 30 ~ 35 GB 필요
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>MARS 에이전트에 대 한 캐시 위치를 변경 하려면 어떻게 해야 합니까?


1. Backup 엔진을 중지 하려면 명령 프롬프트에서이 명령을 실행 합니다.

    ```PS C:\> Net stop obengine```

2. 파일을 이동 하지 마십시오. 대신 캐시 공간 폴더를 충분 한 공간이 있는 다른 드라이브로 복사 합니다.
3. 새 캐시 폴더의 경로 사용 하 여 다음 레지스트리 항목을 업데이트 합니다.<br/>

    | 레지스트리 경로 | 레지스트리 키 | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*새 캐시 폴더 위치* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*새 캐시 폴더 위치* |

4. 관리자 권한 명령 프롬프트에서 Backup 엔진을 다시 시작 합니다.

    ```PS C:\> Net start obengine```

5. 백업을 새 위치를 사용 하 여 성공적으로 완료 되 면 원래 캐시 폴더를 제거할 수 있습니다.


### <a name="where-should-the-cache-folder-be-located"></a>캐시 폴더를 위치 배치할?

캐시 폴더에 대해 다음 위치는 권장되지 않습니다.

* 네트워크 공유와 이동식 미디어: 캐시 폴더는 온라인 백업을 사용하여 백업해야 하는 서버에 대해 로컬이어야 합니다. 네트워크 위치 또는 USB 드라이브 같은 이동식 미디어는 지원되지 않습니다.
* 오프 라인 볼륨: 캐시 폴더는 Azure Backup 에이전트를 사용하는 예상된 백업에 대해 온라인 상태여야 합니다.

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>지원 되지 않는 캐시 폴더의 특성이 있나요?
다음과 같은 특성 또는 해당 조합은 캐시 폴더에 지원되지 않습니다.

* 암호화
* 중복 제거
* 압축
* 스파스
* 재분석 지점

캐시 폴더와 메타데이터 VHD에는 모두 Azure Backup 에이전트에 필요한 특성이 없습니다.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>백업에 사용 되는 대역폭의 양을 조정 하는 방법이?
 
예를 사용할 수 있습니다 합니다 **속성 변경** 대역폭 및 타이밍을 조정 하려면 MARS 에이전트의 옵션입니다. [자세한](backup-configure-vault.md#enable-network-throttling)* * 합니다.

## <a name="restore"></a>복원

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>진행 중인 복원 작업을 취소하면 어떻게 되나요?

진행 중인 복원 작업을 취소 되 면 복원 프로세스를 중지 합니다. 취소 하기 전에 복원 된 모든 파일이 구성 된 대상 (원래 또는 대체 위치), 모든 롤백 없이 유지 됩니다.


## <a name="next-steps"></a>다음 단계

[에 대해 알아봅니다](tutorial-backup-windows-server-to-azure.md) Windows 컴퓨터를 백업 하는 방법입니다.
