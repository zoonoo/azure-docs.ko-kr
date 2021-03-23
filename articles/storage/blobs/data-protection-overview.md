---
title: 데이터 보호 개요
titleSuffix: Azure Storage
description: Blob Storage 및 Azure Data Lake Storage Gen2 데이터에 사용할 수 있는 데이터 보호 옵션을 사용 하 여 데이터를 삭제 하거나 덮어쓰지 않도록 보호할 수 있습니다. 삭제 되거나 덮어쓴 데이터를 복구 해야 하는 경우이 가이드를 통해 시나리오에 가장 적합 한 복구 옵션을 선택할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 03/22/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: afd98e629500bc90cc9ddd1ed4ab2472f733e845
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803802"
---
# <a name="data-protection-overview"></a>데이터 보호 개요

Azure Storage은 Blob Storage 및 Azure Data Lake Storage Gen2에 대 한 데이터 보호 기능을 제공 하 여 삭제 되거나 덮어쓴 데이터를 복구 해야 하는 시나리오를 준비 하는 데 도움이 됩니다. 문제가 발생 하기 전에 데이터를 가장 잘 보호 하는 방법을 고려 하 여 손상 될 수 있는 것이 중요 합니다. 이 가이드는 시나리오에 필요한 데이터 보호 기능과 구현 방법을 미리 결정 하는 데 도움이 될 수 있습니다. 삭제 되거나 덮어쓴 데이터를 복구 해야 하는 경우이 개요는 시나리오에 따라 진행 하는 방법에 대 한 지침을 제공 합니다.

