---
title: 보안 컨트롤
description: Azure SQL 데이터베이스 평가를 위한 보안 컨트롤검사 목록
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190686"
---
# <a name="security-controls-for-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 보안 제어

이 문서에서는 Azure SQL Database에 기본 제공 된 보안 컨트롤을 문서화 합니다.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL Database에는 [단일 데이터베이스와](sql-database-single-index.yml) [관리되는 인스턴스가 모두 포함됩니다.](sql-database-managed-instance.md) 다음 항목은 달리 언급된 경우를 제외하고 두 오퍼링모두에 적용됩니다.

## <a name="network"></a>네트워크

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| yes | 단일 [데이터베이스에만](sql-database-single-index.yml) 적용됩니다. |
| Azure 가상 네트워크 주입 지원| yes | [관리되는 인스턴스에만](sql-database-managed-instance.md) 적용됩니다. |
| 네트워크 격리 및 방화벽 지원| yes | 데이터베이스 수준과 서버 수준 모두에서 방화벽을 구축합니다. 네트워크 격리는 [관리되는](sql-database-managed-instance.md) 인스턴스용입니다. |
| 강제 터널링 지원| yes | [익스프레스루트](../expressroute/index.yml) VPN을 통해 [관리되는 인스턴스.](sql-database-managed-instance.md) |

## <a name="monitoring--logging"></a>모니터링 & 로깅

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 로그 분석 또는 응용 프로그램 인사이트와 같은 Azure 모니터링 지원| yes | Imperva의 SIEM 솔루션인 SecureSphere는 [SQL 감사를](sql-database-auditing.md)통해 Azure [이벤트 허브](../event-hubs/index.yml) 통합을 통해 지원됩니다. |
| 제어 평면 및 관리 평면 로깅 및 감사| yes | 일부 이벤트에만 예 |
| 데이터 평면 로깅 및 감사 | yes | [SQL 감사를](sql-database-auditing.md) 통해 |

## <a name="identity"></a>Identity

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 인증| yes | Azure AD(Azure Active Directory) |
| 권한 부여| yes | None |

## <a name="data-protection"></a>데이터 보호

| 보안 제어 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 측 암호화: Microsoft에서 관리하는 키 | yes | "사용 중 암호화"라고 하는 문서에 설명된 대로 [항상 암호화됩니다.](sql-database-always-encrypted.md) 서버 측 암호화는 [투명 한 데이터 암호화를](transparent-data-encryption-azure-sql.md)사용 합니다.|
| 전송 중 암호화:<ul><li>Azure 익스프레스루트 암호화</li><li>가상 네트워크의 암호화</li><li>가상 네트워크 간의 암호화</ul>| yes | HTTPS를 사용합니다. |
| CMK 또는 BYOK와 같은 암호화 키 처리| yes | 서비스 관리 및 고객 관리 키 처리가 모두 제공됩니다. 후자는 Azure [키 볼트를](../key-vault/index.yml)통해 제공됩니다. |
| Azure 데이터 서비스에서 제공하는 열 수준 암호화| yes | [항상 암호화를](sql-database-always-encrypted.md)통해 . |
| 암호화된 API 호출| yes | HTTPS/TLS 사용. |

## <a name="configuration-management"></a>구성 관리

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 구성 버전 관리와 같은 구성 관리 지원| 예  | None |

## <a name="additional-security-controls-for-sql-database"></a>SQL 데이터베이스에 대한 추가 보안 제어

| 보안 제어 | 예/아니요 | 메모|
|---|---|--|
| 예방: 취약점 평가 | yes | [SQL 취약성 평가 서비스를 참조하면 데이터베이스 취약점을 식별하는 데 도움이 됩니다.](sql-vulnerability-assessment.md) |
| 예방: 데이터 검색 및 분류  | yes | [Azure SQL 데이터베이스 및 SQL 데이터 웨어하우스 데이터 검색 & 분류를](sql-database-data-discovery-and-classification.md)참조하십시오. |
| 탐지: 위협 탐지 | yes | [Azure SQL 데이터베이스에 대한 고급 위협 보호를](sql-database-threat-detection-overview.md)참조하십시오. |

## <a name="next-steps"></a>다음 단계

- Azure 서비스 [에서 기본 제공 보안 컨트롤에](../security/fundamentals/security-controls.md)대해 자세히 알아봅니다.
