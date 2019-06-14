---
title: Azure SQL Database에 대 한 보안 특성
description: Azure SQL Database를 평가 하는 것에 대 한 보안 특성에 대 한 검사 목록
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c495456a5a3295abe5460ff6b5586e41fab2d95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001031"
---
# <a name="security-attributes-for-azure-sql-database"></a>Azure SQL Database에 대 한 보안 특성

이 문서에서는 Azure SQL Database에 내장 된 일반 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Azure SQL Database는 둘 다 [단일 데이터베이스](sql-database-single-index.yml) 하 고 [관리 되는 인스턴스](sql-database-managed-instance.md)합니다. 아래 항목을 별도로 명시 된 경우 두 제품 모두에 제외 하 고 적용 됩니다.

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>기타 암호화 기능(예: 클라이언트 쪽, 상시 암호화 등)</ul>| 예 | 라고 "암호화 사용", 문서에 설명 된 대로 [Always Encrypted](sql-database-always-encrypted.md)합니다. 서비스 쪽 암호화를 사용 하 여 [투명 한 데이터 암호화](transparent-data-encryption-azure-sql.md) (TDE).|
| 전송 중 암호화:<ul><li>ExpressRoute 암호화</li><li>VNet 암호화</li><li>VNet 간 암호화</ul>| 예 | HTTPS를 사용합니다. |
| 암호화 키 처리 (예: CMK, BYOK)| 예 | 서비스 관리 및 고객 관리 키 처리에 제공 됩니다 (통해 후자 [Azure Key Vault](../key-vault/index.yml)합니다. |
| 열 수준 암호화 (Azure Data Services)| 예 | 통해 [상시 암호화](sql-database-always-encrypted.md)합니다. |
| API 호출 암호화| 예 | HTTPS/SSL을 사용합니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 끝점 지원| 예 | 적용 대상 [단일 데이터베이스](sql-database-single-index.yml) 만 합니다. |
| VNet 삽입 지원| 예 | 적용 대상 [관리 되는 인스턴스](sql-database-managed-instance.md) 만 합니다. |
| 네트워크 격리 및 방화벽 지원| 예 | 방화벽에서 두 데이터베이스 및 서버 수준의; 네트워크에 대 한 격리 [관리 되는 인스턴스](sql-database-managed-instance.md) 만 |
| 강제 터널링 지원| 예 | [관리 되는 인스턴스](sql-database-managed-instance.md) 를 통해 [Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Imperva (SecureSphere)에서 타사 SIEM 솔루션도 지원 되며 통해 [Azure Event Hubs](../event-hubs/index.yml) 통합을 통해 [SQL audit](sql-database-auditing.md)합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | Azure Active Directory. |
| 권한 부여| 예 |  |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 제어 및 관리 평면 로깅 및 감사| 예 | 일부 이벤트에만 예입니다. |
| 데이터 평면 로깅 및 감사 | 예 | 통해 [SQL audit](sql-database-auditing.md)합니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 아닙니다.  | | 

## <a name="additional-security-attributes-for-sql-database"></a>SQL Database에 대 한 추가 보안 특성

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 취약성 평가 위한 예방 합니다. | 예 | 참조 [서비스를 사용 하면 데이터베이스 취약성을 식별 하는 SQL Vulnerability Assessment](sql-vulnerability-assessment.md)합니다. |
| 예방: 데이터 검색 및 분류  | 예 | 참조 [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)합니다. |
| 감지: 위협 감지 | 예 | 참조 [Advanced Threat Protection Azure SQL Database에 대 한](sql-database-threat-detection-overview.md)합니다. |
