---
title: Azure Storage에서 데이터 복제 | Microsoft Docs
description: Microsoft Azure Storage 계정의 데이터는 내구성 및 고가용성을 위해 복제됩니다. 복제 옵션은 (LRS) 로컬 중복 저장소(LRS), 영역 중복 저장소 (ZRS), 지역 중복 저장소 (GRS) 및 읽기 액세스 지역 중복 저장소 (RA-GRS)에 포함 됩니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 600b66af3b7da24c5a40d09d5cdf76f2d5be67ac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-storage-replication"></a>Azure Storage 복제

Microsoft Azure Storage 계정 데이터는 항상 내구성 및 고가용성을 위해 복제됩니다. 복제는 일시적인 하드웨어 장애로부터 데이터가 보호되도록 데이터를 복사하여 응용 프로그램을 최신 상태로 유지합니다. 

동일한 데이터 센터, 동일한 지역 내의 데이터 센터 또는 전체 지역에 데이터를 복제하도록 선택할 수 있습니다. 데이터가 여러 데이터 센터 또는 지역에 걸쳐 복제되는 경우 단일 위치의 치명적인 장래로부터 보호됩니다.

복제를 사용하면 Storage 계정은 오류 상황에서도 [Storage용 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/)를 충족하게 됩니다. Azure Storage의 내구성 및 가용성 보장에 대한 정보는 SLA를 확인하세요.

저장소 계정을 만들 때 다음 복제 옵션 중 하나를 선택할 수 있습니다.

