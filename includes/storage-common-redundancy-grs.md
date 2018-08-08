---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399960"
---
GRS(지역 중복 저장소)는 기본 지역에서 수백 마일 떨어진 보조 지역에 데이터를 복제하여 지정된 1년에 걸쳐 99.99999999999999%(16개의 9) 이상의 개체 내구성을 제공하도록 설계되었습니다. 저장소 계정에서 GRS를 활성화하면, 전체 지역 가동 중단 또는 기본 지역을 복구할 수 없는 재해이더라도 데이터는 지속됩니다.

GRS를 선택하는 경우 선택할 수 있는 두 가지 관련 옵션은 다음과 같습니다.

* GRS는 데이터를 보조 지역의 다른 데이터 센터에 복제하지만, Microsoft에서 주 지역에서 보조 지역으로 장애 조치를 시작하는 경우에만 해당 데이터를 읽을 수 있습니다. 
* RA-GRS(읽기 액세스 지역 중복 저장소)는 GRS를 기반으로 합니다. RA-GRS는 데이터를 보조 지역의 다른 데이터 센터에 복제하고, 보조 지역에서 읽을 수 있는 옵션도 제공합니다. RA-GRS를 사용하면 Microsoft에서 주 지역에서 보조 지역으로 장애 조치를 시작하는지 여부에 관계없이 보조 지역에서 읽을 수 있습니다. 

GRS 또는 RA-GRS를 사용하도록 설정된 저장소 계정의 경우 모든 데이터는 먼저 LRS(로컬 중복 저장소)를 사용하여 복제됩니다. 업데이트는 먼저 기본 위치에 커밋되고 LRS를 사용하여 복제됩니다. 그런 다음, 업데이트는 GRS를 사용하여 보조 지역에 비동기적으로 복제됩니다. 데이터가 보조 위치에 기록되는 경우 LRS를 사용하여 해당 위치 내에도 복제됩니다. 

주 지역 및 보조 지역에서 모두 별도의 장애 도메인에서 복제본을 관리하고, 저장소 배율 단위 내에서 도메인을 업그레이드합니다. 저장소 배율 단위는 데이터 센터 내의 기본 복제 단위입니다. 이 수준의 복제는 LRS에서 제공합니다. 자세한 내용은 [LRS(로컬 중복 저장소): Azure Storage에 대한 저렴한 데이터 중복](../articles/storage/common/storage-redundancy-lrs.md)을 참조하세요.

사용할 복제 옵션을 결정할 때 다음 사항에 유의하세요.

* ZRS(영역 중복 저장소)는 동기 복제를 사용하여 고가용성을 제공하고, 일부 시나리오에서 GRS 또는 RA-GRS보다 더 적합할 수 있습니다. ZRS에 대한 자세한 내용은 [ZRS](../articles/storage/common/storage-redundancy-zrs.md)를 참조하세요.
* 비동기 복제에는 지연이 수반되므로 지역 재해가 발생할 경우 주 지역에서 데이터를 복구할 수 없으면 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.
* GRS를 사용하면 Microsoft에서 보조 지역으로 장애 조치를 시작하지 않는 한 복제본을 사용할 수 없습니다. Microsoft가 보조 지역에 장애 조치(Failover)를 시작하면 장애 조치(Failover)가 완료된 후 해당 데이터에 대한 읽기 및 쓰기 액세스를 갖게 됩니다. 자세한 내용은 [재해 복구 지침](../articles/storage/common/storage-disaster-recovery-guidance.md)을 참조하세요.
* 응용 프로그램이 보조 지역에서 읽어야 하는 경우 RA-GRS를 사용하도록 설정합니다.

## <a name="read-access-geo-redundant-storage"></a>읽기 액세스 지역 중복 저장소

RA-GRS(읽기 액세스 지역 중복 저장소)는 저장소 계정의 가용성을 최대화합니다. RA-GRS는 두 지역의 지리적 복제와 더불어 보조 위치의 데이터에 대한 읽기 전용 액세스를 제공합니다.

보조 지역에서 데이터에 대한 읽기 전용 액세스를 활성화하면 저장소 계정의 기본 끝점은 물론 보조 끝점에서 데이터를 사용할 수 있습니다. 보조 끝점은 기본 끝점과 유사하지만 접미사 `–secondary` 가 계정 이름에 추가됩니다. 예를 들어, Blob service에 대한 기본 끝점이 `myaccount.blob.core.windows.net`인 경우, 보조 끝점은 `myaccount-secondary.blob.core.windows.net`입니다. 저장소 계정에 대한 액세스 키는 기본 및 보조 끝점에 대해 동일합니다.

