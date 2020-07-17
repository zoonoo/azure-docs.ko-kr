---
title: Azure File Sync로의 온-프레미스 NAS 마이그레이션
description: Azure File Sync 및 Azure 파일 공유를 사용 하 여 온-프레미스 NAS (네트워크 연결 저장소) 위치에서 하이브리드 클라우드 배포로 파일을 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fd8e845734169bcd73fa0e087c30c0f2fd6ef4f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510308"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Azure File Sync를 사용 하 여 NAS (네트워크 연결 저장소)에서 하이브리드 클라우드 배포로 마이그레이션

Azure File Sync은 DAS (직접 연결 된 저장소) 위치에서 작동 하며 NAS (네트워크 연결 저장소) 위치에 대 한 동기화를 지원 하지 않습니다.
이를 통해 필요한 파일의 마이그레이션을 수행 하 고이 문서에서 이러한 마이그레이션의 계획 및 실행을 안내 합니다.

## <a name="migration-goals"></a>마이그레이션 목표

여기서의 목표는 NAS 어플라이언스에 있는 공유를 Windows Server로 이동 하는 것입니다. 그런 다음 하이브리드 클라우드 배포에 대 한 Azure File Sync 활용 합니다. 마이그레이션을 수행 하는 동안 가용성 뿐만 아니라 프로덕션 데이터의 무결성을 보장 하는 방법으로이 마이그레이션을 수행 해야 합니다. 후자를 사용 하려면 최소 가동 중지 시간을 유지 하 여 정기적인 유지 관리 기간에만 또는 약간 초과할 수 있도록 해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

Azure Files [마이그레이션 개요 문서](storage-files-migration-overview.md)에 설명 된 것 처럼 올바른 복사 도구와 방법을 사용 하는 것이 중요 합니다. NAS 어플라이언스는 로컬 네트워크에서 직접 SMB 공유를 노출 합니다. 이 마이그레이션 시나리오에서는 Windows Server에 기본적으로 제공 되는 RoboCopy가 파일을 이동 하는 가장 좋은 방법입니다.

