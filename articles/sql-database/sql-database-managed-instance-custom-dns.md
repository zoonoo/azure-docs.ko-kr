---
title: 관리되는 인스턴스 사용자 지정 DNS
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
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247081"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 대한 사용자 지정 DNS 구성

Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 내에서 Azure SQL Database Managed Instance를 배포해야 합니다. 프라이빗 호스트 이름이 Managed Instance에서 확인되어야 하는 몇 가지 시나리오(예: db 메일, 클라우드 또는 하이브리드 환경의 다른 SQL 인스턴스에 연결된 서버)가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. 

관리되는 인스턴스는 내부 작업에 동일한 DNS를 사용하므로 공용 도메인 이름을 확인할 수 있도록 사용자 지정 DNS 서버를 구성합니다.

> [!IMPORTANT]
> 메일 서버, SQL Server 인스턴스 및 기타 서비스에 대해 개인 DNS 영역 내에 있더라도 항상 정규화된 도메인 이름(FQDN)을 사용합니다. 예를 들어 `smtp.contoso.com` 메일 서버가 `smtp` 올바르게 해결되지 않으므로 사용합니다. 동일한 가상 네트워크 내에서 SQL VM을 참조하는 연결된 서버 또는 복제를 만들려면 FQDN과 기본 DNS 접미사가 필요합니다. `SQLVM.internal.cloudapp.net`)을 입력합니다. 자세한 내용은 [사용자 고유의 DNS 서버를 사용하는 이름 확인을](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)참조하십시오.

> [!IMPORTANT]
> 가상 네트워크 DNS 서버를 업데이트하는 것은 관리되는 인스턴스에 즉시 영향을 주지 않습니다. 관리되는 인스턴스 DNS 구성은 DHCP 임대가 만료된 후 또는 플랫폼 업그레이드 후(중 먼저 발생하는 경우) 업데이트됩니다. **사용자는 첫 번째 관리형 인스턴스를 만들기 전에 가상 네트워크 DNS 구성을 설정하는 것이 좋습니다.**

## <a name="next-steps"></a>다음 단계

- 개요는 [관리되는 인스턴스란 무엇이있는지](sql-database-managed-instance.md) 를 참조하십시오.
- Managed Instance를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- Managed Instance에 대해 VNet을 구성하는 방법에 대한 내용은 [Managed Instance의 VNet 구성](sql-database-managed-instance-connectivity-architecture.md)을 참조하세요.
