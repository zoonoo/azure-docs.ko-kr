---
title: 포함 파일
description: 포함 파일
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664592"
---
### <a name="sql-servers"></a>SQL Server

|  |  |
|---------|---------|
| [Azure Active Directory 관리자가 없을 경우 감사](../articles/azure-policy/scripts/audit-no-aad-admin.md) | SQL Server에 할당된 Azure Active Directory 관리자가 없을 때 감사합니다. |
| [서버 수준 위협 감지 설정 감사](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | 해당 정책이 지정된 상태로 설정되지 않은 경우 SQL 데이터베이스 보안 경고 정책을 감사합니다. 위협 감지가 사용 또는 사용 안 함으로 설정되었는지 여부를 나타내는 값을 지정 합니다.  |
| [SQL Server 감사 설정 감사](../articles/azure-policy/scripts/sql-server-audit.md) | 감사 설정 사용 여부에 따라 SQL Server를 감사합니다. |
| [Microsoft SQL Server 수준 감사 설정 감사](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | 해당 설정이 지정한 설정과 일치하지 않는 경우 Microsoft SQL Server 감사 설정을 감사합니다. 감사 설정이 사용 또는 사용 안 함으로 설정되었는지 여부를 나타내는 값을 지정 합니다. |
| [SQL Server 버전 12.0 필요](../articles/azure-policy/scripts/req-sql-12.md) | Microsoft SQL Server 버전 12.0을 사용해야 합니다.  |