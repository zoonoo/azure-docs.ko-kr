---
title: 관리 되는 인스턴스 사용자 지정 DNS
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
ms.date: 07/17/2019
ms.openlocfilehash: 3161d931c6e912c05c595db7f3c790da454dd5ed
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823348"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 대한 사용자 지정 DNS 구성

Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 내에서 Azure SQL Database Managed Instance를 배포해야 합니다. 프라이빗 호스트 이름이 Managed Instance에서 확인되어야 하는 몇 가지 시나리오(예: db 메일, 클라우드 또는 하이브리드 환경의 다른 SQL 인스턴스에 연결된 서버)가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. 

Managed Instance는 내부 작동에 동일한 DNS를 사용 하기 때문에 공용 도메인 이름을 확인할 수 있도록 사용자 지정 DNS 서버를 구성 합니다.

> [!IMPORTANT]
> 항상 메일 서버, SQL Server 인스턴스 및 다른 서비스에 대해 FQDN (정규화 된 도메인 이름)을 사용 합니다 .이는 개인 DNS 영역 내에 있더라도 예를 들어 `smtp`이 올바르게 확인 되지 않으므로 메일 서버에 `smtp.contoso.com`을 사용 합니다. 동일한 가상 네트워크 내에서 SQL Vm을 참조 하는 연결 된 서버 또는 복제를 만들려면 FQDN과 기본 DNS 접미사도 필요 합니다. 예: `SQLVM.internal.cloudapp.net` 자세한 내용은 [자체 DNS 서버를 사용 하는 이름 확인](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)을 참조 하세요.

> [!IMPORTANT]
> 가상 네트워크 DNS 서버를 업데이트 하면 Managed Instance에 즉시 영향을 주지 않습니다. Managed Instance DNS 구성은 DHCP 임대가 만료 된 후 또는 플랫폼이 upgarade 된 후 (어느 쪽이 든 먼저 발생 하는 경우) 업데이트 됩니다. **사용자는 첫 번째 Managed Instance을 만들기 전에 가상 네트워크 DNS 구성을 설정 하는 것이 좋습니다.**

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- Managed Instance를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- Managed Instance에 대해 VNet을 구성하는 방법에 대한 내용은 [Managed Instance의 VNet 구성](sql-database-managed-instance-connectivity-architecture.md)을 참조하세요.
