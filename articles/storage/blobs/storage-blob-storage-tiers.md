---
title: Blob에 대한 핫, 쿨 및 아카이브 액세스 계층 - Azure 저장소
description: Azure 저장소 계정에 대한 핫, 쿨 및 아카이브 액세스 계층입니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: c803d489b70cda6910865f6096d21c2021c4ae3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393698"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층

Azure 저장소는 다양한 액세스 계층을 제공하므로 Blob 개체 데이터를 가장 비용 효율적인 방식으로 저장할 수 있습니다. 사용 가능한 액세스 계층은 다음과 같습니다.

- **핫** - 자주 액세스되는 데이터를 저장하는 데 최적화되어 있습니다.
- **쿨** - 30일 이상 자주 액세스하고 저장되지 않는 데이터를 저장하는 데 최적화되어 있습니다.
- **보관** - 유연한 대기 시간 요구 사항(시간 순서)으로 최소 180일 동안 액세스및 저장되지 않는 데이터를 저장하는 데 최적화되어 있습니다.

다음 고려 사항은 다른 액세스 계층에 적용됩니다.

- 계정 수준에서 핫 및 쿨 액세스 계층만 설정할 수 있습니다. 계정 수준에서 아카이브 액세스 계층을 사용할 수 없습니다.
- 핫, 쿨 및 아카이브 계층은 업로드 하는 동안 또는 업로드 하는 동안 또는 Blob 수준에서 설정할 수 있습니다.
- 멋진 액세스 계층의 데이터는 가용성을 약간 낮출 수 있지만 핫 데이터와 유사한 높은 내구성, 검색 대기 시간 및 처리량 특성이 필요합니다. 멋진 데이터의 경우 핫 데이터에 비해 가용성 서비스 수준 계약(SLA)이 약간 낮고 액세스 비용이 높아지므로 스토리지 비용이 절감되는 데 적합합니다.
- 아카이브 스토리지는 데이터를 오프라인으로 저장하고 가장 낮은 스토리지 비용을 제공하지만 가장 높은 데이터 재수화 및 액세스 비용을 제공합니다.

클라우드에 저장된 데이터는 기하급수적으로 증가합니다. 스토리지 확장 요구에 대한 비용을 관리하려면 비용 최적화를 위해 액세스 빈도 및 계획 보존 기간과 같은 속성을 기반으로 하여 데이터를 구성하는 것이 좋습니다. 클라우드에 저장된 데이터는 수명 동안 생성, 처리 및 액세스하는 방법에 따라 다를 수 있습니다. 일부 데이터는 수명 기간 전반에 걸쳐 활발하게 액세스되고 수정됩니다. 일부 데이터는 수명 기간 초반에는 빈번하게 액세스되지만 데이터가 오래될수록 액세스 빈도가 급격하게 떨어집니다. 일부 데이터는 클라우드에서 유휴 상태로 유지되며 저장된 후에도 액세스하는 경우는 거의 없습니다.

이러한 각 데이터 액세스 시나리오는 특정 액세스 패턴에 최적화된 다른 액세스 계층의 이점을 제공합니다. Azure Blob 저장소는 핫, 쿨 및 아카이브 액세스 계층을 통해 별도의 가격 책정 모델을 통해 차별화된 액세스 계층에 대한 이러한 필요성을 해결합니다.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>계층을 지원하는 스토리지 계정

핫, 쿨 및 아카이브 간의 개체 저장소 데이터 계층화는 Blob 저장소 및 범용 v2(GPv2) 계정에서만 지원됩니다. 범용 v1(GPv1) 계정은 계층화를 지원하지 않습니다. 고객은 Azure 포털을 통해 기존 GPv1 또는 Blob 저장소 계정을 GPv2 계정으로 쉽게 변환할 수 있습니다. GPv2는 Blob, 파일 및 큐에 대한 새로운 가격 책정 및 기능을 제공합니다. 일부 기능 및 가격 인하만 GPv2 계정에서 제공됩니다. 가격을 종합적으로 검토한 후 GPv2 계정을 사용하여 평가합니다. 일부 워크로드는 GPv1보다 GPv2에서 더 비쌀 수 있습니다. 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

