---
title: 파일 및 폴더 백업 - 일반적인 질문
description: Azure Backup을 통해 파일 및 폴더 백업에 대한 일반적인 질문을 해결합니다.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 55819ce7ec5196812d935a21c096c132144d78af
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421315"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>파일 및 폴더 백업에 대한 일반적인 질문

이 문서에서는 [Azure 백업](backup-overview.md) 서비스의 MARS(Mars) 에이전트를 통해 파일 및 폴더를 백업하는 일반적인 질문에 대해 답변합니다.

## <a name="configure-backups"></a>백업 구성

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>MARS 에이전트의 최신 버전은 어디에서 다운로드할 수 있습니까?

Windows 서버 컴퓨터, 시스템 센터 DPM 및 Microsoft Azure 백업 서버를 백업할 때 사용되는 최신 MARS 에이전트를 [다운로드할](https://aka.ms/azurebackup_agent)수 있습니다.

### <a name="how-long-are-vault-credentials-valid"></a>자격 증명 자격 증명은 얼마 동안 유효합니까?

자격 증명 모음 자격 증명은 48시간이 지나면 만료됩니다. 자격 증명 파일이 만료되면 Azure 포털에서 파일을 다시 다운로드합니다.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>어떤 드라이브에서 파일과 폴더를 백업 할 수 있습니까?

다음 유형의 드라이브 및 볼륨을 백업할 수 없습니다.

* 이동식 미디어: 모든 백업 항목 원본은 고정된 것으로 보고해야 합니다.
* 읽기 전용 볼륨: 볼륨 섀도우 복사 서비스(VSS)가 작동하려면 볼륨을 사용할 수 있어야 합니다.
* 오프라인 볼륨: VSS가 작동하려면 볼륨이 온라인 상태입니다.
* 네트워크 공유: 볼륨은 온라인 백업을 사용하여 백업하려면 서버에 로컬이어야 합니다.
* BitLocker로 보호된 볼륨: 백업이 발생하기 전에 볼륨을 잠금 해제해야 합니다.
* 파일 시스템 식별: NTFS가 유일하게 지원되는 파일 시스템입니다.

### <a name="what-file-and-folder-types-are-supported"></a>어떤 파일 및 폴더 유형이 지원됩니까?

백업에 지원되는 파일 및 폴더 유형에 대해 [자세히 알아보세요.](backup-support-matrix-mars-agent.md#supported-file-types-for-backup)

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>MARS 에이전트를 사용하여 Azure VM에서 파일 및 폴더를 백업할 수 있습니까?  

예. Azure 백업은 Azure VM 에이전트에 대한 VM 확장을 사용하여 Azure VM에 대한 VM 수준 백업을 제공합니다. VM의 게스트 Windows 운영 체제에서 파일 및 폴더를 백업하려면 MARS 에이전트를 설치하여 백업할 수 있습니다.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Mars 에이전트를 사용하여 Azure VM의 임시 저장소에 파일 및 폴더를 백업할 수 있습니까?

예. MARS 에이전트를 설치하고 게스트 Windows 운영 체제에 파일 및 폴더를 임시 저장소에 백업합니다.

* 임시 저장소 데이터가 지워지면 백업 작업이 실패합니다.
* 임시 저장소 데이터가 삭제된 경우 비휘발성 저장소로만 복원할 수 있습니다.

### <a name="how-do-i-register-a-server-to-another-region"></a>다른 지역에 서버를 등록하려면 어떻게 해야 합니까?

백업 데이터는 서버가 등록된 볼트의 데이터 센터로 전송됩니다. 데이터 센터를 변경하는 가장 쉬운 방법은 에이전트를 제거하고 다시 설치한 다음 필요한 지역의 새 자격 증명 모음에 컴퓨터를 등록하는 것입니다.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 에이전트가 Windows Server 2012 중복 제거를 지원합니까?

예. MARS 에이전트는 백업 작업을 준비할 때 중복 제거된 데이터를 일반 데이터로 변환합니다. 그런 다음 백업을 위해 데이터를 최적화하고 데이터를 암호화한 다음 암호화된 데이터를 볼트로 보냅니다.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>MARS 에이전트를 설치하고 구성하려면 관리자 권한이 필요합니까?

예. MARS 에이전트를 설치하고 MARS 콘솔을 사용하여 백업을 구성하려면 사용자가 보호된 서버의 로컬 관리자여야 합니다.

## <a name="manage-backups"></a>백업 관리

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>백업용으로 구성된 Windows 컴퓨터의 이름을 바꾸면 어떻게 되나요?

Windows 컴퓨터의 이름을 바꾸면 현재 구성된 모든 백업이 중지됩니다.

* 백업 자격 증명 모음에 새 컴퓨터 이름을 등록해야 합니다.
* 볼트에 새 이름을 등록하면 첫 번째 작업은 *전체* 백업입니다.
* 이전 서버 이름으로 볼트에 백업된 데이터를 복구해야 하는 경우 이 옵션을 사용하여 데이터 복구 마법사의 대체 위치로 복원합니다. [자세히 알아보기](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>백업의 최대 파일 경로 길이는 얼마입니까?

MARS 에이전트는 NTFS를 사용하며 [Windows API에](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)의해 제한된 파일 경로 길이 사양을 사용합니다. 보호하려는 파일이 허용된 값보다 긴 경우 상위 폴더 또는 디스크 드라이브를 백업합니다.  

### <a name="what-characters-are-allowed-in-file-paths"></a>파일 경로에는 어떤 문자가 허용됩니까?

MARS 에이전트는 NTFS를 사용하며 파일 이름/경로에서 [지원되는 문자를](/windows/desktop/FileIO/naming-a-file#naming-conventions) 허용합니다.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>"이 서버에 대해 Azure 백업이 구성되지 않았습니다" 경고가 나타납니다.

이 경고는 백업 정책을 구성한 경우에도 로컬 서버에 저장된 백업 일정 설정이 백업 자격 증명 모음에 저장된 설정과 같지 않은 경우에 나타날 수 있습니다.

* 서버 또는 설정이 알려진 양호한 상태로 복구되면 백업 일정이 동기화되지 않은 상태가 될 수 있습니다.
* 이 경고가 발생하면 백업 정책을 다시 [구성한](backup-azure-manage-windows-server.md) 다음 온디맨드 백업을 실행하여 로컬 서버를 Azure와 다시 동기화합니다.

## <a name="manage-the-backup-cache-folder"></a>백업 캐시 폴더 관리

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>캐시 폴더의 최소 크기 요구 사항은 무엇인가요?

캐시 폴더의 크기는 백업하는 데이터의 양에 따라 결정됩니다.

* 캐시 폴더 볼륨에는 백업 데이터의 총 크기의 5~10% 이상인 여유 공간이 있어야 합니다.
* 볼륨의 여유 공간이 5% 미만인 경우 볼륨 크기를 늘리거나 [다음 단계를](#how-do-i-change-the-cache-location-for-the-mars-agent)수행하여 충분한 공간이 있는 볼륨으로 캐시 폴더를 이동합니다.
* Windows 시스템 상태를 백업하는 경우 캐시 폴더가 포함된 볼륨에 30~35GB의 여유 공간이 추가로 필요합니다.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>스크래치 폴더가 유효하고 액세스 가능한지 확인하는 방법은 무엇입니까?

1. 기본적으로 스크래치 폴더는`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. 스크래치 폴더 위치의 경로가 아래 표시된 레지스트리 키 항목의 값과 일치하는지 확인합니다.

    | 레지스트리 경로 | 레지스트리 키 | 값 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*새 캐시 폴더 위치* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*새 캐시 폴더 위치* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>MARS 에이전트의 캐시 위치를 변경하려면 어떻게 해야 합니까?

1. 백업 엔진을 중지하려면 높은 명령 프롬프트에서 이 명령을 실행합니다.

    ```Net stop obengine```
2. 시스템 상태 백업을 구성한 경우 디스크 관리를 열고 형식의 `"CBSSBVol_<ID>"`이름으로 디스크를 마운트 해제합니다.
3. 기본적으로 스크래치 폴더는`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. 전체 `\Scratch` 폴더를 충분한 공간이 있는 다른 드라이브에 복사합니다. 내용을 이동하지 않고 복사해야 합니다.
5. 새로 이동한 스크래치 폴더의 경로로 다음 레지스트리 항목을 업데이트합니다.

    | 레지스트리 경로 | 레지스트리 키 | 값 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*새 스크래치 폴더 위치* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*새 스크래치 폴더 위치* |

6. 높은 명령 프롬프트에서 백업 엔진을 다시 시작합니다.

    ```command
    Net stop obengine

    Net start obengine
    ```

7. 주문형 백업을 실행합니다. 백업이 새 위치를 사용하여 성공적으로 완료되면 원래 캐시 폴더를 제거할 수 있습니다.

### <a name="where-should-the-cache-folder-be-located"></a>캐시 폴더는 어디에 위치해야 합니까?

캐시 폴더의 다음 위치는 권장되지 않습니다.

* 네트워크 공유/이동식 미디어: 캐시 폴더는 온라인 백업을 사용하여 백업해야 하는 서버에 로컬이어야 합니다. 네트워크 위치 또는 USB 드라이브와 같은 이동식 미디어는 지원되지 않습니다.
* 오프라인 볼륨: Azure 백업 에이전트를 사용하여 예상되는 백업을 위해 캐시 폴더가 온라인 상태입니다.

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>지원되지 않는 캐시 폴더의 특성이 있습니까?

다음과 같은 특성 또는 해당 조합은 캐시 폴더에 지원되지 않습니다.

* 암호화
* 중복 제거
* Compressed
* 스파스
* 재분석 지점

캐시 폴더와 메타데이터 VHD에는 Azure 백업 에이전트에 필요한 특성이 없습니다.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>백업에 사용되는 대역폭의 양을 조정할 수있는 방법이 있습니까?

예. MARS 에이전트의 **속성 변경** 옵션을 사용하여 대역폭과 타이밍을 조정할 수 있습니다. [자세히 알아보기](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>복원

### <a name="manage"></a>관리

**암호를 잊어버린 경우 복구할 수 있습니까?**
Azure Backup 에이전트는 복원 하는 동안 백업 된 데이터를 해독 하기 위해 암호 (등록 하는 동안 제공)가 필요 합니다. 잃어버린 암호를 처리하기위한 옵션을 이해하려면 아래 시나리오를 검토하십시오.

| 오리지널 머신 <br> *(백업이 수행된 소스 컴퓨터)* | 암호 | 사용 가능한 옵션 |
| --- | --- | --- |
| 사용 가능 |실패 |백업이 수행된 원래 컴퓨터를 사용할 수 있고 동일한 복구 서비스 자격 증명 모음에 등록된 경우 다음 [단계를](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)수행하여 암호를 다시 생성할 수 있습니다.  |
| 실패 |실패 |데이터 또는 데이터를 복구할 수 없습니다. |

다음 조건을 확인합니다.

* 동일한 원래 컴퓨터에서 에이전트를 제거하고 다시 등록하는 경우
  * *동일한 암호*, 당신은 당신의 백업 된 데이터를 복원 할 수 있습니다.
  * *다른 암호는*백업 된 데이터를 복원 할 수 없습니다.
* *에이전트를 다른 컴퓨터에* 설치하는 경우
  * 원래 컴퓨터에서 사용되는 *동일한 암호는* 백업 된 데이터를 복원 할 수 있습니다.
  * *다른 암호는*백업 된 데이터를 복원 할 수 없습니다.
* 원래 컴퓨터가 손상되었지만(MARS 콘솔을 통해 암호를 재생성하지 못하게 함) MARS 에이전트가 사용하는 원래 스크래치 폴더를 복원하거나 액세스할 수 있는 경우 복원할 수 있습니다(암호를 잊어버린 경우). 자세한 내용은 고객 지원에 문의하십시오.

**원래 컴퓨터를 분실한 경우(백업이 수행된 경우) 복구하려면 어떻게 해야 합니까?**

원래 컴퓨터의 암호(등록 시 제공한 암호)가 있는 경우 백업된 데이터를 대체 컴퓨터로 복원할 수 있습니다. 아래 시나리오를 검토하여 복원 옵션을 이해합니다.

| 오리지널 머신 | 암호 | 사용 가능한 옵션 |
| --- | --- | --- |
| 실패 |사용 가능 |원래 컴퓨터를 등록하는 동안 제공한 것과 동일한 암호를 통해 다른 컴퓨터에 MARS 에이전트를 설치하고 등록할 수 있습니다. **복구 옵션을** > **선택하여** 복원을 수행할 다른 위치를 선택합니다. 자세한 내용은 이 [문서를](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine)참조하십시오.
| 실패 |실패 |데이터 또는 데이터를 복구할 수 없습니다. |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>진행 중인 복원 작업을 취소하면 어떻게 되나요?

진행 중인 복원 작업이 취소되면 복원 프로세스가 중지됩니다. 취소 전에 복원된 모든 파일은 롤백 없이 구성된 대상(원래 또는 대체 위치)에 유지됩니다.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>MARS 에이전트가 파일, 폴더 및 볼륨에 설정된 ACL을 백업하고 복원합니까?

* MARS 에이전트는 파일, 폴더 및 볼륨에 설정된 ACL을 백업합니다.
* 볼륨 복원 복구 옵션의 경우 MARS 에이전트는 복구 중인 파일 또는 폴더에 대한 ACL 사용 권한을 계속 복원하지 않는 옵션을 제공합니다.
* 개별 파일 및 폴더 복구 옵션의 경우 MARS 에이전트는 ACL 권한으로 복원됩니다(ACL 복원을 건너뛸 수 있는 옵션은 없습니다).

## <a name="next-steps"></a>다음 단계

Windows 컴퓨터를 백업하는 방법에 [대해 알아봅니다.](tutorial-backup-windows-server-to-azure.md)
