---
title: 포함 파일
description: 포함 파일
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 70128735aef64d273c63236a13b0ae28edb6077d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003774"
---
### <a name="sql-databases"></a>SQL Database

|  |  |
|---------|---------|
| [허용되는 SQL DB SKU](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Microsoft Azure SQL Database는 승인된 SKU를 사용합니다. 허용되는 SKU ID 배열 또는 허용되는 SKU 이름 배열을 지정합니다. |
| [DB 수준 위협 감지 설정 감사](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | 해당 정책이 지정된 상태로 설정되지 않은 경우 SQL 데이터베이스 보안 경고 정책을 감사합니다. 위협 감지가 사용 또는 사용 안 함으로 설정되었는지 여부를 나타내는 값을 지정 합니다.  |
| [SQL Database 암호화 감사](../articles/governance/policy/samples/sql-database-encryption-audit.md) | SQL 데이터베이스가 투명 데이터 암호화가 사용되지 않는지 감사합니다. |
| [Microsoft SQL DB 수준 감사 설정 감사](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | 해당 설정이 지정한 설정과 일치하지 않는 경우 Microsoft Azure SQL Database 감사 설정을 감사합니다. 감사 설정이 사용 또는 사용 안 함으로 설정되었는지 여부를 나타내는 값을 지정 합니다.  |
| [투명한 데이터 암호화 상태 감사](../articles/governance/policy/samples/audit-trans-data-enc-status.md) | Microsoft Azure SQL Database 투명한 데이터 암호화를 사용하도록 설정되지 않은 경우 감사합니다.  |