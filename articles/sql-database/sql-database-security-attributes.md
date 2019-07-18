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
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798681"
---
# <a name="security-attributes-for-azure-sql-database"></a>Azure SQL Database에 대 한 보안 특성

이 문서에서는 Azure SQL Database에 기본 제공 되는 일반적인 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL Database는 둘 다 [단일 데이터베이스](sql-database-single-index.yml) 하 고 [관리 되는 인스턴스](sql-database-managed-instance.md)합니다. 다음 항목을 제외 하 고 명시 된 경우 두 제품 모두에 적용 됩니다.

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 저장 데이터 암호화:<ul><li>서버 쪽 암호화</li><li>고객 관리 키로 서버 쪽 암호화</li><li>클라이언트 쪽 또는 상시 암호화와 같은 다른 암호화 기능</ul>| 예 | 이 문서에 설명 된 대로 "암호화-사용 중인"를 호출 [Always Encrypted](sql-database-always-encrypted.md)합니다. 다음을 사용 하 여 서버 쪽 암호화 [투명 한 데이터 암호화](transparent-data-encryption-azure-sql.md)합니다.|
| 전송 중 암호화:<ul><li>Azure ExpressRoute 암호화</li><li>가상 네트워크에서 암호화</li><li>가상 네트워크 간에 암호화</ul>| 예 | HTTPS를 사용합니다. |
| 암호화 키를 BYOK CMK 등 처리| 예 | 서비스 관리 및 고객 관리 키 처리 제공 됩니다. 후자를 통해 제공 됩니다 [Azure Key Vault](../key-vault/index.yml)합니다. |
| Azure 데이터 서비스에서 제공 하는 열 수준 암호화| 예 | 통해 [상시 암호화](sql-database-always-encrypted.md)합니다. |
| 암호화 된 API 호출| 예 | HTTPS/SSL을 사용합니다. |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 참고 |
|---|---|--|
| 서비스 끝점 지원| 예 | 적용 대상 [단일 데이터베이스](sql-database-single-index.yml) 만 합니다. |
| Azure Virtual Network 삽입 지원| 예 | 적용 대상 [관리 되는 인스턴스](sql-database-managed-instance.md) 만 합니다. |
| 네트워크 격리 및 방화벽 지원| 예 | 데이터베이스 수준 및 서버 수준 모두에서 방화벽입니다. 에 대 한 네트워크 격리가 [관리 되는 인스턴스](sql-database-managed-instance.md) 만 합니다. |
| 강제 터널링 지원| 예 | [관리 되는 인스턴스](sql-database-managed-instance.md) 를 통해를 [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| Azure Log Analytics 또는 Application Insights와 같은 지원, 모니터링| 예 | 통해 SecureSphere, Imperva에서 SIEM 솔루션도 지원 됩니다 [Azure Event Hubs](../event-hubs/index.yml) 통합을 통해 [SQL 감사](sql-database-auditing.md)합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 인증| 예 | Azure AD(Azure Active Directory) |
| Authorization| 예 | 없음 |

## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 제어 평면과 관리 평면 로깅 및 감사| 예 | 일부 이벤트에만 예 |
| 데이터 평면 로깅 및 감사 | 예 | 통해 [SQL 감사](sql-database-auditing.md) |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 구성의 버전 관리 등의 구성 관리 지원| 아니요  | 없음 |

## <a name="additional-security-attributes-for-sql-database"></a>SQL Database에 대 한 추가 보안 특성

| 보안 특성 | 예/아니요 | 참고|
|---|---|--|
| 취약성 평가 위한 예방 합니다. | 예 | 참조 [서비스를 사용 하면 데이터베이스 취약성을 식별 하는 SQL Vulnerability Assessment](sql-vulnerability-assessment.md)합니다. |
| 예방: 데이터 검색 및 분류  | 예 | 참조 [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)합니다. |
| 감지: 위협 감지 | 예 | 참조 [Advanced Threat Protection Azure SQL Database에 대 한](sql-database-threat-detection-overview.md)합니다. |