Blob 저장소 및 GPv2 계정은 계정 수준에서 **액세스 계층** 특성을 노출합니다. 이 특성을 사용하면 개체 수준에서 명시적 집합이 없는 Blob에 대한 기본 액세스 계층을 지정할 수 있습니다. 객체 수준에서 계층이 설정된 개체의 경우 계정 계층이 적용되지 않습니다. 아카이브 계층은 개체 수준에서만 적용할 수 있습니다. 언제든지 이러한 액세스 계층 간에 전환할 수 있습니다.

## <a name="hot-access-tier"></a>핫 액세스 계층

핫 액세스 계층은 쿨 및 아카이브 계층보다 스토리지 비용이 높지만 액세스 비용이 가장 낮습니다. 핫 액세스 계층에 대한 사용 시나리오의 예는 다음과 같습니다.

- 활성 상태이거나 자주 액세스(읽기 및 기록)될 것으로 예상되는 데이터입니다.
- 처리 및 최종 마이그레이션을 위해 준비된 데이터입니다.

## <a name="cool-access-tier"></a>쿨 액세스 계층

멋진 액세스 계층은 핫 스토리지에 비해 스토리지 비용이 절감되고 액세스 비용이 높습니다. 이 계층은 최소 30일 동안 쿨 계층에 유지되는 데이터를 위한 계층입니다. 멋진 액세스 계층에 대한 사용 시나리오의 예는 다음과 같습니다.

- 단기 백업 및 재해 복구 데이터 세트
- 자주 감상하지 않으나, 액세스할 때 즉시 사용할 수 있어야 하는 오래된 미디어 콘텐츠
- 향후 처리를 위해 더 많은 데이터를 수집하는 동안 경제적으로 저장되어야 하는 대용량 데이터 집합 (*예:* 과학적 데이터의 장기 스토리지, 제조 설비의 원시 원격 분석 데이터)

## <a name="archive-access-tier"></a>보관 액세스 계층

아카이브 액세스 계층의 저장소 비용이 가장 낮습니다. 그러나 핫 및 쿨 계층에 비해 데이터 검색 비용이 더 높습니다. 아카이브 계층의 데이터를 검색하는 데 몇 시간이 걸릴 수 있습니다. 데이터는 최소 180일 동안 보관 계층에 보관되거나 조기 삭제 요금이 부과됩니다.

Blob이 아카이브 저장소에 있는 동안 Blob 데이터는 오프라인이며 읽거나 덮어쓰거나 수정할 수 없습니다. 아카이브에서 Blob을 읽거나 다운로드하려면 먼저 온라인 계층에 하이드레이드를 다시 처리해야 합니다. 아카이브 저장소에서 Blob의 스냅숏을 만들 수 없습니다. 그러나 Blob 메타데이터는 온라인 상태로 유지되며 사용할 수 있으므로 Blob 및 해당 속성을 나열할 수 있습니다. 아카이브의 Blob의 경우 유효한 작업은 GetBlobProperties, GetBlob메타데이터, ListBlobs, SetBlobTier, CopyBlob 및 DeleteBlob뿐입니다. 자세한 내용은 [아카이브 계층의 Blob 데이터에 수분 공급](storage-blob-rehydration.md) 을 참조하세요.

아카이브 액세스 계층의 사용 시나리오는 다음과 같습니다.

- 장기 백업, 보조 백업 및 보관 데이터 세트
- 사용 가능한 최종 형태로 처리된 후에도 보존할 필요가 있는 원래(원시) 데이터.
- 장기간 저장할 필요가 있거나 거의 액세스하지 않는 규정 준수 및 보관 데이터.

## <a name="account-level-tiering"></a>계정 수준 계층화

세 액세스 계층 모두의 Blob은 동일한 계정 내에서 공존할 수 있습니다. 명시적으로 할당된 계층이 없는 Blob은 계정 액세스 계층 설정에서 계층을 추론합니다. 액세스 계층이 계정에서 오는 경우 **액세스 계층 추론** Blob 속성이 "true"로 설정되어 있고 Access **Tier** Blob 속성이 계정 계층과 일치합니다. Azure 포털에서 _액세스 계층 추론_ 속성은 Blob 액세스 계층을 **핫(추론)** 또는 **Cool(유추)으로**표시됩니다.