Azure Storage 설명서에서 *데이터 보호* 는 저장소 계정 및 저장소 계정 내의 데이터를 삭제 또는 수정 하지 않도록 보호 하기 위한 전략과, 삭제 또는 수정 된 후 데이터를 복원 하는 전략을 의미 합니다. 또한 Azure Storage는 하드웨어 문제나 자연 재해로 인 한 서비스 중단 으로부터 데이터를 보호 하기 위한 여러 수준의 중복성을 포함 하는 *재해 복구* 옵션과 주 지역의 데이터 센터를 사용할 수 없게 되는 경우 고객 관리 장애 조치 (failover)를 제공 합니다. 서비스가 중단 되지 않도록 데이터를 보호 하는 방법에 대 한 자세한 내용은 [재해 복구](#disaster-recovery)를 참조 하세요.

## <a name="recommendations-for-basic-data-protection"></a>기본 데이터 보호에 대 한 권장 사항

저장소 계정 및 저장소 계정에 포함 된 데이터에 대 한 기본 데이터 보호 범위를 찾고 있는 경우 다음 단계를 수행 하 여 시작 하는 것이 좋습니다.

- 저장소 계정에 대 한 Azure Resource Manager 잠금을 구성 하 여 삭제 또는 구성 변경 으로부터 계정을 보호 합니다. [더 알아보세요...](../common/lock-account-resource.md)
- 저장소 계정에 대해 컨테이너 일시 삭제를 사용 하도록 설정 하 여 삭제 된 컨테이너 및 해당 콘텐츠를 복구 합니다. [더 알아보세요...](soft-delete-container-enable.md)
- 일정 한 간격으로 blob의 상태를 저장 합니다.
  - Blob Storage 작업의 경우 blob을 삭제 하거나 덮어쓸 때마다 blob 버전 관리를 사용 하 여 데이터의 상태를 자동으로 저장 합니다. [더 알아보세요...](versioning-enable.md)
  - Azure Data Lake Storage 워크 로드의 경우 수동으로 스냅숏을 만들어 특정 시점에 데이터의 상태를 저장 합니다. [더 알아보세요...](snapshots-overview.md)

다음 섹션에서는 이러한 옵션 및 기타 시나리오에 대 한 추가 데이터 보호 옵션에 대해 자세히 설명 합니다.

이러한 기능과 관련 된 비용의 개요는 [비용 고려 사항 요약](#summary-of-cost-considerations)을 참조 하세요.

## <a name="overview-of-data-protection-options"></a>데이터 보호 옵션 개요

다음 표에서는 일반적인 데이터 보호 시나리오에 대 한 Azure Storage에서 사용할 수 있는 옵션을 요약 합니다. 사용자에 게 제공 되는 옵션에 대 한 자세한 내용을 보려면 상황에 맞는 시나리오를 선택 합니다. 계층 네임 스페이스가 사용 하도록 설정 된 저장소 계정에 대해서는 지금은 일부 기능을 사용할 수 없습니다.

| 시나리오 | 데이터 보호 옵션 | 권장 사항 | 보호 혜택 | Data Lake Storage에 사용 가능 |
|--|--|--|--|--|
| 저장소 계정이 삭제 되거나 수정 되지 않도록 방지 합니다. | Azure Resource Manager 잠금<br />[더 알아보세요...](../common/lock-account-resource.md) | 저장소 계정 삭제를 방지 하려면 Azure Resource Manager 잠금으로 모든 저장소 계정을 잠급니다. | 삭제 또는 구성 변경에 대해 저장소 계정을 보호 합니다.<br /><br />는 계정의 컨테이너 또는 blob을 삭제 하거나 덮어쓰지 않도록 보호 하지 않습니다. | 예 |
| 사용자가 제어 하는 간격 동안 컨테이너 및 해당 blob이 삭제 되거나 수정 되지 않도록 합니다. | 컨테이너에 대 한 불변성 정책<br />[더 알아보세요...](storage-blob-immutable-storage.md) | 비즈니스에 중요 한 문서 (예: 법률 또는 규정 준수 요구 사항을 충족 하기 위해)를 보호 하기 위해 컨테이너에 대해 불변성 정책을 설정 합니다. | 모든 삭제 및 덮어쓰기 로부터 컨테이너 및 해당 blob을 보호 합니다.<br /><br />법적 보류 또는 잠긴 시간 기반 보존 정책이 적용 되는 경우 저장소 계정도 삭제 되지 않도록 보호 됩니다. 불변성 정책이 설정 되지 않은 컨테이너는 삭제 되지 않도록 보호 됩니다. | 예, 미리 보기 |
| 지정 된 간격 내에 삭제 된 컨테이너를 복원 합니다. | 컨테이너 일시 삭제 (미리 보기)<br />[더 알아보세요...](soft-delete-container-overview.md) | 최소 보존 간격인 7 일을 사용 하 여 모든 저장소 계정에 대해 컨테이너 일시 삭제를 사용 하도록 설정 합니다.<br /><br />컨테이너 일시 삭제와 함께 blob 버전 관리 및 blob 일시 삭제를 사용 하도록 설정 하 여 컨테이너의 개별 blob을 보호 합니다.<br /><br />별도의 저장소 계정에서 다른 보존 기간이 필요한 컨테이너를 저장 합니다. | 삭제 된 컨테이너와 해당 콘텐츠는 보존 기간 내에 복원 될 수 있습니다.<br /><br />컨테이너 수준 작업 (예: [컨테이너 삭제](/rest/api/storageservices/delete-container))만 복원할 수 있습니다. 컨테이너 일시 삭제는 blob이 삭제 된 경우 컨테이너의 개별 blob을 복원 하는 것을 허용 하지 않습니다. | 예, 미리 보기 |
| Blob의 상태를 덮어쓰거나 삭제 하면 이전 버전으로 자동 저장 합니다. | Blob 버전 관리<br />[더 알아보세요...](versioning-overview.md) | Blob 데이터에 대 한 최적의 보호를 필요로 하는 저장소 계정에 대해 컨테이너 일시 삭제 및 blob 일시 삭제와 함께 blob 버전 관리를 사용 하도록 설정 합니다.<br /><br />비용을 제한 하기 위해 별도의 계정에 버전 관리가 필요 하지 않은 blob 데이터를 저장 합니다. | 모든 blob 덮어쓰기 또는 삭제 작업은 새 버전을 만듭니다. Blob을 삭제 하거나 덮어쓰는 경우 이전 버전에서 blob를 복원할 수 있습니다. | 아니요 |
| 지정 된 간격 내에 삭제 된 blob 또는 blob 버전을 복원 합니다. | Blob 일시 삭제<br />[더 알아보세요...](soft-delete-blob-overview.md) | 최소 보존 간격인 7 일을 사용 하 여 모든 저장소 계정에 대해 blob 일시 삭제를 사용 하도록 설정 합니다.<br /><br />Blob 데이터에 대 한 최적의 보호를 위해 blob 일시 삭제와 함께 blob 버전 관리 및 컨테이너 일시 삭제를 사용 하도록 설정 합니다.<br /><br />별도의 저장소 계정에 다른 보존 기간이 필요한 blob을 저장 합니다. | 삭제 된 blob 또는 blob 버전은 보존 기간 내에 복원 될 수 있습니다. | 아니요 |
| 블록 blob 집합을 이전 시점으로 복원 합니다. | 지정 시간 복원<br />[더 알아보세요...](point-in-time-restore-overview.md) | 지정 시간 복원을 사용 하 여 이전 상태로 되돌리려면 컨테이너를 삭제 하는 대신 개별 블록 blob을 삭제 하도록 응용 프로그램을 디자인 합니다. | 블록 blob 집합은 과거의 특정 지점에서 해당 상태로 되돌릴 수 있습니다.<br /><br />블록 blob에 대해 수행 되는 작업만 되돌립니다. 컨테이너, 페이지 blob 또는 추가 blob에 대해 수행 되는 모든 작업은 되돌리지 않습니다. | 아니요 |
| 지정 된 시점에 blob의 상태를 수동으로 저장 합니다. | Blob 스냅숏<br />[더 알아보세요...](snapshots-overview.md) | 비용이 나 기타 고려 사항 또는 저장소 계정에 계층적 네임 스페이스를 사용 하는 경우 시나리오에 적합 하지 않은 버전 관리의 경우 blob 버전 관리 대신 사용 하는 것이 좋습니다. | Blob을 덮어쓰는 경우 스냅숏에서 blob를 복원할 수 있습니다. Blob이 삭제 된 경우에도 스냅숏은 삭제 됩니다. | 예, 미리 보기 |
| Blob을 삭제 하거나 덮어쓸 수 있지만 데이터는 정기적으로 두 번째 저장소 계정에 복사 됩니다. | Azure Storage 개체 복제 또는 AzCopy 또는 Azure Data Factory와 같은 도구를 사용 하 여 두 번째 계정으로 데이터를 복사 하기 위한 고유한 솔루션입니다. | 예기치 않은 의도적인 작업 또는 예측할 수 없는 시나리오 로부터 안심할 수 있는 보호에 권장 됩니다.<br /><br />송신 요금이 발생 하지 않도록 기본 계정과 동일한 지역에 두 번째 저장소 계정을 만듭니다. | 주 계정이 손상 된 경우 두 번째 저장소 계정에서 데이터를 복원할 수 있습니다. | AzCopy 및 Azure Data Factory 지원 됩니다.<br /><br />개체 복제가 지원 되지 않습니다. |

## <a name="data-protection-by-resource-type"></a>리소스 유형별 데이터 보호

다음 표에는 보호 되는 리소스에 따라 Azure Storage 데이터 보호 옵션이 요약 되어 있습니다.

| 데이터 보호 옵션 | 계정이 삭제 되지 않도록 보호 합니다. | 컨테이너를 삭제 하지 않도록 보호 합니다. | Blob이 삭제 되지 않도록 보호 합니다. | 덮어쓸 때 blob을 보호 합니다. |
|--|--|--|--|--|
| Azure Resource Manager 잠금 | 예 | 아니요<sup>1</sup> | 아니요 | 아니요 |
| 컨테이너에 대 한 불변성 정책 | 예<sup>2</sup> | 예 | 예 | 예 |
| 컨테이너 일시 삭제 | 아니요 | 예 | 아니요 | 아니요 |
| Blob 버전<sup>3</sup> | 아니요 | 아니요 | 예 | 예 |
| Blob 일시 삭제<sup>3</sup> | 아니요 | 아니요 | 예 | 예 |
| 지정 시간 복원<sup>3</sup> | 아니요 | 아니요 | 예 | 예 |
| Blob 스냅숏 | 아니요 | 아니요 | 아니요 | 예 |
| 두 번째 계정으로 데이터를 복사 하는 사용자 고유의 솔루션<sup>4</sup> | 아니요 | 예 | 예 | 예 |

<sup>1</sup> Azure Resource Manager 잠금은 컨테이너를 삭제 하지 않도록 보호 하지 않습니다.<br />
<sup>2</sup> 법적 보류 또는 잠긴 시간 기반 보존 정책이 컨테이너에 적용 되는 동안 저장소 계정도 삭제 되지 않도록 보호 됩니다.<br />
<sup>3</sup> 현재 Data Lake Storage 작업에 대해 지원 되지 않습니다.<br />
<sup>4</sup> AzCopy 및 Azure Data Factory는 Blob Storage 및 Data Lake Storage 워크 로드 모두에 대해 지원 되는 옵션입니다. 개체 복제는 Blob Storage 작업에 대해서만 지원 됩니다.<br />

## <a name="recover-deleted-or-overwritten-data"></a>삭제 되거나 덮어쓴 데이터 복구

덮어쓰거나 삭제 된 데이터를 복구 해야 하는 경우에는 사용 하도록 설정한 데이터 보호 옵션과 영향을 받는 리소스에 따라 진행 되는 방식이 달라 집니다. 다음 표에서는 데이터를 복구 하기 위해 수행할 수 있는 작업에 대해 설명 합니다.

| 삭제 되거나 덮어쓴 리소스 | 가능한 복구 작업 | 복구 요구 사항 |
|--|--|--|
| 스토리지 계정 | 삭제 된 저장소 계정 복구 시도<br />[더 알아보세요...](../common/storage-account-recover.md) | 저장소 계정은 원래 Azure Resource Manager 배포 모델을 사용 하 여 만들어졌으며 지난 14 일 이내에 삭제 되었습니다. 원래 계정이 삭제 된 후 이름이 같은 새 저장소 계정이 만들어지지 않았습니다. |
| 컨테이너 | 일시 삭제 된 컨테이너 및 해당 콘텐츠 복구<br />[더 알아보세요...](soft-delete-container-enable.md) | 컨테이너 일시 삭제를 사용 하도록 설정 되어 있으며 컨테이너 일시 삭제 보존 기간이 아직 만료 되지 않았습니다. |
| 컨테이너 및 Blob | 두 번째 저장소 계정에서 데이터 복원 | 모든 컨테이너 및 blob 작업은 두 번째 저장소 계정에 효과적으로 복제 되었습니다. |
| Blob (모든 유형) | 이전 버전에서 blob 복원<sup>1</sup><br />[더 알아보세요...](versioning-enable.md) | Blob 버전 관리가 사용 하도록 설정 되어 있고 blob에 하나 이상의 이전 버전이 있습니다. |
| Blob (모든 유형) | 일시 삭제 된 blob<sup>1</sup> 복구<br />[더 알아보세요...](soft-delete-blob-enable.md) | Blob 일시 삭제를 사용 하도록 설정 하 고 일시 삭제 보존 간격이 만료 되지 않았습니다. |
| Blob (모든 유형) | 스냅숏에서 blob 복원<br />[더 알아보세요...](snapshots-manage-dotnet.md) | Blob에 하나 이상의 스냅숏이 있습니다. |
| 블록 blob 집합 | 블록 blob 집합을 이전 시점에서의 상태로 복구 합니다.<sup>1</sup><br />[더 알아보세요...](point-in-time-restore-manage.md) | 지정 시간 복원을 사용 하도록 설정 하 고 복원 지점이 보존 간격 내에 있습니다. 저장소 계정이 손상 되었거나 손상 되지 않았습니다. |
| Blob 버전 | 일시 삭제 된 버전<sup>1</sup> 복구<br />[더 알아보세요...](soft-delete-blob-enable.md) | Blob 일시 삭제를 사용 하도록 설정 하 고 일시 삭제 보존 간격이 만료 되지 않았습니다. |

<sup>1</sup> 현재 Data Lake Storage 작업에 대해 지원 되지 않습니다.

## <a name="summary-of-cost-considerations"></a>비용 고려 사항 요약

다음 표에는이 가이드에서 설명 하는 다양 한 데이터 보호 옵션에 대 한 비용 고려 사항이 요약 되어 있습니다.

| 데이터 보호 옵션 | 비용 고려 사항 |
|-|-|
| 저장소 계정에 대 한 Azure Resource Manager 잠금 | 저장소 계정에 대 한 잠금을 구성 하는 것은 무료입니다. |
| 컨테이너에 대 한 불변성 정책 | 컨테이너에 대해 불변성 정책을 구성 하는 요금이 청구 되지 않습니다. |
| 컨테이너 일시 삭제 | 저장소 계정에 대해 컨테이너 일시 삭제를 사용 하도록 설정 하는 것은 무료로 제공 됩니다. 일시 삭제 된 컨테이너의 데이터는 일시 삭제 된 컨테이너가 영구적으로 삭제 될 때까지 활성 데이터와 동일한 속도로 청구 됩니다. |
| Blob 버전 관리 | 저장소 계정에 대해 blob 버전 관리를 사용 하도록 설정 하는 것은 무료로 제공 됩니다. Blob 버전 관리를 사용 하도록 설정 하면 계정에서 blob에 대 한 모든 쓰기 또는 삭제 작업이 새 버전을 만들므로 용량 비용이 늘어날 수 있습니다.<br /><br />Blob 버전은 고유한 블록이 나 페이지를 기준으로 요금이 청구 됩니다. 따라서 기본 blob이 특정 버전에서 달라 지므로 비용이 증가 합니다. Blob 또는 blob 버전의 계층을 변경 하면 청구에 영향을 줄 수 있습니다. 자세한 내용은 [가격 책정 및 청구](versioning-overview.md#pricing-and-billing)를 참조 하세요.<br /><br />수명 주기 관리를 사용 하 여 비용을 제어 하기 위해 필요에 따라 이전 버전을 삭제 합니다. 자세한 내용은 [Azure Blob Storage 액세스 계층을 자동화 하 여 비용 최적화](storage-lifecycle-management-concepts.md)를 참조 하세요. |
| Blob 일시 삭제 | 저장소 계정에 대해 blob 일시 삭제를 사용 하도록 설정 하는 것은 무료로 제공 됩니다. 일시 삭제 된 blob의 데이터는 일시 삭제 된 blob이 영구적으로 삭제 될 때까지 활성 데이터와 동일한 속도로 청구 됩니다. |
| 지정 시간 복원 | 저장소 계정에 대 한 지정 시간 복원을 사용 하도록 설정 하는 것은 무료입니다. 그러나 특정 시점 복원을 사용 하도록 설정 하면 blob 버전 관리, 일시 삭제 및 변경 피드가 가능해 지 며,이로 인해 추가 요금이 발생할 수 있습니다.<br /><br />복원 작업을 수행 하는 경우 지정 시간 복원에 대 한 요금이 청구 됩니다. 복원 작업의 비용은 복원 되는 데이터의 양에 따라 달라 집니다. 자세한 내용은 [가격 책정 및 청구](point-in-time-restore-overview.md#pricing-and-billing)를 참조 하세요. |
| Blob 스냅숏 | 스냅숏의 데이터는 고유 블록이 나 페이지를 기준으로 요금이 청구 됩니다. 따라서 기본 blob 달라 지므로 스냅숏에서 비용이 증가 합니다. Blob 또는 스냅숏의 계층을 변경 하면 청구에 영향을 줄 수 있습니다. 자세한 내용은 [가격 책정 및 청구](snapshots-overview.md#pricing-and-billing)를 참조 하세요.<br /><br />수명 주기 관리를 사용 하 여 필요에 따라 오래 된 스냅숏을 삭제 하 여 비용을 제어 합니다. 자세한 내용은 [Azure Blob Storage 액세스 계층을 자동화 하 여 비용 최적화](storage-lifecycle-management-concepts.md)를 참조 하세요. |
| 두 번째 저장소 계정에 데이터 복사 | 두 번째 저장소 계정에서 데이터를 유지 관리 하면 용량과 트랜잭션 비용이 발생 합니다. 두 번째 저장소 계정이 원본 계정이 아닌 다른 지역에 있는 경우 해당 두 번째 계정으로 데이터를 복사 하면 추가로 송신 요금이 발생 합니다. |

## <a name="disaster-recovery"></a>재해 복구

Azure Storage은 일시적 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획 되거나 계획 되지 않은 이벤트 로부터 보호 되도록 항상 데이터의 여러 복사본을 유지 관리 합니다. 중복성은 저장소 계정이 오류 발생 시에도 가용성 및 내구성 목표를 충족 하는지 확인 합니다. 고가용성을 위해 저장소 계정을 구성 하는 방법에 대 한 자세한 내용은 [Azure Storage 중복성](../common/storage-redundancy.md)을 참조 하세요.

데이터 센터에서 오류가 발생 하는 경우 저장소 계정이 두 지리적 지역 (지역 중복)에서 중복 되는 경우 주 지역에서 보조 지역으로 계정을 장애 조치 (failover) 할 수 있는 옵션이 있습니다. 자세한 내용은 [재해 복구 및 저장소 계정 장애 조치(failover)](../common/storage-disaster-recovery-guidance.md)를 참조하세요.

계층 구조 네임 스페이스를 사용 하는 저장소 계정에는 현재 고객 관리 장애 조치 (failover)가 지원 되지 않습니다. 자세한 내용은 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob storage 기능](data-lake-storage-supported-blob-storage-features.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 중복성](../common/storage-redundancy.md)
- [재해 복구 및 저장소 계정 장애 조치(failover)](../common/storage-disaster-recovery-guidance.md)
