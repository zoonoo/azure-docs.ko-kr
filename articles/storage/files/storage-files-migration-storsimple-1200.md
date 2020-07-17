---
title: Azure File Sync로 StorSimple 1200 마이그레이션
description: Azure File Sync로 StorSimple 1200 시리즈 가상 어플라이언스를 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d9cf7b3cf996e41f90e3a40a6ee08d0fd51c8457
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510340"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Azure File Sync로 StorSimple 1200 마이그레이션

StorSimple 1200 시리즈는 온-프레미스 데이터 센터에서 실행 되는 가상 어플라이언스입니다. 이 어플라이언스에서 Azure File Sync 환경으로 데이터를 마이그레이션할 수 있습니다. Azure File Sync는 StorSimple 어플라이언스를 마이그레이션할 수 있는 기본 및 전략적 장기 Azure 서비스입니다.

StorSimple 1200 시리즈는 12 월 2022에 [수명 종료](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) 에 도달 합니다.  가능한 한 빨리 마이그레이션 계획을 시작 하는 것이 중요 합니다. 이 문서에서는 성공적인 마이그레이션에 대 한 필요한 배경 지식 및 마이그레이션 단계를 Azure File Sync으로 제공 합니다. 

## <a name="azure-file-sync"></a>Azure 파일 동기화

> [!IMPORTANT]
> Microsoft는 마이그레이션하는 고객을 지원 하기 위해 최선을 다하고 있습니다. AzureFilesMigration@microsoft마이그레이션 중에 지원 뿐만 아니라 사용자 지정 된 마이그레이션 계획에 대 한 Email.

Azure File Sync는 다음과 같은 두 가지 주요 구성 요소를 기반으로 하는 Microsoft 클라우드 서비스입니다.

* 파일 동기화 및 클라우드 계층화.
* 파일 공유는 SMB 및 파일 REST와 같은 여러 프로토콜을 통해 액세스할 수 있는 Azure의 기본 저장소로 공유 됩니다. Azure 파일 공유는 기본적으로 네트워크 드라이브로 탑재할 수 있는 Windows 서버의 파일 공유와 비교할 수 있습니다. 특성, 권한 및 타임 스탬프와 같은 중요 한 파일 충실도 측면을 지원 합니다. StorSimple과 달리 응용 프로그램/서비스는 클라우드에 저장 된 파일 및 폴더를 해석 하는 데 필요 하지 않습니다. 클라우드의 일부 응용 프로그램 데이터 뿐만 아니라 범용 파일 서버 데이터를 저장 하는 이상적인 가장 유연한 방법입니다.

이 문서에서는 마이그레이션 단계를 중점적으로 설명 합니다. 마이그레이션하기 전에 Azure File Sync에 대해 자세히 알아보려면 다음 문서를 수행 하는 것이 좋습니다.

