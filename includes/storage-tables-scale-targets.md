---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78941077"
---
다음 표에서는 테이블 스토리지의 용량, 확장성 및 성능 목표에 대해 설명합니다.

| 리소스 | 대상 |
|----------|---------------|
| Azure Storage 계정에서 테이블의 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 테이블에 있는 파티션 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 파티션의 엔터티 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 단일 테이블의 최대 크기 | 500TiB |
| 모든 속성 값을 비롯한 단일 엔터티의 최대 크기 | 1MiB |
| 테이블 엔터티의 최대 속성 수 | 255개(세 가지 시스템 속성 **PartitionKey**, **RowKey** 및 **Timestamp** 포함) |
| 엔터티에 있는 개별 속성의 최대 전체 크기 | 속성 유형에 따라 다릅니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](/rest/api/storageservices/understanding-the-table-service-data-model)의 **속성 유형**을 참조하세요. |
| **PartitionKey** | 최대 1KiB의 문자열 |
| **RowKey** | 최대 1KiB의 문자열 |
| 엔터티 그룹 트랜잭션의 크기 | 한 개 트랜잭션에는 최대 100개의 엔터티가 포함될 수 있고, 페이로드 크기는 4MiB 미만이어야 합니다. 엔터티 그룹 트랜잭션은 엔터티 업데이트를 한 번만 포함할 수 있습니다. |
| 테이블별로 저장된 최대 액세스 정책 수 | 5 |
| 스토리지 계정당 최대 요청 속도 | 초당 20,000개 트랜잭션(1KiB 엔터티 크기로 가정) |
| 단일 테이블 파티션의 목표 처리량(1KiB 엔터티) | 초당 최대 2,000개 엔터티 |