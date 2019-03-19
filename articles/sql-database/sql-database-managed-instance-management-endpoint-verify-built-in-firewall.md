---
title: Azure SQL Database Managed Instance 기본 제공 방화벽 검색 | Microsoft Docs
description: Azure SQL Database Managed Instance에서 기본 제공 방화벽 보호를 확인하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: f059ac4149a385a12b440db0ad6394a343a1f810
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761925"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Managed Instance 기본 제공 방화벽 확인

Managed Instance [필수 인바운드 보안 규칙](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules)을 사용하려면 Managed Instance를 보호하는 NSG(네트워크 보안 그룹)의 **모든 소스**에서 관리 포트 9000, 9003, 1438, 1440, 1452가 열려 있어야 합니다. 이러한 포트는 NSG 수준에서 열리지만 기본 제공 방화벽으로는 네트워크 수준에서 보호됩니다.

## <a name="verify-firewall"></a>방화벽 확인

이러한 포트를 확인하려면 보안 스캐너 도구를 사용하여 이러한 포트를 테스트합니다. 다음 스크린샷은 이러한 도구 중 하나를 사용하는 방법을 보여 줍니다.

![기본 제공 방화벽 확인](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>다음 단계

Managed Instance 및 연결에 대한 자세한 내용은 [Azure SQL Database Managed Instance 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)를 참조하세요.