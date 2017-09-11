---
title: "Azure Storage에서 데이터 복제 | Microsoft Docs"
description: "Microsoft Azure Storage 계정의 데이터는 내구성 및 고가용성을 위해 복제됩니다. 복제 옵션은 (LRS) 로컬 중복 저장소(LRS), 영역 중복 저장소 (ZRS), 지역 중복 저장소 (GRS) 및 읽기 액세스 지역 중복 저장소 (RA-GRS)에 포함 됩니다."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: c35f53c5c80a67cb33b457e9e3235d0e745536cf
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="azure-storage-replication"></a>Azure 저장소 복제

Microsoft Azure Storage 계정 데이터는 항상 내구성 및 고가용성을 위해 복제됩니다. 복제는 선택한 복제 옵션에 따라 동일한 데이터 센터 내 또는 보조 데이터 센터에 데이터를 복사합니다. 일시적인 하드웨어 오류가 발생할 경우에 대비하여 복제를 통해 데이터를 보호하고 응용 프로그램 가동 시간을 유지합니다. 보조 데이터 센터에 데이터를 복제하면 기본 위치에서 발생하는 치명적인 오류로부터 보호됩니다.

복제를 사용하면 저장소 계정은 오류 상황에서도 [저장소용 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/)를 충족하게 됩니다. Azure Storage의 내구성 및 가용성 보장에 대한 정보는 SLA를 확인하세요.

저장소 계정을 만들 때 다음 복제 옵션 중 하나를 선택할 수 있습니다.

