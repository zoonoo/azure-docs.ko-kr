---
title: Azure Storage 확장성 및 성능 목표
description: 용량, 요청 속도, 인바운드 및 아웃바운드 대역폭 등 표준 Azure Storage 계정의 확장성 및 성능 목표를 알아보세요.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/08/2018
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 2f00b01bb07aafca847897f0c31d24d4add7cdbf
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328880"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>표준 스토리지 계정의 Azure Storage 확장성 및 성능 목표

이 문서에서는 Azure Storage 계정의 확장성 및 성능 목표에 대해 자세히 설명합니다. 여기에 나열된 확장성 및 성능 목표는 최첨단 목표이지만 달성할 수 있습니다. 모든 경우, 계정 사용량에 따라 달성된 요청 속도 및 대역폭은 저장된 개채의 크기 및 애플리케이션이 수행한 작업 형태에 따릅니다. 

해당 성능이 요구 사항을 충족시키는지 여부를 확인 하려면 서비스를 반드시 테스트해야 합니다. 가능하면 트래픽 속도가 갑자기 증가하지 않고 파티션 간의 트래픽이 적절하게 분산되도록 보장합니다.

애플리케이션이 파티션의 작업 처리 가능한 제한에 도달하면 Azure Storage는 오류 코드 503(서버 작업 중) 또는 오류 코드 500(작업 시간 제한) 응답을 반송하기 시작합니다. 503 오류가 발생하는 경우 재시도를 위해 지수 백오프 정책을 사용하도록 애플리케이션을 수정하는 것이 좋습니다. 지수 백오프는 파티션에 대한 부하를 감소시키고 해당 파티션에 트래픽의 급증을 완화할 수 있습니다.

## <a name="standard-storage-account-scale-limits"></a>표준 스토리지 계정 크기 조정 제한
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-storage-account-scale-limits"></a>Premium Storage 계정 크기 조정 제한
[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>스토리지 리소스 공급자 크기 조정 제한

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 크기 조정 목표
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure Files 크기 조정 목표
Azure File 및 Azure 파일 동기화의 크기 조정 및 성능 목표에 대한 자세한 내용은 [Azure Files 확장성 및 성능 목표](../files/storage-files-scale-targets.md)를 참조하세요.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure 파일 동기화의 크기 조정 목표
Azure 파일 동기화는 무제한 사용을 목적으로 설계되었으나 무제한 사용이 가능하지 않은 경우도 있습니다. 아래 표에는 Microsoft의 테스트 경계와 하드 한도인 목표가 표시되어 있습니다.

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue 저장소 크기 조정 목표
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table Storage 크기 조정 목표
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>참고 항목
* [저장소 가격 정보](https://azure.microsoft.com/pricing/details/storage/)
* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-subscription-service-limits.md)
* [Azure Storage 복제](../storage-redundancy.md)
* [Microsoft Azure Storage 성능 및 확장성 검사 목록](../storage-performance-checklist.md)

