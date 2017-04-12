
---
title: "Azure 백업 FAQ | Microsoft 문서"
description: "서비스 작동 방식, Azure Backup 에이전트, Recovery Services 자격 증명 모음 및 백업 및 보존 제한에 대한 일반적인 질문에 대한 대답."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "백업 및 재해 복구; 백업 서비스"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/10/2017
ms.author: markgal;giridham;arunak;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 87d500d886feb2dbd61d15d5a980ab2c4018c2f2
ms.lasthandoff: 04/03/2017


---
# <a name="questions-about-the-azure-backup-service"></a>Azure Backup 서비스에 대한 질문
이 문서에는 Azure Backup 구성 요소를 신속하게 이해할 수 있도록 돕는 일반적인 질문(답변 포함)의 섹션이 있습니다. 대답 중 일부에는 포괄적인 정보를 포함하는 문서에 대한 링크가 있습니다. **의견**(오른쪽)을 클릭하여 Azure Backup에 대한 질문을 할 수 있습니다. 의견은 이 문서의 하단에 나타납니다. Livefyre 계정은 메모가 필수입니다. 또한 [토론 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)에 Azure 백업 서비스에 대한 질문도 게시할 수 있습니다.

이 문서의 섹션을 신속하게 검색하려면 **이 문서** 아래의 오른쪽에서 링크를 사용합니다.


