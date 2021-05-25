---
title: Azure DataBox를 통해 온-프레미스 NAS를 Azure 파일 동기화로 마이그레이션
description: Azure DataBox를 통해 Azure 파일 동기화를 사용하여 온-프레미스 NAS(Network Attached Storage) 위치에서 하이브리드 클라우드 배포로 파일을 마이그레이션하는 방법을 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584070"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>DataBox를 사용하여 NAS(Network Attached Storage)에서 Azure 파일 동기화를 사용하는 하이브리드 클라우드 배포로 마이그레이션

이 마이그레이션 문서는 NAS, Azure 파일 동기화 및 Azure DataBox라는 키워드와 관련된 여러 문서 중 하나입니다. 이 문서가 시나리오에 적용되는지 확인하십시오.

> [!div class="checklist"]
> * 데이터 원본: NAS(Network Attached Storage)
> * 마이그레이션 경로: NAS &rArr; DataBox &rArr; Azure 파일 공유 &rArr; Windows Server와 동기화
> * 온-프레미스에서 파일 캐싱: 그렇습니다, 최종 목표는 Azure 파일 동기화 배포입니다.

시나리오가 다른 경우 [마이그레이션 가이드 표](storage-files-migration-overview.md#migration-guides)를 참조하세요.

Azure 파일 동기화는 DAS(Direct Attached Storage) 위치에서 작동하며 NAS(Network Attached Storage) 위치로의 동기화를 지원하지 않습니다.
그래서 파일 마이그레이션이 필요하고, 이 문서를 통해 이러한 마이그레이션의 계획 및 실행을 안내합니다.

## <a name="migration-goals"></a>마이그레이션 목표

목표는 NAS 어플라이언스에 있는 공유를 Windows Server로 이동하는 것입니다. 그런 다음, 하이브리드 클라우드 배포에 Azure 파일 동기화를 활용합니다. 이 마이그레이션은 마이그레이션하는 동안 프로덕션 데이터의 무결성과 가용성을 보장하는 방식으로 수행해야 합니다. 후자를 위해서는 가동 중지 시간을 최소화(정기적인 유지 관리 기간에 맞추거나 약간만 초과되도록)해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

마이그레이션 프로세스는 몇 가지 단계로 구성됩니다. Azure 스토리지 계정 및 파일 공유를 배포하고, 온-프레미스 Windows Server를 배포하고, Azure 파일 동기화를 구성하고, RoboCopy를 사용하여 마이그레이션하고, 마지막으로 중단을 수행해야 합니다. 다음 섹션에서는 마이그레이션 프로세스의 단계를 자세히 설명합니다.

> [!TIP]
> 이 문서를 다시 읽는 경우, 오른쪽의 탐색을 사용하여 중단한 마이그레이션 단계로 이동할 수 있습니다.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>1단계: 필요한 Azure 파일 공유 수 확인

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>2단계: Azure 스토리지 리소스 배포

이 단계에서는 1단계의 매핑 테이블을 참조하여 정확한 수의 Azure Storage 계정과 해당 계정 내의 파일 공유를 프로비저닝합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>3단계: 필요한 Azure DataBox 어플라이언스 수 확인

이 단계는 이전 단계를 완료한 경우에만 시작해야 합니다. 이때 Azure 스토리지 리소스(스토리지 계정 및 파일 공유)를 만들어야 합니다. DataBox 주문 중에 DataBox가 데이터를 이동할 스토리지 계정을 지정해야 합니다.

이 단계에서는 이전 단계의 마이그레이션 계획 결과를 사용 가능한 DataBox 옵션의 제한에 매핑해야 합니다. 이러한 고려 사항은 선택해야 하는 DataBox 옵션과 NAS 공유를 Azure 파일 공유로 이동하는 데 필요한 옵션 수를 계획하는 데 도움이 됩니다.

어떤 형식의 디바이스가 얼마나 많이 필요한지 확인하려면 다음과 같은 중요한 제한을 고려합니다.

* Azure DataBox는 데이터를 최대 10개의 스토리지 계정으로 이동할 수 있습니다. 
* 각 DataBox 옵션에는 자체 사용 가능한 용량이 제공됩니다. [DataBox 옵션](#databox-options)을 참조하세요.

만들기로 결정한 스토리지 계정 수와 각 계정의 공유에 대해서는 마이그레이션 계획을 참조하세요. 그런 다음, NAS에서 각 공유의 크기를 확인합니다. 이 정보를 결합하면 어떤 어플라이언스가 어떤 스토리지 계정으로 데이터를 전송해야 하는지 결정하고 최적화할 수 있습니다. DataBox 디바이스 두 개가 파일을 동일한 스토리지 계정으로 이동하도록 할 수 있지만, 단일 파일 공유의 콘텐츠를 DataBox 2개에 분할하지 마십시오.

### <a name="databox-options"></a>DataBox 옵션

표준 마이그레이션의 경우 다음 세 가지 DataBox 옵션 중 하나 또는 조합을 선택해야 합니다. 

* DataBox Disks Microsoft는 최대 총 40TiB에 대해 용량이 각각 8TiB인 SSD 디스크를 1개 및 최대 5개 보내드립니다. 사용 가능한 용량은 암호화 및 파일 시스템 오버헤드로 인해 약 20% 더 적습니다. 자세한 내용은 [DataBox Disks 설명서](../../databox/data-box-disk-overview.md)를 참조하세요.
* DataBox 가장 일반적인 옵션입니다. NAS와 유사하게 작동하는 러기드 DataBox 어플라이언스가 배송됩니다. 사용 가능한 용량은 80TiB입니다. 자세한 내용은 [DataBox 설명서](../../databox/data-box-overview.md)를 참조하세요.
* DataBox Heavy 이 옵션은 바퀴가 달린 러기드 DataBox 어플라이언스가 특징이고 NAS와 유사하게 작동하며 용량은 1PiB입니다. 사용 가능한 용량은 암호화 및 파일 시스템 오버헤드로 인해 약 20% 더 적습니다. 자세한 내용은 [DataBox Heavy 설명서](../../databox/data-box-heavy-overview.md)를 참조하세요.

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>4단계: 온-프레미스에서 적절한 Windows Server 프로비전

Azure DataBox가 도착하기를 기다리는 동안 Azure 파일 동기화에서 사용할 하나 이상의 Windows Server 요구 사항 검토를 시작할 수 있습니다.

* Windows Server 2019(최소 2012R2 이상)를 가상 머신 또는 물리적 서버로 만듭니다. Windows Server 장애 조치(failover) 클러스터도 지원됩니다.
* DAS(Direct Attached Storage, 지원되지 않는 NAS와 비교)를 프로비저닝하거나 추가합니다.

배포하는 Windows Server의 리소스 구성(컴퓨팅 및 RAM)은 대부분 동기화할 항목(파일 및 폴더)의 수에 따라 달라집니다. 문제가 있는 경우 더 높은 성능 구성이 권장됩니다.

[동기화해야 하는 항목(파일 및 폴더)의 수를 기준으로 Windows Server 크기를 조정하는 방법에 대해 알아봅니다.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 이전 연결 문서는 서버 메모리(RAM) 범위를 포함하는 테이블을 제공합니다. 서버에 대해 더 작은 수를 지정할 수 있지만 초기 동기화에 더 많은 시간이 걸릴 수 있습니다.

## <a name="phase-5-copy-files-onto-your-databox"></a>5단계: DataBox에 파일 복사

DataBox가 도착하면 NAS 어플라이언스를 보는 방향에서 DataBox를 설정해야 합니다. 주문한 DataBox 유형에 대한 설치 설명서를 따르십시오.

* [Data Box 설정](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk 설정](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy 설정](../../databox/data-box-heavy-quickstart-portal.md)

DataBox 유형에 따라 사용 가능한 DataBox 복사 도구가 있을 수 있습니다. 이 시점에는 Azure 파일 공유로 마이그레이션하는 것이 좋지 않습니다. 파일이 DataBox에 완전히 충실하게 복사되지 않기 때문입니다. 대신 RoboCopy를 사용하세요.

DataBox가 도착하면 주문 시 지정한 각 스토리지 계정에 사용할 수 있는 미리 프로비저닝된 SMB 공유가 있습니다.

* 파일이 프리미엄 Azure 파일 공유로 이동하는 경우, SMB 공유는 프리미엄 "File Storage" 스토리지 계정당 하나가 있습니다.
* 파일이 표준 스토리지 계정으로 이동하는 경우, SMB 공유는 표준(GPv1 및 GPv2) 스토리지 계정당 세 개가 입니다. `_AzFiles`로 끝나는 파일 공유만 마이그레이션과 관련이 있습니다. 블록 및 페이지 Blob 공유는 무시하세요.

Azure DataBox 설명서의 단계를 수행합니다.

1. [Data Box에 연결](../../databox/data-box-deploy-copy-data.md)
1. Data Box에 데이터 복사
1. [Azure로 출발하기 위해 DataBox 준비](../../databox/data-box-deploy-picked-up.md)

연결된 DataBox 설명서에는 RoboCopy 명령이 명시되어 있습니다. 하지만 이 명령은 전체 파일 및 폴더 충실도를 유지하는 데 적합하지 않습니다. 대신 다음 명령을 사용하세요.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>6단계: Azure 파일 동기화 클라우드 리소스 배포

이 가이드를 계속하기 전에 모든 파일이 올바른 Azure 파일 공유에 도착할 때까지 기다리세요. DataBox 데이터를 배송하고 수집하는 과정에는 시간이 걸립니다.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>7단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>8단계: Windows Server에서 Azure 파일 동기화 구성

이 프로세스를 위해서는 등록된 온-프레미스 Windows Server가 준비되어 있고 인터넷에 연결되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

클라우드 계층화 기능을 켜고 초기 다운로드 섹션에서 "네임스페이스만"을 선택합니다.

> [!IMPORTANT]
> 클라우드 계층화는 로컬 서버가 클라우드에 저장되는 것보다 적은 스토리지 용량을 가지면서도 전체 네임스페이스를 사용할 수 있도록 하는 AFS 기능입니다. 로컬에서 필요한 데이터가 로컬에 캐시되어 빠른 액세스 성능도 제공합니다. 클라우드 계층화는 Azure 파일 동기화 "서버 엔드포인트"당 선택 가능한 기능입니다. Windows Server에 모든 클라우드 데이터를 저장할 수 있는 로컬 디스크 용량이 충분하지 않고 클라우드에서 모든 데이터를 다운로드하지 않으려면 이 기능을 사용해야 합니다!

동기화 그룹을 생성하고 일치하는 서버 폴더를 모든 Azure 파일 공유/서버 위치에 대한 서버 엔드포인트로 추가하는 단계를 반복합니다. 동기화를 위해 구성해야 합니다. 네임스페이스 동기화가 완료될 때까지 기다립니다. 다음 섹션에는 이를 확인하는 방법이 자세히 설명되어 있습니다.

> [!NOTE]
> 서버 엔드포인트를 만든 후에는 동기화가 작동합니다. 하지만 동기화는 DataBox를 통해 Azure 파일 공유로 이동한 파일 및 폴더를 열거(검색)해야 합니다. 네임스페이스의 크기에 따라서는 클라우드의 네임스페이스가 서버에 나타나기 시작하는 데 상당한 시간이 걸릴 수 있습니다.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>9단계: 네임스페이스가 서버에 완전히 나타날 때까지 대기

서버가 클라우드 공유에서 네임스페이스를 완전히 다운로드할 때까지 마이그레이션의 다음 단계를 기다려야 합니다. 파일을 서버로 너무 일찍 이동하기 시작하면 불필요한 업로드 및 파일 동기화 충돌의 위험이 있습니다.

서버에서 초기 다운로드 동기화를 완료했는지를 확인하려면 동기화하는 Windows Server에서 이벤트 뷰어를 열고 Azure 파일 동기화 원격 분석 이벤트 로그를 사용합니다.
원격 분석 이벤트 로그는 Applications 및 Services\Microsoft\FileSync\Agent 아래 이벤트 뷰어에 있습니다.

가장 최근의 9102 이벤트를 검색합니다. 동기화 시스템이 완료되면 이벤트 ID 9102가 기록됩니다. 이벤트 텍스트에는 다운로드 동기화 방향에 대한 필드가 있습니다. (`HResult`는 0이어야 하며 파일도 다운로드되어야 합니다.)
 
이 유형 및 콘텐츠의 연속 이벤트 두 개를 보고 서버가 네임스페이스 다운로드를 완료했는지 확인하는 것이 좋습니다. 두 개의 9102 이벤트 사이에 다른 이벤트가 발생해도 괜찮습니다.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>10단계: NAS에서 RoboCopy 보완

서버가 클라우드 공유에서 전체 네임스페이스의 초기 동기화를 완료하면 이 단계를 진행할 수 있습니다. 이 단계를 계속하기 전에 이 단계를 완료해야 합니다. 자세한 내용은 이전 섹션을 확인하세요.

이 단계에서는 RoboCopy 작업을 실행하여 공유를 DataBox에 포크한 이후 NAS의 최신 변경 내용으로 클라우드 공유를 보완합니다.
이러한 보완 RoboCopy는 NAS 공유에서 발생한 변동의 양에 따라 빠르게 완료되거나 다소 시간이 걸릴 수 있습니다.

> [!WARNING]
> Windows Server 2019에서 이전 상태로 되돌려진 RoboCopy 동작으로 인해 RoboCopy의 /MIR 스위치는 계층화된 대상 디렉터리와 호환되지 않습니다. 이 마이그레이션 단계에는 Windows Server 2019 또는 Windows 10 클라이언트를 사용하지 않아야 합니다. 중간 Windows Server 2016에서 RoboCopy를 사용하세요.

기본 마이그레이션 방식은 NAS 어플라이언스에서 Windows Server로 RoboCopy 및 Azure 파일 공유로 Azure 파일 동기화입니다.

Windows Server 대상 폴더에 첫 번째 로컬 복사를 실행합니다.

1. NAS 어플라이언스의 첫 번째 위치를 식별합니다.
1. Azure 파일 동기화가 이미 구성되어 있는 Windows Server에서 일치하는 폴더를 식별합니다.
1. RoboCopy를 사용하여 복사를 시작합니다.

다음 RoboCopy 명령은 NAS 스토리지에서 Windows Server 대상 폴더로 차이점(업데이트된 파일 및 폴더)만 복사합니다. 그러면 이것을 Windows Server가 Azure 파일 공유에 동기화합니다. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

NAS 어플라이언스에서 파일이 차지하는 것보다 적은 스토리지를 Windows Server에 프로비저닝한 경우에는 클라우드 계층화가 구성된 것입니다. 로컬 Windows Server 볼륨이 가득 차면 [클라우드 계층화](storage-sync-cloud-tiering-overview.md)가 시작되고 이미 동기화된 파일이 계층화됩니다. 클라우드 계층화는 NAS 어플라이언스에서 복사를 계속하기에 충분한 공간을 생성합니다. 클라우드 계층화는 한 시간에 한 번씩 확인하여 무엇이 동기화되었는지 확인하고 99%의 볼륨 여유 공간에 도달할 수 있도록 디스크 공간을 확보합니다.
RoboCopy는 Windows Server에서 수많은 파일(로컬 스토리지보다 더 많이)을 이동해야 할 수 있습니다. 평균적으로 Azure 파일 동기화가 파일을 업로드하고 로컬 볼륨에서 계층화할 수 있는 것보다 RoboCopy가 훨씬 빠르게 이동할 것으로 예상할 수 있습니다. RoboCopy는 실패할 수 있습니다. 이를 방지하는 시퀀스로 공유를 처리하는 것이 좋습니다. 모든 공유에 대해 RoboCopy 작업을 동시에 시작하지 않거나 Windows Server에서 현재 여유 공간의 크기에 맞는 공유만 이동하는 것을 예로 들 수 있습니다. /MIR 스위치는 델타만 이동하며 일단 델타가 이동되면 다시 시작된 작업에서 이 파일을 다시 이동할 필요가 없다는 점이 좋습니다.

### <a name="user-cut-over"></a>사용자 중단

RoboCopy 명령을 처음 실행하는 경우 사용자와 애플리케이션은 계속해서 NAS의 파일에 액세스하며 해당 파일을 변경할 수도 있습니다. RoboCopy가 디렉터리를 처리하고 다음으로 이동한 후 원본 위치(NAS)의 사용자가 파일을 추가, 변경 또는 삭제할 수 있으며, 이것은 현재 RoboCopy 실행에서는 처리되지 않습니다. 이는 정상적인 동작입니다.

첫 번째 실행은 대량의 변동된 데이터를 Windows Server로 그리고 Azure 파일 동기화를 통해 클라우드로 이동하는 것입니다. 첫 번째 복사는 다음 사항에 따라 시간이 오래 걸릴 수 있습니다.

* 업로드 대역폭
* 로컬 네트워크 속도 및 RoboCopy 스레드 수가 최적으로 매칭할 수 있는 수
* RoboCopy 및 Azure 파일 동기화에서 처리해야 하는 항목(파일 및 폴더)의 수

초기 실행이 완료되면 명령을 다시 실행합니다.

두 번째로 동일한 공유에 대해 RoboCopy를 실행하면 더 빨리 완료됩니다. 마지막 실행 이후 발생한 변경 내용만 전송하면 되기 때문입니다. 동일한 공유에 대해 반복 작업을 실행할 수 있습니다.

가동 중지 시간을 허용할 수 있다고 생각되면 NAS 기반 공유에 대한 사용자 액세스를 제거해야 합니다. 이것은 사용자가 파일 및 폴더 구조와 콘텐츠를 변경하지 못하도록 하는 단계를 수행하여 구현할 수 있습니다. 예를 들어 DFS-네임스페이스가 존재하지 않는 위치를 가리키도록 하거나 공유에서 루트 ACL을 변경하는 것입니다.

RoboCopy를 마지막으로 한 번 실행합니다. 그러면 놓쳤을 수도 있는 변경 내용이 복사됩니다.
마지막 단계에 걸리는 시간은 RoboCopy 검색 속도에 따라 달라집니다. 이 시간은 이전 실행에 걸린 시간(가동 중지 시간과 동일함)을 측정하여 예상할 수 있습니다.

Windows Server 폴더에 공유를 만들고 이를 가리키도록 DFS-N 배포를 조정할 수 있습니다. NAS SMB 공유와 동일한 공유 수준의 권한을 설정해야 합니다. 엔터프라이즈급 도메인에 가입된 NAS가 있으면 사용자 SID는 자동으로 매칭됩니다. Active Directory에 사용자가 존재하고 RoboCopy가 파일과 메타데이터를 완전한 충실도로 복사하기 때문입니다. NAS에서 로컬 사용자를 사용한 경우 해당 사용자를 Windows Server 로컬 사용자로 다시 만들고 RoboCopy가 Windows Server로 이동한 기존 SID를 새 Windows Server 로컬 사용자의 SID에 매핑해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션하는 작업이 완료되었습니다. (1단계의 매핑에 따라)

이러한 복사본 중 일부를 병렬로 실행할 수 있습니다. Azure 파일 공유 범위 하나를 한 번에 처리하는 것이 좋습니다.

## <a name="troubleshoot"></a>문제 해결

발생할 가능성이 높은 문제는 RoboCopy 명령이 Windows Server의 "볼륨 가득 참"으로 인해 실패하는 것입니다. 클라우드 계층화는 매시간 한 번씩 작동하여 동기화된 로컬 Windows Server 디스크에서 콘텐츠를 비웁니다. 목표는 볼륨의 여유 공간이 99%에 도달하는 것입니다.

동기화를 진행하여 클라우드 계층화를 통해 디스크 공간을 확보하세요. 이 상황은 Windows 서버의 파일 탐색기에서 확인할 수 있습니다.

Windows 서버에 사용 가능 용량이 충분할 때 명령을 다시 실행하면 문제가 해결됩니다. 이런 상황이 발생해도 큰 문제는 없으며 계속 작업을 진행해도 됩니다. 다만, 명령을 다시 실행해야 한다는 불편함이 있습니다.

Azure 파일 동기화 문제를 해결하려면 다음 섹션의 링크를 확인하세요.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 및 Azure 파일 동기화에 대해 더 많이 알아볼 수 있습니다. 다음 문서는 고급 옵션, 모범 사례를 이해하는 데 유용하며 문제 해결 도움말도 포함되어 있습니다. 이러한 문서는 필요에 따라 [Azure 파일 공유 설명서](storage-files-introduction.md)로 연결됩니다.

* [마이그레이션 개요](storage-files-migration-overview.md)
* [AFS 개요](./storage-sync-files-planning.md)
* [AFS 배포 가이드](./storage-how-to-create-file-share.md)
* [AFS 문제 해결](storage-sync-files-troubleshoot.md)
