---
title: Azure Storage에서 지역 간 내구성을 위한 GRS(지역 중복 스토리지) | Microsoft Docs
description: GRS(지역 중복 저장소)는 매우 멀리 떨어진 두 지역 간에 데이터를 복제합니다. GRS는 데이터센터의 하드웨어 오류뿐 아니라 지역적인 재해로부터 보호합니다.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/20/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: 2dc409743ce94ecb73e351b839a5a2fb09eadab2
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512111"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>GRS(지역 중복 스토리지): Azure Storage의 지역 간 복제
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>읽기 액세스 지역 중복 저장소
RA-GRS(읽기 액세스 지역 중복 저장소)는 저장소 계정의 가용성을 최대화합니다. RA-GRS는 두 지역의 지리적 복제와 더불어 보조 위치의 데이터에 대한 읽기 전용 액세스를 제공합니다.

보조 지역에서 데이터에 대한 읽기 전용 액세스를 활성화하면 저장소 계정의 기본 엔드포인트는 물론 보조 엔드포인트에서 데이터를 사용할 수 있습니다. 보조 엔드포인트는 기본 엔드포인트와 유사하지만 접미사 `–secondary` 가 계정 이름에 추가됩니다. 예를 들어, Blob service에 대한 기본 엔드포인트가 `myaccount.blob.core.windows.net`인 경우, 보조 엔드포인트는 `myaccount-secondary.blob.core.windows.net`입니다. 저장소 계정에 대한 액세스 키는 기본 및 보조 엔드포인트에 대해 동일합니다.

RA-GRS를 사용할 때 유의해야 할 몇 가지 고려 사항:

* 애플리케이션은 RA-GRS를 사용하는 경우 상호 작용하는 엔드포인트를 관리해야 합니다.
* 비동기 복제는 지연을 발생시키므로 주 지역에서 데이터를 복구할 수 없는 경우 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.
* 저장소 계정의 마지막 동기화 시간을 확인할 수 있습니다. 마지막 동기화 시간은 GMT 날짜/시간 값입니다. 마지막 동기화 시간 이전의 모든 기본 쓰기는 보조 위치에 성공적으로 기록되었으므로 보조 위치에서 읽을 수 있습니다. 마지막 동기화 시간 이후 기본 쓰기는 읽기에 사용할 수도 있고 사용하지 못할 수 있습니다. [Azure Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md) 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 이 값을 쿼리할 수 있습니다.
* 보조 지역으로 GRS 또는 RA-GRS 계정의 계정 장애 조치(Failover)(미리 보기)를 시작하면 장애 조치가 완료된 후 해당 계정에 대한 쓰기 권한이 복원됩니다. 자세한 내용은 [재해 복구 및 스토리지 계정 장애 조치(Failover)(미리 보기)](storage-disaster-recovery-guidance.md)를 참조하세요.
* RA-GRS는 높은 가용성을 위해 제공됩니다. 확장성 지침은 [성능 검사 목록](storage-performance-checklist.md)을 검토하세요.
* RA-GRS를 사용하여 고가용성을 설계하는 방법에 대한 제안 사항은 [RA-GRS 저장소를 사용하여 고가용성 애플리케이션 설계](storage-designing-ha-apps-with-ragrs.md)를 참조하세요.

## <a name="what-is-the-rpo-and-rto-with-grs"></a>GRS에서 RPO 및 RTO란?

**RPO(복구 지점 목표):** GRS 및 RA-GRS에서는 스토리지 서비스가 기본 위치에서 보조 위치로 데이터를 비동기식으로 지역에서 복제합니다. 주 지역을 사용할 수 없게 되는 경우 보조 지역으로 계정 장애 조치(미리 보기)를 수행할 수 있습니다. 장애 조치를 시작하면 아직 지역 복제되지 않은 최근 변경 사항이 손실될 수 있습니다. 손실될 가능성이 있는 데이터의 시간(분)은 RPO라고 합니다. RPO는 데이터를 복구할 수 있는 시점을 나타냅니다. Azure Storage의 RPO는 일반적으로 15분 미만이지만 현재 지역 복제의 소요 시간에 대한 SLA는 없습니다.

**RTO(복구 시간 목표):** RTO는 장애 조치(failover)를 수행하고 스토리 계정을 다시 온라인 상태로 만드는 데 걸리는 시간을 측정한 값입니다. 장애 조치(failover)를 수행하는 시간에는 다음 작업이 포함됩니다.

   * 고객이 주 지역에서 보조 지역으로 스토리지 계정의 장애 조치를 시작할 때까지의 시간입니다.
   * 보조 위치를 가리키도록 기본 DNS 항목을 변경하여 장애 조치(failover)를 수행하는 데 필요한 시간입니다.

## <a name="paired-regions"></a>쌍을 이루는 지역 
저장소 계정을 만들 때 계정에 대한 기본 지역을 선택합니다. 쌍을 이루는 보조 지역은 주 지역에 따라 결정되며 변경할 수 없습니다. Azure에서 지원하는 지역에 대한 최신 정보는 [BCDR(비즈니스 연속성 및 재해 복구): Azure 쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
- [Azure Storage 복제](storage-redundancy.md)
- [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복성](storage-redundancy-lrs.md)
- [ZRS(영역 중복 스토리지): 고가용성 Azure Storage 애플리케이션](storage-redundancy-zrs.md)
