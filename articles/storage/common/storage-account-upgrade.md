---
title: 범용 v2 저장소 계정-Azure Storage로 업그레이드 | Microsoft Docs
description: 범용 v2 저장소 계정으로 업그레이드 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.openlocfilehash: a1ee95ad847d20159c79af8f080cc7878e114759
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444511"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>범용 v2 저장소 계정으로 업그레이드

최신 Azure Storage 기능을 지원 하 고 모든 Blob 저장소 계정 및 범용 v1의 기능을 통합 하는 범용 v2 저장소 계정을 합니다. 범용 v2 계정은 대부분의 저장소 시나리오에 권장됩니다. 범용 v2 계정은 업계 경쟁력 있는 트랜잭션 가격 뿐만 아니라 Azure Storage에 대해서도 가장 낮은 기가바이트당 용량 가격을 제공합니다.

범용 v1에에서 범용 v2 저장소 계정 또는 Blob 저장소 계정으로 업그레이드 하는 것은 간단 합니다. Azure Portal, PowerShell 또는 Azure CLI를 사용하여 업그레이드할 수 있습니다.

> [!IMPORTANT]
> 범용 v1 또는 Blob storage 저장소 계정을 범용 v2로 업그레이드는 영구적 이며 취소할 수 없습니다.

## <a name="upgrade-using-the-azure-portal"></a>Azure Portal을 사용하여 업그레이드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 저장소 계정으로 이동합니다.
3. **설정** 섹션에서 **구성**을 클릭합니다.
4. **계정 종류** 아래에서 **업그레이드**를 클릭합니다.
5. **업그레이드 확인**에서 계정 이름을 입력합니다.
6. 블레이드 하단에서 **업그레이드**를 클릭합니다.

    ![계정 종류를 업그레이드 합니다.](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

## <a name="upgrade-with-powershell"></a>Powershell로 업그레이드

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell을 사용 하 여 범용 v2 계정을 범용 v1 계정으로 업그레이드 하려면 먼저 PowerShell 최신 버전의 사용을 업데이트 합니다 **Az.Storage** 모듈입니다. PowerShell 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-Az-ps)을 참조하세요.

다음으로, 리소스 그룹의 이름 및 저장소 계정을 대신하여 계정을 업그레이드하도록 다음 명령을 호출합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Azure CLI를 사용하여 업그레이드

범용 v1 계정으로 Azure CLI를 사용 하 여 범용 v2 계정을 업그레이드 하려면 먼저 최신 버전의 Azure CLI를 설치 합니다. CLI 설치에 대한 자세한 내용은 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

