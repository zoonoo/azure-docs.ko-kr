---
title: Azure Cosmos DB의 특정 시점 복원 기능을 사용한 지속적인 백업
description: Azure Cosmos DB의 특정 시점 복원 기능은 실수로 인한 쓰기, 삭제 작업으로부터 데이터를 복구하거나 임의의 지역으로 데이터를 복원하는 데 도움이 됩니다. 가격 책정 및 현재 제한 사항에 대해 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393142"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Azure Cosmos DB의 특정 시점 복원 기능(미리 보기)을 사용한 지속적인 백업
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대한 특정 시점 복원 기능(지속적인 백업 모드)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Cosmos DB의 특정 시점 복원 기능(미리 보기)은 다음과 같은 여러 시나리오에서 유용합니다.

* 컨테이너 내에서 실수로 인한 쓰기 또는 삭제 작업으로부터 복구
* 삭제된 계정, 데이터베이스 또는 컨테이너를 복원
* 복원 시점에 백업이 존재하던 모든 지역으로 복원

Azure Cosmos DB는 추가로 프로비전된 처리량(RU)을 사용하지 않으며 데이터베이스의 성능 및 가용성에 영향을 주지 않고 백그라운드에서 데이터 백업을 수행합니다. 지속적인 백업은 계정이 있는 모든 지역에서 수행됩니다. 다음 이미지는 미국 서부에 쓰기 지역이 있고 미국 동부 및 미국 동부 2에 읽기 지역이 있는 컨테이너가 각 지역의 원격 Azure Blob Storage 계정에 백업되는 방법을 보여 줍니다. 기본적으로 각 지역은 로컬 중복 스토리지 계정에 백업을 저장합니다. 지역에서 [가용성 영역](high-availability.md#availability-zone-support)을 사용하는 경우 백업은 영역 중복 스토리지 계정에 저장됩니다.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Blob Storage로 Azure Cosmos DB 데이터 백업" lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

복원에 사용할 수 있는 기간(보존 기간이라고도 함)은 '30일 이전' 또는 '리소스 생성 시점' 중 작은 값입니다. 복원 시점은 보존 기간 내에 모든 타임스탬프가 될 수 있습니다.

공개 미리 보기에서는 [Azure Portal](continuous-backup-restore-portal.md), [Azure 명령줄 인터페이스](continuous-backup-restore-command-line.md)(az CLI), [Azure PowerShell](continuous-backup-restore-powershell.md) 또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용하여 특정 시점의 SQL API 또는 MongoDB 콘텐츠용 Azure Cosmos DB 계정을 다른 계정으로 복원할 수 있습니다.

## <a name="what-is-restored"></a>복원되는 항목

안정적인 상태에서 원본 계정(데이터베이스, 컨테이너 및 항목 포함)에서 수행되는 모든 변경은 100초 이내에 비동기적으로 백업됩니다. 백업 미디어(Azure 스토리지)가 다운되었거나 사용할 수 없는 경우에는 미디어를 다시 사용할 수 있을 때까지 변경 내용이 로컬로 유지되다가 복원될 수 있는 작업의 충실도 손실을 방지하기 위해 플러시됩니다.

프로비저닝된 처리량 컨테이너, 공유 처리량 데이터베이스 또는 전체 계정 조합을 복원하도록 선택할 수 있습니다. 복원 작업을 수행하면 모든 데이터 및 해당 인덱스 속성이 새 계정으로 복원됩니다. 복원 프로세스에 따라 계정, 데이터베이스 또는 컨테이너에 복원된 모든 데이터가 지정된 복원 시간까지 일관되도록 보장됩니다. 복원 기간은 복원해야 하는 데이터의 양에 따라 달라집니다.

> [!NOTE]
> 지속적인 백업 모드를 사용하면 Azure Cosmos DB 계정을 사용할 수 있는 모든 지역에서 백업이 수행됩니다. 각 지역 계정에 대해 수행되는 백업은 기본적으로 로컬 중복이며, 계정이 해당 지역에서 [가용성 영역](high-availability.md#availability-zone-support) 기능이 사용하도록 설정된 경우에는 영역 중복입니다. 복원 작업은 항상 데이터를 새 계정으로 복원합니다.

## <a name="what-is-not-restored"></a>복원되지 않는 항목

특정 시점 복원 후에는 다음 구성이 복원되지 않습니다.

* 방화벽, VNET, 프라이빗 엔드포인트 설정.
* 일관성 설정 기본적으로 계정은 세션 일관성을 사용하여 복원됩니다.  
* 지역.
* 저장 프로시저, 트리거 및 UDF.

복원이 완료된 후에 이러한 구성을 복원된 계정에 추가할 수 있습니다.

## <a name="restore-scenarios"></a>복원 시나리오

다음은 특정 시점 복원 기능으로 해결되는 주요 시나리오 중 일부입니다. 시나리오 [a]~[c]는 복원 타임스탬프를 미리 알 수 있는 경우 복원을 트리거하는 방법을 보여 줍니다.
그러나 실수로 인한 삭제 또는 손상의 정확한 시점을 모르는 시나리오가 있을 수 있습니다. 시나리오 [d] 및 [e]는 복원 가능한 데이터베이스 또는 컨테이너에서 새 이벤트 피드 API를 사용하여 복원 타임스탬프를 '검색'하는 방법을 보여 줍니다.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="복원 가능한 계정에 대한 타임스탬프가 포함된 수명 주기 이벤트." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **삭제된 계정 복원** - 복원할 수 있는 모든 삭제된 계정은 **복원** 창에 표시됩니다. 예를 들어 '계정 A'가 타임스탬프 T3에 삭제되었습니다. 이 경우 T3 직전 타임스탬프, 위치, 대상 계정 이름, 리소스 그룹이면 [Azure Portal](continuous-backup-restore-portal.md#restore-deleted-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore) 또는 [CLI](continuous-backup-restore-command-line.md#trigger-restore)에서 복원하는 데 충분합니다.  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="복원 가능한 데이터베이스 및 컨테이너에 대한 타임 스탬프가 포함된 수명 주기 이벤트." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **특정 지역에 있는 계정의 데이터를 복원** - 예를 들어 '계정 A'가 타임스탬프 T3에 두 지역 '미국 동부' 및 '미국 서부'에 존재합니다. *미국 서부* 의 계정 A의 복사본이 필요한 경우 [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore) 또는 [CLI](continuous-backup-restore-command-line.md#trigger-restore)에서 미국 서부를 대상 위치로 사용하여 특정 시점 복원을 수행할 수 있습니다.

다. **알려진 복원 타임스탬프를 사용하여 컨테이너 내에서 실수로 인 한 쓰기 또는 삭제 작업으로부터 복구** - 예를 들어 '데이터베이스 1' 내의 '컨테이너 1'에서 타임스탬프 T3에 실수로 콘텐츠가 수정된 것을 **알았습니다**. [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore) 또는 [CLI](continuous-backup-restore-command-line.md#trigger-restore)에서 타임스탬프 T3의 다른 계정으로 특정 시점 복원을 수행하여 컨테이너를 원하는 상태로 복구할 수 있습니다.

d. **데이터베이스를 실수로 삭제하기 전 시점으로 계정을 복원** - [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)에서 이벤트 피드 창을 사용하여 데이터베이스가 삭제된 시간을 확인하고 복원 시점을 찾을 수 있습니다. 마찬가지로 [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) 또는 [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)을 사용하면 데이터베이스 이벤트 피드를 열거하여 데이터베이스 삭제 이벤트를 검색한 다음 필요한 매개 변수를 사용하여 restore 명령을 트리거할 수 있습니다.

e. **컨테이너 속성을 실수로 삭제 또는 수정하기 전 시점으로 계정을 복원** - [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)에서 이벤트 피드 창을 사용하여 컨테이너가 생성, 수정 또는 삭제된 시기를 확인하여 복원 시점을 찾을 수 있습니다. 마찬가지로 [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) 또는 [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)을 사용하면 컨테이너 이벤트 피드를 열거하여 모든 컨테이너 이벤트를 검색한 다음 필요한 매개 변수를 사용하여 restore 명령을 트리거할 수 있습니다.

## <a name="permissions"></a>권한

Azure Cosmos DB를 사용하면 지속적인 백업 계정에 대한 복원 권한을 특정 역할 또는 보안 주체로 분리하고 제한할 수 있습니다. 계정의 소유자는 복원을 트리거하고 다른 보안 주체에 역할을 할당하여 복원 작업을 수행할 수 있습니다. 자세히 알아보려면 [사용 권한](continuous-backup-restore-permissions.md) 문서를 참조하세요.

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>가격 책정

지속적인 백업을 사용하도록 설정된 Azure Cosmos DB 계정은 '백업을 저장'하고 '데이터를 복원'하기 위한 추가 월별 요금이 발생합니다. 복원 비용은 복원 작업을 시작할 때마다 추가됩니다. 지속적인 백업을 사용하여 계정을 구성하지만 데이터를 복원하지 않는 경우에는 백업 스토리지 비용만 청구서에 포함됩니다.

다음 예는 미국의 비정부 지역에 배포된 Azure Cosmos 계정에 대한 가격을 기준으로 합니다. 가격 책정 및 계산은 사용 중인 지역에 따라 다를 수 있습니다. 최신 가격 책정 정보는 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

* 지속적인 백업 정책을 사용하도록 설정된 모든 계정은 다음과 같이 계산되는 백업 스토리지에 대한 추가 월별 요금이 부과됩니다.

  $0.20/GB * 계정 내 데이터 크기(GB) * 지역 수

* 모든 복원 API 호출에는 일회성 요금이 발생합니다. 이 요금은 데이터 복원 용량에 따라 다음과 같이 계산됩니다.

  $0.15/GB * 데이터 크기(GB)

예를 들어 두 지역에 1TB의 데이터가 있는 경우

* 백업 스토리지 비용은 (1000 * 0.20 * 2) = $400/월로 계산됩니다.

* 복원 비용은 복원당 (1000 * 0.15) = $150로 계산됩니다.

## <a name="current-limitations-public-preview"></a>현재 제한 사항(공개 미리 보기)

현재 특정 시점 복원 기능은 공개 미리 보기로 제공되며 다음과 같은 제한 사항이 있습니다.

* 지속적인 백업은 SQL 및 MongoDB에 대한 Azure Cosmos DB API만 지원됩니다. Cassandra, Table 및 Gremlin API는 아직 지원되지 않습니다.

* 기본 정기 백업 정책을 사용하는 기존 계정은 지속적인 백업 모드를 사용하도록 변환할 수 없습니다.

* Azure 소버린 및 Azure Government 클라우드 지역은 아직 지원되지 않습니다.

* 고객 관리형 키를 사용하는 계정은 지속적인 백업 사용이 지원되지 않습니다.

* 다중 지역 쓰기 계정은 지원되지 않습니다.

* Synapse Link가 설정된 계정은 지원되지 않습니다.

* 복원된 계정은 원본 계정이 있는 동일한 지역에 만들어집니다. 원본 계정이 존재하지 않는 지역으로 계정을 복원할 수는 없습니다.

* 복원 기간은 단 30일이며 변경할 수 없습니다.

* 백업은 지리적 재해 방지를 자동으로 수행하지 않습니다. 계정 및 백업에 대한 복원력을 갖도록 다른 지역을 명시적으로 추가해야 합니다.

* 복원이 진행 중일 때 계정에 대한 사용 권한을 부여하거나 VNET, 방화벽 구성을 변경하는 IAM(ID 및 액세스 관리) 정책을 수정하거나 삭제하지 마세요.

* 컨테이너를 만든 후 고유 인덱스를 만드는 SQL 또는 MongoDB 계정에 대한 Azure Cosmos DB API는 지속적인 백업에 대해 지원되지 않습니다. 초기 컨테이너 생성의 일부로 고유 인덱스를 만드는 컨테이너만 지원됩니다. MongoDB 계정의 경우 [확장 명령](mongodb-custom-commands.md)을 사용하여 고유 인덱스를 만듭니다.

* 특정 시점 복원 기능은 항상 새 Azure Cosmos 계정으로 복원됩니다. 기존 계정으로의 복원은 현재 지원되지 않습니다. 내부 복원에 대한 피드백을 제공하려면 계정 담당자 또는 [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)를 통해 Azure Cosmos DB 팀에 문의하세요.

* `RestorableDatabaseAccount`, `RestorableSqlDatabases`, `RestorableSqlContainer`, `RestorableMongodbDatabase`, `RestorableMongodbCollection` 목록에 대해 노출된 모든 새 API는 기능이 미리 보기 상태인 동안 변경될 수 있습니다.

* 복원 후 특정 컬렉션의 경우 일관성 있는 인덱스가 다시 작성될 수 있습니다. [IndexTransformationProgress](how-to-manage-indexing-policy.md) 속성을 통해 다시 빌드 작업의 상태를 확인할 수 있습니다.

* 복원 프로세스는 TTL 구성을 포함하여 컨테이너의 모든 속성을 복원합니다. 결과적으로 복원된 데이터는 그렇게 구성한 경우 즉시 삭제될 수 있습니다. 이러한 상황을 방지하기 위해 TTL 속성이 컨테이너에 추가되기 전에 복원 타임스탬프가 있어야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) 또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용하여 지속적인 백업을 구성하고 관리
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한 관리](continuous-backup-restore-permissions.md)
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
