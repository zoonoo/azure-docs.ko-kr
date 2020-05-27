---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78941077"
---
다음 표에서는 테이블 저장소의 용량, 확장성 및 성능 목표에 대해 설명 합니다.

| 리소스 | 대상 |
|----------|---------------|
| Azure Storage 계정에서 테이블의 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 테이블에 있는 파티션 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 파티션의 엔터티 수 | 스토리지 계정의 용량에 의해서만 제한됨 |
| 단일 테이블의 최대 크기 | 500TiB |
| 모든 속성 값을 포함 하는 단일 엔터티의 최대 크기 | 1MiB |
| 테이블 엔터티의 최대 속성 수 | 255 (3 개의 시스템 속성, **PartitionKey**, **Rowkey**및 **Timestamp**포함) |
| 엔터티에 있는 개별 속성의 최대 전체 크기 | 속성 유형에 따라 달라 집니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](/rest/api/storageservices/understanding-the-table-service-data-model)의 **속성 형식** 을 참조 하세요. |
| **PartitionKey** | 최대 1 KiB 크기의 문자열 |
| **RowKey** | 최대 1 KiB 크기의 문자열 |
| 엔터티 그룹 트랜잭션의 크기 | 트랜잭션은 최대 100 개의 엔터티를 포함할 수 있으며 페이로드 크기는 4 MiB 미만 이어야 합니다. 엔터티 그룹 트랜잭션은 엔터티에 대 한 업데이트를 한 번만 포함할 수 있습니다. |
| 테이블당 저장 된 액세스 정책의 최대 수 | 5 |
| 스토리지 계정당 최대 요청 속도 | 1 KiB 엔터티 크기를 가정 하는 2만 초당 트랜잭션 |
| 단일 테이블 파티션의 목표 처리량 (1 KiB) | 초당 최대 2000 엔터티 |