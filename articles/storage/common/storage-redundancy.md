---
title: Azure Storage에서 데이터 중복성 | Microsoft Docs
description: Microsoft Azure Storage 계정의 데이터는 내구성 및 고가용성을 위해 복제됩니다. 중복성 옵션에는 LRS (로컬 중복 저장소), ZRS (영역 중복 저장소), GRS (지역 중복 저장소), 읽기 액세스 지역 중복 저장소 (RA-GRS), 지역 중복 저장소 (GZRS) (미리 보기) 및 읽기 액세스 지역 중복이 포함 됩니다. 저장소 (RA-GZRS) (미리 보기).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: cdddf284028c6fc9749082e1991e5b9dee4acf99
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75560371"
---
# <a name="azure-storage-redundancy"></a>Azure Storage 중복성

Microsoft Azure Storage 계정 데이터는 항상 내구성 및 고가용성을 위해 복제됩니다. Azure Storage는 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되었거나 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 데이터를 복사합니다. 동일한 데이터 센터, 동일한 지역 내의 영역 데이터 센터 또는 지리적으로 분리된 지역 간에 데이터를 복제하도록 선택할 수 있습니다.

복제를 사용하면 스토리지 계정은 오류 상황에서도 [Storage용 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/)를 충족하게 됩니다. Azure Storage의 내구성 및 가용성 보장에 대한 정보는 SLA를 확인하세요.

Azure Storage는 CRCs (순환 중복 검사)를 사용 하 여 저장 된 데이터의 무결성을 정기적으로 확인 합니다. 데이터 손상이 감지 되 면 중복 데이터를 사용 하 여 복구 됩니다. 또한 Azure Storage는 데이터를 저장 하거나 검색할 때 데이터 패킷의 손상을 검색 하기 위해 모든 네트워크 트래픽에 대 한 체크섬을 계산 합니다.

## <a name="choosing-a-redundancy-option"></a>중복 옵션 선택

스토리지 계정을 만들 때 다음 중복성 옵션 중 하나를 선택할 수 있습니다.

[!INCLUDE [azure-storage-redundancy](../../../includes/azure-storage-redundancy.md)]

다음 표는 각 복제 전략에서 특정 유형의 이벤트(또는 영향이 비슷한 이벤트)에 제공하는 내구성 및 가용성 범위를 간략하게 보여줍니다.

| 시나리오                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (미리 보기)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| 데이터 센터 내에서 노드를 사용할 수 없음                                                                 | 예                             | 예                              | 예                                  | 예                                  |
| 전체 데이터 센터(영역 또는 비영역)를 사용할 수 없게 됨                                           | 아닙니다.                              | 예                              | 예                                  | 예                                  |
| 전체 지역 가동 중단                                                                                     | 아닙니다.                              | 아닙니다.                               | 예                                  | 예                                  |
| 지역 전체가 사용할 수 없는 우 데이터(원격 지역 복제 지역의)에 대한 읽기 권한 | 아닙니다.                              | 아닙니다.                               | 예 (RA-GRS 사용)                                   | 예 (RA-GZRS 사용)                                 |
| 지정 된 1 년 동안 개체의 \_\_ 내 구성을 제공 하도록 설계 되었습니다.<sup>1</sup>                                          | 최소 99.999999999% | 최소 99.9999999999% | 최소 99.99999999999999% | 최소 99.99999999999999% |
| 지원 되는 저장소 계정 유형<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| 읽기 요청에 대 한 가용성 SLA<sup>1</sup>  | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | GRS에 대 한 최소 99.9% (쿨 액세스 계층의 경우 99%)<br /><br />RA-GRS에 대 한 최소 99.99% (쿨 액세스 계층의 경우 99.9%) | GZRS에 대 한 최소 99.9% (쿨 액세스 계층의 경우 99%)<br /><br />RA-GZRS에 대 한 최소 99.99% (쿨 액세스 계층의 경우 99.9%) |
| 쓰기 요청에 대 한 가용성 SLA<sup>1</sup>  | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) |

<sup>1</sup> 내구성 및 가용성을 보장 하는 Azure Storage에 대 한 자세한 내용은 [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/)를 참조 하십시오.   

