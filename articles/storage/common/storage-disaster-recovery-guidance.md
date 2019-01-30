---
title: Azure Storage 중단이 발생할 경우 수행할 작업 | Microsoft Docs
description: Azure Storage 중단이 발생할 경우 수행할 작업
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 39a938d45c8f15c21b44bb5b04b1429fb4733b5a
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323271"
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Azure Storage 중단이 발생할 경우 수행할 작업
Microsoft에서는 서비스를 항상 사용할 수 있도록 하기 위해 많은 노력을 기울입니다. 경우에 따라 강제적으로 우리의 제어 영향을 벗어나 하나 이상의 지역에서 계획되지 않은 서비스 중단이 발생되는 경우가 있습니다. 이러한 드문 경우를 처리할 수 있도록 Azure Storage 서비스에 대해 다음과 같은 높은 수준의 지침을 제공합니다.

## <a name="how-to-prepare"></a>준비 방법
모든 고객은 자체적으로 재해 복구 계획을 준비하는 것이 중요합니다. 저장소 중단을 복구하려면 일반적으로 응용 프로그램을 작동 중인 상태로 다시 활성화하기 위해 운영 직원과 자동화된 프로시저가 모두 작업에 관여합니다. 자체 재해 복구 계획을 빌드하려면 아래 Azure 설명서를 참조하세요.

