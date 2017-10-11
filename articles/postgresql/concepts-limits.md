---
title: "PostgreSQL용 Azure 데이터베이스의 제한 사항 | Microsoft Docs"
description: "PostgreSQL용 Azure 데이터베이스의 제한 사항을 설명합니다."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: article
ms.date: 06/01/2017
ms.openlocfilehash: 38988fc5c0dc05331ea078534cd1a05e9eca2493
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="limitations-in-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스의 제한 사항
PostgreSQL용 Azure 데이터베이스 서비스는 공개 미리 보기 상태입니다. 다음 섹션에서는 데이터베이스 서비스의 용량 및 기능 제한에 대해 설명합니다.

## <a name="service-tier-maximums"></a>서비스 계층 최대값
PostgreSQL용 Azure 데이터베이스에는 서버를 만들 때 선택할 수 있는 여러 서비스 계층이 있습니다. 자세한 내용은 [각 서비스 계층에서 사용할 수 있는 기능 이해](concepts-service-tiers.md)를 참조하세요.  

다음과 같이 서비스 미리 보기 동안 각 서비스 계층에는 연결, Compute 단위 및 저장소의 최대 수가 지정되어 있습니다. 

|                            |                   |
| :------------------------- | :---------------- |
| **최대 연결**        |                   |
| 기본 50 Compute 단위     | 50개 연결    |
| 기본 100 Compute 단위    | 100개 연결   |
| 표준 100 Compute 단위 | 200개 연결   |
| 표준 200 Compute 단위 | 300개 연결   |
| 표준 400 Compute 단위 | 400개 연결   |
| 표준 800 Compute 단위 | 500개 연결   |
| **최대 Compute 단위**      |                   |
| 기본 서비스 계층         | 100 Compute 단위 |
| 표준 서비스 계층      | 800 Compute 단위 |
| **최대 저장소**            |                   |
| 기본 서비스 계층         | 1TB              |
| 표준 서비스 계층      | 1TB              |

너무 많은 연결에 도달하면 다음 오류가 나타날 수 있습니다.
> 오류: 너무 많은 클라이언트가 이미 연결되어 있습니다.

## <a name="preview-functional-limitations"></a>미리 보기 기능 제한 사항
### <a name="scale-operations"></a>크기 조정 작업
1.  서비스 계층 간 서버의 동적 크기 조정은 현재 지원되지 않습니다. 즉, 기본 및 표준 서비스 계층 간 전환은 가능하지 않습니다.
2.  미리 생성된 서버에서 필요 시 저장소의 동적 증가는 현재 지원되지 않습니다.
3.  서버 저장소 크기를 줄이는 것은 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다.

### <a name="subscription-management"></a>구독 관리
- 구독 및 리소스 그룹에서 미리 생성된 서버를 동적으로 이동하는 것은 현재 지원되지 않습니다.

### <a name="point-in-time-restore"></a>특정 시점 복원
1.  다른 서비스 계층 및/또는 Compute 단위 및 저장소 크기로 복원할 수 없습니다.
2.  삭제된 서버를 복원하는 것은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [각 가격 책정 계층에서 사용할 수 있는 기능](concepts-service-tiers.md) 이해
- [지원되는 PostgreSQL Database 버전](concepts-supported-versions.md) 이해
- [Azure Portal을 사용하여 PostgreSQL용 Azure 데이터베이스에서 서버를 백업 및 복원하는 방법](howto-restore-server-portal.md) 검토