RA-GRS를 사용할 때 유의해야 할 몇 가지 고려 사항:

* 응용 프로그램은 RA-GRS를 사용하는 경우 상호 작용하는 끝점을 관리해야 합니다.
* 비동기 복제에는 지연이 발생하기 때문에 주 지역에서 데이터를 복구할 수 없으면(예를 들어 지역에 재해가 발생하여) 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.
* 저장소 계정의 마지막 동기화 시간을 확인할 수 있습니다. 마지막 동기화 시간은 GMT 날짜/시간 값입니다. 마지막 동기화 시간 이전의 모든 주 쓰기는 보조 위치에 성공적으로 기록되었으므로 보조 위치에서 읽을 수 있습니다. 마지막 동기화 시간 이후 주 쓰기는 읽기에 사용 또는 사용하지 못할 수 있습니다. [Azure Portal](https://portal.azure.com/), [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md) 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 이 값을 쿼리할 수 있습니다.
* Microsoft가 보조 지역에 장애 조치(Failover)를 시작하면 장애 조치(Failover)가 완료된 후 해당 데이터에 대한 읽기 및 쓰기 액세스를 갖게 됩니다. 자세한 내용은 [재해 복구 지침](../articles/storage/common/storage-disaster-recovery-guidance.md)을 참조하세요.
* 보조 지역으로 전환하는 방법에 대한 내용은 [Azure Storage 중단이 발생할 경우 수행할 작업](../articles/storage/common/storage-disaster-recovery-guidance.md)을 참조하세요.
* RA-GRS는 높은 가용성을 위해 제공됩니다. 확장성 지침은 [성능 검사 목록](../articles/storage/common/storage-performance-checklist.md)을 검토하세요.
* RA-GRS를 사용하여 고가용성을 설계하는 방법에 대한 제안 사항은 [RA-GRS 저장소를 사용하여 고가용성 응용 프로그램 설계](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md)를 참조하세요.

## <a name="what-is-the-rpo-and-rto-with-grs"></a>GRS에서 RPO 및 RTO란?
**RPO(복구 지점 목표):** GRS 및 RA-GRS에서 저장소 서비스는 주 위치에서 보조 위치로 데이터를 비동기적으로 지역에서 복제합니다. 주 지역에서 심각한 지역 재해가 발생하는 경우 Microsoft는 보조 영역으로 장애 조치(failover)를 수행합니다. 장애 조치(failover)가 발생하면 아직 지역 복제되지 않은 최근 변경 사항이 손실될 수 있습니다. 잠재적인 데이터 손실 시간(분)을 RPO라고 하며, 이것은 데이터를 복구할 수 있는 특정 시점을 나타냅니다. Azure Storage의 RPO는 일반적으로 15분 미만이지만 현재 지리적 복제에 대한 소요 시간에 대한 SLA는 없습니다.

**RTO(복구 시간 목표):** RTO는 장애 조치(failover)를 수행하고 저장소 계정을 다시 온라인 상태로 만드는 데 걸리는 시간에 대한 측정값입니다. 장애 조치(failover)를 수행하는 시간에는 다음 작업이 포함됩니다.

   * Microsoft가 기본 위치에서 데이터를 복구할 수 있는지 또는 장애 조치(failover)가 필요한지 여부를 확인하는 데 필요한 시간.
   * 보조 위치를 가리키도록 기본 DNS 항목을 변경하여 저장소 계정의 장애 조치(failover)를 수행하는 시간.

   Microsoft는 데이터를 유지할 책임을 진지하게 수행하고 있습니다. 주 지역의 데이터를 복구할 기회가 있으면 장애 조치(failover)를 지연하고 데이터 복구에 집중합니다. 이 서비스의 향후 버전에서는 RTO를 직접 제어할 수 있도록 계정 수준에서 장애 조치(failover)를 트리거하는 것이 허용될 예정입니다.

## <a name="paired-regions"></a>쌍을 이루는 지역 

저장소 계정을 만들 때 계정에 대한 기본 지역을 선택합니다. 쌍을 이루는 보조 지역은 주 지역에 따라 결정되며 변경할 수 없습니다. Azure에서 지원하는 지역에 대한 최신 정보는 [BCDR(무중단 업무 방식 및 재해 복구): Azure 쌍을 이루는 지역](../articles/best-practices-availability-paired-regions.md)을 참조하세요.