- 1 단계: [필요한 Azure 파일 공유 수 확인](#phase-1-identify-how-many-azure-file-shares-you-need)
- 2 단계: [온-프레미스에서 적절 한 Windows Server 프로 비전](#phase-2-provision-a-suitable-windows-server-on-premises)
- 3 단계: [클라우드 리소스 Azure File Sync 배포](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 4 단계: [Azure storage 리소스 배포](#phase-4-deploy-azure-storage-resources)
- 5 단계: [Azure File Sync 에이전트 배포](#phase-5-deploy-the-azure-file-sync-agent)
- 6 단계: [Windows Server에서 Azure File Sync 구성](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 7 단계: [RoboCopy](#phase-7-robocopy)
- 8 단계: [사용자 잘림](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1 단계: 필요한 Azure 파일 공유 수 확인

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>2 단계: 온-프레미스에서 적절 한 Windows Server 프로 비전

* 최소 2012R2-가상 머신 또는 물리적 서버로 Windows Server 2019을 만듭니다. Windows Server 장애 조치 (failover) 클러스터도 지원 됩니다.
* 직접 연결 된 저장소 (지원 되지 않는 NAS와 비교 하 여 DAS)를 프로 비전 하거나 추가 합니다.

    Azure 파일 동기화 [클라우드 계층화](storage-sync-cloud-tiering.md) 기능을 사용 하는 경우 프로 비전 하는 저장소의 양은 NAS 어플라이언스에서 현재 사용 중인 것 보다 작을 수 있습니다.
    그러나 이후 단계에서 더 큰 NAS 공간에서 더 작은 Windows Server 볼륨으로 파일을 복사 하는 경우에는 일괄 처리로 작업 해야 합니다.

    1. 디스크에 맞는 파일 집합을 이동 합니다.
    2. 파일 동기화 및 클라우드 계층화 참여 허용
    3. 볼륨에 사용 가능한 공간이 더 많이 만들어진 경우 다음 파일 일괄 처리를 진행 합니다. 
    
    파일이 NAS 어플라이언스에서 사용 되는 Windows Server에 동일한 공간을 프로 비전 하 여 이러한 일괄 처리 방법을 방지할 수 있습니다. NAS/Windows에서 중복 제거를 고려 합니다. 이 많은 양의 저장소를 Windows Server에 영구적으로 커밋하지 않으려는 경우 마이그레이션 후와 클라우드 계층화 정책을 조정 하기 전에 볼륨 크기를 줄일 수 있습니다. 그러면 Azure 파일 공유의 더 작은 온-프레미스 캐시가 생성 됩니다.

배포 하는 Windows Server의 리소스 구성 (계산 및 RAM)은 대부분 동기화 할 항목 (파일 및 폴더)의 수에 따라 달라 집니다. 문제가 있는 경우 더 높은 성능 구성으로 이동 하는 것이 좋습니다.

[동기화 해야 하는 항목 (파일 및 폴더)의 수를 기준으로 Windows Server 크기를 조정 하는 방법에 대해 알아봅니다.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 이전에 연결 된 문서는 서버 메모리 (RAM) 범위를 포함 하는 테이블을 제공 합니다. 서버에 대해 더 적은 수의 방향을 지정할 수 있지만 초기 동기화에 시간이 훨씬 더 걸릴 수 있습니다.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>3 단계: 클라우드 리소스 Azure File Sync 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>4 단계: Azure storage 리소스 배포

이 단계에서는 1 단계의 매핑 테이블을 참조 하 고이를 사용 하 여 Azure storage 계정 및 파일 공유의 올바른 수를 프로 비전 합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>5 단계: Azure File Sync 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>6 단계: Windows Server에서 Azure File Sync 구성

이 프로세스를 위해 등록 된 온-프레미스 Windows Server가 준비 되어 있고 인터넷에 연결 되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 클라우드 계층화는 로컬 서버에서 클라우드에 저장 된 것 보다 저장소 용량을 줄일 수 있도록 하는 AFS 기능이 며, 전체 네임 스페이스를 사용할 수 있습니다. 또한 로컬에서 흥미로운 데이터는 빠른 액세스 성능을 위해 로컬로 캐시 됩니다. 클라우드 계층화는 Azure File Sync "서버 끝점"에 따라 선택적 기능입니다.

> [!WARNING]
> NAS 어플라이언스에서 사용 되는 데이터 보다 더 작은 저장소를 Windows server 볼륨에 프로 비전 한 경우 클라우드 계층화는 필수입니다. 클라우드 계층화를 설정 하지 않으면 서버는 모든 파일을 저장할 공간을 확보 하지 않습니다. 일시적으로 마이그레이션에 대 한 계층화 정책을 99%의 사용 가능한 볼륨 공간으로 설정 합니다. 마이그레이션이 완료 된 후 클라우드 계층화 설정으로 돌아가서 장기적인 유용한 수준으로 설정 해야 합니다.

동기화 그룹 만들기 및 동기화를 위해 구성 해야 하는 모든 Azure 파일 공유/서버 위치에 대 한 서버 끝점으로 일치 서버 폴더 추가의 단계를 반복 합니다.

모든 서버 끝점을 만든 후에는 동기화가 작동 합니다. 테스트 파일을 만들고 서버 위치에서 연결 된 Azure 파일 공유로 동기화를 확인할 수 있습니다 (동기화 그룹의 클라우드 끝점에서 설명).

두 위치 모두 서버 폴더와 Azure 파일 공유는 비어 있으며 두 위치의 데이터를 대기 합니다. 다음 단계에서는 Azure File Sync에 대 한 Windows Server로 파일을 복사 하 여 클라우드로 이동 하는 작업을 시작 합니다. 클라우드 계층화를 사용 하도록 설정한 경우 서버는 계층 파일을 시작 하 고, 로컬 볼륨의 용량이 부족 하 게 됩니다.

## <a name="phase-7-robocopy"></a>7 단계: RoboCopy

기본 마이그레이션 방식은 NAS 어플라이언스에서 Windows 서버로의 RoboCopy 이며 Azure 파일 공유에 대 한 Azure File Sync입니다.

Windows Server 대상 폴더에 대 한 첫 번째 로컬 복사본을 실행 합니다.

* NAS 어플라이언스의 첫 번째 위치를 식별 합니다.
* 이미 Azure File Sync 구성 되어 있는 Windows Server의 일치 하는 폴더를 식별 합니다.
* RoboCopy를 사용 하 여 복사 시작

다음 RoboCopy 명령은 NAS 저장소의 파일을 Windows Server 대상 폴더로 복사 합니다. Windows Server가 Azure 파일 공유와 동기화 합니다. 

NAS 어플라이언스에서 파일을 차지 하는 것 보다 더 작은 저장소를 Windows 서버에서 프로 비전 한 경우 클라우드 계층화를 구성 했습니다. 로컬 Windows Server 볼륨이 가득 차면 [클라우드 계층화](storage-sync-cloud-tiering.md) 는 이미 동기화 된 및 계층 파일에서 시작 됩니다. 클라우드 계층화는 NAS 어플라이언스에서 복사를 계속 하는 데 충분 한 공간을 생성 합니다. 클라우드 계층화는 한 시간에 한 번 확인 하 여 동기화 된 항목을 확인 하 고 디스크 공간을 확보 하 여 99%의 사용 가능한 볼륨 공간에 도달 합니다.
따라서 RoboCopy는 클라우드 및 계층에 로컬로 동기화 하 여 로컬 디스크 공간이 부족 하 게 되는 것 보다 더 빠르게 파일을 이동할 수 있습니다. RoboCopy가 실패 합니다. 이를 방지 하는 순서 대로 공유를 수행 하는 것이 좋습니다. 예를 들어 모든 공유에 대해 RoboCopy 작업을 동시에 시작 하거나 Windows Server에서 현재 사용 가능한 공간의 크기에 맞는 공유만 이동 하는 것은 몇 가지입니다.

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
      /UNILOG:\<file name\>
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
      에서이 RoboCopy 명령을 여러 번 실행 하 여 동일한 대상/대상에 순차적으로 실행할 수 있습니다. 이전에 복사 된 항목을 식별 하 고 생략 합니다. 마지막으로 실행 한 이후에 발생 한 변경 내용, 추가 내용 및 "*삭제*"만 처리 됩니다. 이전에 명령을 실행 하지 않은 경우에는 아무 것도 생략 됩니다. */MIR* 플래그는 여전히 적극적으로 사용 되 고 변경 되는 원본 위치에 대 한 뛰어난 옵션입니다.
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

## <a name="phase-8-user-cut-over"></a>8 단계: 사용자 잘림

RoboCopy 명령을 처음 실행 하는 경우 사용자와 응용 프로그램은 계속 해 서 NAS의 파일에 액세스 하며 잠재적으로 변경 될 수 있습니다. RoboCopy가 디렉터리를 처리 하 고, 다음으로 이동한 다음, 원본 위치 (NAS)의 사용자가 현재 RoboCopy 실행에서 처리 되지 않는 파일을 추가, 변경 또는 삭제 하는 것이 가능 합니다. 이는 정상적인 동작입니다.

첫 번째 실행은 Azure File Sync를 통해 Windows Server 및 클라우드로 대량 데이터를 이동 하는 것입니다. 이 첫 번째 복사는 다음에 따라 시간이 오래 걸릴 수 있습니다.

* 다운로드 대역폭
* 업로드 대역폭
* 로컬 네트워크 속도 및이와 일치 하는 RoboCopy 스레드 수의 최적 수
* RoboCopy 및 Azure File Sync에서 처리 해야 하는 항목 (파일 및 폴더)의 수입니다.

초기 실행이 완료 되 면 명령을 다시 실행 합니다.

마지막 실행 후에 발생 한 변경 내용만 전송 하면 되기 때문에 두 번째 시간이 더 빨리 완료 됩니다. 이 두 번째 실행 중에도 새 변경 내용이 누적 될 수 있습니다.

특정 위치에 대 한 RoboCopy를 완료 하는 데 소요 되는 시간이 가동 중지 시간에 대해 허용 가능한 기간 내에 충족 될 때까지이 프로세스를 반복 합니다.

가동 중지 시간을 허용 하 고 NAS 위치를 오프 라인으로 전환할 준비가 된 경우: 사용자 액세스를 오프 라인 상태로 전환 하려면 사용자가 더 이상 위치에 액세스할 수 없거나 NAS의이 폴더에서 콘텐츠를 변경 하지 못하도록 하는 다른 적절 한 단계를 수행할 수 있도록 공유 루트의 Acl을 변경 하는 옵션을 사용할 수 있습니다.

마지막 RoboCopy 라운드 하나를 실행 합니다. 누락 되었을 수 있는 모든 변경 내용을 선택 합니다.
이 마지막 단계가 걸리는 시간은 RoboCopy 검색 속도에 따라 달라 집니다. 이전 실행이 걸린 시간을 측정 하 여 시간 (가동 중지 시간)을 예상할 수 있습니다.

Windows Server 폴더에 대 한 공유를 만들고 DFS-N 배포를 조정 하 여이를 가리키도록 할 수 있습니다. NAS SMB 공유와 동일한 공유 수준의 사용 권한을 설정 해야 합니다. 엔터프라이즈급 도메인 가입 NAS가 있는 경우 사용자가 Active Directory에 있는 경우 사용자 Sid가 자동으로 일치 하 고, RoboCopy는 파일 및 메타 데이터를 완전히 충실도로 복사 합니다. NAS에서 로컬 사용자를 사용한 경우 이러한 사용자를 Windows Server 로컬 사용자로 다시 만들고 Windows Server로 이동 하는 기존 Sid RoboCopy를 새로운 Windows Server 로컬 사용자의 Sid로 매핑해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션하는 작업이 완료 되었습니다. (1 단계의 매핑에 따라)

이러한 복사본 중 일부를 병렬로 실행할 수 있습니다. 한 번에 하나의 Azure 파일 공유의 범위를 처리 하는 것이 좋습니다.

> [!WARNING]
> 모든 데이터를 NAS에서 Windows Server로 이동 하 고 마이그레이션이 완료 된 후에는 Azure Portal의 ***모든*** 동기화 그룹으로 돌아가서 클라우드 계층화 볼륨의 사용 가능한 공간 (%) 값을 캐시 사용률에 더 적합 한 값 (예: 20%)으로 조정 합니다. 

클라우드 계층화 볼륨의 사용 가능한 공간 정책은 잠재적으로 여러 서버 끝점에서 동기화 되는 볼륨 수준에서 작동 합니다. 하나의 서버 끝점에서 사용 가능한 공간을 조정 하는 것을 잊은 경우, 동기화는 가장 제한적인 규칙을 계속 적용 하 고 99%의 사용 가능한 디스크 공간을 유지 하 여 로컬 캐시가 정상적으로 작동 하지 않도록 합니다. 거의 액세스 하지 않는 볼륨에 대 한 네임 스페이스만 보유 하 고 있는 경우에만 보관 데이터를 보관 하 고 나머지는 다른 시나리오에 대 한 저장소 공간을 예약 하는 것입니다.

## <a name="troubleshoot"></a>문제 해결

실행할 수 있는 가장 가능성이 높은 문제는 RoboCopy 명령이 Windows Server 쪽에서 *"볼륨 전체"* 와 함께 실패 하는 것입니다. 클라우드 계층화는 1 시간 마다 한 번씩 작동 하 여 로컬 Windows Server 디스크에서 콘텐츠를 이동할 동기화 했습니다. 목표는 볼륨에서 99%의 사용 가능한 공간에 도달 하는 것입니다.

동기화 진행률 및 클라우드 계층화의 디스크 공간을 확보 합니다. Windows 서버의 파일 탐색기에서이를 확인할 수 있습니다.

Windows 서버에 사용 가능한 용량이 충분 한 경우 명령을 다시 실행 하면 문제가 해결 됩니다. 이러한 상황이 발생 하는 경우에는 아무 것도 중단 되지 않으며, 자신 있게 이동할 수도 있습니다. 명령을 다시 실행 하는 것은 불편 합니다.

Azure File Sync 문제를 해결 하려면 다음 섹션의 링크를 확인 하세요.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 및 Azure File Sync에 대해 자세히 알아볼 수 있습니다. 다음 문서는 고급 옵션, 모범 사례 및 문제 해결 도움말을 이해 하는 데 도움이 됩니다. 이러한 문서는 [Azure 파일 공유 설명서](storage-files-introduction.md) 에 대 한 링크를 적절 하 게 합니다.

* [AFS 개요](https://aka.ms/AFS)
* [AFS 배포 가이드](storage-files-deployment-guide.md)
* [AFS 문제 해결](storage-sync-files-troubleshoot.md)
