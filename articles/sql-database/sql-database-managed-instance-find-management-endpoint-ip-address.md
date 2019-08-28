---
title: Azure SQL Database Managed Instance 관리 엔드포인트 검색 | Microsoft Docs
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
ms.openlocfilehash: a0d47c61bf84cfb22c767fd09b3feed74f55b7fc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567492"
---
# <a name="determine-the-management-endpoint-ip-address"></a>관리 엔드포인트 IP 주소 확인

Azure SQL Database Managed Instance 가상 클러스터에는 Microsoft에서 관리 작업에 사용하는 관리 엔드포인트가 포함됩니다. 관리 엔드포인트는 네트워크 수준의 기본 제공 방화벽과 애플리케이션 수준의 상호 인증서 확인으로 보호됩니다. 관리 엔드포인트의 IP 주소를 확인할 수 있지만 이 엔드포인트에 액세스할 수는 없습니다.

관리 IP 주소를 확인 하려면 관리 되는 인스턴스 FQDN ( `mi-name.zone_id.database.windows.net`)에서 DNS 조회를 수행 합니다. 그러면와 같은 `trx.region-a.worker.vnet.database.windows.net`DNS 항목이 반환 됩니다. 그런 다음 "vnet"을 제거 하 여이 FQDN에서 DNS 조회를 수행할 수 있습니다. 그러면 관리 IP 주소가 반환 됩니다. 

이 PowerShell은 MI FQDN \<\> 을 관리 되는 인스턴스의 DNS 항목으로 대체 하는 경우 모든 작업을 수행 합니다 `mi-name.zone_id.database.windows.net`.:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Managed Instance 및 연결에 대한 자세한 내용은 [Azure SQL Database Managed Instance 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)를 참조하세요.
