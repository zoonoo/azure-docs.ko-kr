---
title: 관리 끝점 IP 주소 검색
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 관리 끝점 공용 IP 주소를 가져오고 기본 제공 방화벽 보호를 확인 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 40a44fe46cf38c633380c4c353960cc4e11f2f3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708725"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>관리 끝점 IP 주소 확인-Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance 가상 클러스터에는 Azure에서 관리 작업에 사용 하는 관리 끝점이 포함 되어 있습니다. 관리 엔드포인트는 네트워크 수준의 기본 제공 방화벽과 애플리케이션 수준의 상호 인증서 확인으로 보호됩니다. 관리 엔드포인트의 IP 주소를 확인할 수 있지만 이 엔드포인트에 액세스할 수는 없습니다.

관리 IP 주소를 확인 하려면 SQL Managed Instance FQDN:에 대 한 [DNS 조회](/windows-server/administration/windows-commands/nslookup) 를 수행 `mi-name.zone_id.database.windows.net` 합니다. 그러면와 같은 DNS 항목이 반환 됩니다 `trx.region-a.worker.vnet.database.windows.net` . 그런 다음 "vnet"을 제거 하 여이 FQDN에서 DNS 조회를 수행할 수 있습니다. 그러면 관리 IP 주소가 반환 됩니다. 

이 PowerShell 코드는를 \<MI FQDN\> SQL Managed Instance::의 DNS 항목으로 바꾸면이 작업을 수행 합니다. `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

SQL Managed Instance 및 연결에 대 한 자세한 내용은 [AZURE sql Managed Instance 연결 아키텍처](connectivity-architecture-overview.md)를 참조 하세요.
