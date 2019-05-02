---
title: Azure SQL Database 단일 데이터베이스란? | Microsoft Docs
description: Azure SQL Database의 단일 데이터베이스에 대해 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 4406789e3f9a10ff814aca0c2fd114de8a2abb69
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572661"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Azure SQL Database의 단일 데이터베이스란?

단일 데이터베이스 배포 옵션은 자체 리소스 세트를 사용하여 Azure SQL Database에 데이터베이스를 만들고 SQL Database 서버를 통해 관리됩니다. 단일 데이터베이스를 사용하면 각 데이터베이스가 서로 격리되고 이식 가능하며, [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 또는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md) 및 보장된 컴퓨팅 크기 내에서 자체의 고유 서비스 계층을 갖추게 됩니다.

> [!IMPORTANT]
> 단일 데이터베이스는 Azure SQL Database의 세 가지 배포 옵션 중 하나입니다. 나머지 두 가지 옵션은 [탄력적 풀](sql-database-elastic-pool.md)과 [관리되는 인스턴스](sql-database-managed-instance.md)입니다.
> [!NOTE]
> Azure SQL Database의 용어집은 [SQL Database 용어집](sql-database-glossary-terms.md)을 참조하세요.

## <a name="dynamic-scalability"></a>동적 확장성

가격/성능이 낮은 서비스 계층에서 저렴한 월간 비용으로 소규모 단일 데이터베이스에 첫 번째 앱을 빌드한 다음, 솔루션의 요구 사항에 맞게 언제든지 수동 또는 프로그래밍 방식으로 이 [서비스 계층을 가격/성능이 높은 서비스 계층으로 변경](sql-database-single-database-scale.md)할 수 있습니다. 앱이나 고객에게 가동 중지 시간 없이 성능을 조정할 수 있습니다. 동적 확장성을 통해 데이터베이스는 급변하는 리소스 요구 사항에 투명하게 대응할 수 있으며, 필요할 때 필요한 리소스에 대해서만 비용을 지불할 수 있습니다.

## <a name="single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀

리소스를 공유하기 위해 단일 데이터베이스를 [탄력적 풀](sql-database-elastic-pool.md)의 내부 또는 외부로 이동할 수 있습니다. 특히 사용 패턴이 비교적 예측 가능한 경우 많은 비즈니스 및 애플리케이션에서 단일 데이터베이스를 만들고 필요에 따라 충분히 성능을 확장하거나 축소할 수 있습니다. 하지만 사용 패턴을 예측할 수 없는 경우 비용과 비즈니스 모델을 관리하기 어려워질 수 있습니다. 탄력적 풀은 이 문제를 해결하도록 설계되었습니다. 개념은 간단합니다. 개별 데이터베이스가 아니라 풀에 성능 리소스를 할당하고 단일 데이터베이스 성능이 아니라 풀의 전체 성능 리소스에 대해 요금을 지불합니다.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고

성능 등급과 결합된 [기본 제공 성능 모니터링](sql-database-performance.md) 및 [경고](sql-database-insights-alerts-portal.md) 도구를 사용합니다. 이 도구를 사용하면 현재 또는 프로젝트의 성능 요구에 기반하여 확장 또는 축소의 영향을 신속하게 평가할 수 있습니다. 또한 SQL Database는 쉬운 모니터링을 위해 [메트릭 및 진단 로그를 내보낼](sql-database-metrics-diag-logging.md) 수 있습니다.

## <a name="availability-capabilities"></a>가용성 기능

단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스 모든 다양 한 가용성 특성을 제공합니다. 자세한 내용은 [가용성 특성](sql-database-technical-overview.md#availability-capabilities)을 참조하세요.

## <a name="transact-sql-differences"></a>Transact-SQL 차이점

애플리케이션에서 사용하는 대부분의 Transact-SQL 기능은 Microsoft SQL Server와 Azure SQL Database 모두에서 완전하게 지원됩니다. 예를 들어, 데이터 형식, 연산자, 문자열, 산술, 논리 및 커서 함수 같은 핵심 SQL 구성 요소는 SQL Server 및 SQL Database에서 동일하게 작동합니다. 그러나 DDL(데이터 정의 언어) 및 DML(데이터 조작 언어) 요소에는 몇 가지 T-SQL 차이점이 있으며 그 결과 T-SQL 문 및 쿼리가 부분적으로만 지원됩니다(이 문서의 뒷부분에서 설명).
또한 Azure SQL Database는 마스터 데이터베이스 및 운영 체제에 대한 종속성으로부터 기능을 격리하도록 설계되어 전혀 지원되지 않는 기능과 구문도 있습니다. 따라서 대부분의 서버 수준 작업이 SQL Database에 적합하지 않습니다. T-SQL 문과 옵션은 서버 수준 옵션 또는 운영 체제 구성 요소를 구성하거나 파일 시스템 구성을 지정하는 경우에 사용할 수 없습니다. 이러한 기능이 필요한 경우 SQL Database 또는 다른 Azure 기능이나 서비스에서 다른 방법으로 사용할 수 있는 적합한 대안이 있는 경우가 많습니다.

자세한 내용은 [SQL Database로의 마이그레이션 중 Transact-SQL 차이점 해결](sql-database-transact-sql-information.md)을 참조하세요.

## <a name="security"></a>보안

SQL Database는 다양한 [기본 제공 보안 및 규정 준수 기능](sql-database-security-overview.md)을 제공하여 애플리케이션이 다양한 보안 및 규정 준수 요구 사항을 충족할 수 있도록 도와줍니다.

> [!IMPORTANT]
> Azure SQL Database (모든 배포 옵션)을 여러 규정 준수 표준에 대해 인증 받았습니다. 자세한 내용은 참조는 [Microsoft Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 있는 SQL Database 규정 준수 인증의 최신 목록을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스를 신속하게 시작할 수 있도록 [단일 데이터베이스 빠른 시작 guide.md](sql-database-single-database-quickstart-guide.md)를 시작합니다.
- SQL Server 데이터베이스를 Azure로 마이그레이션하는 방법에 대한 자세한 내용은 [Azure SQL Database로 마이그레이션](sql-database-single-database-migrate.md)을 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.