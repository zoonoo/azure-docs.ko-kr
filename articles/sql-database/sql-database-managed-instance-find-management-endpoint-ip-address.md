---
title: 관리형 인스턴스 관리 엔드포인트 검색
description: Azure SQL Database Managed Instance 관리 엔드포인트 공용 IP 주소를 가져오고 해당 기본 제공 방화벽 보호를 확인하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825712"
---
# <a name="determine-the-management-endpoint-ip-address"></a>관리 엔드포인트 IP 주소 확인

Azure SQL Database Managed Instance 가상 클러스터에는 Microsoft에서 관리 작업에 사용하는 관리 엔드포인트가 포함됩니다. 관리 엔드포인트는 네트워크 수준의 기본 제공 방화벽과 애플리케이션 수준의 상호 인증서 확인으로 보호됩니다. 관리 엔드포인트의 IP 주소를 확인할 수 있지만 이 엔드포인트에 액세스할 수는 없습니다.

관리 IP 주소를 확인하려면 관리되는 인스턴스 FQDN에서 DNS `mi-name.zone_id.database.windows.net`조회를 수행합니다. 이렇게 하면 다음과 같은 `trx.region-a.worker.vnet.database.windows.net`DNS 항목이 반환됩니다. 그런 다음 ".vnet"을 제거하여 이 FQDN에서 DNS 조회를 수행할 수 있습니다. 그러면 관리 IP 주소가 반환됩니다. 

MI FQDN을 \<\> 관리 되는 인스턴스의 DNS 항목으로 대체 하는 경우이 `mi-name.zone_id.database.windows.net`PowerShell 당신을 위해 모든 작업을 수행 합니다.
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Managed Instance 및 연결에 대한 자세한 내용은 [Azure SQL Database Managed Instance 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)를 참조하세요.