* [LRS(로컬 중복 저장소)](#locally-redundant-storage)
* [ZRS(영역 중복 저장소)](#zone-redundant-storage)
* [GRS(지역 중복 저장소)](#geo-redundant-storage)
* [RA-GRS(읽기 액세스 지역 중복 저장소)](#read-access-geo-redundant-storage)

LRS(로컬 중복 저장소)는 저장소 계정을 만드는 경우 기본 옵션입니다.

다음 테이블에는 LRS, ZRS, GRS 및 RA-GRS 사이의 차이점에 대한 간략한 개요가 제공됩니다. 이 문서의 후속 섹션에는 복제의 각 유형이 자세히 설명되어 있습니다.

| 복제 전략 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 데이터가 여러 데이터 센터에 걸쳐 복제됩니다. |아니오 |예 |예 |예 |
| 기본 위치와 보조 위치에서 데이터를 읽을 수 있습니다. |아니오 |아니오 |아니오 |예 |
| 지정된 연도 동안 ___의 개체 내구성을 제공하도록 설계되었습니다. |최소 99.999999999%|최소 99.9999999999%|최소 99.99999999999999%|최소 99.99999999999999%|

다른 이중화 옵션에 대한 가격 정보를 보려면 [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage/) 을 참조하세요.

> [!NOTE]
> Premium Storage는 LRS(로컬 중복 저장소)만 지원합니다. Premium Storage에 대한 자세한 내용은 [Premium Storage: Azure Virtual Machine에 대한 고성능 저장소](../../virtual-machines/windows/premium-storage.md)를 참조하세요.
>

## <a name="locally-redundant-storage"></a>로컬 중복 저장소
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>영역 중복 저장소
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="zrs-classic-accounts"></a>ZRS 클래식 계정

기존 ZRS 기능은 이제 ZRS 클래식이라고 합니다. ZRS 클래식 계정은 범용 V1 저장소 계정의 블록 Blob에 대해서만 사용할 수 있습니다. 

ZRS 클래식은 1~2개의 영역 내에서 데이터 센터 간에 데이터를 비동기적으로 복제합니다. 복제본은 Microsoft가 보조 지역에 장애 조치를 시작하지 않는 한 사용할 수 없습니다. 

ZRS 클래식 계정은 LRS, GRS 또는 RA-GRS 계정으로 변환하거나 이러한 계정을 ZRS 클래식 계정으로 변환할 수 없습니다. ZRS 클래식 계정은 메트릭이나 로깅을 지원하지 않습니다.   

ZRS를 한 지역에서 일반적으로 사용할 수 있게 되면 해당 지역의 포털에서 ZRS 클래식 계정을 더 이상 만들 수 없지만 다른 방법으로 ZRS 클래식 계정을 만들 수 있습니다.  
ZRS 클래식에서 ZRS로 자동 마이그레이션 프로세스는 앞으로 제공될 예정입니다.

ZRS 계정 데이터는 LRS, ZRS 클래식, GRS 또는 RAGRS 계정 간에 수동으로 마이그레이션할 수 있습니다. 이러한 수동 마이그레이션은 AzCopy, Azure Storage 탐색기, Azure PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 수행할 수 있습니다.

> [!NOTE]
> ZRS Classic 계정은 사용이 중단될 예정이며 2021년 3월 31일에 마이그레이션해야 합니다. 사용이 중단되기 전에 ZRS 클래식 고객에게 자세한 내용이 전송될 예정입니다.

추가적인 질문은 [질문과 대답](#frequently-asked-questions) 섹션에 언급되어 있습니다. 

## <a name="geo-redundant-storage"></a>지역 중복 저장소
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>읽기 액세스 지역 중복 저장소
RA-GRS(읽기 액세스 지역 중복 저장소)는 저장소 계정의 가용성을 최대화합니다. RA-GRS는 두 지역의 지리적 복제와 더불어 보조 위치의 데이터에 대한 읽기 전용 액세스를 제공합니다.

보조 지역에서 데이터에 대한 읽기 전용 액세스를 활성화하면 저장소 계정의 기본 엔드포인트는 물론 보조 엔드포인트에서 데이터를 사용할 수 있습니다. 보조 끝점은 기본 끝점과 유사하지만 접미사 `–secondary` 가 계정 이름에 추가됩니다. 예를 들어, Blob service에 대한 기본 끝점이 `myaccount.blob.core.windows.net`인 경우, 보조 끝점은 `myaccount-secondary.blob.core.windows.net`입니다. 저장소 계정에 대한 액세스 키는 기본 및 보조 끝점에 대해 동일합니다.

RA-GRS를 사용할 때 유의해야 할 몇 가지 고려 사항:

* 응용 프로그램은 RA-GRS를 사용하는 경우 상호 작용하는 끝점을 관리해야 합니다.
* 비동기 복제에는 지연이 발생하기 때문에 주 지역에서 데이터를 복구할 수 없으면(예를 들어 지역에 재해가 발생하여) 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.
* Microsoft가 보조 지역에 장애 조치(Failover)를 시작하면 장애 조치(Failover)가 완료된 후 해당 데이터에 대한 읽기 및 쓰기 액세스를 갖게 됩니다. 자세한 내용은 [재해 복구 지침](../storage-disaster-recovery-guidance.md)을 참조하세요.
* RA-GRS는 높은 가용성을 위해 제공됩니다. 확장성 지침은 [성능 검사 목록](../storage-performance-checklist.md)을 검토하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. 내 저장소 계정의 지역 복제 유형을 변경하려면 어떻게 하나요?

   [Azure Portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md) 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용하면 저장소 계정의 지역 복제 유형을 변경할 수 있습니다.

   > [!NOTE]
   > ZRS 계정은 LRS 또는 GRS로 변환할 수 없습니다. 마찬가지로, 기존 LRS 또는 GRS 계정도 ZRS 계정으로 변환할 수 없습니다.
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2. 저장소 계정의 복제 유형을 변경하면 가동 중지 시간이 발생하나요?

   아니요, 저장소 계정의 복제 유형을 변경해도 가동 중지 시간이 발생하지 않습니다.

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3. 저장소 계정의 복제 유형을 변경하면 추가 비용이 발생하나요?

   예. 저장소 계정에 대해 LRS에서 GRS(또는 RA-GRS)로 변경하는 경우 기본 위치에서 보조 위치로 기존 데이터를 복사하는 송신과 관련하여 추가 비용이 발생합니다. 초기 데이터가 복사된 후에는 기본 위치에서 보조 위치로 데이터를 지역에서 복제하는 데 추가 송신 비용이 없습니다. 대역폭 요금에 대한 자세한 내용은 [Azure Storage 가격 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

   GRS에서 LRS로 변경하는 경우 추가 비용은 없지만 보조 위치에서 데이터가 삭제됩니다.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. RA-GRS가 어떻게 도움이 되나요?

   GRS 저장소는 주 지역에서 수백 마일 떨어져 있는 보조 지역에 데이터 복제를 제공합니다. GRS를 사용하는 경우 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해가 발생하더라도 데이터는 지속됩니다. RA-GRS 저장소에는 GRS 복제가 제공되며 보조 위치에서 데이터를 읽을 수 있는 기능이 추가됩니다. 고가용성을 위한 설계 방법에 대한 제한 사항은 [RA-GRS를 사용하여 항상 사용 가능한 응용 프로그램 설계](../storage-designing-ha-apps-with-ragrs.md)를 참조하세요.

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. 주 지역에서 보조 지역으로 데이터를 복제하는 데 시간이 얼마나 걸리는지 확인하는 방법이 있나요?

   RA-GRS 저장소를 사용하는 경우 저장소 계정의 마지막 동기화 시간을 확인할 수 있습니다. 마지막 동기화 시간은 GMT 날짜/시간 값입니다. 마지막 동기화 시간 이전의 모든 주 쓰기는 보조 위치에 성공적으로 기록되었으므로 보조 위치에서 읽을 수 있습니다. 마지막 동기화 시간 이후 주 쓰기는 읽기에 사용 또는 사용하지 못할 수 있습니다. [Azure Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md) 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 이 값을 쿼리할 수 있습니다.

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6. 주 지역에 가동 중단이 발생하면 보조 지역으로 어떻게 전환할 수 있나요?

   자세한 내용은 [Azure Storage 중단이 발생할 경우 수행할 작업](../storage-disaster-recovery-guidance.md)을 참조하세요.

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. GRS에서 RPO 및 RTO란?

   **RPO(복구 지점 목표):** GRS 및 RA-GRS에서는 저장소 서비스가 기본 위치에서 보조 위치로 데이터를 비동기식으로 지역에서 복제합니다. 주 지역에서 심각한 지역 재해가 발생하는 경우 Microsoft는 보조 영역으로 장애 조치(failover)를 수행합니다. 장애 조치(failover)가 발생하면 아직 지역 복제되지 않은 최근 변경 사항이 손실될 수 있습니다. 잠재적인 데이터 손실 시간(분)을 RPO라고 하며,이것은 데이터를 복구할 수 있는 특정 시점을 나타냅니다. Azure Storage의 RPO는 일반적으로 15분 미만이지만 현재 지리적 복제에 대한 소요 시간에 대한 SLA는 없습니다.

   **RTO(복구 시간 목표):** RTO는 장애 조치(failover)를 수행하고 저장소 계정을 다시 온라인 상태로 만드는 데 걸리는 시간에 대한 측정값입니다. 장애 조치(failover)를 수행하는 시간에는 다음 작업이 포함됩니다.

   * Microsoft에서 기본 위치에서 데이터를 복구할 수 있는지 또는 장애 조치(failover)가 필요한지 여부를 확인하는 데 필요한 시간.
   * 보조 위치를 가리키도록 기본 DNS 항목을 변경하여 저장소 계정의 장애 조치(failover)를 수행하는 시간.

   Microsoft는 데이터를 유지할 책임을 진지하게 수행하고 있습니다. 주 지역의 데이터를 복구할 기회가 있으면 장애 조치(failover)를 지연하고 데이터 복구에 집중합니다. 이 서비스의 향후 버전에서는 RTO를 직접 제어할 수 있도록 계정 수준에서 장애 조치(failover)를 트리거하는 것이 허용될 예정입니다.

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8. ZRS에서 지원하는 Azure Storage 개체는 무엇입니까? 
블록 Blob, 페이지 Blob(VM 디스크 백업 항목 제외), 테이블, 파일 및 큐. 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9. ZRS에 지역 복제도 포함되나요? 
현재 ZRS는 지역 복제를 지원하지 않습니다. 시나리오에서 재해 복구를 위해 지역간 복제가 필요한 경우 GRS 또는 RA-GRS 저장소 계정을 대신 사용합니다.   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10. 하나 이상의 ZRS 영역이 중단되면 어떻게 되나요? 
첫 번째 영역이 중단되면 ZRS는 해당 지역의 나머지 2개 영역에서 데이터의 복제본을 계속 작성합니다. 두 번째 영역이 중단되면 2개 이상의 영역을 다시 사용할 수 있을 때까지 일기 및 쓰기 액세스를 사용할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
* [RA-GRS 저장소를 사용하여 항상 사용 가능한 응용 프로그램 설계](../storage-designing-ha-apps-with-ragrs.md)
* [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)
* [Azure 저장소 계정 정보](../storage-create-storage-account.md)
* [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)
* [Microsoft Azure Storage 중복 옵션 및 읽기 액세스 지역 중복 저장소 ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP 문서 - Azure Storage: 일관성과 가용성이 뛰어난 클라우드 저장소 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
