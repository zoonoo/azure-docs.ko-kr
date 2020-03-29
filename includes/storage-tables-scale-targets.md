---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78941077"
---
다음 표에서는 Table 저장소에 대한 용량, 확장성 및 성능 목표를 설명합니다.

| 리소스 | 대상 |
|----------|---------------|
| Azure Storage 계정에서 테이블의 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 테이블에 있는 파티션 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 파티션의 엔터티 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 단일 테이블의 최대 크기 | 500TiB |
| 모든 속성 값을 포함한 단일 엔터티의 최대 크기 | 1MiB |
| 테이블 엔터티의 최대 속성 수 | 255 (세 가지 시스템 속성, **파티션키,** **RowKey**및 **타임스탬프**포함) |
| 엔터티의 개별 속성의 최대 총 크기 | 속성 유형에 따라 다릅니다. 자세한 내용은 테이블 서비스 [데이터 모델 이해의](/rest/api/storageservices/understanding-the-table-service-data-model) **속성 유형을** 참조하십시오. |
| **PartitionKey** | 최대 1 KiB 사이즈의 끈 |
| **RowKey** | 최대 1 KiB 사이즈의 끈 |
| 엔터티 그룹 트랜잭션의 크기 | 트랜잭션에는 최소 100개의 엔터티가 포함될 수 있으며 페이로드의 크기는 4MiB 미만이어야 합니다. 엔터티 그룹 트랜잭션에는 엔터티에 대한 업데이트가 한 번만 포함될 수 있습니다. |
| 테이블당 최대 저장된 액세스 정책 수 | 5 |
| 스토리지 계정당 최대 요청 속도 | 초당 20,000개의 트랜잭션으로 1KiB 엔터티 크기를 가정합니다. |
| 단일 테이블 파티션에 대한 목표 처리량(KiB 엔터티 1개) | 초당 최대 2,000개의 엔터티 |