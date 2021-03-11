---
title: Azure DataBox를 통해 Azure 파일 동기화로의 온-프레미스 NAS 마이그레이션
description: Azure DataBox를 통해 Azure 파일 동기화를 사용 하 여 온-프레미스 NAS (네트워크 연결 저장소) 위치에서 하이브리드 클라우드 배포로 파일을 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584070"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>DataBox를 사용 하 여 NAS (네트워크 연결 저장소)에서 Azure 파일 동기화를 사용 하는 하이브리드 클라우드 배포로 마이그레이션

이 마이그레이션 문서는 NAS, Azure 파일 동기화 및 Azure DataBox 키워드와 관련 된 여러 가지 중 하나입니다. 이 문서가 시나리오에 적용 되는지 확인 합니다.

> [!div class="checklist"]
> * 데이터 원본: NAS (네트워크 연결 저장소)
> * 마이그레이션 경로: NAS &rArr; DataBox &rArr; &rArr; Windows Server와 Azure 파일 공유 동기화
> * 온-프레미스에서 파일 캐시: 예, 최종 목표는 Azure 파일 동기화 배포입니다.

시나리오가 다른 경우 [마이그레이션 가이드의 표](storage-files-migration-overview.md#migration-guides)를 참조 하세요.

Azure 파일 동기화은 DAS (직접 연결 된 저장소) 위치에서 작동 하며 NAS (네트워크 연결 저장소) 위치에 대 한 동기화를 지원 하지 않습니다.
이를 통해 필요한 파일의 마이그레이션을 수행 하 고이 문서에서 이러한 마이그레이션의 계획 및 실행을 안내 합니다.

## <a name="migration-goals"></a>마이그레이션 목표

여기서의 목표는 NAS 어플라이언스에 있는 공유를 Windows Server로 이동 하는 것입니다. 그런 다음 하이브리드 클라우드 배포에 대 한 Azure 파일 동기화 활용 합니다. 이 마이그레이션은 마이그레이션하는 동안 프로덕션 데이터 및 가용성의 무결성을 보장 하는 방식으로 수행 해야 합니다. 후자를 사용 하려면 최소 가동 중지 시간을 유지 하 여 정기적인 유지 관리 기간에만 또는 약간 초과할 수 있도록 해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

마이그레이션 프로세스는 여러 단계로 구성 됩니다. Azure storage 계정 및 파일 공유를 배포 하 고, 온-프레미스 Windows Server를 배포 하 고, Azure 파일 동기화를 구성 하 고, RoboCopy를 사용 하 여 마이그레이션하고, 마지막으로, 중단을 수행 해야 합니다. 다음 섹션에서는 마이그레이션 프로세스의 단계에 대해 자세히 설명 합니다.

> [!TIP]
> 이 문서를 반환 하는 경우 오른쪽의 탐색을 사용 하 여 중단 한 마이그레이션 단계로 이동 합니다.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1 단계: 필요한 Azure 파일 공유 수 확인

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>2 단계: Azure storage 리소스 배포

이 단계에서는 1 단계의 매핑 테이블을 참조 하 고이를 사용 하 여 Azure storage 계정 및 파일 공유의 올바른 수를 프로 비전 합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>3 단계: 필요한 Azure DataBox 어플라이언스 수 결정

이전 단계를 완료 한 경우에만이 단계를 시작 합니다. 지금은 Azure storage 리소스 (저장소 계정 및 파일 공유)를 만들어야 합니다. DataBox 주문 하는 동안 DataBox에서 데이터를 이동 하는 저장소 계정을 지정 해야 합니다.

이 단계에서는 이전 단계의 마이그레이션 계획 결과를 사용 가능한 DataBox 옵션의 제한에 매핑해야 합니다. 이러한 고려 사항은 선택 해야 하는 DataBox 옵션 및 NAS 공유를 Azure 파일 공유로 이동 하는 데 필요한 옵션의 수에 대 한 계획을 만드는 데 도움이 됩니다.

필요한 형식의 장치 수를 확인 하려면 다음과 같은 중요 한 제한을 고려 합니다.

* 모든 Azure DataBox은 최대 10 개의 저장소 계정으로 데이터를 이동할 수 있습니다. 
* 각 DataBox 옵션은 사용 가능한 용량으로 제공 됩니다. [DataBox options](#databox-options)를 참조 하세요.

만들려는 저장소 계정 수와 각 저장소의 공유에 대 한 마이그레이션 계획을 참조 하세요. 그런 다음 NAS에서 각 공유의 크기를 확인 합니다. 이 정보를 결합 하 여 저장소 계정에 데이터를 전송 해야 하는 어플라이언스를 최적화 하 고 결정할 수 있습니다. 두 개의 DataBox 장치에서 파일을 동일한 저장소 계정으로 이동할 수 있지만, 단일 파일 공유의 콘텐츠를 2 DataBoxes 간에 분할 하지 않습니다.

### <a name="databox-options"></a>DataBox 옵션

표준 마이그레이션의 경우 다음 세 가지 DataBox 옵션 중 하나 또는 조합을 선택 해야 합니다. 

* DataBox Disks Microsoft는 최대 총 40 TiB의 최대 5 개 TiB의 용량이 8 개인 SSD 디스크를 하나 이상 보냅니다. 사용 가능한 용량은 암호화 및 파일 시스템 오버 헤드로 인해 약 20% 더 낮습니다. 자세한 내용은 [DataBox Disks 설명서](../../databox/data-box-disk-overview.md)를 참조 하세요.
* DataBox 가장 일반적인 옵션입니다. NAS와 유사 하 게 작동 하는 견고한 DataBox 어플라이언스는 귀하에 게 제공 됩니다. 80 TiB의 사용 가능한 용량이 있습니다. 자세한 내용은 [DataBox 설명서](../../databox/data-box-overview.md)를 참조 하세요.
* DataBox이 옵션은 NAS의 견고한 DataBox 어플라이언스를 제공 합니다 .이 어플라이언스는 NAS와 유사 하 게 작동 하며,이는 1 PiB의 용량을 가집니다. 사용 가능한 용량은 암호화 및 파일 시스템 오버 헤드로 인해 약 20% 더 낮습니다. 자세한 내용은 [DataBox 중형 설명서](../../databox/data-box-heavy-overview.md)를 참조 하세요.

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>4 단계: 온-프레미스에서 적절 한 Windows Server 프로 비전

Azure DataBox가 도착할 때까지 기다리는 동안 Azure 파일 동기화에서 사용할 하나 이상의 Windows Server에 대 한 요구 사항을 검토 하는 것이 이미 시작 될 수 있습니다.

* 최소 2012R2-가상 머신 또는 물리적 서버로 Windows Server 2019을 만듭니다. Windows Server 장애 조치 (failover) 클러스터도 지원 됩니다.
* 직접 연결 된 저장소 (지원 되지 않는 NAS와 비교 하 여 DAS)를 프로 비전 하거나 추가 합니다.

배포 하는 Windows Server의 리소스 구성 (계산 및 RAM)은 대부분 동기화 할 항목 (파일 및 폴더)의 수에 따라 달라 집니다. 문제가 있는 경우 더 높은 성능 구성을 권장 합니다.

[동기화 해야 하는 항목 (파일 및 폴더)의 수를 기준으로 Windows Server 크기를 조정 하는 방법에 대해 알아봅니다.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 이전에 연결 된 문서는 서버 메모리 (RAM) 범위를 포함 하는 테이블을 제공 합니다. 서버에 대해 더 적은 수의 방향을 지정할 수 있지만 초기 동기화에 시간이 훨씬 더 걸릴 수 있습니다.

## <a name="phase-5-copy-files-onto-your-databox"></a>5 단계: DataBox로 파일 복사

DataBox 도착 하면 NAS 어플라이언스에 대 한 시야에 DataBox를 설정 해야 합니다. 주문한 DataBox 유형에 대 한 설치 설명서를 따릅니다.

* [Data Box 설정](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk 설정](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy 설정](../../databox/data-box-heavy-quickstart-portal.md)

DataBox 유형에 따라 DataBox 복사 도구를 사용할 수 있습니다. 이 시점에서 DataBox에 대 한 완전 한 충실도로 파일을 복사 하지 않으므로 Azure 파일 공유로의 마이그레이션에는 권장 되지 않습니다. 대신 RoboCopy를 사용 하십시오.

DataBox 도착 하면 주문 시 지정한 각 저장소 계정에 대해 미리 프로 비전 된 SMB 공유를 사용할 수 있습니다.

* 파일이 프리미엄 Azure 파일 공유로 이동 하는 경우 premium "File storage" 저장소 계정 마다 하나의 SMB 공유가 있습니다.
* 파일이 표준 저장소 계정으로 이동 하는 경우 표준 (GPv1 및 GPv2) 저장소 계정에 세 개의 SMB 공유가 있습니다. 로 끝나는 파일 공유만 `_AzFiles` 마이그레이션과 관련이 있습니다. 모든 블록 및 페이지 blob 공유를 무시 합니다.

Azure DataBox 설명서의 단계를 따릅니다.

1. [Data Box에 연결](../../databox/data-box-deploy-copy-data.md)
1. Data Box에 데이터 복사
1. [Azure로 출발 DataBox 준비](../../databox/data-box-deploy-picked-up.md)

연결 된 DataBox 설명서는 RoboCopy 명령을 지정 합니다. 그러나이 명령은 전체 파일 및 폴더 충실도를 유지 하는 데 적합 하지 않습니다. 이 명령을 대신 사용 합니다.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>6 단계: 클라우드 리소스 Azure 파일 동기화 배포

이 가이드를 계속 하기 전에 모든 파일이 올바른 Azure 파일 공유에 도착할 때까지 기다립니다. 수집 DataBox 데이터를 배달 하는 과정에 시간이 걸립니다.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>7 단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>8 단계: Windows Server에서 Azure 파일 동기화 구성

이 프로세스를 위해 등록 된 온-프레미스 Windows Server가 준비 되어 있고 인터넷에 연결 되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

클라우드 계층화 기능을 설정 하 고 초기 다운로드 섹션에서 "Namespace only"를 선택 합니다.

> [!IMPORTANT]
> 클라우드 계층화는 로컬 서버에서 클라우드에 저장 된 것 보다 저장소 용량을 줄일 수 있도록 하는 AFS 기능이 며, 전체 네임 스페이스를 사용할 수 있습니다. 또한 로컬에서 흥미로운 데이터는 빠른 액세스 성능을 위해 로컬로 캐시 됩니다. 클라우드 계층화는 Azure 파일 동기화 "서버 끝점"에 따라 선택적 기능입니다. 모든 클라우드 데이터를 보관할 수 있는 충분 한 로컬 디스크 용량이 없고 클라우드에서 모든 데이터를 다운로드 하지 않으려는 경우이 기능을 사용 해야 합니다.

동기화 그룹 만들기 및 동기화를 위해 구성 해야 하는 모든 Azure 파일 공유/서버 위치에 대 한 서버 끝점으로 일치 서버 폴더 추가의 단계를 반복 합니다. 네임 스페이스의 동기화가 완료 될 때까지 기다립니다. 다음 섹션에서는이를 확인 하는 방법에 대해 자세히 설명 합니다.

> [!NOTE]
> 서버 끝점을 만든 후에는 동기화가 작동 합니다. 그러나 동기화는 DataBox를 통해 이동 하는 파일 및 폴더를 Azure 파일 공유로 열거 (검색) 해야 합니다. 네임 스페이스의 크기에 따라 클라우드의 네임 스페이스를 서버에 표시 하기 시작 하기까지 시간이 오래 걸릴 수 있습니다.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>9 단계: 네임 스페이스가 서버에 완전히 표시 될 때까지 대기

서버가 클라우드 공유에서 네임 스페이스를 완전히 다운로드 한 다음 마이그레이션의 다음 단계를 기다려야 합니다. 서버로 파일을 너무 일찍 이동 하기 시작 하면 불필요 한 업로드 및 파일 동기화 충돌이 발생할 수 있습니다.

서버에서 초기 다운로드 동기화를 완료 했는지를 확인 하려면 동기화 하는 Windows Server에서 이벤트 뷰어를 열고 Azure 파일 동기화 원격 분석 이벤트 로그를 사용 합니다.
원격 분석 이벤트 로그는 응용 프로그램 및 Services\microsoft\filesync\agent 아래 이벤트 뷰어에 있습니다.

가장 최근의 9102 이벤트를 검색 합니다. 동기화 시스템이 완료되면 이벤트 ID 9102가 기록됩니다. 이벤트 텍스트에는 다운로드 동기화 방향에 대 한 필드가 있습니다. ( `HResult` 0 및 다운로드 한 파일도 필요 합니다.)
 
서버에서 네임 스페이스 다운로드를 완료 한 것으로 표시 하기 위해이 유형과 콘텐츠의 연속 이벤트 두 개를 표시 하려고 합니다. 2 9102 이벤트 간에 발생 하는 이벤트가 서로 다른 경우에는 정상입니다.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>10 단계: NAS에서 RoboCopy 파악

서버에서 클라우드 공유의 전체 네임 스페이스에 대 한 초기 동기화를 완료 한 후에는이 단계를 진행할 수 있습니다. 이 단계를 계속 하기 전에이 단계를 완료 해야 합니다. 자세한 내용은 이전 섹션을 참조 하세요.

이 단계에서 RoboCopy 작업을 실행 하 여 공유를 DataBox로 분기 이후 NAS의 최신 변경 내용으로 클라우드 공유를 catch 합니다.
이러한 RoboCopy는 NAS 공유에서 발생 하는 변동 양에 따라 신속 하 게 완료 되거나 다소 시간이 걸릴 수 있습니다.

> [!WARNING]
> Windows Server 2019의 재발 된 RoboCopy 동작으로 인해 RoboCopy의/MIR 스위치는 계층화 된 대상 디렉터리와 호환 되지 않습니다. 이 마이그레이션 단계에서는 Windows Server 2019 또는 Windows 10 클라이언트를 사용 하지 않아야 합니다. 중간 Windows Server 2016에서 RoboCopy를 사용 합니다.

기본 마이그레이션 방식은 NAS 어플라이언스에서 Windows 서버로의 RoboCopy 이며 Azure 파일 공유에 대 한 Azure 파일 동기화입니다.

Windows Server 대상 폴더에 대 한 첫 번째 로컬 복사본을 실행 합니다.

1. NAS 어플라이언스의 첫 번째 위치를 식별 합니다.
1. 이미 Azure 파일 동기화 구성 되어 있는 Windows Server의 일치 하는 폴더를 식별 합니다.
1. RoboCopy를 사용 하 여 복사 시작

다음 RoboCopy 명령은 NAS 저장소의 차이점 (업데이트 된 파일 및 폴더)만 Windows Server 대상 폴더에 복사 합니다. 그러면 Windows Server가 Azure 파일 공유와 동기화 합니다. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

NAS 어플라이언스에서 파일을 차지 하는 것 보다 더 작은 저장소를 Windows 서버에서 프로 비전 한 경우 클라우드 계층화를 구성 했습니다. 로컬 Windows Server 볼륨이 가득 차면 [클라우드 계층화](storage-sync-cloud-tiering-overview.md) 는 이미 동기화 된 및 계층 파일에서 시작 됩니다. 클라우드 계층화는 NAS 어플라이언스에서 복사를 계속 하는 데 충분 한 공간을 생성 합니다. 클라우드 계층화는 한 시간에 한 번 확인 하 여 동기화 된 항목을 확인 하 고 디스크 공간을 확보 하 여 99%의 사용 가능한 볼륨 공간에 도달 합니다.
RoboCopy는 Windows Server에 대 한 로컬 저장소 보다 많은 파일을 이동 해야 할 수 있습니다. 평균적으로 RoboCopy는 파일을 업로드 하 고 로컬 볼륨에서 외부에서 계층 Azure 파일 동기화 하는 것 보다 훨씬 더 빠르게 이동할 수 있습니다. RoboCopy가 실패 합니다. 이를 방지 하는 순서 대로 공유를 수행 하는 것이 좋습니다. 예를 들어 모든 공유에 대해 RoboCopy 작업을 동시에 시작 하거나 Windows Server에서 현재 사용 가능한 공간의 크기에 맞는 공유만 이동 하는 것은 몇 가지입니다. 좋은 소식은/MIR 스위치는 델타를 이동 하 고 델타를 이동한 후 다시 시작 된 작업은이 파일을 다시 이동 하지 않아도 된다는 것입니다.

### <a name="user-cut-over"></a>사용자 잘라내기

RoboCopy 명령을 처음 실행 하는 경우 사용자와 응용 프로그램은 계속 해 서 NAS의 파일에 액세스 하며 잠재적으로 변경 될 수 있습니다. RoboCopy가 디렉터리를 처리 하 고, 다음으로 이동한 다음, 원본 위치 (NAS)의 사용자가 현재 RoboCopy 실행에서 처리 되지 않는 파일을 추가, 변경 또는 삭제 하는 것이 가능 합니다. 이는 정상적인 동작입니다.

첫 번째 실행은 Azure 파일 동기화를 통해 Windows Server 및 클라우드로 대량 변동 데이터를 이동 하는 것입니다. 이 첫 번째 복사는 다음에 따라 시간이 오래 걸릴 수 있습니다.

* 업로드 대역폭
* 로컬 네트워크 속도 및이와 일치 하는 RoboCopy 스레드 수의 최적 수
* RoboCopy 및 Azure 파일 동기화에서 처리 해야 하는 항목 (파일 및 폴더)의 수입니다.

초기 실행이 완료 되 면 명령을 다시 실행 합니다.

두 번째로 동일한 공유에 대해 RoboCopy를 실행 하면 마지막으로 실행 한 이후에 발생 한 변경 내용만 전송 하면 되기 때문에 더 빨리 완료 됩니다. 같은 공유에 대해 반복 되는 작업을 실행할 수 있습니다.

가동 중지 시간을 고려 하는 경우 NAS 기반 공유에 대 한 사용자 액세스 권한을 제거 해야 합니다. 사용자가 파일 및 폴더 구조와 콘텐츠를 변경 하지 못하도록 하는 단계를 수행 하 여이 작업을 수행할 수 있습니다. 예를 들어 DFS-Namespace가 존재 하지 않는 위치를 가리키도록 하거나 공유에서 루트 Acl을 변경 합니다.

마지막 RoboCopy 라운드 하나를 실행 합니다. 누락 되었을 수 있는 모든 변경 내용을 선택 합니다.
이 마지막 단계가 걸리는 시간은 RoboCopy 검색 속도에 따라 달라 집니다. 이전 실행이 걸린 시간을 측정 하 여 시간 (가동 중지 시간)을 예상할 수 있습니다.

Windows Server 폴더에 대 한 공유를 만들고 DFS-N 배포를 조정 하 여이를 가리키도록 할 수 있습니다. NAS SMB 공유와 동일한 공유 수준의 사용 권한을 설정 해야 합니다. 엔터프라이즈급 도메인 가입 NAS가 있는 경우 사용자가 Active Directory에 있는 경우 사용자 Sid가 자동으로 일치 하 고, RoboCopy는 파일 및 메타 데이터를 완전히 충실도로 복사 합니다. NAS에서 로컬 사용자를 사용한 경우 이러한 사용자를 Windows Server 로컬 사용자로 다시 만들고 Windows Server로 이동 하는 기존 Sid RoboCopy를 새로운 Windows Server 로컬 사용자의 Sid로 매핑해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션하는 작업이 완료 되었습니다. (1 단계의 매핑에 따라)

이러한 복사본 중 일부를 병렬로 실행할 수 있습니다. 한 번에 하나의 Azure 파일 공유의 범위를 처리 하는 것이 좋습니다.

## <a name="troubleshoot"></a>문제 해결

실행할 수 있는 가장 가능성이 높은 문제는 RoboCopy 명령이 Windows Server 쪽에서 *"볼륨 전체"* 와 함께 실패 하는 것입니다. 클라우드 계층화는 1 시간 마다 한 번씩 작동 하 여 로컬 Windows Server 디스크에서 콘텐츠를 이동할 동기화 했습니다. 목표는 볼륨에서 99%의 사용 가능한 공간에 도달 하는 것입니다.

동기화 진행률 및 클라우드 계층화의 디스크 공간을 확보 합니다. Windows 서버의 파일 탐색기에서이를 확인할 수 있습니다.

Windows 서버에 사용 가능한 용량이 충분 한 경우 명령을 다시 실행 하면 문제가 해결 됩니다. 이러한 상황이 발생 하는 경우에는 아무 것도 중단 되지 않으며, 자신 있게 이동할 수도 있습니다. 명령을 다시 실행 하는 것은 불편 합니다.

Azure 파일 동기화 문제를 해결 하려면 다음 섹션의 링크를 확인 하세요.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 및 Azure 파일 동기화에 대해 자세히 알아볼 수 있습니다. 다음 문서는 고급 옵션, 모범 사례 및 문제 해결 도움말을 이해 하는 데 도움이 됩니다. 이러한 문서는 [Azure 파일 공유 설명서](storage-files-introduction.md) 에 대 한 링크를 적절 하 게 합니다.

* [마이그레이션 개요](storage-files-migration-overview.md)
* [AFS 개요](./storage-sync-files-planning.md)
* [AFS 배포 가이드](./storage-how-to-create-file-share.md)
* [AFS 문제 해결](storage-sync-files-troubleshoot.md)
