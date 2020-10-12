---
title: 보안 컨트롤
description: Azure SQL Database 평가를 위한 보안 컨트롤의 검사 목록
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442091"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance에 대 한 보안 제어
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance에 기본 제공 되는 보안 컨트롤을 설명 합니다.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>네트워크

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 | [SQL Database](../index.yml) 에만 적용 됩니다. |
| Azure Virtual Network 주입 지원| 예 | [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 에만 적용 됩니다. |
| 네트워크 격리 및 방화벽 지원| 예 | 데이터베이스 수준 및 서버 수준 모두에서의 방화벽. 네트워크 격리는 [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 에만 해당 됩니다. |
| 강제 터널링 지원| 예 | [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) [express](../expressroute/../index.yml) 경로 VPN을 통해 |

## <a name="monitoring--logging"></a>모니터링 및 로깅

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| Log Analytics 또는 Application Insights와 같은 Azure 모니터링 지원| 예 | Imperva의 SIEM 솔루션인 SecureSphere는 [SQL 감사](../../azure-sql/database/auditing-overview.md)를 통해 [Azure Event Hubs](../event-hubs/../index.yml) 통합을 통해서도 지원 됩니다. |
| 제어 평면 및 관리-평면 로깅 및 감사| 예 | 일부 이벤트에 대해서만 예 |
| 데이터 평면 로깅 및 감사 | 예 | [SQL 감사](../../azure-sql/database/auditing-overview.md) 를 통해 |

## <a name="identity"></a>ID

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 인증| 예 | Azure AD(Azure Active Directory) |
| 권한 부여| 예 | 없음 |

## <a name="data-protection"></a>데이터 보호

| 보안 컨트롤 | 예/아니요 | 메모 |
|---|---|--|
| 미사용 서버 쪽 암호화: Microsoft 관리형 키 | 예 | [Always Encrypted](always-encrypted-certificate-store-configure.md)문서에 설명 된 대로 "사용 중인 암호화" 라고 합니다. 서버 쪽 암호화는 [투명 한 데이터 암호화](transparent-data-encryption-tde-overview.md)를 사용 합니다.|
| 전송 중 암호화:<ul><li>Azure Express 경로 암호화</li><li>가상 네트워크의 암호화</li><li>가상 네트워크 간 암호화</ul>| 예 | HTTPS를 사용합니다. |
| 암호화-키 처리 (예: CMK 또는 BYOK)| 예 | 서비스 관리 및 고객 관리 키 처리가 모두 제공 됩니다. 후자는 [Azure Key Vault](../key-vault/../index.yml)를 통해 제공 됩니다. |
| Azure 데이터 서비스에서 제공 하는 열 수준 암호화| 예 | [Always Encrypted](always-encrypted-certificate-store-configure.md). |
| 암호화 된 API 호출| 예 | HTTPS/TLS를 사용 합니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (예: 구성의 버전 관리)| 예  | None |

## <a name="additional-security-controls-for-sql-database"></a>SQL Database에 대 한 추가 보안 제어

| 보안 컨트롤 | 예/아니요 | 메모|
|---|---|--|
| 예방: 취약성 평가 | 예 | [SQL 취약성 평가 서비스를 참조 하 여 데이터베이스 취약성을 식별할 수 있습니다](sql-vulnerability-assessment.md). |
| 예방: 데이터 검색 및 분류  | 예 | [Azure SQL Database 및 Azure Synapse Analytics 데이터 검색 & 분류를](data-discovery-and-classification-overview.md)참조 하세요. |
| 검색: 위협 검색 | 예 | [Azure SQL Database에 대 한 Advanced Threat Protection을](threat-detection-overview.md)참조 하세요. |

## <a name="next-steps"></a>다음 단계

- [Azure 서비스의 기본 제공 보안 컨트롤](../../security/fundamentals/security-controls.md)에 대해 자세히 알아봅니다.