* [LRS(로컬 중복 저장소)](#locally-redundant-storage)
* [ZRS(영역 중복 저장소)](#zone-redundant-storage)
* [GRS(지역 중복 저장소)](#geo-redundant-storage)
* [RA-GRS(읽기 액세스 지역 중복 저장소)](#read-access-geo-redundant-storage)

RA-GRS(읽기 액세스 지역 중복 저장소)는 저장소 계정을 만드는 경우 기본 옵션입니다.

다음 표에서 후속 섹션에서는 각 유형의 복제를 더 자세히 다루고 LRS, ZRS, GRS, RA-GRS 간의 차이점에 대해 간략하게 설명합니다.

| 복제 전략 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 데이터가 여러 데이터 센터에 걸쳐 복제됩니다. |아니요 |예 |예 |예 |
| 기본 위치와 보조 위치에서 데이터를 읽을 수 있습니다. |아니요 |아니요 |아니요 |예 |
| 별도 노드에서 유지 관리되는 데이터 복사본 수입니다. |3 |3 |6 |6 |

다른 이중화 옵션에 대한 가격 정보를 보려면 [Azure 저장소 가격](https://azure.microsoft.com/pricing/details/storage/) 을 참조하세요.

> [!NOTE]
> 프리미엄 저장소는 LRS(로컬 중복 저장소)만 지원합니다. 프리미엄 저장소에 대한 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터에 대한 고성능 저장소](../storage-premium-storage.md)를 참조하세요.
>

## <a name="locally-redundant-storage"></a>로컬 중복 저장소
LRS(로컬 중복 저장소)는 저장소 계정을 만든 지역의 데이터 센터에 호스트된 저장소 배율 단위 내에서 데이터를 세 번 복제합니다. 쓰기 요청은 3개의 복제본 모두에 쓰여진 경우에만 성공적으로 반환합니다. 이러한 3개의 복제본은 하나의 저장소 배율 단위 내에서 각기 별도 오류 도메인 및 업그레이드 도메인에 상주합니다.

저장소 배율 단위는 저장소 노드의 랙 컬렉션입니다. 오류 도메인(FD)은 실패의 물리적 단위를 나타내는 노드 그룹이며 동일한 물리적 랙에 속하는 노드로 간주될 수 있습니다. UD(업그레이드 도메인)는 서비스 업그레이드(롤아웃) 중 함께 업그레이드되는 노드 그룹입니다. 세 개의 복제본이 하나의 저장소 배율 단위 내 UD 및 FD에 걸쳐 분포되어 하드웨어 오류가 단일 랙에 영향을 주는 경우나 롤아웃 도중 노드가 업그레이드되는 경우에도 데이터를 사용할 수 있도록 보장합니다.

LRS는 가장 저렴한 옵션이며 다른 옵션에 비해 최소한의 영속성을 제공합니다. 데이터 센터 수준 재해(화재, 홍수 등) 발생 시 세 개의 복제본은 손실되거나 복구할 수 없을 수 있습니다. 이러한 위험을 완화하기 위해 GRS(지역 중복 저장소)가 대부분의 응용 프로그램에 권장됩니다.

로컬 중복 저장소는 특정 시나리오에서 적합할 수 있습니다.

* Azure Storage 복제 옵션 중 가장 높은 최대 대역폭을 제공합니다.
* 응용 프로그램이 쉽게 재구성될 수 있는 데이터를 저장하는 경우 LRS를 선택할 수 있습니다.
* 일부 응용 프로그램은 데이터 관리 요구 사항으로 인해 단일 국가 내에서만 데이터를 복제하도록 제한됩니다. 쌍을 이루는 지역은 다른 국가일 수 있습니다. 쌍을 이루는 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

## <a name="zone-redundant-storage"></a>영역 중복 저장소
ZRS(영역 중복 저장소)는 LRS와 유사하게 세 개의 복제본을 저장함으로써 LRS보다 높은 영속성을 제공하는 것 외에도 데이터를 하나 또는 두 개 지역 내 데이터 센터에 걸쳐 비동기적으로 복제합니다. ZRS에 저장된 데이터는 기본 데이터 센터를 사용할 수 없거나 복구할 수 없는 경우에도 지속성을 가집니다.
ZRS를 사용하려는 고객은 다음을 알고 있어야 합니다.

* ZRS는 범용 저장소 계정의 블록 Blob에 대해서만 사용할 수 있으며, 저장소 서비스 버전 2014-02-14 이상에서만 지원됩니다.
* 비동기 복제는 지연이 발생하므로 지역 재해가 발생한 경우 주 지역에서 데이터를 복구할 수 없다면 아직 보조 지역으로 복제되지 않은 변경 내용은 손실될 수 있습니다.
* 복제본은 Microsoft가 보조 지역에 장애 조치를 시작하기 전에는 사용할 수 없습니다.
* ZRS 계정은 나중에 LRS 또는 GRS로 변환할 수 없습니다. 마찬가지로, 기존 LRS 또는 GRS 계정도 ZRS 계정으로 변환할 수 없습니다.
* ZRS 계정에는 메트릭 또는 로깅 기능이 없습니다.

## <a name="geo-redundant-storage"></a>지역 중복 저장소
지역 중복 저장소(GRS)는 주 지역에서 수백 마일 떨어져 있는 보조 지역에 데이터를 복제합니다. 저장소 계정에서 GRS를 활성화하면, 전체 지역 가동 중단 또는 기본 지역을 복구할 수 없는 재해이더라도 데이터는 지속됩니다.

GRS를 활성화 하는 저장소 계정의 경우, 먼저 업데이트가 기본 지역으로 커미트되며, 여기서 세 번 복제됩니다. 그런 다음 업데이트는 보조 지역에 비동기식으로 복제되며 이 또한 세 번 복제됩니다.

GRS를 사용하면 기본 지역 및 보조 지역은 LRS에 설명된 바와 같이 별도의 장애 도메인에서 복제본을 관리하고 저장소 배율 단위 내에서 도메인을 업그레이드합니다.

고려 사항:

* 비동기 복제는 지연이 발생하므로 지역 재해가 발생한 경우 주 지역에서 데이터를 복구할 수 없다면 아직 보조 지역으로 복제되지 않은 변경 내용은 손실될 수 있습니다.
* 복제본은 Microsoft가 보조 지역에 장애 조치를 시작하기 전에는 사용할 수 없습니다. Microsoft가 보조 지역에 장애 조치(Failover)를 시작하면 장애 조치(Failover)가 완료된 후 해당 데이터에 대한 읽기 및 쓰기 액세스를 갖게 됩니다. 자세한 내용은 [재해 복구 지침](../storage-disaster-recovery-guidance.md)을 참조하세요. 
* 응용 프로그램이 보조 지역에서 읽으려는 경우 사용자가 RA-GRS를 사용하도록 설정해야 합니다.

저장소 계정을 만들 때 계정에 대한 기본 지역을 선택합니다. 보조 지역은 기본 지역에 따라 결정되며 변경할 수 없습니다. 다음 표에서는 기본 및 보조 지역 쌍을 보여 줍니다.

| 보조 | 주 |
| --- | --- |
| 미국 중북부 |미국 중남부 |
| 미국 중남부 |미국 중북부 |
| 미국 동부 |미국 서부 |
| 미국 서부 |미국 동부 |
| 미국 동부 2 |미국 중부 |
| 미국 중부 |미국 동부 2 |
| 북유럽 |서유럽 |
| 서유럽 |북유럽 |
| 동남아시아 |동아시아 |
| 동아시아 |동남아시아 |
| 중국 동부 |중국 북부 |
| 중국 북부 |중국 동부 |
| 일본 동부 |일본 서부 |
| 일본 서부 |일본 동부 |
| 브라질 남부 |미국 중남부 |
| 오스트레일리아 동부 |오스트레일리아 남동부 |
| 오스트레일리아 남동부 |오스트레일리아 동부 |
| 인도 남부 |인도 중부 |
| 인도 중부 |인도 남부 |
| 인도 서부 |인도 남부 |
| 미국 정부 아이오와 |미국 정부 버지니아 |
| 미국 정부 버지니아 |미국 정부 텍사스 |
| 미국 정부 텍사스 |미국 정부 애리조나 |
| 미국 정부 애리조나 |미국 정부 텍사스 |
| 캐나다 중부 |캐나다 동부 |
| 캐나다 동부 |캐나다 중부 |
| 영국 서부 |영국 남부 |
| 영국 남부 |영국 서부 |
| 독일 중부 |독일 북동부 |
| 독일 북동부 |독일 중부 |
| 미국 서부 2 |미국 중서부 |
| 미국 중서부 |미국 서부 2 |

Azure에서 지원되는 지역에 대한 최신 정보는 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

>[!NOTE]  
> 미국 버지니아 주 정부 보조 지역은 미국 텍사스 주 정부입니다. 이전에는 미국 버지니아 주 정부가 보조 지역으로 미국 아이오와 주 정부를 활용했습니다. 보조 지역으로 미국 아이오와 주 정부를 활용하는 저장소 계정은 보조 지역으로 미국 텍사스 주 정부를 활용하는 계정으로 마이그레이션됩니다. 
> 
> 

## <a name="read-access-geo-redundant-storage"></a>읽기 액세스 지역 중복 저장소
읽기 액세스 지역 중복 저장소(RA-GRS)는 GRS에서 제공한 두 지역에 걸쳐 복제하는 것 외에도 보조 위치에서 데이터에 대한 읽기 전용 액세스를 제공하여 저장소 계정의 가용성을 최대화합니다.

보조 지역에서 데이터를 읽기 전용 액세스를 설정할 때 데이터는 저장소 계정에 대한 기본 끝점 뿐만 아니라 보조 끝점에서 사용할 수 있습니다. 보조 끝점은 기본 끝점과 유사하지만 접미사 `–secondary` 가 계정 이름에 추가됩니다. 예를 들어, Blob 서비스에 대한 기본 끝점이 `myaccount.blob.core.windows.net`인 경우, 보조 끝점은 `myaccount-secondary.blob.core.windows.net`입니다. 저장소 계정에 대한 액세스 키는 기본 및 보조 끝점에 대해 동일합니다.

고려 사항:

* 응용 프로그램은 RA-GRS를 사용하는 경우 상호 작용하는 끝점을 관리해야 합니다.
* 비동기 복제는 지연이 발생하므로 지역 재해가 발생한 경우 주 지역에서 데이터를 복구할 수 없다면 아직 보조 지역으로 복제되지 않은 변경 내용은 손실될 수 있습니다.
* Microsoft가 보조 지역에 장애 조치(Failover)를 시작하면 장애 조치(Failover)가 완료된 후 해당 데이터에 대한 읽기 및 쓰기 액세스를 갖게 됩니다. 자세한 내용은 [재해 복구 지침](../storage-disaster-recovery-guidance.md)을 참조하세요. 
* RA-GRS는 높은 가용성을 위해 제공됩니다. 확장성 지침은 [성능 검사 목록](../storage-performance-checklist.md)을 검토하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. 내 저장소 계정의 지역에서 복제 유형을 변경하려면 어떻게 하나요?

   [Azure Portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md) 또는 여러 저장소 클라이언트 라이브러리 중 하나를 프로그래밍 방식으로 사용하여 저장소 계정의 지역에서 복제 유형을 LRS, GRS 및 RA-GRS 간에 변경할 수 있습니다. ZRS 계정은 LRS 또는 GRS로 변환할 수 없습니다. 마찬가지로, 기존 LRS 또는 GRS 계정도 ZRS 계정으로 변환할 수 없습니다.

<a id="changedowntime"></a>
#### <a name="2-will-there-be-any-down-time-if-i-change-the-replication-type-of-my-storage-account"></a>2. 저장소 계정의 복제 유형을 변경하면 중단 시간이 발생하나요?

   아니요, 중단 시간이 발생하지 않습니다.

<a id="changecost"></a>
#### <a name="3-will-there-be-any-additional-cost-if-i-change-the-replication-type-of-my-storage-account"></a>3. 저장소 계정의 복제 유형을 변경하면 추가 비용이 발생하나요?

   예. 저장소 계정에 대해 LRS에서 GRS(또는 RA-GRS)로 변경하는 경우 기본 위치에서 보조 위치로 기존 데이터를 복사하는 송신과 관련하여 추가 비용이 발생합니다. 초기 데이터가 복사된 후에는 주 위치에서 보조 위치로 데이터를 지역에서 복제하는 데 추가 송신 비용이 발생하지 않습니다. 대역폭 요금에 대한 자세한 내용은 [Azure Storage 가격 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)에서 확인할 수 있습니다. GRS에서 LRS로 변경하는 경우 추가 비용은 없지만 보조 위치에서 데이터가 삭제됩니다.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. RA-GRS가 어떻게 도움이 되나요?
   
   GRS 저장소는 주 지역에서 수백 마일 떨어져 있는 보조 지역에 데이터 복제를 제공합니다. 이 경우 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해가 발생하더라도 데이터는 지속됩니다. RA-GRS 저장소에는 이외에도 보조 위치에서 데이터를 읽을 수 있는 기능이 추가됩니다. 이 기능을 활용하는 방법에 대한 몇 가지 아이디어는 [RA-GRS 저장소를 사용하여 항상 사용 가능한 응용 프로그램 설계](../storage-designing-ha-apps-with-ragrs.md)를 참조하세요. 

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-for-me-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. 주 지역에서 보조 지역으로 데이터를 복제하는 데 시간이 얼마나 걸리는지 확인하는 방법이 있나요?
   
   RA-GRS 저장소를 사용하는 경우 저장소 계정의 마지막 동기화 시간을 확인할 수 있습니다. 마지막 동기화 시간은 GMT 날짜/시간 값입니다. 마지막 동기화 시간 이전의 모든 주 쓰기는 보조 위치에 성공적으로 기록되었으므로 보조 위치에서 읽을 수 있습니다. 마지막 동기화 시간 이후 주 쓰기는 읽기에 사용 또는 사용하지 못할 수 있습니다. [Azure Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md)을 사용하거나 REST API를 사용하여 프로그래밍 방식으로 또는 저장소 클라이언트 라이브러리 중 하나를 사용하여 이 값을 쿼리할 수 있습니다. 

<a id="outage"></a>
#### <a name="6-how-can-i-switch-to-the-secondary-region-if-there-is-an-outage-in-the-primary-region"></a>6. 주 지역에 가동 중단이 있는 경우 보조 지역으로 어떻게 전환할 수 있나요?
   
   자세한 내용은 [Azure Storage 중단이 발생할 경우 수행할 작업](../storage-disaster-recovery-guidance.md)을 참조하세요.

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. GRS에서 RPO 및 RTO란?
   
   RPO(복구 지점 목표): GRS 및 RA-GRS에서는 저장소 서비스가 주 위치에서 보조 위치로 데이터를 비동기식으로 지역에서 복제합니다. 중대한 지역 재해 발생이 있고 장애 조치(failover)를 수행해야 하는 경우 아직 지역에서 복제되지 않은 최근 델타 변경 내용이 손실될 수 있습니다. 잠재적인 데이터 손실 시간(분)을 RPO(즉, 데이터를 복구할 수 있는 특정 시점)라고 합니다. 현재 지역에서 복제를 수행하는 데 소요되는 시간에 대한 SLA는 없지만 일반적으로 RPO는 15분 미만입니다.

   RTO(복구 시간 목표): 장애 조치(failover)를 수행하고 장애 조치를 수행해야 하는 경우 저장소 계정을 다시 온라인으로 가져오는 데 소요되는 시간에 대한 측정값입니다. 장애 조치를 수행하는 시간에는 다음이 포함됩니다.
    * 기본 위치에서 데이터를 복구할 수 있는지 또는 장애 조치를 수행해야 하는지를 조사하고 결정하는 데 걸리는 시간입니다.
    * 보조 위치를 가리키도록 주 DNS 항목을 변경하여 계정을 장애 조치합니다.

   Microsoft는 사용자의 데이터를 매우 중요하게 보존할 책임이 있으며, 따라서 데이터를 복구할 기회가 있는 경우 장애 조치를 보류하고 기본 위치에서 데이터를 복구하는 데 주력할 것입니다. 향후에는 계정 수준에서 장애 조치(failover)를 트리거할 수 있는 API를 제공하여 RTO를 직접 제어하도록 할 계획입니다(아직은 제공되지 않음).
   
## <a name="next-steps"></a>다음 단계
* [RA-GRS 저장소를 사용하여 항상 사용 가능한 응용 프로그램 설계](../storage-designing-ha-apps-with-ragrs.md)
* [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)
* [Azure 저장소 계정 정보](../storage-create-storage-account.md)
* [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)
* [Microsoft Azure Storage 중복 옵션 및 읽기 액세스 지역 중복 저장소 ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP 문서 - Azure Storage: 일관성과 가용성이 뛰어난 클라우드 저장소 서비스](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)


