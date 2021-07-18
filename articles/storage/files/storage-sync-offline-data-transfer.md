---
title: Azure Data Box를 사용하여 데이터를 Azure 파일 동기화로 마이그레이션
description: Azure 파일 동기화와 호환되는 대량 데이터를 오프라인으로 마이그레이션합니다. 파일 충돌을 방지하고, 동기화를 사용하도록 설정한 후 파일 및 폴더 ACL과 타임스탬프를 유지합니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017630"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Azure DataBox를 사용하여 대량 데이터를 Azure 파일 동기화로 마이그레이션
다음 두 가지 방법으로 대량 데이터를 Azure 파일 동기화로 마이그레이션할 수 있습니다.

* **Azure 파일 동기화를 사용하여 파일 업로드.** 이 방법이 가장 간단합니다. 로컬에서 파일을 Windows Server 2012 R2 이상으로 이동하고 Azure 파일 동기화 에이전트를 설치합니다. 동기화를 설정하면 파일이 서버에서 업로드됩니다. 현재 고객은 2일마다 1TiB의 평균 업로드 속도를 경험하고 있습니다. 서버에서 데이터 센터에 너무 많은 대역폭을 사용하지 않도록 하려면 [대역폭 제한 일정](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)을 설정하는 것이 좋습니다.
* **오프라인으로 파일 전송.** 대역폭이 충분하지 않은 경우 적절한 시간 내에 Azure로 파일을 업로드하지 못할 수 있습니다. 여기서 문제는 전체 파일 세트의 초기 동기화입니다. 이러한 문제를 극복하려면 [Azure Data Box 제품군](https://azure.microsoft.com/services/storage/databox) 같은 오프라인 대량 마이그레이션 도구를 사용합니다. 

이 문서에서는 Azure 파일 동기화와 호환되는 방식으로 파일을 오프라인으로 마이그레이션하는 방법을 설명합니다. 다음 지침에 따라 파일 충돌을 방지하고 동기화를 사용하도록 설정한 후 파일 및 폴더 ACL(액세스 제어 목록)과 타임스탬프를 유지합니다.

## <a name="migration-tools"></a>마이그레이션 도구
이 문서에서 설명하는 프로세스는 Data Box뿐만 아니라 다른 오프라인 마이그레이션 도구에도 적용됩니다. 또한 인터넷을 통해 바로 작동하는 AzCopy, Robocopy, 파트너 도구 및 서비스 같은 도구에도 적용됩니다. 그러나 초기 업로드 문제를 극복하려면 이 문서의 단계에 수행하여 Azure 파일 동기화와 호환되는 방식으로 이러한 도구를 사용합니다.

경우에 따라 Azure 파일 동기화를 채택하기 전에 Windows Server 간에 이동해야 할 수 있습니다. SMS([스토리지 마이그레이션 서비스](/windows-server/storage/storage-migration-service/overview))를 사용하면 도움이 될 수 있습니다. Azure 파일 동기화에서 지원하는 서버 OS 버전(Windows Server 2012 R2 이상)으로 마이그레이션해야 하는지, Azure 파일 동기화용으로 새 시스템을 구입하여 마이그레이션이 필요한지 여부에 따라 SMS는 마이그레이션을 원활하게 수행하는 데 도움이 되는 다양한 기능 및 장점을 제공합니다.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>데이터를 오프라인으로 전송하는 도구 사용의 이점
오프라인 마이그레이션에 Data Box와 같은 전송 도구를 사용할 경우의 주요 이점은 다음과 같습니다.

- 네트워크를 통해 모든 파일을 업로드할 필요가 없습니다. 네임스페이스가 큰 경우 이 도구를 사용하면 상당한 네트워크 대역폭과 시간을 절약할 수 있습니다.
- Azure 파일 동기화를 사용하면 사용하는 전송 도구(Data Box, Azure Import/Export 서비스 등)에 관계없이 라이브 서버는 Azure로 데이터를 이동한 후에 변경되는 파일만 업로드합니다.
- Azure 파일 동기화는 오프라인 대량 마이그레이션 도구에서 ACL을 전송하지 않는 경우에도 파일 및 폴더 ACL을 동기화합니다.
- Data Box 및 Azure 파일 동기화에는 가동 중지 시간이 필요하지 않습니다. Data Box를 사용하여 Azure로 데이터를 전송하면 네트워크 대역폭을 효율적으로 사용하고 파일 충실도를 유지합니다. 또한 Azure로 데이터를 이동한 후에 변경되는 파일만 업로드하여 네임스페이스를 최신 상태로 유지할 수 있습니다.

## <a name="prerequisites-for-the-offline-data-transfer"></a>오프라인 데이터 전송에 대한 필수 구성 요소
오프라인 데이터 전송을 완료하기 전에 마이그레이션하는 서버에서 동기화를 사용하도록 설정하면 안 됩니다. 시작하기 전에 다음과 같은 사항을 고려해야 합니다.

- 대량 마이그레이션에 Data Box를 사용하려면 [Data Box에 대한 배포 필수 구성 요소](../../databox/data-box-deploy-ordered.md#prerequisites)를 검토합니다.
- 최종 Azure 파일 동기화 토폴로지를 계획합니다. [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)을 참조하세요.
- 동기화하려는 파일 공유를 포함할 Azure Storage 계정을 선택합니다. 동일한 스토리지 계정의 임시 스테이징 공유 위치로 대량 마이그레이션이 실행되도록 합니다. 대량 마이그레이션은 동일한 스토리지 계정에 있는 최종 및 스테이징 공유 위치로만 수행할 수 있습니다.
- 대량 마이그레이션은 서버 위치와의 새 동기화 관계를 만들 때만 사용할 수 있습니다. 기존 동기화 관계로는 대량 마이그레이션을 사용하도록 설정할 수 없습니다.


## <a name="process-for-offline-data-transfer"></a>오프라인 데이터 전송 프로세스
다음은 Azure Data Box와 같은 대량 마이그레이션 도구와 호환되는 방식으로 Azure 파일 동기화를 설정하는 방법입니다.

![Azure 파일 동기화 설정 방법을 보여 주는 다이어그램](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 단계 | 세부 정보 |
|---|---------------------------------------------------------------------------------------|
| ![1단계](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Data Box를 주문](../../databox/data-box-deploy-ordered.md)합니다. Data Box 제품군은 요구 사항에 맞는 [여러 제품](https://azure.microsoft.com/services/storage/databox/data)을 제공합니다. Data Box를 받으면 [데이터 복사 설명서에 따라](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) Data Box의 UNC 경로( *\\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>* )에 데이터를 복사합니다. 여기서 *ShareName* 은 스테이징 공유의 이름입니다. Data Box를 Azure로 다시 보냅니다. |
| ![2단계](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 임시 스테이징 공유로 선택한 Azure 파일 공유에 파일이 표시될 때까지 기다립니다. ‘이러한 공유로 동기화를 사용하도록 설정하지 마세요.’ |
| ![3단계](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Data Box에서 만든 각 파일 공유에 대해 비어 있는 새 공유를 만듭니다. 이 새로운 공유는 Data Box 공유와 동일한 스토리지 계정에 있어야 합니다. [Azure 파일 공유를 만드는 방법](storage-how-to-create-file-share.md)</li><li>스토리지 동기화 서비스에서 [동기화 그룹을 만듭니다](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint). 빈 공유를 클라우드 엔드포인트로 참조합니다. 모든 Data Box 파일 공유에 대해 이 단계를 반복합니다. [Azure 파일 동기화 설정](storage-sync-files-deployment-guide.md)</li></ul> |
| ![4단계](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [라이브 서버 디렉터리를 서버 엔드포인트로 추가](storage-sync-files-deployment-guide.md#create-a-server-endpoint)합니다. 프로세스에서 파일을 Azure로 이동했음을 지정하고 스테이징 공유를 참조합니다. 필요에 따라 클라우드 계층화를 사용하거나 사용하지 않도록 설정할 수 있습니다. 라이브 서버에서 서버 엔드포인트를 만드는 동안 스테이징 공유를 참조합니다. **서버 엔드포인트 추가** 블레이드의 **오프라인 데이터 전송** 에서 **사용** 을 선택한 다음, 클라우드 엔드포인트와 동일한 스토리지 계정에 있어야 하는 스테이징 공유를 선택합니다. 여기에서 사용 가능한 공유 목록은 아직 동기화되지 않은 공유 및 스토리지 계정으로 필터링됩니다. 이 표 다음의 스크린샷은 Azure Portal에서 서버 엔드포인트를 만드는 동안 DataBox 공유를 참조하는 방법을 보여 줍니다. |
| ![5단계](media/storage-sync-files-offline-data-transfer/bullet_5.png) | 이전 단계에서 서버 엔드포인트를 추가한 다음에는 데이터가 올바른 원본에서 자동으로 흐르기 시작합니다. [공유 동기화](#syncing-the-share) 섹션에서는 DataBox 공유 또는 Windows Server에서 데이터가 흐르는 경우에 대해 설명합니다. |
| |

![새 서버 엔드포인트를 만드는 동안 오프라인 데이터 전송을 사용하도록 설정하는 방법을 보여 주는 Azure Portal 사용자 인터페이스의 스크린샷](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>공유 동기화
서버 엔드포인트를 만든 후 동기화가 시작됩니다. 동기화 프로세스에서는 서버에 있는 각 파일이 Data Box에서 파일을 보관한 스테이징 공유에도 있는지 여부를 확인합니다. 파일이 있을 경우 동기화 프로세스는 서버에서 파일을 업로드하지 않고 스테이징 공유에서 파일을 복사합니다. 파일이 스테이징 공유에 없거나 로컬 서버에서 최신 버전을 사용할 수 있는 경우 동기화 프로세스는 로컬 서버에서 파일을 업로드합니다.

공유를 동기화하면 동기화는 로컬 서버의 파일 변형에서 누락된 파일 특성, 권한, 타임스탬프를 병합하여 DataBox 공유의 해당 파일과 결합합니다. 이렇게 하면 각 파일 및 폴더는 가능한 모든 파일 충실도로 Azure 파일 공유에 도착합니다.

> [!IMPORTANT]
> 서버 엔드포인트를 만드는 동안에만 대량 마이그레이션 모드를 사용하도록 설정할 수 있습니다. 서버 엔드포인트를 설정한 다음에는 이미 동기화된 서버에서 대량 마이그레이션된 데이터를 네임스페이스로 통합할 수 없습니다.

## <a name="acls-and-timestamps-on-files-and-folders"></a>파일 및 폴더에 대한 ACL 및 타임스탬프
Azure 파일 동기화는 사용된 대량 마이그레이션 도구가 초기에 ACL을 전송하지 않은 경우에도 파일 및 폴더 ACL이 라이브 서버에서 동기화되도록 합니다. 따라서 스테이징 공유에는 파일 및 폴더에 대한 ACL이 포함되지 않아도 됩니다. 새 서버 엔드포인트를 만들 때 오프라인 데이터 마이그레이션 기능을 사용하도록 설정하면 모든 파일 ACL이 서버에서 동기화됩니다. 새로 만들고 수정한 타임스탬프도 동기화됩니다.

## <a name="shape-of-the-namespace"></a>네임스페이스의 모양
동기화를 사용하도록 설정하면 서버의 내용에 따라 네임스페이스의 모양이 결정됩니다. Data Box 스냅샷 및 마이그레이션이 완료된 후 로컬 서버에서 파일을 삭제하면 이러한 파일은 라이브 상태로 전환되지 않고 네임스페이스를 동기화합니다. 이러한 파일은 스테이징 공유에 유지되지만 복사되지는 않습니다. 동기화할 경우 라이브 서버에 따라 네임스페이스가 유지되므로 필요한 작업입니다. Data Box ‘스냅샷’은 효율적인 파일 복사를 위한 스테이징 기반일 뿐입니다. 라이브 네임스페이스의 모양에 대한 권한이 아닙니다.

## <a name="cleaning-up-after-bulk-migration"></a>대량 마이그레이션 후 정리 
서버에서 네임스페이스의 초기 동기화를 완료하면 Data Box 대량 마이그레이션된 파일은 스테이징 파일 공유를 사용합니다. Azure Portal의 **서버 엔드포인트 속성** 블레이드에 있는 **오프라인 데이터 전송** 섹션에서 상태가 **진행 중** 에서 **완료됨** 으로 변경됩니다. 

![오프라인 데이터 전송의 상태 및 사용 안 함 컨트롤이 있는 서버 엔드포인트 속성 블레이드의 스크린샷](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

이제 스테이징 공유를 정리하여 비용을 절감할 수 있습니다.

1. **서버 엔드포인트 속성** 블레이드에서 상태가 **완료됨** 인 경우 **오프라인 데이터 전송 사용 안 함** 을 선택합니다.
2. 스테이징 공유를 삭제하여 비용을 절감하는 것이 좋습니다. 스테이징 공유에는 파일 및 폴더 ACL이 포함되어 있지 않으므로 유용하지 않을 수 있습니다. 특정 시점 백업을 위해 [Azure 파일 공유 동기화의 실제 스냅샷](storage-snapshots-files.md)을 만듭니다. 일정에 따라 [스냅샷을 만들도록 Azure Backup을 설정]( ../../backup/backup-afs.md)할 수 있습니다.

상태가 **완료됨** 인 경우 또는 잘못된 구성으로 인해 취소하려는 경우에만 오프라인 데이터 전송 모드를 사용하지 않도록 설정합니다. 배포하는 동안 이 모드를 사용하지 않도록 설정하면 스테이징 공유를 여전히 사용할 수 있더라도 서버에서 파일이 업로드되기 시작합니다.

> [!IMPORTANT]
> 오프라인 데이터 전송 모드를 사용하지 않도록 설정한 후에는 대량 마이그레이션의 스테이징 공유를 여전히 사용할 수 있더라도 다시 사용하도록 설정할 수 없습니다.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>클라우드의 Azure 파일 동기화 및 미리 시드된 파일

DataBox가 아닌 다른 방법(예: AzCopy, 클라우드 백업의 RoboCopy, 다른 모든 방법)으로 Azure 파일 공유에서 파일을 시드한 경우 이 문서에 설명된 [오프라인 데이터 전송 프로세스](#process-for-offline-data-transfer)를 계속 수행해야 합니다. 클라우드로 파일을 이동하는 방법으로 DataBox를 무시하면 됩니다. 그러나 Azure 파일 동기화의 최종 연결된 공유가 아니라 ‘스테이징 공유’로 파일을 시드하는 프로세스를 계속 수행하도록 해야 합니다.

> [!WARNING]
> Azure 파일 동기화의 **최종 연결된 공유가 아니라 스테이징 공유로 파일을 시드하는 프로세스를 따릅니다**. 그러지 않으면 파일 충돌이 발생(두 파일 버전이 모두 저장됨)할 뿐만 아니라 라이브 서버에서 삭제된 파일이 시드된 이전 파일 세트에 계속 있는 경우 다시 돌아올 수 있습니다. 또한 폴더 변경 내용이 서로 병합되므로 이러한 실수 후에는 네임스페이스를 구분하기가 매우 어렵습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)