* [Azure File Sync-개요](https://aka.ms/AFS "개요")
* [Azure File Sync-배포 가이드](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>마이그레이션 목표

목표는 가용성 보장 뿐만 아니라 프로덕션 데이터의 무결성을 보장 하는 것입니다. 후자를 사용 하려면 최소 가동 중지 시간을 유지 하 여 정기적인 유지 관리 기간에만 또는 약간 초과할 수 있도록 해야 합니다.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Azure File Sync에 대 한 StorSimple 1200 마이그레이션 경로

Azure File Sync 에이전트를 실행 하려면 로컬 Windows 서버가 필요 합니다. Windows Server는 최소 2012R2 서버에 있을 수 있지만 Windows Server 2019입니다.

다 수의 대체 마이그레이션 경로가 있으며,이를 통해 모든 항목을 문서화 하 고,이 문서에서 모범 사례로 인해 발생 하는 경로에 대 한 위험 또는 단점을 보여 주는 이유를 설명 하는 문서가 너무 깁니다.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="마이그레이션 경로이 문서의 아래에 있는 단계에 대 한 개요입니다.":::

이전 이미지는이 문서의 섹션에 해당 하는 단계를 보여 줍니다.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>1 단계: 온-프레미스 Windows Server 및 저장소 프로 비전

1. 최소 2012R2-가상 머신 또는 물리적 서버로 Windows Server 2019을 만듭니다. Windows Server 장애 조치 (failover) 클러스터도 지원 됩니다.
2. 직접 연결 된 저장소 (지원 되지 않는 NAS와 비교 하 여 DAS)를 프로 비전 하거나 추가 합니다. Windows Server 저장소 크기는 가상 StorSimple 1200 어플라이언스의 사용 가능한 용량 크기 보다 크거나 같아야 합니다.

### <a name="step-2-configure-your-windows-server-storage"></a>2 단계: Windows Server 저장소 구성

이 단계에서는 StorSimple 저장소 구조 (볼륨 및 공유)를 Windows Server 저장소 구조에 매핑합니다.
저장소 구조를 변경 하려는 경우 (예를 들어 볼륨 수, 볼륨에 데이터 폴더 연결 또는 현재 SMB/NFS 공유의 상위 또는 하위 폴더 구조), 이제 이러한 변경을 고려 하는 시간이 소요 됩니다.
Azure File Sync 구성 된 후 파일 및 폴더 구조를 변경 하는 것은 번거로울 수 있으므로 피해 야 합니다.
이 문서에서는 사용자가 1:1을 매핑 한다고 가정 하므로이 문서의 단계를 수행 하는 경우 매핑을 고려 하 여 매핑을 변경 해야 합니다.

* 프로덕션 데이터는 Windows Server 시스템 볼륨에서 끝나지 않습니다. 클라우드 계층화는 시스템 볼륨에서 지원 되지 않습니다. 그러나이 기능은 마이그레이션 및 StorSimple 교체로 서 연속 작업에 필요 합니다.
* StorSimple 1200 가상 어플라이언스와 동일한 수의 볼륨을 Windows Server에 프로 비전 합니다.
* 필요한 Windows Server 역할, 기능 및 설정을 구성 합니다. OS를 안전 하 고 최신 상태로 유지 하기 위해 Windows Server 업데이트를 옵트인 (opt in) 하는 것이 좋습니다. 마찬가지로 Azure File Sync 에이전트를 포함 하 여 Microsoft 응용 프로그램을 최신 상태로 유지 하기 위해 Microsoft 업데이트를 옵트인 하는 것이 좋습니다.
* 다음 단계를 읽기 전에 폴더 또는 공유를 구성 하지 마십시오.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>3 단계: 첫 번째 Azure File Sync 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>4 단계: 로컬 볼륨 및 폴더 구조를 Azure File Sync 및 Azure 파일 공유 리소스와 일치

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>5 단계: Azure 파일 공유 프로 비전

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>6 단계: Windows Server 대상 폴더 구성

이전 단계에서는 동기화 토폴로지의 구성 요소를 결정 하는 모든 측면을 고려 했습니다. 업로드할 파일을 수신 하도록 서버를 준비 하는 시간입니다.

각각의 Azure 파일 공유에 동기화 되는 **모든** 폴더를 만듭니다.
앞에서 설명한 폴더 구조를 따르는 것이 중요 합니다. 예를 들어 여러 로컬 SMB 공유를 단일 Azure 파일 공유로 동기화 하기로 결정 한 경우 볼륨의 공통 루트 폴더 아래에 저장 해야 합니다. 이제 볼륨에서이 대상 루트 폴더를 만듭니다.

프로 비전 한 Azure 파일 공유의 수는이 단계에서 만든 폴더 수 + 루트 수준에서 동기화 할 볼륨 수와 일치 해야 합니다.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>7 단계: Azure File Sync 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>8 단계: 동기화 구성

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **클라우드 계층화를 켜야 합니다.** 이는 로컬 서버에 StorSimple 클라우드 저장소에 있는 데이터의 전체 크기를 저장 하는 데 충분 한 공간이 없는 경우에 필요 합니다. 일시적으로 마이그레이션에 대 한 계층화 정책을 99%의 사용 가능한 볼륨 공간으로 설정 합니다.

동기화 그룹 만들기 및 동기화를 위해 구성 해야 하는 모든 Azure 파일 공유/서버 위치에 대 한 서버 끝점으로 일치 서버 폴더 추가의 단계를 반복 합니다.

### <a name="step-9-copy-your-files"></a>9 단계: 파일 복사

기본적인 마이그레이션 방식은 StorSimple 가상 어플라이언스에서 Windows 서버로의 RoboCopy 이며 Azure 파일 공유에 대 한 Azure File Sync입니다.

Windows Server 대상 폴더에 대 한 첫 번째 로컬 복사본을 실행 합니다.

* 가상 StorSimple 어플라이언스의 첫 번째 위치를 식별 합니다.
* 이미 Azure File Sync 구성 되어 있는 Windows Server의 일치 하는 폴더를 식별 합니다.
* RoboCopy를 사용 하 여 복사 시작

다음 RoboCopy 명령은 StorSimple Azure 저장소에서 로컬 StorSimple으로 파일을 회수 한 다음 Windows Server 대상 폴더로 이동 합니다. Windows Server가 Azure 파일 공유와 동기화 합니다. 로컬 Windows Server 볼륨이 가득 차면 클라우드 계층화는 이미 동기화 된 및 계층 파일에서 시작 됩니다. 클라우드 계층화는 StorSimple 가상 어플라이언스에서 복사를 계속 하는 데 충분 한 공간을 생성 합니다. 클라우드 계층화는 한 시간에 한 번 확인 하 여 동기화 된 항목을 확인 하 고 디스크 공간을 확보 하 여 99%의 사용 가능한 볼륨 공간에 도달 합니다.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

배경:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      는 RoboCopy가 다중 스레드를 실행할 수 있도록 합니다. 기본값은 8이 고 최대값은 128입니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      로그 파일에 대 한 상태를 유니코드로 출력 합니다 (기존 로그 덮어쓰기).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /T
   :::column-end:::
   :::column span="1":::
      콘솔 창에 출력 합니다. 로그 파일에 대 한 출력과 함께 사용 됩니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      백업 응용 프로그램에서 사용 하는 것과 동일한 모드에서 RoboCopy를 실행 합니다. 이를 통해 RoboCopy는 현재 사용자에 게 권한이 없는 파일을 이동할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      에서이 RoboCopy 명령을 여러 번 실행 하 여 동일한 대상/대상에 순차적으로 실행할 수 있습니다. 이전에 복사 된 항목을 식별 하 고 생략 합니다. 마지막으로 실행 한 이후에 발생 한 변경 내용, 추가 내용 및 "*삭제*"만 처리 됩니다. 이전에 명령을 실행 하지 않은 경우에는 아무 것도 생략 됩니다. 이는 여전히 적극적으로 사용 되 고 변경 되는 원본 위치에 대 한 뛰어난 옵션입니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      파일 복사의 충실도 (기본값은/COPY: DAT), 복사 플래그: D = 데이터, A = 특성, T = 타임 스탬프, S = 보안 = NTFS Acl, O = 소유자 정보, U = 감사 정보
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      모든 파일 정보 복사 (/COPY: DATSOU와 동일)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      디렉터리 복사본에 대 한 충실도 (기본값:/DCOPY: DA), 복사 플래그: D = 데이터, A = 특성, T = 타임 스탬프
   :::column-end:::
:::row-end:::

RoboCopy 명령을 처음 실행 하는 경우 사용자와 응용 프로그램은 StorSimple 파일 및 폴더에 계속 액세스 하 고 잠재적으로 변경 합니다. RoboCopy는 디렉터리를 처리 하 고, 다음으로 이동한 다음, 원본 위치 (StorSimple)의 사용자가 현재 RoboCopy 실행에서 처리 되지 않는 파일을 추가, 변경 또는 삭제할 수 있습니다. 괜찮습니다.

첫 번째 실행은 데이터를 다시 온-프레미스로 다시 이동 하 여 Windows Server로 이동 하 고 Azure File Sync를 통해 클라우드로 백업 하는 것입니다. 다음에 따라 시간이 오래 걸릴 수 있습니다.

* 다운로드 대역폭
* StorSimple 클라우드 서비스의 회수 속도입니다.
* 업로드 대역폭
* 각 서비스에서 처리 해야 하는 항목 (파일 및 폴더)의 수입니다.

초기 실행이 완료 되 면 명령을 다시 실행 합니다.

마지막 실행 후에 발생 한 변경 내용만 전송 하면 되기 때문에 두 번째 시간이 더 빨리 완료 됩니다. 이러한 변경 사항은 최신 버전 이기 때문에 이미 StorSimple에 로컬인 것일 수 있습니다. 이렇게 하면 클라우드에서 회수할 필요가 없기 때문에 시간이 더 줄어듭니다. 이 두 번째 실행 중에도 새 변경 내용이 누적 될 수 있습니다.

완료 하는 데 걸리는 시간이 허용 되는 가동 중지 시간을 만족할 때까지이 프로세스를 반복 합니다.

가동 중지 시간이 허용 되는 것으로 간주 되 고 StorSimple 위치를 오프 라인 상태로 전환할 준비가 되 면 이제는이 작업을 수행 합니다. 예를 들어, 사용자가 폴더에 액세스 하거나 StorSimple의이 폴더에서 콘텐츠를 변경 하지 못하게 하는 다른 적절 한 단계를 수행할 수 없도록 SMB 공유를 제거 합니다.

마지막 RoboCopy 라운드 하나를 실행 합니다. 이 경우 누락 된 변경 내용이 모두 선택 됩니다.
이 마지막 단계가 걸리는 시간은 RoboCopy 검색 속도에 따라 달라 집니다. 이전 실행이 걸린 시간을 측정 하 여 시간 (가동 중지 시간)을 예상할 수 있습니다.

Windows Server 폴더에 대 한 공유를 만들고 DFS-N 배포를 조정 하 여이를 가리키도록 할 수 있습니다. StorSimple SMB 공유와 동일한 공유 수준 권한을 설정 해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션하는 작업이 완료 되었습니다. (매핑한 내용에 따라 동일한 Azure 파일 공유로 이동 해야 한다고 결정 했습니다.)

이러한 복사본 중 일부를 병렬로 실행할 수 있습니다. 한 번에 하나의 Azure 파일 공유의 범위를 처리 하는 것이 좋습니다.

> [!WARNING]
> StorSimple의 모든 데이터를 Windows Server로 이동 하 고 마이그레이션이 완료 되 면 Azure Portal의 ***모든*** 동기화 그룹으로 돌아가서 클라우드 계층화 볼륨의 사용 가능한 공간 백분율 값을 캐시 사용률 (20%)에 더 적합 한 값으로 조정 합니다. 

클라우드 계층화 볼륨의 사용 가능한 공간 정책은 잠재적으로 여러 서버 끝점에서 동기화 되는 볼륨 수준에서 작동 합니다. 하나의 서버 끝점에서 사용 가능한 공간을 조정 하는 것을 잊은 경우, 동기화는 가장 제한적인 규칙을 계속 적용 하 고 99%의 사용 가능한 디스크 공간을 유지 하 여 로컬 캐시가 정상적으로 작동 하지 않도록 합니다. 드물게 액세스 하는 볼륨에 대 한 네임 스페이스만 보유 하 고 있는 경우를 제외 하 고는 보관 데이터를 포함 합니다.

## <a name="troubleshoot"></a>문제 해결

실행할 수 있는 가장 가능성이 높은 문제는 RoboCopy 명령이 Windows Server 쪽에서 *"볼륨 전체"* 와 함께 실패 하는 것입니다. 이 경우 다운로드 속도가 업로드 속도 보다 더 좋을 수 있습니다. 클라우드 계층화는 1 시간 마다 한 번씩 작동 하 여 로컬 Windows Server 디스크에서 콘텐츠를 이동할 동기화 했습니다.

동기화 진행률 및 클라우드 계층화의 디스크 공간을 확보 합니다. Windows 서버의 파일 탐색기에서이를 확인할 수 있습니다.

Windows 서버에 사용 가능한 용량이 충분 한 경우 명령을 다시 실행 하면 문제가 해결 됩니다. 이러한 상황이 발생 하는 경우에는 아무 것도 중단 되지 않으며, 자신 있게 이동할 수도 있습니다. 명령을 다시 실행 하는 것은 불편 합니다.

다른 Azure File Sync 문제를 실행할 수도 있습니다.
그럴 가능성은 거의 없어 **문제 해결 가이드 Azure File Sync 링크**를 살펴보세요.

## <a name="relevant-links"></a>관련 링크

마이그레이션 콘텐츠:

* [StorSimple 8000 시리즈 마이그레이션 가이드](storage-files-migration-storsimple-8000.md)

Azure File Sync 콘텐츠:

* [AFS 개요](https://aka.ms/AFS)
* [AFS 배포 가이드](storage-files-deployment-guide.md)
* [AFS 문제 해결](storage-sync-files-troubleshoot.md)
