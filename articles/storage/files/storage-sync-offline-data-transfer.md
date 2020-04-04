---
title: Azure 데이터 상자를 사용하여 데이터를 Azure 파일 동기화로 마이그레이션
description: Azure 파일 동기화와 호환되는 방식으로 대량 데이터를 마이그레이션합니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d0331419de89775062f1309c5d854cd7325c68e4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656755"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Azure DataBox를 사용하여 대량 데이터를 Azure 파일 동기화로 마이그레이션
다음 두 가지 방법으로 대량 데이터를 Azure File Sync로 마이그레이션할 수 있습니다.

* **Azure 파일 동기화를 사용하여 파일을 업로드합니다.** 이것이 가장 간단한 방법입니다. 파일을 Windows Server 2012 R2 이상으로 로컬로 이동하고 Azure 파일 동기화 에이전트를 설치합니다. 동기화를 설정하면 서버에서 파일이 업로드됩니다. (현재 고객은 약 2일마다 평균 1TiB의 업로드 속도를 경험합니다.) 서버가 데이터 센터에 너무 많은 대역폭을 사용하지 않도록 하려면 [대역폭 제한 일정을](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)설정할 수 있습니다.
* **파일을 오프라인으로 전송합니다.** 대역폭이 충분하지 않으면 적절한 시간 내에 Azure에 파일을 업로드하지 못할 수 있습니다. 문제는 전체 파일 집합의 초기 동기화입니다. 이 문제를 해결하려면 Azure 데이터 상자 [패밀리와](https://azure.microsoft.com/services/storage/databox)같은 오프라인 대량 마이그레이션 도구를 사용합니다. 

이 문서에서는 Azure 파일 동기화와 호환되는 방식으로 오프라인으로 파일을 마이그레이션하는 방법을 설명합니다. 이 지침에 따라 파일 충돌을 방지하고 동기화를 사용하도록 설정한 후 파일 및 폴더 액세스 제어 목록(ACL) 및 타임스탬프를 유지합니다.

## <a name="migration-tools"></a>마이그레이션 도구
이 문서에서 설명하는 프로세스는 데이터 상자뿐만 아니라 다른 오프라인 마이그레이션 도구에도 적합합니다. 또한 AzCopy, 로보카피 또는 인터넷을 통해 바로 작동하는 파트너 도구 및 서비스와 같은 도구에도 사용할 수 있습니다. 그러나 초기 업로드 문제를 해결하려면 이 문서의 단계를 따라 Azure File Sync와 호환되는 방식으로 이러한 도구를 사용합니다.

경우에 따라 Azure 파일 동기화를 채택하기 전에 한 Windows 서버에서 다른 Windows 서버로 이동해야 합니다. [SMS(저장소 마이그레이션 서비스)가](https://aka.ms/storagemigrationservice) 도움이 될 수 있습니다. Azure File Sync(Windows Server 2012R2 이상)에서 지원하는 서버 OS 버전으로 마이그레이션해야 하거나 Azure File Sync용 새 시스템을 구입하기 때문에 마이그레이션해야 하는 경우 SMS에는 마이그레이션을 원활하게 수행하는 데 도움이 되는 다양한 기능과 이점이 있습니다.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>도구를 사용하여 데이터를 오프라인으로 전송하는 이점
다음은 오프라인 마이그레이션을 위해 데이터 상자와 같은 전송 도구를 사용할 때의 주요 이점입니다.

- 네트워크를 통해 모든 파일을 업로드할 필요는 없습니다. 큰 네임스페이스의 경우 이 도구는 상당한 네트워크 대역폭과 시간을 절약할 수 있습니다.
- Azure File Sync를 사용하는 경우 사용하는 전송 도구(데이터 상자, Azure 가져오기/내보내기 서비스 등)에 관계없이 라이브 서버는 데이터를 Azure로 이동한 후 변경되는 파일만 업로드합니다.
- Azure File Sync는 오프라인 대량 마이그레이션 도구가 ACL을 전송하지 않는 경우에도 파일 및 폴더 ACL을 동기화합니다.
- 데이터 상자 및 Azure 파일 동기화에는 가동 중지 시간이 필요하지 않습니다. 데이터 상자를 사용하여 Azure로 데이터를 전송하는 경우 네트워크 대역폭을 효율적으로 사용하고 파일 충실도를 유지합니다. 또한 데이터를 Azure로 이동한 후 변경되는 파일만 업로드하여 네임스페이스를 최신 상태로 유지합니다.

## <a name="prerequisites-for-the-offline-data-transfer"></a>오프라인 데이터 전송을 위한 필수 구성 조건
오프라인 데이터 전송을 완료하기 전에 마이그레이션할 서버에서 동기화를 사용하도록 설정하면 안 됩니다. 시작하기 전에 고려해야 할 다른 사항은 다음과 같습니다.

- 대량 마이그레이션에 데이터 상자를 사용하려는 경우: [데이터 상자의 배포 필수 구성 조건을](../../databox/data-box-deploy-ordered.md#prerequisites)검토합니다.
- 최종 Azure 파일 동기화 토폴로지 계획: [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- 동기화하려는 파일 공유를 포함할 Azure Storage 계정을 선택합니다. 동일한 스토리지 계정의 임시 스테이징 공유 위치로 대량 마이그레이션이 실행되도록 합니다. 대량 마이그레이션은 동일한 스토리지 계정에 있는 최종 및 스테이징 공유 위치로만 수행할 수 있습니다.
- 대량 마이그레이션은 서버 위치와의 새 동기화 관계를 만들 때만 사용할 수 있습니다. 기존 동기화 관계로는 대량 마이그레이션을 사용하도록 설정할 수 없습니다.


## <a name="process-for-offline-data-transfer"></a>오프라인 데이터 전송 프로세스
Azure 데이터 상자와 같은 대량 마이그레이션 도구와 호환되는 방식으로 Azure 파일 동기화를 설정하는 방법은 다음과 같습니다.

![Azure 파일 동기화를 설정하는 방법을 보여 주어 도표](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 단계 | 세부 정보 |
|---|---------------------------------------------------------------------------------------|
| ![1단계](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Data Box를 주문](../../databox/data-box-deploy-ordered.md)합니다. Data Box 제품군은 고객의 요구에 맞는 [여러 제품을](https://azure.microsoft.com/services/storage/databox/data) 제공합니다. 데이터 상자를 받으면 [설명서를](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) 따라 데이터 상자의 이 UNC 경로 * \\ \>\<\>\<\>StorageAccountName_AzFile<에*데이터를 복사합니다. 여기서 *ShareName은* 스테이징 공유의 이름입니다. Data Box를 Azure로 다시 보냅니다. |
| ![2단계](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 파일이 임시 스테이징 공유로 선택한 Azure 파일 공유에 표시될 때까지 기다립니다. *이러한 공유에 동기화를 사용하도록 설정하지 마십시오.* |
| ![3단계](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>데이터 상자가 만든 각 파일 공유에 대해 새 빈 공유를 만듭니다. 이 새 공유는 데이터 상자 공유와 동일한 저장소 계정에 있어야 합니다. [Azure 파일 공유를 만드는 방법](storage-how-to-create-file-share.md)</li><li>저장소 동기화 서비스에서 [동기화 그룹을 만듭니다.](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) 빈 공유를 클라우드 끝점으로 참조합니다. 모든 Data Box 파일 공유에 대해 이 단계를 반복합니다. [Azure 파일 동기화 설정](storage-sync-files-deployment-guide.md).</li></ul> |
| ![4단계:](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [라이브 서버 디렉터리를 서버 엔드포인트로 추가](storage-sync-files-deployment-guide.md#create-a-server-endpoint)합니다. 프로세스에서 파일을 Azure로 이동하도록 지정하고 스테이징 공유를 참조합니다. 필요에 따라 클라우드 계층화를 활성화하거나 사용하지 않도록 설정할 수 있습니다. 라이브 서버에서 서버 끝점을 만드는 동안 스테이징 공유를 참조합니다. 서버 **끝점 블레이드 추가에서** **오프라인 데이터 전송에서** **사용 중**에서 를 선택한 다음 클라우드 끝점과 동일한 저장소 계정에 있어야 하는 스테이징 공유를 선택합니다. 여기서 사용 가능한 공유 목록은 저장소 계정 및 아직 동기화되지 않은 공유로 필터링됩니다. 이 테이블의 스크린샷은 Azure Portal에서 서버 끝점을 만드는 동안 DataBox 공유를 참조하는 방법을 보여 주며 있습니다. |
| ![5단계](media/storage-sync-files-offline-data-transfer/bullet_5.png) | 이전 단계에서 서버 끝점을 추가하면 데이터가 올바른 원본에서 자동으로 흐르기 시작합니다. [공유 동기화](#syncing-the-share) 섹션에서는 DataBox 공유 또는 Windows 서버에서 데이터가 흐르는 시기를 설명합니다. |
| |

![새 서버 끝점을 만드는 동안 오프라인 데이터 전송을 활성화하는 방법을 보여 주는 Azure 포털 사용자 인터페이스의 스크린샷](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>공유 동기화
서버 끝점을 만든 후 동기화가 시작됩니다. 동기화 프로세스는 서버의 각 파일이 데이터 박스가 파일을 보관한 스테이징 공유에도 존재하는지 여부를 결정합니다. 파일이 있는 경우 동기화 프로세스는 서버에서 파일을 업로드하는 대신 스테이징 공유에서 파일을 복사합니다. 파일이 스테이징 공유에 없거나 로컬 서버에서 최신 버전을 사용할 수 있는 경우 동기화 프로세스는 로컬 서버에서 파일을 업로드합니다.

공유를 동기화할 때 동기화는 로컬 서버의 파일 변형에서 누락된 파일 특성, 사용 권한 또는 타임스탬프를 병합하여 DataBox 공유의 파일 속성과 결합합니다. 이렇게 하면 각 파일 및 폴더가 Azure 파일 공유에서 가능한 모든 파일 충실도를 갖도록 합니다.

> [!IMPORTANT]
> 서버 끝점을 만드는 동안에만 대량 마이그레이션 모드를 활성화할 수 있습니다. 서버 끝점을 설정한 후에는 이미 동기화된 서버에서 대량 으로 마이그레이션된 데이터를 네임스페이스에 통합할 수 없습니다.

## <a name="acls-and-timestamps-on-files-and-folders"></a>파일 및 폴더에 대한 ACL 및 타임스탬프
Azure File Sync는 사용한 대량 마이그레이션 도구가 ACL을 처음에 전송하지 않은 경우에도 파일 및 폴더 ACL이 라이브 서버에서 동기화되도록 합니다. 따라서 스테이징 공유에 파일 및 폴더에 대한 ACL이 포함되어 있지 않아도 됩니다. 새 서버 끝점을 만들 때 오프라인 데이터 마이그레이션 기능을 사용하도록 설정하면 모든 파일 ACL이 서버에 동기화됩니다. 새로 생성되고 수정된 타임스탬프도 동기화됩니다.

## <a name="shape-of-the-namespace"></a>네임스페이스의 모양
동기화를 사용하도록 설정하면 서버의 내용이 네임스페이스의 모양을 결정합니다. 데이터 상자 스냅숏 및 마이그레이션이 완료된 후 로컬 서버에서 파일이 삭제되면 이러한 파일은 네임스페이스를 동기화하는 라이브로 이동하지 않습니다. 스테이징 공유에 남아 있지만 복사되지 는 않습니다. 동기화는 라이브 서버에 따라 네임스페이스를 유지하므로 이 필요합니다. 데이터 상자 *스냅숏은* 효율적인 파일 복사를 위한 스테이징 그라운드일 뿐입니다. 라이브 네임스페이스의 모양에 대한 권한이 아닙니다.

## <a name="cleaning-up-after-bulk-migration"></a>대량 마이그레이션 후 정리 
서버가 네임스페이스의 초기 동기화를 완료하면 데이터 상자 대량 마이그레이션 파일은 스테이징 파일 공유를 사용합니다. Azure 포털의 **서버 끝점 속성** 블레이드에서 **오프라인 데이터 전송** 섹션에서 상태가 진행 **중에서** **완료됨으로**변경됩니다. 

![오프라인 데이터 전송을 위한 상태 및 비활성화 컨트롤이 있는 서버 끝점 속성 블레이드의 스크린샷](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

이제 스테이징 공유를 정리하여 비용을 절감할 수 있습니다.

1. 서버 **끝점 속성** 블레이드에서 상태가 **완료되면** **오프라인 데이터 전송 을 선택합니다.**
2. 비용을 절감하기 위해 스테이징 공유를 삭제하는 것이 좋습니다. 스테이징 공유에는 파일 및 폴더 ACL이 포함되어 있지 않으므로 유용하지 않을 수 있습니다. 백업 시점-인-시의 경우 동기화 [Azure 파일 공유의 실제 스냅숏을](storage-snapshots-files.md)만듭니다. Azure [Backup을 설정하여]( ../../backup/backup-afs.md) 일정에 따라 스냅숏을 만들 수 있습니다.

상태가 **완료되거나** 잘못된 구성으로 인해 취소하려는 경우에만 오프라인 데이터 전송 모드를 사용하지 않도록 설정합니다. 배포 중에 모드를 사용하지 않도록 설정하면 스테이징 공유를 계속 사용할 수 있는 경우에도 파일이 서버에서 업로드되기 시작합니다.

> [!IMPORTANT]
> 오프라인 데이터 전송 모드를 사용하지 않도록 설정한 후에는 대량 마이그레이션의 스테이징 공유를 계속 사용할 수 있는 경우에도 다시 활성화할 수 없습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