계정 액세스 계층 변경은 명시적 계층 집합이 없는 계정에 저장된 모든 _액세스 계층 유추_ 객체에 적용됩니다. 계정 계층을 핫에서 쿨로 전환하면 GPv2 계정에서만 설정된 계층이 없는 모든 Blob에 대해 쓰기 작업(10,000개당)에 대한 요금이 부과됩니다. Blob 저장소 계정의 이 변경에 대한 요금은 없습니다. Blob 저장소 또는 GPv2 계정에서 쿨에서 핫으로 전환하는 경우 읽기 작업(10,000개당)과 데이터 검색(GB당)에 대해 요금이 부과됩니다.

## <a name="blob-level-tiering"></a>Blob 수준 계층화

Blob 수준 계층화는 [Blob 넣기](/rest/api/storageservices/put-blob) 또는 [블록 목록 넣기](/rest/api/storageservices/put-block-list) 작업을 사용하여 선택한 액세스 계층에 데이터를 업로드하고 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업 또는 [수명 주기 관리](#blob-lifecycle-management) 기능을 사용하여 개체 수준에서 데이터 계층을 변경할 수 있습니다. 필요한 액세스 계층에 데이터를 업로드한 다음 계정 간에 데이터를 이동하지 않고도 사용 패턴이 변경될 때 핫, 쿨 또는 아카이브 계층 간에 Blob 액세스 계층을 쉽게 변경할 수 있습니다. 모든 계층 변경 요청은 즉시 발생하며 핫 및 쿨 간의 계층 변경은 즉각적입니다. 그러나 보관에서 Blob을 리하이드레이션하는 데 몇 시간이 걸릴 수 있습니다.

마지막 Blob 계층 변경 시간은 **액세스 계층 변경 시간** Blob 속성을 통해 노출됩니다. 핫 또는 쿨 계층에서 Blob을 덮어쓰는 경우 새로 만든 Blob은 새 Blob 액세스 계층이 생성 시 명시적으로 설정되지 않는 한 덮어쓴 Blob의 계층을 상속합니다. Blob이 아카이브 계층에 있는 경우 덮어쓸 수 없으므로 이 시나리오에서는 동일한 Blob을 업로드할 수 없습니다. 

> [!NOTE]
> 보관 스토리지 및 Blob 수준 계층화는 블록 Blob만 지원합니다. 또한 현재 스냅숏이 있는 블록 Blob의 계층을 변경할 수 없습니다.

### <a name="blob-lifecycle-management"></a>Blob 수명 주기 관리

Blob Storage 수명 주기 관리는 데이터를 최상의 액세스 계층으로 전환하고 수명 주기가 끝날 때 데이터를 만료하는 데 사용할 수 있는 풍부한 규칙 기반 정책을 제공합니다. [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)를 참조하여 자세히 알아보세요.  

> [!NOTE]
> 블록 Blob 저장소 계정에 저장된 데이터(프리미엄 성능)는 현재 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 또는 Azure Blob 저장소 수명 주기 관리를 사용하여 핫, 쿨 또는 아카이브로 계층화할 수 없습니다.
> 데이터를 이동하려면 블록 Blob 저장소 계정에서 [URL 에서 넣기 URL API](/rest/api/storageservices/put-block-from-url) 또는 이 API를 지원하는 AzCopy 버전을 사용하여 다른 계정의 핫 액세스 계층으로 Blob을 동기적으로 복사해야 합니다.
> *URL에서 블록 배치* API는 서버에서 데이터를 동기적으로 복사합니다. 이는 모든 데이터를 원래 서버 위치에서 대상 위치로 이동하면 호출이 완료된다는 의미입니다.

### <a name="blob-level-tiering-billing"></a>Blob 수준 계층화 청구

Blob이 핫, 쿨 또는 아카이브 계층으로 업로드되거나 이동되면 계층 변경 즉시 해당 요금이 부과됩니다.

BLOB이 쿨 계층으로 이동하는 경우(핫->쿨, 핫->아카이브, 또는 쿨->아카이브) 작업은 대상 계층 쓰기 작업으로 청구되며 대상 계층의 쓰기 작업(10,000개당) 및 데이터 쓰기(GB당) 요금이 적용됩니다.

Blob이 더 따뜻한 계층(아카이브->쿨, 아카이브 >핫 또는 쿨 >핫)으로 이동하면 읽기 작업(10,000개당) 및 원본 계층의 데이터 검색(GB당) 요금이 적용되는 원본 계층에서 읽기로 인해 작업이 청구됩니다. 쿨 또는 보관 계층에서 이동한 모든 Blob에 대한 초기 삭제 요금도 적용해야 합니다. [아카이브에서 데이터를 다시 수화하는](storage-blob-rehydration.md) 데는 시간이 걸리며 데이터가 온라인으로 복원되고 Blob 계층이 뜨겁거나 차가운 상태가 될 때까지 데이터가 아카이브 가격이 청구됩니다. 다음 표에는 계층 변경에 대한 요금이 청구되는 방법을 요약한 내용입니다.

| | **쓰기 요금(작업 + 액세스)** | **읽기 요금(작업 + 액세스)**
| ---- | ----- | ----- |
| **SetBlobTier 방향** | 뜨거운 >시원한,<br> 핫 >아카이브,<br> 멋진 >아카이브 | 아카이브 ->멋진,<br> 아카이브 >뜨거운,<br> 시원한 >뜨거운

### <a name="cool-and-archive-early-deletion"></a>쿨 및 보관 초기 삭제

쿨 계층으로 이동되는 모든 Blob(GPv2 계정만 해당)은 30일의 초기 삭제 기간이 적용됩니다. 아카이브 계층으로 이동되는 모든 Blob에는 180일의 아카이브 조기 삭제 기간이 적용됩니다. 요금이 비례하여 배분됩니다. 예를 들어 Blob을 보관으로 이동한 다음 45일 후에 삭제하거나 핫 티어로 이동한 경우 해당 Blob을 보관에 저장한 일수의 135일(180일에서 45일)에 해당하는 조기 삭제 수수료가 부과됩니다.

액세스 계층변경이 없는 경우 Blob 속성인 **Last-Modified를**사용하여 초기 삭제를 계산할 수 있습니다. 그렇지 않으면 Blob 속성인 **액세스 계층 변경 시간을**확인하여 액세스 계층이 마지막으로 수정되어 냉각되거나 보관될 때 사용할 수 있습니다. Blob 속성에 대한 자세한 내용은 [Blob 속성 가져오기](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)를 참조하세요.

## <a name="comparing-block-blob-storage-options"></a>블록 Blob 저장소 옵션 비교

다음 표에서는 프리미엄 성능 블록 Blob 저장소와 핫, 쿨 및 아카이브 액세스 계층을 비교한 것을 보여 주며,

|                                           | **프리미엄 성능**   | **핫 티어** | **쿨 티어**       | **아카이브 계층**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **가용성**                          | 99.9%                     | 99.9%        | 99%                 | 오프라인           |
| **가용성** <br> **(RA-GRS 읽기)**  | 해당 없음                       | 99.99%       | 99.9%               | 오프라인           |
| **사용 요금**                         | 스토리지 비용 증가, 액세스 및 트랜잭션 비용 절감 | 스토리지 비용 더 높음, 액세스 및 트랜잭션 비용 더 낮음 | 스토리지 비용 더 낮음, 액세스 및 트랜잭션 비용 더 높음 | 스토리지 비용 가장 낮음, 액세스 및 트랜잭션 비용 가장 높음 |
| **최소 개체 크기**                   | 해당 없음                       | 해당 없음          | 해당 없음                 | 해당 없음               |
| **최소 스토리지 기간**              | 해당 없음                       | 해당 없음          | 30 일<sup>1</sup> | 180일
| **대기 시간** <br> **(첫 번째 바이트까지의 시간)** | 한 자리 밀리초 | 밀리초 | 밀리초        | <sup>2시간</sup> |

<sup>1</sup> GPv2 계정의 멋진 계층에 있는 개체의 보존 기간은 최소 30일입니다. Blob 저장소 계정에는 멋진 계층에 대한 최소 보존 기간이 없습니다.

<sup>2</sup> 아카이브 스토리지는 현재 다른 검색 대기 시간을 제공하는 2 개의 수화 우선 순위인 높음 및 표준을 지원합니다. 자세한 내용은 [아카이브 계층의 Blob 데이터에 하이드레이트 데이터를](storage-blob-rehydration.md)참조하십시오.

> [!NOTE]
> Blob 저장소 계정은 범용 v2 저장소 계정과 동일한 성능 및 확장성 목표를 지원합니다. 자세한 내용은 [Azure 스토리지의 확장성 및 성능 목표](scalability-targets.md)를 참조하세요.

## <a name="quickstart-scenarios"></a>빠른 시작 시나리오

이 섹션에서는 Azure 포털 및 powershell을 사용하여 다음 시나리오를 보여 주시면 됩니다.

- GPv2 또는 Blob Storage 계정의 기본 계정 액세스 계층을 변경하는 방법입니다.
- GPv2 또는 Blob Storage 계정의 계층을 변경하는 방법입니다.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2 또는 Blob Storage 계정의 기본 계정 액세스 계층을 변경합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 포털에서 모든 리소스를 검색하고 **선택합니다.**

1. 사용자의 스토리지 계정을 선택합니다.

1. **설정에서** **구성을** 선택하여 계정 구성을 보고 변경합니다.

1. 필요에 맞는 액세스 계층 선택: **액세스 계층을** **쿨** 또는 **핫으로**설정합니다.

1. 위쪽에서 **저장** 을 클릭합니다.

![저장소 계정 계층 변경](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
다음 PowerShell 스크립트를 사용하여 계정 계층을 변경할 수 있습니다. 변수는 `$rgName` 리소스 그룹 이름으로 초기화되어야 합니다. 변수는 `$accountName` 저장소 계정 이름으로 초기화해야 합니다. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>GPv2 또는 Blob 저장소 계정에서 Blob 계층 변경
# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 포털에서 모든 리소스를 검색하고 **선택합니다.**

1. 사용자의 스토리지 계정을 선택합니다.

1. 컨테이너를 선택한 다음 Blob을 선택합니다.

1. **Blob 속성에서** **계층 변경을**선택합니다.

1. **핫,** **쿨**또는 **보관** 액세스 계층을 선택합니다. Blob이 현재 보관 되어 있고 온라인 계층으로 수화하려는 경우 **표준** 또는 **높음의**수화물 재수화물 우선 순위를 선택할 수도 있습니다.

1. 하단에 **저장을** 선택합니다.

![저장소 계정 계층 변경](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
다음 PowerShell 스크립트를 사용하여 Blob 계층을 변경할 수 있습니다. 변수는 `$rgName` 리소스 그룹 이름으로 초기화되어야 합니다. 변수는 `$accountName` 저장소 계정 이름으로 초기화해야 합니다. 변수는 `$containerName` 컨테이너 이름으로 초기화되어야 합니다. 변수는 `$blobName` Blob 이름으로 초기화되어야 합니다. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

모든 저장소 계정은 각 Blob의 계층을 기반으로 Blob 저장소 차단에 대한 가격 책정 모델을 사용합니다. 다음과 같은 비용 청구 고려 사항을 염두에 둡니다.

- **저장소 비용**: 저장된 데이터의 양 외에도 데이터 저장 비용은 액세스 계층에 따라 다릅니다. 계층이 차가워질수록 기가바이트당 비용이 감소합니다.
- **데이터 액세스 비용**: 계층이 차가워질수록 데이터 액세스 요금이 증가합니다. 멋진 및 아카이브 액세스 계층의 데이터에 대해 읽기에 대해 기가바이트당 데이터 액세스 요금이 부과됩니다.
- **거래 비용:** 계층이 원격으로 증가함에 따라 증가하는 모든 계층에 대해 트랜잭션당 요금이 부과됩니다.
- **지역에서 복제 데이터 전송 비용**: 이 요금은 GRS 및 RA-GRS를 포함하여 지역에서 복제가 구성된 계정에만 해당합니다. 지역 복제 데이터 전송에는 기가바이트당 요금이 발생합니다.
- **아웃바운드 데이터 전송 비용**: 아웃바운드 데이터 전송(Azure 지역 밖으로 전송된 데이터)에서는 기가바이트당 요금을 기준으로 대역폭 사용 요금이 발생하며 범용 스토리지 계정과 같습니다.
- **액세스 계층 변경**: 계정 액세스 계층을 변경하면 명시적 계층 집합이 없는 계정에 저장된 _액세스 계층 유추_ Blob에 대한 계층 변경 요금이 부과됩니다. 단일 Blob에 대한 액세스 계층 변경에 대한 자세한 내용은 [Blob 수준 계층화 청구를](#blob-level-tiering-billing)참조하십시오.

> [!NOTE]
> Blob 블록의 가격 책정에 대한 자세한 내용은 [Azure 저장소 가격](https://azure.microsoft.com/pricing/details/storage/blobs/) 책정 페이지를 참조하세요. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 페이지를 참조하세요.

## <a name="faq"></a>FAQ

**데이터를 계층화하려면 Blob 저장소 또는 GPv2 계정을 사용해야 합니까?**

계층화에 Blob Storage 계정 대신 GPv2를 사용하는 것이 좋습니다. GPv2는 Blob Storage 계정이 지원하는 모든 기능을 지원합니다. Blob Storage와 GPv2 간의 가격 책정은 거의 동일하지만 몇 가지 새로운 기능 및 가격 구분은 GPv2 계정에서만 사용할 수 있습니다. GPv1 계정은 계층화를 지원하지 않습니다.

GPv1과 GPv2 계정 간에 가격 책정 구조가 다르며 고객은 GPv2 계정을 사용하기 전에 둘을 신중하게 평가해야 합니다. 간단히 한 번 클릭하는 프로세스를 통해 기존 Blob Storage 또는 GPv1 계정을 GPv2로 쉽게 변환할 수 있습니다. 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

**세 가지(핫, 쿨 및 아카이브) 액세스 계층에 동일한 계정에 개체를 저장할 수 있습니까?**

예. 계정 수준에서 설정된 **Access Tier** 특성은 명시적 집합 계층이 없는 해당 계정의 모든 개체에 적용되는 기본 계정 계층입니다. Blob 수준 계층화는 계정의 액세스 계층 설정에 관계없이 개체 수준에서 액세스 계층을 설정할 수 있도록 합니다. 세 가지 액세스 계층 중 임의의 Blob(핫, 쿨 또는 아카이브)이 동일한 계정 내에 존재할 수 있습니다.

**Blob 또는 GPv2 저장소 계정의 기본 액세스 계층을 변경할 수 있습니까?**

예. 저장소 계정에 **Access 계층** 특성을 설정하여 기본 계정 계층을 변경할 수 있습니다. 계정 계층 변경은 명시적 계층 집합이 없는 계정에 저장된 모든 개체(예: **핫(추론)** 또는 **쿨(추론))에**적용됩니다. 계정 계층을 핫에서 냉각으로 전환하면 GPv2 계정의 설정 계층이 없는 모든 Blob에 대해 쓰기 작업(10,000개당)을 설정하고 쿨에서 핫으로 전환하면 읽기 작업(10,000개당)과 Blob 저장소 및 GPv2 계정의 모든 Blob에 대한 데이터 검색(GB당) 요금이 모두 발생합니다.

**기본 계정 액세스 계층을 보관 계층으로 설정할 수 있나요?**

아니요. 핫 및 쿨 액세스 계층만 기본 계정 액세스 계층으로 설정할 수 있습니다. 보관은 개체 수준에서만 설정할 수 있습니다. Blob 업로드시 기본 계정 계층에 관계없이 선택한 액세스 계층을 핫, 쿨 또는 보관하도록 지정합니다. 이 기능을 사용하면 데이터를 아카이브 계층에 직접 작성하여 Blob 저장소에서 데이터를 만드는 순간부터 비용을 절감할 수 있습니다.

**어떤 지역에서 사용할 수 있는 핫, 쿨 및 아카이브 액세스 계층은 있습니까?**

모든 리전에서 Blob 수준 계층과 함께 핫 및 쿨 액세스 계층을 사용할 수 있습니다. 보관 스토리지는 선택 영역에서만 사용할 수 있습니다. 전체 목록은 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

**멋진 액세스 계층의 Blob이 핫 액세스 계층의 Blob과 다르게 수행되나요?**

핫 액세스 계층의 Blob은 GPv1, GPv2 및 Blob 저장소 계정의 Blob과 동일한 대기 시간을 갖습니다. 멋진 액세스 계층의 Blob은 GPv1, GPv2 및 Blob 저장소 계정의 Blob과 비슷한 대기 시간(밀리초)을 갖습니다. 아카이브 액세스 계층의 Blob에는 GPv1, GPv2 및 Blob 저장소 계정에서 몇 시간의 대기 시간이 있습니다.

멋진 액세스 계층의 Blob은 핫 액세스 계층에 저장된 Blob보다 가용성 서비스 수준(SLA)이 약간 낮습니다. 자세한 내용은 [스토리지에 대한 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)를 참조하세요.

**핫, 쿨 및 보관 계층 간에 작업은 동일한가요?**

핫 및 쿨 간의 모든 작업은 100% 일치합니다. GetBlobProperties, GetBlob메타데이터, 리스트블블, SetBlobTier 및 DeleteBlob을 포함한 모든 유효한 아카이브 작업은 핫하고 멋진 것과 100% 일치합니다. Blob 데이터는 다시 수화될 때까지 아카이브 계층에 있는 동안 읽거나 수정할 수 없습니다. 보관 하는 동안 Blob 메타 데이터 읽기 작업만 지원 됩니다.

**아카이브 계층에서 핫 또는 쿨 티어로 Blob을 재수화할 때 재수화가 완료되면 어떻게 알 수 있습니까?**

재수화 하는 동안 Blob 속성 수집 작업을 사용 하 여 **보관 상태** 특성을 폴링 하 고 계층 변경완료 시기를 확인할 수 있습니다. 상태는 대상 계층에 따라 "rehydrate-pending-to-hot" 또는 "rehydrate-pending-to-cool"을 읽습니다. 완료되면 보관 상태 속성이 제거되고 **액세스 계층** Blob 속성은 새로운 핫 또는 쿨 계층을 반영합니다. 자세한 내용은 [아카이브 계층의 Blob 데이터에 수분 공급](storage-blob-rehydration.md) 을 참조하세요.

**Blob의 계층을 설정한 후에 언제부터 해당하는 요금이 청구되기 시작하나요?**

각 Blob은 항상 Blob의 **액세스 계층** 속성에서 표시하는 계층에 따라 비용이 청구됩니다. Blob에 대한 새 온라인 계층을 설정하면 **Access Tier** 속성은 모든 전환에 대한 새 계층을 즉시 반영합니다. 그러나 오프라인 아카이브 계층에서 핫 또는 쿨 계층으로 Blob을 다시 수화하는 데는 몇 시간이 걸릴 수 있습니다. 이 경우 재수화가 완료될 때까지 보관 요금이 청구되며, 이 때 Access Tier 속성이 새 계층을 **반영합니다.** 온라인 계층으로 재수화되면 해당 Blob에 대해 뜨겁거나 차가운 요금으로 요금이 청구됩니다.

**멋진 또는 아카이브 계층에서 Blob을 삭제하거나 이동할 때 조기 삭제 요금이 발생하는지 확인하려면 어떻게 해야 합니까?**

쿨(GPv2 계정만) 또는 보관 계층에서 각각 30일 및 180일 이전에 삭제되거나 이동되는 모든 Blob은 비례 배분된 초기 삭제 요금이 발생합니다. 마지막 계층 변경스탬프를 제공하는 **액세스 계층 변경 시간** Blob 속성을 확인하여 Blob이 쿨 또는 보관 계층에 있었던 시간을 확인할 수 있습니다. Blob의 계층이 변경되지 않은 경우 마지막으로 **수정된** Blob 속성을 확인할 수 있습니다. 자세한 내용은 [쿨 및 아카이브 초기 삭제를](#cool-and-archive-early-deletion)참조하십시오.

**Blob 수준 계층화 및 아카이브 저장소를 지원하는 Azure 도구 및 SDK는 무엇입니까?**

Azure Portal, PowerShell과 CLI 도구 및 .NET, Java, Python과 Node.js 클라이언트 라이브러리는 모두 Blob 수준 계층화 및 보관 스토리지를 지원합니다.  

**핫, 쿨 및 아카이브 계층에 저장할 수 있는 데이터의 양**

다른 제한과 함께 데이터 저장소는 액세스 계층이 아닌 계정 수준에서 설정됩니다. 한 계층 또는 세 계층 모두에 대해 모든 제한을 사용하도록 선택할 수 있습니다. 자세한 내용은 [표준 저장소 계정에 대한 확장성 및 성능 목표를](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)참조하십시오.

## <a name="next-steps"></a>다음 단계

GPv2 및 Blob 저장소 계정에서 핫, 쿨 및 아카이브 평가

- [지역별 핫, 쿨 및 보관의 가용성 확인](https://azure.microsoft.com/regions/#services)
- [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)
- [아카이브 계층에서 Blob 데이터 리하이드레이션에 대해 알아보기](storage-blob-rehydration.md)
- [프리미엄 성능이 앱에 도움이 되는지 확인](storage-blob-performance-tiers.md)
- [Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](../common/storage-enable-and-view-metrics.md)
- [지역별 Blob 저장소 및 GPv2 계정에서 핫, 쿨 및 아카이브 가격 확인](https://azure.microsoft.com/pricing/details/storage/)
- [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)