* [가용성 검사 목록](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Azure용 복원 응용 프로그램 디자인](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Azure Site Recovery 서비스](https://azure.microsoft.com/services/site-recovery/)
* [Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Azure Backup 서비스](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>검색 방법
Azure 서비스 상태를 확인하는 권장 방법은 [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)를 구독하는 것입니다.

## <a name="what-to-do-if-a-storage-outage-occurs"></a>저장소 중단이 발생할 경우 수행할 작업
하나 이상의 지역에서 하나 이상의 Storage 서비스를 일시적으로 사용할 수 없는 경우 두 가지 옵션을 고려할 수 있습니다. 데이터에 즉시 액세스하려는 경우에는 옵션 2를 고려합니다.

### <a name="option-1-wait-for-recovery"></a>옵션 1: 복구 대기
이 경우에 사용자의 조치가 필요하지 않습니다. Azure 서비스 가용성을 복원하기 위해 열심히 노력 중입니다. [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)에서 서비스 상태를 모니터링할 수 있습니다.

### <a name="option-2-copy-data-from-secondary"></a>옵션 2: 보조에서 데이터 복사
저장소 계정에 대해 [RA-GRS(읽기 액세스 지역 중복 저장소)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (권장)를 선택한 경우 보조 지역의 데이터에 대한 읽기 권한을 가집니다. [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) 및 [Azure 데이터 이동 라이브러리](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)와 같은 도구를 사용하여 보조 지역의 데이터를 영향을 받지 않은 지역의 다른 저장소 계정으로 복사할 수 있으며, 응용 프로그램이 읽기 및 쓰기 가용성 모두에 대한 해당 저장소 계정을 가리키도록 할 수 있습니다.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>저장소 장애 조치(failover)가 발생할 경우 예상 결과
[GRS(지역 중복 저장소)](storage-redundancy-grs.md) 또는 [RA-GRS(읽기 액세스 지역 중복 저장소)](storage-redundancy-grs.md#read-access-geo-redundant-storage)(권장)를 선택한 경우 Azure Storage가 두 지역(기본 및 보조)에 데이터를 지속적으로 유지합니다. Azure Storage는 두 지역에 여러 데이터 복제본을 계속 유지합니다.

지역 재해가 주 지역에 영향을 미칠 경우 먼저 해당 지역에서 서비스 복원을 시도하여 RTO 및 RPO의 최적 조합을 제공합니다. 재해 및 그 영향에 따라 드물지만 기본 지역을 복원하지 못할 수 있습니다. 이때 지역 장애 조치(failover)를 수행합니다. 지역 간 데이터 복제는 지연될 수 있는 비동기 프로세스이므로 보조 지역으로 미처 복제되지 않은 변경 내용은 손실될 수 있습니다.

저장소 지역 장애 조치(failover) 환경에 대한 참고 사항:

* 스토리지 지역 장애 조치(failover)는 Azure Storage 팀에 의해서만 트리거되며 고객이 수행할 작업은 없습니다. Azure Storage 팀이 동일한 지역에서 데이터를 복원하는 모든 옵션을 모두 사용한 경우 장애 조치가 트리거되어 RTO 및 RPO의 최적 조합을 제공합니다.
* Blob, 테이블, 큐 및 파일에 대한 기존 저장소 서비스 엔드포인트는 장애 조치(failover) 후에도 그대로 유지되며 주 지역에서 보조 지역으로 전환하려면 Microsoft 제공 DNS 항목을 업데이트해야 합니다. Microsoft는 지리적 장애 조치 프로세스의 일환으로 이 업데이트를 자동으로 수행합니다.
* 지역 장애 조치(failover) 전과 수행하는 동안에는 재해 영향으로 인해 저장소 계정에 대한 쓰기 권한이 없지만 저장소 계정이 RA-GRS로 구성된 경우에는 보조에서 여전히 읽을 수 있습니다.
* 지역 장애 조치(failover)가 완료되고 DNS 변경 사항이 전파되면 GRS 또는 RA-GRS가 있는 경우 저장소 계정에 대한 읽기 및 쓰기 액세스가 복원됩니다. 이전에 보조 엔드포인트였던 엔드포인트가 기본 엔드포인트가 됩니다. 
* 기본 위치의 상태를 확인하고 저장소 계정에 대한 최근 지역 장애 조치 시간을 쿼리할 수 있습니다. 자세한 내용은 [저장소 계정 - 속성 가져오기](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/getproperties)를 참조하세요.
* 장애 조치(failover) 후에 저장소 계정이 완벽하게 작동하게 되지만 지역 복제가 불가능한 독립 실행형 지역에 호스트되므로 "성능이 저하된" 상태가 됩니다. 이러한 위험을 완화하기 위해 원래 기본 지역을 복원한 다음 원래의 상태를 복원하기 위해 지역 장애 복구(failback)를 수행합니다. 원래 기본 지역을 복구할 수 없는 경우 다른 보조 지역을 할당합니다.

## <a name="best-practices-for-protecting-your-data"></a>데이터 보호에 대한 모범 사례
저장소 데이터를 정기적으로 백업하는 몇 가지 권장 접근 방식이 있습니다.

* VM 디스크- [Azure Backup 서비스](https://azure.microsoft.com/services/backup/) 를 사용하여 Azure 가상 머신에서 사용하는 VM 디스크를 백업합니다.
* 블록 Blob – [일시 삭제](../blobs/storage-blob-soft-delete.md)를 설정하여 개체 수준 삭제 및 덮어쓰기에 대해 보호하거나 [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) 또는 [Azure 데이터 이동 라이브러리](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)를 사용하여 다른 지역에 있는 다른 저장소 계정에 Blob을 복사합니다.
* 테이블 – [AzCopy](storage-use-azcopy.md) 를 사용하여 다른 지역에 있는 다른 저장소 계정으로 테이블 데이터를 내보냅니다.
* 파일 – [AzCopy](storage-use-azcopy.md) 또는 [Azure PowerShell](storage-powershell-guide-full.md)을 사용하여 다른 지역에 있는 다른 저장소 계정에 파일을 복사합니다.

RA-GRS 기능을 충분히 활용하는 응용 프로그램 생성에 대한 자세한 내용은 [RA-GRS Storage를 사용하여 항상 사용 가능한 응용 프로그램 설계](../storage-designing-ha-apps-with-ragrs.md)를 참조하세요.
