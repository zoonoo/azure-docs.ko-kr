---
title: Azure SQL Database Managed Instance 사용자 지정 DNS | Microsoft Docs
description: 이 항목에서는 Azure SQL Database Managed Instance를 사용하는 사용자 지정 DNS의 구성 옵션에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 07/17/2019
ms.openlocfilehash: 674c5d48dad5d3cfd138853d7ea38ae4a216c93d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309874"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 대한 사용자 지정 DNS 구성

Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 내에서 Azure SQL Database Managed Instance를 배포해야 합니다. 프라이빗 호스트 이름이 Managed Instance에서 확인되어야 하는 몇 가지 시나리오(예: db 메일, 클라우드 또는 하이브리드 환경의 다른 SQL 인스턴스에 연결된 서버)가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. 

Managed Instance는 내부 작동에 동일한 DNS를 사용 하므로 공용 도메인 이름을 확인할 수 있도록 사용자 지정 DNS 서버를 구성 해야 합니다.

   > [!IMPORTANT]
   > 메일 서버, SQL Server 및 기타 서비스가 프라이빗 DNS 영역에 있는 경우에도 항상 FQDN(정규화된 도메인 이름)을 사용합니다. 예를 들어 단순 `smtp`가 제대로 확인되지 않으므로 메일 서버에 `smtp.contoso.com`을 사용합니다.

   > [!IMPORTANT]
   > 가상 네트워크 DNS 서버를 업데이트 하면 Managed Instance에 즉시 영향을 주지 않습니다. Managed Instance DNS 구성은 DHCP 임대가 만료 된 후 또는 플랫폼이 upgarade 된 후에 업데이트 됩니다. **사용자는 첫 번째 Managed Instance을 만들기 전에 가상 네트워크 DNS 구성을 설정 하는 것이 좋습니다.**

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- Managed Instance를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- Managed Instance에 대해 VNet을 구성하는 방법에 대한 내용은 [Managed Instance의 VNet 구성](sql-database-managed-instance-connectivity-architecture.md)을 참조하세요.