다음으로, 리소스 그룹의 이름 및 저장소 계정을 대신하여 계정을 업그레이드하도록 다음 명령을 호출합니다.

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```

## <a name="specify-an-access-tier-for-blob-data"></a>Blob 데이터에 대한 액세스 계층을 지정합니다.

범용 v2 계정은 모든 Azure Storage 서비스 및 데이터 개체를 지원하지만 액세스 계층은 Blob Storage의 블록 blob에 대해서만 사용할 수 있습니다. 범용 v2 저장소 계정으로 업그레이드할 때에 blob 데이터에 대 한 액세스 계층을 지정할 수 있습니다.

액세스 계층을 사용하면 예상된 사용량 패턴에 따라 가장 비용 효율적인 저장소를 선택할 수 있습니다. 블록 Blob은 핫, 쿨 또는 보관 계층에 저장할 수 있습니다. 액세스 계층에 대한 자세한 내용은 [Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](../blobs/storage-blob-storage-tiers.md)을 참조하세요.

기본적으로 새 storage 계정을 핫 액세스 계층에 만들어지고 범용 v1 storage 계정을 핫 액세스 계층으로 업그레이드 됩니다. 업그레이드 후에 데이터를 사용하는 액세스 계층을 탐색하는 경우 사용자의 시나리오를 고려합니다. 범용 v2 계정으로 마이그레이션하기 위한 두 가지 일반적인 사용자 시나리오가 있습니다.

* 기존 범용 v1 저장소 계정을 있고 범용 v2 저장소 계정에 blob 데이터에 대 한 올바른 저장소 액세스 계층을 사용 하 여 업그레이드를 평가 하려고 합니다.
* 범용 v2 저장소 계정을 사용 하 여 또는 이미 하나 있고 핫 또는 쿨 저장소 액세스 계층을 사용 하 여 blob 데이터에 대 한 해야 하는지 여부를 평가 하려면 하도록 결정 했습니다.

두 경우 모두 첫 번째 우선 순위를 저장, 액세스 및 범용 v2 저장소 계정에 저장 된 데이터에서 운영 비용을 산정 하 고 현재 비용과 비교 하는 합니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

V1 저장소 계정을 범용 v2 계정으로 업그레이드 하는 것은 무료입니다. 그러나 저장소 액세스 계층을 변경 변경 될 수 있습니다 청구서에 있습니다. 

모든 스토리지 계정에서는 각 Blob의 계층에 따라 Blob Storage에 가격 책정 모델을 사용합니다. 저장소 계정을 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

* **스토리지 비용**: 데이터 저장 비용은 저장되는 데이터의 양과 스토리지 액세스 계층에 따라 달라집니다. 계층이 차가워질수록 기가바이트당 비용이 감소합니다.

* **데이터 액세스 비용**: 계층이 차가워질수록 데이터 액세스 요금이 증가합니다. 쿨 및 보관 스토리지 액세스 계층에 있는 데이터의 경우 읽기에 대해 기가바이트당 데이터 액세스 요금이 부과됩니다.

* **트랜잭션 비용**: 계층이 차가워질수록 증가하는 모든 계층에 대해 트랜잭션당 요금이 부과됩니다.

* **지역에서 복제 데이터 전송 비용**: 이 요금은 GRS 및 RA-GRS를 포함하여 지역에서 복제가 구성된 계정에만 해당합니다. 지역 복제 데이터 전송에는 기가바이트당 요금이 발생합니다.

* **아웃바운드 데이터 전송 비용**: 아웃 바운드 데이터 전송 (Azure 지역 외부에서 전송 되는 데이터)에 범용 저장소 계정과 일치 기가바이트당에 따라 대역폭 사용량에 대 한 청구를 발생 합니다.

* **스토리지 액세스 계층 변경**: 계정 스토리지 액세스 계층을 쿨에서 핫으로 변경하면 스토리지 계정에 있는 모든 데이터를 읽는 경우와 동일한 금액의 요금이 발생합니다. 하지만 계정 액세스 계층을 핫에서 쿨로 변경하면 모든 데이터를 쿨 계층에 쓰는 경우와 동일한 금액의 요금이 발생합니다(GPv2 계정에만 해당).

> [!NOTE]
> 스토리지 계정의 가격 책정 모델에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 페이지를 참조하세요. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 페이지를 참조하세요.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>현재 사용 패턴에 대한 예상 비용

특정 계층에 범용 v2 저장소 계정에 blob 데이터 저장 및 액세스 비용을 계산 하려면 기존 사용 패턴을 평가 하거나 예상 된 사용 패턴에 합니다. 일반적으로 다음을 파악해야 합니다.

* 다음을 포함한 Blob Storage 사용량(기가바이트 단위):
    - 저장소 계정에 저장되는 데이터의 양
    - 월간 데이터 볼륨 변경 추이, 새 데이터가 기존 데이터를 계속 대체하는지 여부
* 다음을 포함한 Blob Storage 데이터에 대한 기본 액세스 패턴:
    - 읽어오는 데이터의 양과 저장소 계정에 기록되는 데이터의 양
    - 저장소 계정의 데이터에서 발생하는 읽기 작업 대 쓰기 작업의 수

요구 사항에 가장 적합한 액세스 계층을 결정하려면 Blob 데이터 용량 및 해당 데이터를 사용하는 방법을 결정하는 것이 좋습니다. 이렇게 하려면 계정에 대한 모니터링 메트릭을 살펴보는 것이 가장 좋습니다.

### <a name="monitoring-existing-storage-accounts"></a>기존 저장소 계정 모니터링

기존 저장소 계정을 모니터링하고 이 데이터를 수집하기 위해 로깅을 수행하고 저장소 계정에 대한 메트릭 데이터를 제공하는 Azure 저장소 분석을 사용할 수 있습니다. 스토리지 분석은 GPv1, GPv2 및 Blob Storage 계정 형식의 스토리지 서비스에 대한 요청과 관련하여 집계된 트랜잭션 통계 및 용량 데이터를 포함하는 메트릭을 저장할 수 있습니다. 이 데이터는 동일한 저장소 계정에서 잘 알려진 테이블에 저장됩니다.

자세한 내용은 [저장소 분석 메트릭 정보](https://msdn.microsoft.com/library/azure/hh343258.aspx) 및 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/azure/hh343264.aspx)를 참조하세요.

> [!NOTE]
> Blob Storage 계정은 해당 계정의 메트릭 데이터를 저장하고 액세스하는 경우에만 Table service 엔드포인트를 노출합니다.

Blob Storage에 대한 스토리지 사용량을 모니터링하려면 용량 메트릭을 활성화해야 합니다.
이를 활성화하면 용량 데이터는 저장소 계정의 Blob service에 대해 매일 기록되고 동일한 저장소 계정 내에서 *$MetricsCapacityBlob* 테이블에 작성된 테이블 항목으로 기록됩니다.

Blob Storage에 대한 데이터 액세스 패턴을 모니터링하려면 API에서 시간당 트랜잭션 메트릭을 활성화해야 합니다. 시간당 트랜잭션 메트릭을 활성화하면 API당 트랜잭션은 매시간 집계되며 동일한 저장소 계정 내에서 *$MetricsHourPrimaryTransactionsBlob* 테이블로 작성된 테이블 항목으로 기록됩니다. *$MetricsHourSecondaryTransactionsBlob* 테이블은 RA-GRS 저장소 계정을 사용하는 경우 보조 엔드포인트에 트랜잭션을 기록합니다.

> [!NOTE]
> 페이지 blob과 가상 머신 디스크, 또는 큐, 파일 또는 블록와 함께 테이블을 저장 하 고 있는 추가 blob 데이터는 범용 저장소 계정을 만든 경우이 예측 프로세스는 적용 되지 않습니다. 용량 데이터는 다른 형식의 블록 Blob와 다르지 않으므로 기타 데이터 형식에 대한 용량 데이터를 제공하지 않습니다. 이러한 형식을 사용하는 경우 가장 최근의 청구서에서 수량을 보는 것이 대체 방법입니다.

데이터 소비 및 액세스 패턴을 대략적으로 파악하려면 정기적 사용을 나타내는 메트릭의 보존 기간을 선택하고 추정하는 것이 좋습니다. 한 가지 옵션은 7일 동안 메트릭 데이터를 보유하고 월말에 분석을 위해 매주 데이터를 수집하는 것입니다. 또 다른 옵션은 지난 30일 동안의 메트릭 데이터를 보존하고 30일 기간이 끝날 때 데이터를 수집하고 분석하는 것입니다.

사용에 대 한 세부 정보를 수집 및 메트릭 데이터 보기 참조 [저장소 분석 메트릭](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)합니다.

> [!NOTE]
> 분석 데이터 저장, 액세스 및 다운로드는 일반 사용자 데이터와 마찬가지로 청구됩니다.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>사용 현황 메트릭을 활용하여 비용 추정

#### <a name="capacity-costs"></a>용량 비용

*'data'* 행 키가 있는 *$MetricsCapacityBlob* 용량 메트릭 테이블의 최신 항목은 사용자 데이터에서 사용하는 저장소 용량을 보여 줍니다. *'analytics'* 행 키가 있는 *$MetricsCapacityBlob* 용량 메트릭 테이블의 최신 항목은 분석 로그에서 사용하는 저장소 용량을 보여 줍니다.

사용자 데이터 및 분석 로그(활성화된 경우)에서 소비되는 이 전체 용량은 저장소 계정에서 데이터 저장의 비용을 예측하는 데 사용할 수 있습니다. GPv1 저장소 계정에서 저장소 비용을 예상하는 데 동일한 메서드를 사용할 수도 있습니다.

#### <a name="transaction-costs"></a>트랜잭션 비용

트랜잭션 메트릭 테이블에서 API에 대한 모든 항목에 대한 *'TotalBillableRequests'* 의 합계는 특정 API에 대한 트랜잭션의 총 수를 나타냅니다. *예를 들어* 지정된 기간의 총 *'GetBlob'* 트랜잭션 수는 *'user;GetBlob'* 행 키가 있는 모든 항목에 대해 청구 가능한 요청의 총합으로 계산될 수 있습니다.

트랜잭션은 서로 다른 가격이 책정되므로 Blob Storage 계정에 대한 트랜잭션 비용을 예상하려면 트랜잭션을 3개의 그룹으로 세분화해야 합니다.

* *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* 및 *'CopyBlob'* 과 같은 쓰기 트랜잭션
* *'DeleteBlob'* 및 *'DeleteContainer'* 와 같은 삭제 트랜잭션
* 모든 다른 트랜잭션.

GPv1 저장소 계정에 대한 트랜잭션 비용을 예상하려면 작업/API에 관계 없이 모든 트랜잭션을 집계해야 합니다.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>데이터 액세스 및 지역에서 복제 데이터 전송 비용

저장소 분석은 저장소 계정에서 읽고 쓰는 데이터의 양을 제공하지 않지만 트랜잭션 메트릭 테이블을 확인하여 대략적으로 예상할 수 있습니다. 트랜잭션 메트릭 테이블에서 API에 대한 모든 항목에 대한 *'TotalIngress'* 의 합계는 특정 API에 대한 수신 데이터의 총 크기를 바이트로 나타냅니다. 마찬가지로 *'TotalEgress'* 의 합계는 송신 데이터의 총 크기를 바이트로 나타냅니다.

Blob Storage 계정에 대한 데이터 액세스 비용을 예상하려면 트랜잭션을 2개의 그룹으로 세분화해야 합니다.

* 저장소 계정에서 검색되는 데이터 크기는 주로 *'GetBlob'* 및 *'CopyBlob'* 작업에 대한 *'TotalEgress'* 합계를 확인하여 예상할 수 있습니다.

* 저장소 계정에 작성되는 데이터 크기는 주로 *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* 및 *'AppendBlock'* 작업에 대한 *'TotalIngress'* 합계를 확인하여 예상할 수 있습니다.

Blob Storage 계정에 대한 지역에서 복제 데이터 전송의 비용은 GRS 또는 RA-GRS 스토리지 계정을 사용하는 경우 작성된 데이터의 양에 대한 추정을 사용하여 계산할 수도 있습니다.

> [!NOTE]
> 핫 또는 쿨 스토리지 액세스 계층 사용 시의 비용을 계산하는 자세한 예제는 *Azure Storage 가격 책정 페이지*에서 에 [Azure 저장소 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/)합니다.

## <a name="next-steps"></a>다음 단계

- [저장소 계정을 만드는](storage-quickstart-create-account.md)
- [Azure Storage 계정 관리](storage-account-manage.md)
