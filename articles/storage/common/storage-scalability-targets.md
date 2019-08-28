---
title: 확장성 및 성능 목표 Azure Storage-저장소 계정
description: Azure storage 계정에 대 한 용량, 요청 속도, 인바운드 및 아웃 바운드 대역폭을 비롯 한 확장성 및 성능 목표에 대해 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 046c2308d5cef2df7e12b6185fc24b8df4f821dc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326965"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>저장소 계정에 대 한 확장성 및 성능 목표 Azure Storage

이 문서에서는 Azure Storage 계정의 확장성 및 성능 목표에 대해 자세히 설명합니다. 여기에 나열된 확장성 및 성능 목표는 최첨단 목표이지만 달성할 수 있습니다. 모든 경우, 계정 사용량에 따라 달성된 요청 속도 및 대역폭은 스토리지된 개채의 크기 및 애플리케이션이 수행한 작업 형태에 따릅니다.

해당 성능이 요구 사항을 충족시키는지 여부를 확인 하려면 서비스를 반드시 테스트해야 합니다. 가능하면 트래픽 속도가 갑자기 증가하지 않고 파티션 간의 트래픽이 적절하게 분산되도록 보장합니다.

애플리케이션이 파티션의 작업 처리 가능한 제한에 도달하면 Azure Storage는 오류 코드 503(서버 작업 중) 또는 오류 코드 500(작업 시간 제한) 응답을 반송하기 시작합니다. 503 오류가 발생하는 경우 재시도를 위해 지수 백오프 정책을 사용하도록 애플리케이션을 수정하는 것이 좋습니다. 지수 백오프는 파티션에 대한 부하를 감소시키고 해당 파티션에 트래픽의 급증을 완화할 수 있습니다.

## <a name="storage-account-scale-limits"></a>저장소 계정 크기 제한

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Premium performance storage 계정 크기 제한

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>스토리지 리소스 공급자 크기 조정 제한

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 크기 조정 목표

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files 크기 조정 목표

Azure File 및 Azure 파일 동기화의 크기 조정 및 성능 목표에 대한 자세한 내용은 [Azure Files 확장성 및 성능 목표](../files/storage-files-scale-targets.md)를 참조하세요.

> [!IMPORTANT]
> 저장소 계정 제한은 모든 공유에 적용 됩니다. 저장소 계정에 대 한 공유를 하나만 있는 경우에만 저장소 계정에 대 한 최대 크기를 확장할 수 있습니다.
>
> 5 TiB 보다 큰 표준 파일 공유는 미리 보기 상태 이며 특정 제한 사항이 있습니다.
> 이러한 큰 파일 공유 크기의 미리 보기에 대 한 제한 사항 목록과 등록 지침은 Azure Files 계획 가이드의 [표준 파일 공유](../files/storage-files-planning.md#standard-file-shares) 섹션을 참조 하세요.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>프리미엄 파일 크기 조정 대상

프리미엄 파일에는 저장소 계정, 공유 및 파일의 세 가지 범주를 고려해 야 합니다.

예: 단일 공유에서 10만 IOPS를 달성할 수 있으며 단일 파일이 5000 IOPS까지 확장 될 수 있습니다. 예를 들어 한 공유에 세 개의 파일이 있는 경우 해당 공유에서 얻을 수 있는 최대 IOPs는 15000입니다.

#### <a name="premium-file-share-limits"></a>프리미엄 파일 공유 제한

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure 파일 동기화의 크기 조정 목표

Azure 파일 동기화는 무제한 사용을 목적으로 설계되었으나 무제한 사용이 가능하지 않은 경우도 있습니다. 아래 표에는 Microsoft의 테스트 경계와 하드 한도인 목표가 표시되어 있습니다.

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue storage 크기 조정 목표

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table Storage 크기 조정 목표

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>참고자료

- [스토리지 가격 정보](https://azure.microsoft.com/pricing/details/storage/)
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-subscription-service-limits.md)
- [Azure Storage 복제](../storage-redundancy.md)
- [Microsoft Azure Storage 성능 및 확장성 검사 목록](../storage-performance-checklist.md)