<sup>2</sup> 저장소 계정 유형에 대 한 자세한 내용은 [저장소 계정 개요](storage-account-overview.md)를 참조 하세요.

블록 blob, 추가 blob, 페이지 blob, 큐, 테이블 및 파일을 포함 하 여 모든 유형의 저장소 계정에 대 한 모든 데이터를 복제 합니다.

각 중복 옵션에 대한 가격 책정 정보는 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

> [!NOTE]
> Azure Premium 디스크 저장소는 현재 LRS (로컬 중복 저장소)만 지원 합니다. Azure Premium Block Blob Storage는 특정 지역의 LRS (로컬 redudant storage) 및 ZRS (zone redudant storage)를 지원 합니다.

## <a name="changing-replication-strategy"></a>복제 전략 변경

[Azure Portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)또는 [Azure Storage 클라이언트 라이브러리](https://docs.microsoft.com/azure/index#pivot=sdkstools)중 하나를 사용 하 여 저장소 계정의 복제 전략을 변경할 수 있습니다. 스토리지 계정의 복제 유형을 변경해도 가동 중지 시간이 발생하지 않습니다.

> [!NOTE]
> 현재 Azure Portal 또는 Azure Storage 클라이언트 라이브러리를 사용 하 여 계정을 ZRS, GZRS 또는 RA-GZRS로 변환할 수 없습니다. 계정을 ZRS로 마이그레이션하려면, 자세한 내용은 [고가용성 Azure Storage 응용 프로그램을 빌드하기 위한 ZRS (영역 중복 저장소](storage-redundancy-zrs.md) )를 참조 하세요. GZRS 또는 RA-GZRS를 마이그레이션하려면 자세한 내용은 고가용성 [및 최대 내구성 (미리 보기)을 위한 지역 영역 중복 저장소](storage-redundancy-zrs.md) 를 참조 하세요.

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>내 계정의 복제 전략을 변경하는 데 드는 비용이 있나요?

변환 경로에 따라 다릅니다. 최소한의 비용이 많이 드는 Azure Storage 중복성 제공 LRS, ZRS, GRS, RA-GRS, GZRS, RA-GZRS의 순서로 정렬 됩니다. 예를 들어 LRS *에서* 다른 유형의 복제로 이동 하는 경우 보다 정교한 중복성 수준으로 이동 하기 때문에 추가 요금이 발생 합니다. GRS 또는 RA-GRS *로* 마이그레이션하면 사용자의 데이터 (주 지역)가 원격 보조 지역으로 복제 되므로 송신 대역폭 요금이 부과 됩니다. 초기 설치 시에는 일회성 비용이 청구 됩니다. 데이터가 복사 된 후에는 더 이상 마이그레이션 요금이 발생 하지 않습니다. 기존 데이터에 대 한 새 또는 업데이트 복제에 대해서만 요금이 청구 됩니다. 대역폭 요금에 대한 자세한 내용은 [Azure Storage 가격 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

저장소 계정을 GRS에서 LRS로 마이그레이션하는 경우 추가 비용은 없지만 복제 된 데이터는 보조 위치에서 삭제 됩니다.

GRS에서 GRS 또는 LRS로 저장소 계정을 마이그레이션하는 경우 해당 계정은 변환 된 날짜를 초과 하 여 30 일이 경과 하면 RA-GRS로 청구 됩니다.

## <a name="see-also"></a>참고 항목

- [저장소 계정 개요](storage-account-overview.md)
- [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복](storage-redundancy-lrs.md)
- [ZRS(영역 중복 스토리지): 고가용성 Azure Storage 애플리케이션](storage-redundancy-zrs.md)
- [GRS(지역 중복 스토리지): Azure Storage에 대한 지역 간 복제](storage-redundancy-grs.md)
- [고가용성 및 최대 내구성에 대 한 GZRS (지역 영역 중복 저장소) (미리 보기)](storage-redundancy-gzrs.md)
- [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)
- [RA-GRS 스토리지를 사용하여 항상 사용 가능한 애플리케이션 설계](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage 중복 옵션 및 읽기 액세스 지역 중복 저장소](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP 문서 - Azure Storage: 일관성과 가용성이 뛰어난 클라우드 스토리지 서비스](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