## <a name="recovery-services-vault"></a>복구 서비스 자격 증명 모음

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>각 Azure 구독에 만들 수 있는 자격 증명 모음의 개수에 제한이 있나요? <br/>
예. 2016년 9월을 기준으로 구독 당 25개 Recovery Services 또는 백업 자격 증명 모음을 만들 수 있습니다. 구독당 Azure Backup의 지원되는 지역당 최대 25개의 Recovery Services 자격 증명 모음을 만들 수 있습니다. 추가 자격 증명 모음이 필요한 경우 추가 구독을 만드세요.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>각 자격 증명 모음에 대해 등록할 수 있는 서버/컴퓨터 수에 제한이 있나요? <br/>
예, 자격 증명 모음당 컴퓨터를 최대 50대까지 등록할 수 있습니다. Azure IaaS 가상 컴퓨터의 경우 자격 증명 모음당 200대 VM으로 제한됩니다. 더 많은 컴퓨터를 등록해야 할 경우 다른 자격 증명 모음을 만드세요.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>다른 데이터 센터에 내 서버를 등록하려면 어떻게 해야 하나요?<br/>
백업 데이터는 등록된 자격 증명 모음의 데이터 센터로 전송됩니다. 데이터 센터를 변경하는 가장 쉬운 방법은 에이전트를 제거하고 다시 설치한 후 원하는 데이터 센터에 속한 새 자격 증명 모음에 등록하는 것입니다.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>내 조직에 하나의 자격 증명 모음이 있는 경우 데이터를 복원할 때 서버 간에 데이터를 어떻게 격리할 수 있나요?<br/>
동일한 자격 증명 모음에 등록된 모든 서버는 *동일한 암호를 사용*하는 다른 서버에서 백업된 데이터를 복구할 수 있습니다. 조직의 다른 서버에서 백업 데이터를 격리하고 싶은 서버가 있는 경우 해당 서버에 대해 지정된 암호를 사용합니다. 예를 들어 인사부 서버가 첫 번째 암호화 암호를 사용하고, 회계 서버가 두 번째, 저장소 서버가 세 번째 암호화 암호를 사용할 수 있습니다.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>캐시 폴더의 최소 크기 요구 사항은 무엇인가요? <br/>
캐시 폴더의 크기는 백업하는 데이터의 양에 따라 결정됩니다. 캐시 폴더는 데이터 저장에 필요한 공간의 5%여야 합니다.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>구독 간에 내 백업 데이터 또는 자격 증명 모음을 “마이그레이션”할 수 있나요? <br/>
아니요. 자격 증명 모음은 구독 수준에서 만들어지며 생성된 후에는 다른 구독에 다시 할당할 수 없습니다.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Recovery Services 자격 증명 모음은 Resource Manager에 기반합니다. Backup 자격 증명 모음(기본 모드)은 계속 지원되나요? <br/>
[클래식 포털](https://manage.windowsazure.com)의 모든 기존 Backup 자격 증명 모음은 계속 지원됩니다. 하지만 새 Backup 자격 증명 모음을 배포하는 데 더 이상 클래식 포털을 사용할 수 없습니다. 향후 향상되는 기능이 Recovery Services 자격 증명 모음에만 적용되므로 Microsoft에서는 모든 배포에 Recovery Services 자격 증명 모음을 사용하도록 권장합니다. 클래식 포털에서 Backup 자격 증명 모음을 만들려는 경우 [Azure Portal](https://portal.azure.com)에 리디렉션됩니다.

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>복구 서비스 자격 증명 모음에 백업 자격 증명 모음을 마이그레이션할 수 있나요? <br/>
아니요, 안타깝지만 Backup 자격 증명 모음의 내용을 Recovery Services 자격 증명 모음에 마이그레이션할 수 없습니다. 이 기능을 추가하려고 노력하고 있지만 현재는 사용할 수 없습니다.

### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>복구 서비스 자격 증명 모음은 클래식 VM 또는 Resource Manager 기반 VM을 지원하나요? <br/>
복구 서비스 자격 증명 모음은 두 모델을 모두 지원합니다.  Recovery Services 자격 증명 모음에 클래식 포털에서 만들어진 클래식 VM 또는 Azure Portal에서 만들어진 Resource Manager VM을 백업할 수 있습니다.

### <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Backup 자격 증명 모음에 내 클래식 VM을 백업했습니다. 클래식 모드에서 Resource Manager 모드로 내 VM을 마이그레이션하고 Recovery Services 자격 증명 모음에서 보호할 수 있나요?
클래식 모드에서 Resource Manager 모드로 VM을 바꾸는 경우 백업 자격 증명 모음에 있는 클래식 VM 복구 시점은 Recovery Services 자격 증명 모음에 자동으로 마이그레이션되지 않습니다. VM 백업을 전송하려면 다음 단계를 수행합니다.

1. Backup 자격 증명 모음에서 **보호된 항목** 탭으로 이동하고 VM을 선택합니다. [보호 중지](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines)를 클릭합니다. *연결된 백업 데이터 삭제* 옵션을 **검사하지 않음**으로 둡니다.
2. 클래식 모드에서 Resource Manager 모드로 가상 컴퓨터를 마이그레이션합니다. 가상 컴퓨터에 해당하는 저장소 및 네트워크 정보가 Resource Manager 모드로 마이그레이션되도록 합니다.
3. 자격 증명 모음 대시보드를 기반으로 **백업** 작업을 사용하여 Recovery Services 자격 증명 모음을 만들고 마이그레이션된 가상 컴퓨터에 백업을 구성합니다. Recovery Services 자격 증명 모음에 VM을 백업하는 방법에 대한 자세한 내용은 [Recovery Services 자격 증명 모음으로 Azure VM 보호](backup-azure-vms-first-look-arm.md) 문서를 참조하세요.



## <a name="azure-backup-agent"></a>Azure Backup 에이전트

### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>최신 Azure 백업 에이전트를 어디서 다운로드할 수 있나요? <br/>
Windows Server, System Center DPM 또는 Windows 클라이언트를 백업하기 위한 최신 에이전트를 [여기](http://aka.ms/azurebackup_agent)에서 다운로드할 수 있습니다. 가상 컴퓨터를 백업하려는 경우 VM 에이전트(적절한 확장을 자동으로 설치)를 사용합니다. Azure 갤러리에서 만든 가상 컴퓨터에는 VM 에이전트가 이미 있습니다.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Azure 백업 에이전트를 구성할 때 저장소 자격 증명을 입력하라는 메시지가 표시됩니다. 저장소 자격 증명은 만료되나요?
예, 저장소 자격 증명은 48시간이 지나면 만료됩니다. 파일이 만료되면 Azure 포털에 로그인하고 해당 자격 증명 모음에서 저장소 자격 증명 파일을 다운로드하세요.

### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Azure에 데이터를 백업하는 Windows 서버 이름을 바꾸면 어떻게 되나요?<br/>
서버 이름을 바꾸면 현재 구성된 모든 백업이 중지됩니다.
백업 자격 증명 모음에 서버의 새 이름을 등록합니다. 자격 증명 모음에 새 이름을 등록하면 첫 번째 백업 작업은 *전체* 백업입니다. 이전 서버 이름으로 자격 증명 모음에 백업된 데이터를 복원해야 하는 경우에는 **데이터 복구** 마법사의 [**다른 서버**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) 옵션을 사용합니다.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>어떤 드라이브 유형의 파일 및 폴더를 백업할 수 있나요? <br/>
다음과 같은 드라이브/볼륨은 백업할 수 없습니다.

* 이동식 미디어: 모든 백업 항목 원본은 고정된 것으로 보고되어야 합니다.
* 읽기 전용 볼륨: VSS(볼륨 섀도 복사본 서비스)가 작동하려면 볼륨에 데이터 쓰기가 가능해야 합니다.
* 오프라인 볼륨: VSS가 작동하려면 볼륨이 온라인 상태여야 합니다.
* 네트워크 공유: 온라인 백업을 사용하여 백업할 서버의 로컬 볼륨이어야 합니다.
* Bitlocker로 보호된 볼륨: 볼륨의 잠금을 해제해야 백업이 실행될 수 있습니다.
* 파일 시스템 식별: NTFS가 유일하게 지원되는 파일 시스템입니다.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>내 서버에서 어떤 파일 및 폴더 형식을 백업할 수 있나요?<br/>
다음과 같은 형식이 지원됩니다.

* 암호화
* 압축
* 스파스
* 압축 + 스파스
* 하드 링크: 지원되지 않음, 건너뜀
* 재분석 지점: 지원되지 않음, 건너뜀
* 암호화 + 스파스: 지원되지 않음, 건너뜀
* 압축 스트림: 지원되지 않음, 건너뜀
* 스파스 스트림: 지원되지 않음, 건너뜀

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Azure Backup 에이전트를 사용하여 Backup 정책에서 지정할 수 있는 최대 파일 경로 길이는 무엇인가요? <br/>
Azure 백업 에이전트는 NTFS에 의존합니다. [파일 경로 길이 사양은 Windows API에 의해 제한됩니다](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). 보호하려는 파일의 경로가 Windows API에 허용되는 길이보다 긴 경우 상위 폴더 또는 디스크 드라이브를 백업합니다.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Azure 백업 에이전트를 사용하는 Azure 백업 정책의 파일 경로에 어떤 문자가 허용되나요? <br>
 Azure 백업 에이전트는 NTFS에 의존합니다. 파일 사양의 일부분으로 [NTFS 지원 문자](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) 를 사용할 수 있습니다.  

### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Azure 백업 에이전트에 대해 지정된 캐시 위치를 변경하려면 어떻게 해야 하나요?<br/>
다음 목록을 사용하여 캐시 위치를 변경합니다.

* 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 백업 엔진을 중지합니다.

```PS C:\> Net stop obengine```
* 파일을 이동하지 마십시오. 대신 캐시 공간 폴더를 충분한 공간이 있는 다른 드라이브로 복사합니다. 백업이 새 캐시 공간으로 작업 중임을 확인한 후 원래 캐시 공간을 제거할 수 있습니다.
* 다음 레지스트리 항목을 새 캐시 공간 폴더의 경로로 업데이트합니다.<br/>

| 레지스트리 경로 | 레지스트리 키 | 값 |
| --- | --- | --- |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*새 캐시 폴더 위치* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*새 캐시 폴더 위치* |

* 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 백업 엔진을 다시 시작합니다.

```PS C:\> Net start obengine```

새 캐시 위치로 성공적으로 백업 작성이 완료되면 원래 캐시 폴더를 제거할 수 있습니다.

### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>어디에 Azure 백업 에이전트에 대한 캐시 폴더를 설치해야 예상대로 작동할 수 있나요?<br/>
다음 위치는 캐시 폴더에 권장되지 않습니다.

* 네트워크 공유 또는 이동식 미디어: 캐시 폴더는 온라인 백업을 사용하여 백업해야 하는 서버에 대해 로컬에 있어야 합니다. 네트워크 위치 또는 USB 드라이브 같은 이동식 미디어는 지원되지 않습니다.
* 오프라인 볼륨: 캐시 폴더는 Azure 백업 에이전트를 사용하는 예상된 백업을 위해 온라인 상태여야 합니다.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>지원되지 않는 캐시 폴더의 특성이 있나요?<br/>
다음과 같은 특성 또는 해당 조합은 캐시 폴더에 지원되지 않습니다.

* 암호화
* 중복 제거
* 압축
* 스파스
* 재분석 지점

캐시 폴더와 메타데이터 VHD에는 모두 Azure Backup 에이전트에 필요한 특성이 없습니다.


## <a name="virtual-machines"></a>가상 컴퓨터

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>VM 확장을 사용하여 Azure 백업 서비스에서 이미 지원하는 Azure VM에 Azure 백업 에이전트를 설치할 수 있나요? <br/>
그렇습니다. Azure 백업은 VM 확장을 사용하여 Azure VM에 대한 VM 수준 백업을 제공합니다. 게스트 Windows OS의 파일 및 폴더를 보호하려면 게스트 Windows OS에 Azure Backup 에이전트를 설치합니다.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Azure VM에 Azure 백업 에이전트를 설치하여 Azure VM에서 제공하는 임시 저장소에 존재하는 파일 및 폴더를 백업할 수 있나요? <br/>
예. 게스트 Windows OS에 Azure Backup 에이전트를 설치하고 임시 저장소에 파일 및 폴더를 백업합니다. 임시 저장소 데이터가 초기화된 후에는 백업 작업이 실패합니다. 또한 임시 저장소 데이터가 삭제된 경우 비휘발성 저장소에만 복원할 수 있습니다.


## <a name="azure-backup-server-and-data-protection-manager"></a>Azure Backup Server 및 Data Protection Manager

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Azure 백업 서버를 사용하여 물리적 서버에 대한 BMR(완전 복구) 백업을 만들 수 있나요? <br/>
예.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>지원되는 System Center Data Protection Manager의 버전은 무엇인가요? <br/>
System Center DPM(Data Protection Manager)용 최신 업데이트 롤업(UR)에 [최신](http://aka.ms/azurebackup_agent) Azure Backup 에이전트를 설치하는 것이 좋습니다. 2016년 8월을 기준으로 업데이트 롤업 11이 최신 업데이트입니다.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>내 파일 및 폴더를 보호하기 위해 Azure 백업 에이전트를 설치했습니다. 이제 System Center DPM을 설치하여 Azure에 온-프레미스 응용 프로그램/VM 워크로드를 보호하기 위해 Azure Backup 에이전트를 사용할 수 있나요? <br/>
System Center DPM(Data Protection Manager)과 함께 Azure Backup을 사용하려면 먼저 DPM을 설치하고 Azure Backup 에이전트를 설치합니다. Azure Backup 구성 요소를 이 순서대로 설치하면 Azure Backup 에이전트는 DPM과 함께 작동합니다. DPM을 설치하기 전에 Azure Backup 에이전트를 설치하는 것이 권장되거나 지원되지 않습니다.


## <a name="how-azure-backup-works"></a>Azure Backup 작동 방식

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Azure Backup 에이전트는 Windows 2012 중복제거를 사용하는 서버에서 작동합니까? <br/>
예. 에이전트 서비스는 백업 작업을 준비할 때 중복 제거 된 데이터를 일반 데이터로 변환 합니다. 그 다음 백업에 대한 데이터를 최적화하고 데이터를 암호화한 다음 온라인 백업 서비스에 암호화된 데이터를 보냅니다.

### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>백업이 시작된 후 백업 작업을 취소하면 전송된 백업 데이터가 삭제되나요? <br/>
안 됩니다. 백업 작업이 취소되기 전에 자격 증명 모음으로 전송된 모든 데이터는 자격 증명 모음에 유지됩니다. Azure 백업은 백업하는 동안 백업 데이터에 검사점을 간혹 추가하는 검사점 메커니즘을 사용합니다. 백업 데이터에 검사점이 있기 때문에 다음 백업 프로세스에서는 파일의 무결성을 유효성 검사할 수 있습니다. 다음 백업 작업은 이전에 백업한 데이터에 대해 증분됩니다. 증분 백업은 새 데이터 또는 변경된 데이터만 전송하므로 대역폭의 사용률을 개선합니다.

Azure VM에 대한 백업 작업을 취소하면 모든 전송된 데이터는 무시됩니다. 다음 백업 작업은 마지막으로 성공한 백업 작업에서 증분 데이터를 전송합니다.

### <a name="if-a-backup-job-fails-can-i-configure-the-backup-service-to-send-e-mail-br"></a>백업 작업이 실패하는 경우 메일을 보내는 Backup 서비스를 구성할 수 있나요? <br/>
예, 백업 서비스에는 PowerShell 스크립트와 함께 사용할 수 있는 몇 가지 이벤트 기반 알림이 있습니다. 전체 설명은 [구성 알림](backup-azure-monitor-vms.md#configure-notifications)을 참조하세요.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>백업 작업을 예약할 수 있는 시간 또는 횟수에 제한이 있나요?<br/>
예. Windows Server 또는 Windows 워크스테이션에서는 하루 최대 3번까지 백업 작업을 실행할 수 있습니다. System Center DPM에서는 하루 2번까지 백업 작업을 실행할 수 있습니다. IaaS VM의 경우 하루에 한 번 백업 작업을 실행할 수 있습니다. Windows Server 또는 Windows 워크스테이션에 대해 예약 정책을 사용하여 일별 또는 주별 일정을 지정할 수 있습니다. System Center DPM을 사용하여 일별, 주별, 월별, 연도별로 일정을 지정할 수 있습니다.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Recovery Services 자격 증명 모음에 전송된 데이터가 백업한 데이터의 크기보다 작은 이유는 무엇인가요?<br/>
 Azure 백업 에이전트 또는 SCDPM 또는 Azure 백업 서버에서 백업된 모든 데이터를 전송하기 전에 압축하고 암호화합니다. 압축 및 암호화를 적용하면 백업 자격 증명 모음에 있는 데이터 크기가 30-40% 줄어듭니다.

 ### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>백업 서비스에서 사용되는 대역폭의 양을 조정하는 방법이 있나요?<br/>
  예, 백업 에이전트에서 **속성 변경** 옵션을 사용하여 대역폭을 조정합니다. 대역폭을 사용하는 경우 시간과 대역폭의 양을 조정할 수 있습니다. 단계별 지침은 **[네트워크 제한 사용](backup-configure-vault.md#enable-network-throttling)**을 참조하세요.



## <a name="what-can-i-back-up"></a>어떤 것을 백업할 수 있나요?

### <a name="which-operating-systems-do-azure-backup-support-br"></a>Azure Backup에서 지원하는 운영 체제는 무엇인가요? <br/>
Azure Backup은 Azure Backup Server 및 System Center DPM(Data Protection Manager)을 사용하여 보호되는 파일과 폴더 및 워크로드 응용 프로그램의 백업을 위한 다음 목록의 운영 체제를 지원합니다.

| 운영 체제 | 플랫폼 | SKU |
|:--- | --- |:--- |
| Windows 8 및 최신 SP |64비트 |Enterprise, Pro |
| Windows 7 및 최신 SP |64비트 |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 및 최신 SP |64비트 |Enterprise, Pro |
| Windows 10 |64비트 |Enterprise, Pro, Home |
| Windows Server 2016 |64비트 |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 및 최신 SP |64비트 |Standard, Datacenter, Foundation |
| Windows Server 2012 및 최신 SP |64비트 |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 및 최신 SP |64비트 |Standard, Workgroup |
| Windows Storage Server 2012 및 최신 SP |64비트 |Standard, Workgroup |
| Windows Server 2012 R2 및 최신 SP |64비트 |Essential |
| Windows Server 2008 R2 SP1 |64비트 |Standard, Enterprise, Datacenter, Foundation |
| Windows Server 2008 SP2 |64비트 |Standard, Enterprise, Datacenter, Foundation |

**Azure VM 백업의 경우:**

* **Linux**: Azure 백업은 Core OS Linux를 제외한 [Azure 인증 배포 목록](../virtual-machines/linux/endorsed-distros.md) 을 지원합니다.  가상 컴퓨터에서 VM 에이전트를 사용할 수 있고 Python에 대한 지원이 지속하는 한 기타 Bring-Your-Own-Linux 배포도 작동합니다.
* **Windows Server**: Windows Server 2008 R2 이전 버전은 지원되지 않습니다.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>백업하는 각 데이터 원본의 크기에 제한이 있나요? <br/>
자격 증명 모음에 백업할 수 있는 데이터의 양에는 제한이 없습니다. Azure Backup은 데이터 원본에 대한 최대 크기를 제한하지만 이러한 한도는 큽니다. 2015년 8월 기준으로, 지원되는 운영 체제의 최대 크기 데이터 원본은 다음과 같습니다.

| S.No | 운영 체제 | 데이터 원본의 최대 크기 |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 이상 |54,400GB |
| 2 |Windows 8 이상 |54,400GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700GB |
| 4 |Windows 7 |1700GB |

다음 표에서는 각 데이터 원본 크기가 어떻게 결정되는지를 설명합니다.

| 데이터 원본 | 세부 정보 |
|:---:|:--- |
| 볼륨 |서버 또는 클라이언트 컴퓨터의 단일 볼륨에서 백업되는 데이터 양 |
| Hyper-V 가상 컴퓨터 |백업되는 가상 컴퓨터의 모든 VHD 데이터 합계 |
| Microsoft SQL Server 데이터베이스 |백업되는 단일 SQL 데이터베이스 크기 |
| Microsoft SharePoint |백업되는 SharePoint 팜 내의 콘텐츠 및 구성 데이터베이스 합계 |
| Microsoft Exchange |백업되는 Exchange 서버의 모든 Exchange 데이터베이스 합계 |
| BMR/시스템 상태 |백업되는 컴퓨터의 각 개별 BMR 복사본 또는 시스템 상태 |



## <a name="retention-policy-and-recovery-points"></a>보존 정책 및 복구 지점

### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>DPM과 Windows Server/클라이언트(즉, DPM이 없는 Windows Server)에 대한 보존 정책 간에 차이가 있나요?<br/>
아니요, DPM 및 Windows Server/클라이언트 모두 일별, 주별, 월별, 연도별 보존 정책을 포함합니다.

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>내 보존 정책을 선택적으로 구성할 수 있나요? 즉, 연도별, 월별 정책은 구성하지 않고 주별, 일별 정책을 구성할 수 있나요?<br/>
예, Azure 백업 보존 구조를 사용하여 사용자의 요구 사항에 따라 유연하게 보존 정책을 정의할 수 있습니다.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>오후 6시에 "백업을 예약"하고 다른 시간에 "보존 정책"을 지정할 수 있나요?<br/>
안 됩니다. 보존 정책은 백업 지점에만 적용할 수 있습니다. 다음 이미지에서는 보존 정책이 오전 12시와 오후 6시에 수행되는 백업에 지정됩니다. <br/>

![백업 일정 및 보존](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled-br"></a>증분 복사본은 예약된 보존 정책에 따라 전송되나요? <br/>
아니요, 증분 복사본은 백업 일정 페이지에 언급된 시간에 따라 전송됩니다. 보존할 수 있는 지점은 보존 정책에 따라 결정됩니다.

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>오랜 시간 동안 백업을 유지하면 오래된 데이터 지점을 복구하는 데 시간이 더 걸리나요? <br/>
아니요 - 가장 오래된 지점이나 최신 지점을 복구하는 시간은 같습니다. 각 복구 지점은 전체 지점처럼 동작합니다.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>각 복구 지점이 전체 지점과 같은 경우 총 청구 가능 백업 저장소에 영향을 주나요?<br/>
일반적인 장기 보존 지점 제품은 백업 데이터를 전체 지점으로 저장합니다. 전체 지점은 저장소를 *비효율적* 으로 사용하지만 복원은 쉽고 빠릅니다. 증분 복사본은 저장소를 *효율적* 으로 사용하지만 데이터 체인을 복원해야 하며 이는 복구 시간에 영향을 줍니다. Azure 백업 저장소 아키텍처는 데이터를 빠르게 복원할 수 있게 최적화하여 저장하고 저장소 비용을 낮춰 두 가지 이점을 모두 제공합니다. 이 데이터 저장소 방법을 사용하면 수신 및 발신 대역폭이 효율적으로 사용됩니다. 데이터를 복구하는 데 필요한 데이터 저장소와 시간의 양이 최소로 유지됩니다. [증분 백업](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) 저장이 효율적인 방법에 대한 자세한 알아봅니다.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>만들 수 있는 복구 지점의 수에 제한이 있나요?<br/>
보호된 인스턴스당 최대 9999개의 복구 지점을 만들 수 있습니다. 보호된 인스턴스는 Azure에 데이터를 백업하도록 구성된 컴퓨터, 서버(실제 또는 가상) 또는 워크로드입니다. 백업 자격 증명 모음당 보호되는 인스턴스의 수에는 한도가 없습니다. 자세한 내용은 [백업 및 보존](./backup-introduction-to-azure-backup.md#backup-and-retention)과 [보호된 인스턴스란 무엇인가요](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)?의 설명을 참조하세요.

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Azure에 백업된 데이터는 몇 번 복구할 수 있나요?<br/>
Azure 백업에서 수행할 수 있는 복구 횟수에는 제한이 없습니다.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>데이터를 복원할 때 Azure의 송신 트래픽에 대해 요금을 납부하나요? <br/>
아니요. 복구는 무료이며 송신 트래픽에 요금이 부과되지는 않습니다.

### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>백업 정책을 구성했는데도 "Azure Backup이 이 서버에 대해 구성되지 않았습니다"라는 경고가 표시됩니다. <br/>
이 경고는 로컬 서버에 저장된 백업 일정 설정과 백업 자격 증명 모음에 저장된 설정이 동일하지 않은 경우에 발생합니다. 서버 혹은 설정이 좋은 상태로 복구된 경우, 백업 일정은 동기화를 잃을 수 있습니다. 이 경고가 발생하면 [백업 정책을 다시 구성](backup-azure-manage-windows-server.md) 한 다음 **지금 백업 실행** 을 수행하여 로컬 서버를 Azure와 다시 동기화합니다.



## <a name="azure-backup-encryption"></a>Azure Backup 암호화

### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Azure에 전송되는 데이터는 암호화되나요? <br/>
예. 데이터는 온-프레미스 서버/클라이언트/SCDPM 컴퓨터에서 AES256으로 암호화되어 보안 HTTPS 링크를 통해 전송됩니다.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Azure의 백업 데이터도 암호화되나요?<br/>
예. Azure에 전송되는 데이터는 암호화 상태(휴지 상태)를 유지합니다. Microsoft는 어떠한 경우에도 백업 데이터를 암호 해독하지 않습니다. Azure VM을 백업할 때 Azure Backup은 가상 컴퓨터의 암호화가 필요합니다. 예를 들어, Azure Disk Encryption 또는 다른 암호화 기술을 사용하여 VM을 암호화하는 경우 Azure Backup은 해당 암호화를 사용하여 데이터를 보호합니다.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>백업 데이터를 암호화하는 데 사용되는 암호화 키의 최소 길이는 어느 정도인가요? <br/>
암호화 키는 16자 이상이어야 합니다.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>암호화 키를 잃어버리면 어떻게 되나요? 제가 데이터를 복구할 수 있나요? 아니면 Microsoft가 데이터를 복구할 수 있나요? <br/>
백업 데이터를 암호화하는 데 사용되는 키는 고객 사업장에만 존재합니다. Microsoft는 Azure에 복사본을 유지하지 않으며 키에 대한 어떠한 액세스 권한도 없습니다. 고객이 키를 잃어버릴 경우 Microsoft는 백업 데이터를 복구할 수 없습니다.

