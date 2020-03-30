---
title: 저장소 계정 복제 방법 변경
titleSuffix: Azure Storage
description: 기존 저장소 계정의 데이터를 복제하는 방법을 변경하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337087"
---
# <a name="change-how-a-storage-account-is-replicated"></a>저장소 계정 복제 방법 변경

Azure Storage는 일시적인 하드웨어 오류, 네트워크 또는 정전 및 대규모 자연 재해를 포함하여 계획되고 계획되지 않은 이벤트로부터 보호되도록 항상 데이터의 여러 복사본을 저장합니다. 중복성은 저장소 계정이 장애가 발생한 경우에도 [Azure 저장소에 대한 SLA(서비스 수준 계약)를](https://azure.microsoft.com/support/legal/sla/storage/) 충족하는지 확인합니다.

Azure Storage는 다음과 같은 유형의 복제를 제공합니다.

- LRS(로컬 중복 스토리지)
- ZRS(영역 중복 스토리지)
- 지리적 중복 스토리지(GRS) 또는 읽기 액세스 지리적 중복 스토리지(RA-GRS)
- 지리 영역 중복 저장소(GZRS) 또는 읽기 액세스 지리적 영역 중복 저장소(RA-GZRS) (미리 보기)

이러한 각 옵션에 대한 개요는 [Azure 저장소 중복을](storage-redundancy.md)참조하십시오.

## <a name="switch-between-types-of-replication"></a>복제 유형 간 전환

저장소 계정을 한 유형의 복제에서 다른 유형으로 전환할 수 있지만 일부 시나리오는 다른 유형보다 더 간단합니다. 지역 복제를 추가하거나 제거하거나 보조 영역에 대한 읽기 액세스를 추가하려는 경우 Azure 포털, PowerShell 또는 Azure CLI를 사용하여 복제 설정을 업데이트할 수 있습니다. 그러나 LRS에서 ZRS로 이동하거나 그 반대로 데이터를 기본 리전에서 복제하는 방법을 변경하려면 수동 마이그레이션을 수행해야 합니다.

다음 표에서는 각 복제 유형에서 다른 복제 유형으로 전환하는 방법에 대한 개요를 제공합니다.

| 전환 | ... LRS로 | ... GRS/RA-GRS로 | ... ZRS로 | ... GZRS/RA-GZRS로 |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... LRS에서</b> | 해당 없음 | Azure 포털, PowerShell 또는 CLI를 사용하여 복제 설정<sup>1을</sup> 변경합니다. | 수동 마이그레이션 수행 <br /><br />실시간 마이그레이션 요청 | 수동 마이그레이션 수행 <br /><br /> 또는 <br /><br /> 먼저 GRS/RA-GRS로 전환한 다음 라이브 마이그레이션<sup>1을</sup> 요청합니다. |
| <b>... GRS/RA-GRS에서</b> | Azure 포털, PowerShell 또는 CLI를 사용하여 복제 설정을 변경합니다. | 해당 없음 | 수동 마이그레이션 수행 <br /><br /> 또는 <br /><br /> 먼저 LRS로 전환한 다음 라이브 마이그레이션을 요청합니다. | 수동 마이그레이션 수행 <br /><br /> 실시간 마이그레이션 요청 |
| <b>... ZRS에서</b> | 수동 마이그레이션 수행 | 수동 마이그레이션 수행 | 해당 없음 | Azure 포털, PowerShell 또는 CLI를 사용하여 복제 설정<sup>1을</sup> 변경합니다. |
| <b>... GZRS/RA-GZRS에서</b> | 수동 마이그레이션 수행 | 수동 마이그레이션 수행 | Azure 포털, PowerShell 또는 CLI를 사용하여 복제 설정을 변경합니다. | 해당 없음 |

<sup>1</sup> 일회성 송신 요금이 부과됩니다.

> [!CAUTION]
> (RA-)GRS 또는 (RA-) GZRS 계정에 대한 [계정 장애 조치(failover)를](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) 수행한 경우 새 기본 리전에서 로컬로 중복하도록 구성됩니다. 이러한 LRS 계정에 대한 ZRS 또는 GZRS로의 실시간 마이그레이션은 지원되지 않습니다. [수동 마이그레이션을](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs)수행해야 합니다.

## <a name="change-the-replication-setting"></a>복제 설정 변경

Azure 포털, PowerShell 또는 Azure CLI를 사용하여 기본 리전에서 데이터가 복제되는 방법을 변경하지 않는 한 저장소 계정에 대한 복제 설정을 변경할 수 있습니다. 기본 지역의 LRS에서 기본 리전의 ZRS로 마이그레이션하거나 그 반대로 마이그레이션하는 경우 [수동 마이그레이션](#perform-a-manual-migration-to-zrs) 또는 라이브 [마이그레이션을](#request-a-live-migration-to-zrs)수행해야 합니다.

저장소 계정을 복제하는 방법을 변경해도 응용 프로그램의 가동 중지 시간이 발생하지 않습니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure 포털에서 저장소 계정에 대한 중복 옵션을 변경하려면 다음 단계를 따르십시오.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **구성** 설정을 선택합니다.
1. 복제 설정을 **업데이트합니다.**

![포털에서 복제 옵션을 변경하는 방법을 보여주는 스크린샷](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

PowerShell을 사용하여 저장소 계정에 대한 중복 옵션을 변경하려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 `-SkuName` 호출하고 매개 변수를 지정합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 저장소 계정에 대한 중복 옵션을 변경하려면 [az 저장소 계정](/cli/azure/storage/account#az-storage-account-update) `--sku` 업데이트 명령을 호출하고 매개 변수를 지정합니다.

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>ZRS로의 수동 마이그레이션 수행

저장소 계정의 데이터가 기본 리전에서 복제되는 방식을 변경하려면 LRS에서 ZRS로 이동하거나 그 반대로 이동하면 수동 마이그레이션을 수행할 수 있습니다. 수동 마이그레이션은 실시간 마이그레이션보다 더 유연합니다. 수동 마이그레이션 의 타이밍을 제어하므로 특정 날짜까지 마이그레이션을 완료해야 하는 경우 이 옵션을 사용합니다.

LRS에서 기본 리전에서 ZRS로 수동 마이그레이션을 수행하거나 그 반대로 수행할 경우 대상 저장소 계정은 지리적 중복이 될 수 있으며 보조 영역에 대한 읽기 액세스를 위해 구성할 수도 있습니다. 예를 들어 한 단계에서 LRS 계정을 GZRS 또는 RA-GZRS 계정으로 마이그레이션할 수 있습니다.

수동 마이그레이션을 사용하면 결국 애플리케이션 가동 중지 시간이 발생할 수 있습니다. 애플리케이션에 고가용성이 필요한 경우 Microsoft에서는 실시간 마이그레이션 옵션도 제공합니다. 실시간 마이그레이션은 가동 중지 시간이 없는 즉각적인 마이그레이션입니다.

수동 마이그레이션을 사용하면 기존 저장소 계정의 데이터를 기본 리전에서 ZRS를 사용하는 새 저장소 계정으로 복사합니다. 수동 마이그레이션을 수행하려면 다음 옵션 중 하나를 사용할 수 있습니다.

- Azure Storage 클라이언트 라이브러리 중 하나인 AzCopy 또는 신뢰할 수 있는 타사 도구와 같은 기존 도구를 사용하여 데이터를 복사합니다.
- Hadoop 또는 HDInsight에 익숙한 경우 원본 저장소 계정과 대상 저장소 계정 계정을 클러스터에 모두 연결할 수 있습니다. 그런 다음, DistCp와 같은 도구를 사용하여 데이터 복사 프로세스를 병렬 처리합니다.

## <a name="request-a-live-migration-to-zrs"></a>ZRS로의 실시간 마이그레이션 요청

응용 프로그램 가동 중지 시간 없이 LRS 또는 GRS에서 ZRS로 저장소 계정을 마이그레이션해야 하는 경우 Microsoft에서 실시간 마이그레이션을 요청할 수 있습니다. 실시간 마이그레이션 중에 저장소 계정의 데이터에 액세스할 수 있으며 내구성이나 가용성을 잃지 않습니다. Azure 저장소 SLA는 마이그레이션 프로세스 중에 유지됩니다. 라이브 마이그레이션과 관련된 데이터 손실이 없습니다. 서비스 끝점, 액세스 키, 공유 액세스 서명 및 기타 계정 옵션은 마이그레이션 후에도 변경되지 않습니다.

ZRS는 범용 v2 계정만 지원하므로 ZRS로의 실시간 마이그레이션 요청을 제출하기 전에 저장소 계정을 업그레이드해야 합니다. 자세한 내용은 [범용 v2 저장소 계정으로 업그레이드를](storage-account-upgrade.md)참조하십시오. 저장소 계정에는 라이브 마이그레이션을 통해 마이그레이션할 데이터가 포함되어야 합니다.

실시간 마이그레이션은 LRS 또는 GRS 복제를 사용하는 스토리지 계정에 대해서만 지원됩니다. 계정이 RA-GRS를 사용하는 경우 계속 진행하기 전에 먼저 계정의 복제 유형을 LRS 또는 GRS로 변경해야 합니다. 이 중간 단계에서는 마이그레이션 전에 RA-GRS에서 제공한 보조 읽기 전용 엔드포인트가 제거됩니다.

Microsoft에서는 실시간 마이그레이션에 대한 요청을 신속하게 처리하지만 마이그레이션이 완료되는 시기는 보장하지 않습니다. 특정 날짜에 ZRS로 마이그레이션된 데이터가 필요한 경우에는 대신 수동 마이그레이션을 수행하는 것이 좋습니다. 일반적으로 계정에 데이터가 많을수록 해당 데이터를 마이그레이션하는 데 시간이 더 오래 걸립니다.

다음과 같은 경우 수동 마이그레이션을 수행해야 합니다.

- 데이터를 원본 계정과 다른 지역에 있는 ZRS 저장소 계정으로 마이그레이션하려고 합니다.
- 저장소 계정은 프리미엄 페이지 Blob 또는 블록 Blob 계정입니다.
- ZRS에서 LRS, GRS 또는 RA-GRS로 데이터를 마이그레이션하려고 합니다.
- 저장소 계정에는 아카이브 계층의 데이터가 포함됩니다.

실시간 마이그레이션은 [Azure 지원 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)을 통해 요청할 수 있습니다. 포털에서 ZRS로 변환하려는 스토리지 계정을 선택합니다.

1. **새 지원 요청** 선택
2. 사용자 계정 정보를 기반으로 **기본 사항**을 완료합니다. **서비스** 섹션에서 **스토리지 계정 관리** 및 ZRS로 변환하려는 리소스를 선택합니다.
3. **다음**을 선택합니다.
4. **문제** 섹션에서 다음 값을 지정합니다.
    - **심각도**: 기본값을 그대로 둡니다.
    - **문제 유형**: **데이터 마이그레이션**을 선택합니다.
    - **범주**: **ZRS로 마이그레이션을**선택합니다.
    - **제목**: 설명이 포함된 제목, 예를 들어 **ZRS 계정 마이그레이션**을 입력합니다.
    - **세부 정보**: **세부 정보** 상자에 추가 세부 정보를 입력합니다. \_ \_
5. **다음**을 선택합니다.
6. **연락처 정보** 블레이드에서 연락처 정보가 올바른지 확인합니다.
7. **만들기**를 선택합니다.

지원 담당자가 사용자에게 연락하여 필요한 모든 지원을 제공합니다.

> [!NOTE]
> 프리미엄 파일 공유에는 라이브 마이그레이션이 현재 지원되지 않습니다. 현재 수동으로 데이터를 복사하거나 이동하는 것만 지원됩니다.
>
> GZRS 저장소 계정은 현재 아카이브 계층을 지원하지 않습니다. 자세한 내용은 [Azure Blob 저장소: 핫, 쿨 및 아카이브 액세스 계층을](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) 참조하십시오.
>
> 관리되는 디스크는 LRS에서만 사용할 수 있으며 ZRS로 마이그레이션할 수 없습니다. 표준 SSD 관리 디스크의 스냅샷과 이미지를 표준 HDD 스토리지에 저장하고 [LRS 및 ZRS 옵션 중에서 선택할](https://azure.microsoft.com/pricing/details/managed-disks/)수 있습니다. 가용성 집합과의 통합에 대한 자세한 내용은 [Azure 관리 디스크 소개를](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)참조하십시오.

## <a name="switch-from-zrs-classic"></a>ZRS 클래식에서 전환

> [!IMPORTANT]
> Microsoft에서는 2021년 3월 31일에 ZRS 클래식 계정의 사용을 중단하고 마이그레이션할 예정입니다. 사용이 중단되기 전에 ZRS 클래식 고객에게 자세한 내용이 제공됩니다.
>
> 지정된 리전에서 ZRS를 일반적으로 사용할 수 있게 되면 고객은 해당 지역의 Azure 포털에서 ZRS 클래식 계정을 더 이상 만들 수 없습니다. ZRS 클래식의 사용이 중단될 때까지 Microsoft PowerShell 및 Azure CLI를 사용하여 ZRS 클래식 계정을 만드는 것은 옵션입니다. ZRS를 사용할 수 있는 위치에 대한 자세한 내용은 [Azure 저장소 중복을](storage-redundancy.md)참조하십시오.

ZRS 클래식은 1~2개의 지역 내의 데이터 센터에서 데이터를 비동기적으로 복제합니다. 복제된 데이터는 Microsoft가 보조 지역에 장애 조치(failover)를 시작하지 않는 한 사용할 수 없습니다. ZRS 클래식 계정을 LRS, GRS 또는 RA-GRS 계정으로 변환하거나, 이러한 계정에서 ZRS 클래식 계정으로 변환할 수 없습니다. 또한 ZRS 클래식 계정은 메트릭이나 로깅을 지원하지 않습니다.

ZRS 클래식은 GPv1(범용 V1) 스토리지 계정의 **블록 Blob**에서만 사용할 수 있습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

LRS, GRS, RA-GRS 또는 ZRS 클래식 계정에서 ZRS 계정 데이터를 수동으로 마이그레이션하려면 AzCopy, Azure 저장소 탐색기, PowerShell 또는 Azure CLI 와 같은 도구 중 하나를 사용합니다. Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 마이그레이션 솔루션을 빌드할 수도 있습니다.

ZRS를 사용할 수 있는 지역에서 Azure 포털, PowerShell 또는 Azure CLI를 사용하여 ZRS 클래식 저장소 계정을 ZRS로 업그레이드할 수도 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure 포털에서 ZRS로 업그레이드하려면 계정의 **구성** 설정으로 이동하여 **업그레이드를**선택합니다.

![포털에서 ZRS로 ZRS 클래식 업그레이드](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

PowerShell을 사용하여 ZRS로 업그레이드하려면 다음 명령을 호출합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 ZRS로 업그레이드하려면 다음 명령을 호출합니다.

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>데이터 복제 방법 변경과 관련된 비용

데이터 복제 방법 변경과 관련된 비용은 전환 경로에 따라 다릅니다. 최소한의 주문에서 가장 비싼 Azure 저장소 중복 제품은 LRS, ZRS, GRS, RA-GRS, GZRS 및 RA-GZRS를 포함합니다.

예를 들어 *LRS에서* 다른 유형의 복제로 이동하면 보다 정교한 중복 수준으로 이동하기 때문에 추가 요금이 부과됩니다. GRS *to* 또는 RA-GRS로 마이그레이션하면 기본 지역의 데이터가 원격 보조 지역으로 복제되기 때문에 송신 대역폭 요금이 부과됩니다. 이 요금은 초기 설정 시 일회성 비용입니다. 데이터를 복사한 후에는 추가 마이그레이션 요금이 부과되지 않습니다. 대역폭 요금에 대한 자세한 내용은 [Azure Storage 가격 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

저장소 계정을 GRS에서 LRS로 마이그레이션하는 경우 추가 비용이 없지만 복제된 데이터는 보조 위치에서 삭제됩니다.

> [!IMPORTANT]
> 저장소 계정을 RA-GRS에서 GRS 또는 LRS로 마이그레이션하는 경우 해당 계정은 변환된 날짜로부터 추가로 30일 동안 RA-GRS로 청구됩니다.

## <a name="see-also"></a>참조

- [Azure 스토리지 중복성](storage-redundancy.md)
- [저장소 계정의 마지막 동기화 시간 속성을 확인합니다.](last-sync-time-get.md)
- [읽기 액세스 지리적 중복 스토리지를 사용하여 가용성이 높은 응용 프로그램 설계](storage-designing-ha-apps-with-ragrs.md)
