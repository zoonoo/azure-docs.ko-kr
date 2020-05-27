---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457443"
---
| 리소스 | 대상 |
|----------|---------------|
| 단일 테이블의 최대 크기 | 500TiB |
| 테이블 엔터티의 최대 크기 | 1MiB |
| 테이블 엔터티의 최대 속성 수 | 255는 세 가지 시스템 속성을 포함 합니다. PartitionKey, RowKey 및 Timestamp |
| 엔터티에 있는 속성 값의 최대 총 크기 | 1MiB |
| 엔터티에 있는 개별 속성의 최대 전체 크기 | 속성 유형에 따라 달라 집니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](/rest/api/storageservices/understanding-the-table-service-data-model)의 **속성 형식** 을 참조 하세요. |
| 테이블당 저장 된 액세스 정책의 최대 수 | 5 |
| 스토리지 계정당 최대 요청 속도 | 1 KiB 엔터티 크기를 가정 하는 2만 초당 트랜잭션 |
| 단일 테이블 파티션의 목표 처리량 (1 KiB) | 초당 최대 2000 엔터티 |