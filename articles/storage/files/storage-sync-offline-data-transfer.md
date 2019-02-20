---
title: Azure File Sync에 대한 오프라인 수집을 위해 Data Box 및 기타 메서드를 사용합니다.
description: 호환되는 대량 마이그레이션 동기화 지원을 사용하도록 설정하기 위한 프로세스 및 모범 사례입니다.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212669"
---
# <a name="migrate-to-azure-file-sync"></a>Azure 파일 동기화로 마이그레이션
Azure File Sync로 전환하기 위해 다음과 같은 몇 가지 옵션을 사용할 수 있습니다.

### <a name="uploading-files-via-azure-file-sync"></a>Azure File Sync를 통해 파일 업로드
가장 간단한 옵션은 파일을 로컬로 Windows Server 2012 R2 이상으로 이동하고 Azure File Sync 에이전트를 설치하는 것입니다. 동기화가 구성되면 서버에서 파일이 업로드됩니다. 현재, 모든 고객에게서 약 2일 간격으로 1TB의 평균 업로드 속도가 확인되고 있습니다.
서버가 데이터 센터에서 잘 작동하도록 하는 [대역폭 조절 일정](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)을 고려합니다.

### <a name="offline-bulk-transfer"></a>오프라인 대량 전송
업로드하는 것이 확실히 가장 간단한 옵션이지만, 사용 가능한 대역폭이 적절한 시간 내에 Azure로 파일을 동기화하도록 허용하지 않는 경우에는 이 옵션을 사용할 수 없습니다. 여기서 극복해야 할 과제는 전체 파일 세트의 초기 동기화입니다. 나중에 Azure File Sync는 네임스페이스에서 수행된 변경 내용만 이동하므로 일반적으로 훨씬 더 적은 대역폭을 사용합니다.
이러한 초기 업로드 문제를 극복하기 위해 Azure [Data Box 제품군](https://azure.microsoft.com/services/storage/databox)과 같은 오프라인 대량 마이그레이션 도구를 사용할 수 있습니다. 다음 문서는 Azure File Sync 호환 방식으로 오프라인 마이그레이션을 활용하기 위해 수행해야 하는 프로세스를 중점적으로 설명합니다. 또한 동기화를 사용하도록 설정한 경우 파일 충돌을 방지하고 파일 및 폴더 ACL과 타임스탬프를 유지하는 데 도움이 되는 모범 사례를 설명합니다.

### <a name="online-migration-tools"></a>온라인 마이그레이션 도구
아래에서 설명하는 프로세스가 Data Box에만 해당하는 것은 아니며, 오프라인 마이그레이션 도구(예: Data Box) 또는 온라인 도구(예: AzCopy, Robocopy) 또는 타사 도구와 서비스에도 적용됩니다. 초기 업로드가 과제를 극복하는 방법에 관계없이, 아래에 설명된 단계에 따라 동기화 호환 방식으로 이러한 도구를 활용하는 것도 중요합니다.


## <a name="offline-data-transfer-benefits"></a>오프라인 데이터 전송의 이점
Data Box를 사용할 경우 오프라인 마이그레이션의 주요 이점은 다음과 같습니다.

- Data Box와 같은 오프라인 대량 전송 프로세스를 통해 파일을 Azure로 마이그레이션할 때는 네트워크를 통해 서버의 모든 파일을 업로드할 필요가 없습니다. 대형 네임스페이스의 경우 이로 인해 네트워크 대역폭 및 시간 측면에서 비용을 상당히 절감할 수 있습니다.
- Azure File Sync를 사용할 경우 사용하는 전송 모드에 관계없이(Data Box, Azure Import 등) 라이브 서버는 사용자가 Azure로 데이터를 이동한 이후에 변경된 파일만 업로드합니다.
- Azure File Sync는 오프라인 대량 마이그레이션 제품이 ACL을 전송하지 않더라도 파일과 폴더 ACL 역시 동기화되게 합니다.
- Azure Data Box 및 Azure File Sync를 사용할 때는 가동 중지 시간이 제로가 됩니다. Data Box를 사용하여 Azure로 데이터를 전송하면 파일 충실도를 유지하면서 네트워크 대역폭을 효율적으로 사용할 수 있습니다. 또한 Data Box가 전송된 이후에 변경된 파일만 업로드하여 네임스페이스를 최신 상태로 유지합니다.

## <a name="plan-your-offline-data-transfer"></a>오프라인 데이터 전송 계획
시작하기 전에 다음 정보를 검토하세요.

- Azure File Sync를 통한 동기화를 사용하도록 설정하기 전에 하나 또는 여러 Azure 파일 공유에 대한 대량 마이그레이션을 완료합니다.
- 대량 마이그레이션을 위해 Data Box를 사용하려는 경우 [Data Box의 배포 필수 구성 요소](../../databox/data-box-deploy-ordered.md#prerequisites)를 검토합니다.
- 최종 Azure File Sync 토폴로지 계획 [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- 동기화하려는 파일 공유를 포함할 Azure Storage 계정을 선택합니다. 동일한 스토리지 계정의 임시 스테이징 공유 위치로 대량 마이그레이션이 실행되도록 합니다. 대량 마이그레이션은 동일한 스토리지 계정에 있는 최종 및 스테이징 공유 위치로만 수행할 수 있습니다.
- 대량 마이그레이션은 서버 위치와의 새 동기화 관계를 만들 때만 사용할 수 있습니다. 기존 동기화 관계로는 대량 마이그레이션을 사용하도록 설정할 수 없습니다.

## <a name="offline-data-transfer-process"></a>오프라인 데이터 전송 프로세스
이 섹션에서는 Azure Data Box와 같은 대량 마이그레이션 도구와 호환되는 방식으로 Azure File Sync를 설정하는 프로세스를 설명합니다.

![또한 다음 단락에서 자세히 설명하는 프로세스 단계 시각화](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 단계 | 세부 정보 |
|---|---------------------------------------------------------------------------------------|
| ![프로세스 단계 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Data Box를 주문](../../databox/data-box-deploy-ordered.md)합니다. [Data Box 제품군 내에는 사용자의 요구에 맞는 여러 제품](https://azure.microsoft.com/services/storage/databox/data)이 있습니다. Data Box를 수신하고 Data Box [설명서에 따라 데이터를 복사](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)합니다. 데이터가 Data Box의 UNC 경로 `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>`으로 복사되는지 확인합니다. 여기서 `ShareName`은 스테이징 공유의 이름입니다. Data Box를 Azure로 다시 보냅니다. |
| ![프로세스 단계 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 임시 스테이징 공유로 지정한 Azure 파일 공유에 파일이 표시될 때까지 기다립니다. **이러한 공유에 대한 동기화는 사용하지 않도록 설정합니다.** |
| ![프로세스 단계 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Data Box가 만든 각 파일 공유에 대해 비어 있는 새 공유를 만듭니다. 이 새로운 공유가 Data Box 공유와 동일한 스토리지 계정에 있는지 확인합니다. [Azure 파일 공유를 만드는 방법](storage-how-to-create-file-share.md) |
| ![프로세스 단계 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | 스토리지 동기화 서비스에서 [동기화 그룹을 만들고](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) 빈 공유를 클라우드 엔드포인트로 참조합니다. 모든 Data Box 파일 공유에 대해 이 단계를 반복합니다. [Azure File Sync 배포](storage-sync-files-deployment-guide.md) 가이드를 검토하고 Azure File Sync를 설정하는 데 필요한 단계를 수행합니다. |
| ![프로세스 단계 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [라이브 서버 디렉터리를 서버 엔드포인트로 추가](storage-sync-files-deployment-guide.md#create-a-server-endpoint)합니다. 이 프로세스에서 파일을 Azure로 이미 이동했음을 지정하고 스테이징 공유를 참조합니다. 클라우드 계층화를 필요에 따라 사용하거나 사용하지 않도록 설정할 수 있습니다. 라이브 서버에서 서버 엔드포인트를 만드는 동안 스테이징 공유를 참조해야 합니다. 새 서버 엔드포인트 블레이드에서 "오프라인 데이터 전송"(아래 이미지 참조)을 사용하도록 설정하고 클라우드 엔드포인트와 동일한 스토리지 계정에 있어야 하는 스테이징 공유를 참조합니다. 사용 가능한 공유 목록은 스토리지 계정 및 아직 동기화하지 않은 공유에 따라 필터링됩니다. |

![새 서버 엔드포인트를 만드는 동안 오프라인 데이터 전송을 사용하도록 설정하기 위한 Azure Portal 사용자 인터페이스 시각화](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>공유 동기화
서버 엔드포인트를 만들면 동기화가 시작됩니다. 동기화 기능은 서버에 있는 각 파일이 Data Box가 파일을 보관한 스테이징 공유에도 존재하는지를 확인합니다. 해당 위치에 존재할 경우 동기화 기능은 서버에서 파일을 업로드하지 않고 스테이징 공유에서 파일을 복사합니다. 파일이 스테이징 공유에 없거나, 로컬 서버에서 최신 버전을 사용할 수 있는 경우 동기화 기능은 로컬 서버에서 파일을 업로드합니다.

> [!IMPORTANT]
> 서버 엔드포인트를 만드는 동안에만 대량 마이그레이션 모드를 사용하도록 설정할 수 있습니다. 일단 서버 엔드포인트가 설정되면, 이미 동기화 중인 서버의 대량 마이그레이션 데이터를 네임스페이스에 통합할 방법이 현재는 없습니다.

## <a name="acls-and-timestamps-on-files-and-folders"></a>파일 및 폴더에 대한 ACL 및 타임스탬프
Azure File Sync는 사용된 대량 마이그레이션 도구가 초기에는 ACL을 전송하지 않았더라도 파일 및 폴더 ACL이 라이브 서버에서 동기화되도록 합니다. 즉, 스테이징 공유에 파일 및 폴더에 대한 ACL이 없는 것은 정상적인 것입니다. 새 서버 엔드포인트를 만들 때 오프라인 데이터 마이그레이션 기능을 사용하도록 설정하면 ACL은 해당 시간에 서버의 모든 파일에 대해 동기화됩니다. create- 및 modified- 타임스탬프의 경우도 마찬가지입니다.

## <a name="shape-of-the-namespace"></a>네임스페이스의 모양
네임스페이스의 모양은 동기화를 사용하도록 설정했을 때 서버에 있는 항목에 따라 결정됩니다. Data Box "-snapshot" 및 -migration 이후에 로컬 서버에서 파일을 삭제하면 이러한 파일은 라이브 상태가 되지 않고 네임스페이스를 동기화합니다. 스테이징 공유에는 계속 존재하지만 복사되지 않습니다. 동기화할 경우 라이브 서버에 따라 네임스페이스가 유지되므로 바람직한 동작입니다. Data Box "snapshot"은 라이브 네임스페이스 모양을 규정하지 않으며 효율적인 파일 복사를 위한 스테이징 기반일 뿐입니다.

## <a name="finishing-bulk-migration-and-clean-up"></a>대량 마이그레이션 및 정리 완료
다음 스크린샷은 Azure Portal의 서버 엔드포인트 속성 블레이드를 보여 줍니다. 오프라인 데이터 전송 섹션에서 프로세스의 상태를 확인할 수 있습니다. "In Progress" 또는 "Completed" 상태가 표시됩니다.

서버가 전체 네임스페이스의 초기 동기화를 완료하면 Data Box 대량 마이그레이션 파일이 있는 스테이징 파일 공유를 더 이상 사용하지 않게 됩니다. 오프라인 데이터 전송에 대한 서버 엔드포인트 속성에서 상태가 "Completed"로 변경되는 것을 확인합니다. 지금은 비용 절감을 위해 스테이징 공유를 정리할 수 있습니다.

1. 상태가 "Completed"인 경우 서버 엔드포인트 속성에서 "오프라인 데이터 전송 사용 안 함"을 누릅니다.
2. 비용 절감을 위해 스테이징 공유를 삭제하는 것이 좋습니다. 스테이징 공유에는 파일 및 폴더 ACL을 포함할 가능성이 없으며 사용이 제한적입니다. "특정 시점" 백업을 위해서는 [Azure 파일 공유를 동기화하는 실제 스냅숏](storage-snapshots-files.md)을 만드는 것이 좋습니다. 일정에 따라 [Azure Backup을 사용하여 스냅숏을 생성]( ../../backup/backup-azure-files.md)할 수 있습니다.

![오프라인 데이터 전송의 상태 및 사용 안 함 컨트롤이 있는 서버 엔드포인트 속성의 Azure Portal 사용자 인터페이스 시각화](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

상태가 "Completed"일 때만 이 모드를 사용하지 않도록 설정해야 하며, 그렇지 않은 경우 구성 오류로 인해 중단할 수 있습니다. 합법적인 배포 중간에 이 모드를 사용하지 않도록 설정하면 스테이징 공유를 여전히 사용할 수 있더라도 서버에서 파일이 업로드되기 시작합니다.

> [!IMPORTANT]
> 오프라인 데이터 전송을 사용하지 않도록 설정한 후에는 대량 마이그레이션의 스테이징 공유를 여전히 사용할 수 있더라도 다시 사용하도록 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
