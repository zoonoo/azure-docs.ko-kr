---
title: 관리 엔드포인트 IP 주소 검색
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 관리 엔드포인트 공용 IP 주소를 가져오고 해당 기본 제공 방화벽 보호를 확인하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 02c28282bbf35e2e69cb85149c3a7d1187c5c258
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110693997"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>관리 엔드포인트 IP 주소 확인 - Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance 가상 클러스터에는 Azure에서 관리 작업에 사용하는 관리 엔드포인트가 포함됩니다. 관리 엔드포인트는 네트워크 수준의 기본 제공 방화벽과 애플리케이션 수준의 상호 인증서 확인으로 보호됩니다. 관리 엔드포인트의 IP 주소를 확인할 수 있지만 이 엔드포인트에 액세스할 수는 없습니다.

관리 IP 주소를 확인하려면 SQL Managed Instance FQDN((`mi-name.zone_id.database.windows.net`))에서 [DNS 조회](/windows-server/administration/windows-commands/nslookup)를 수행합니다. 그러면 `trx.region-a.worker.vnet.database.windows.net`과 같은 DNS 항목이 반환됩니다. 그런 다음, ".vnet"을 제거하여 이 FQDN에서 DNS 조회를 수행할 수 있습니다. 그러면 관리 IP 주소가 반환됩니다. 

\<MI FQDN\>을 SQL Managed Instance의 DNS 항목(`mi-name.zone_id.database.windows.net`)으로 바꾸면 다음 PowerShell 코드에서 모든 작업을 자동으로 수행합니다.
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

SQL Managed Instance 및 연결에 대한 자세한 내용은 [Azure SQL Managed Instance 연결 아키텍처](connectivity-architecture-overview.md)를 참조하세요.
