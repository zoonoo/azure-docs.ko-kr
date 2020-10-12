---
title: 사용자 지정 DNS
titleSuffix: Azure SQL Managed Instance
description: 이 항목에서는 Azure SQL Managed Instance를 사용 하 여 사용자 지정 DNS에 대 한 구성 옵션을 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: 9fdd61c5799724ab3ab098584f85ce52cdd6ccf4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283261"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대 한 사용자 지정 DNS 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 Azure [VNet (가상 네트워크)](../../virtual-network/virtual-networks-overview.md)내에 배포 되어야 합니다. 프라이빗 호스트 이름이 SQL Managed Instance에서 확인되어야 하는 몇 가지 시나리오(예: db 메일, 클라우드 또는 하이브리드 환경의 다른 SQL Server 인스턴스에 연결된 서버)가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. 

SQL Managed Instance는 내부 작동에 동일한 DNS를 사용 하기 때문에 공용 도메인 이름을 확인할 수 있도록 사용자 지정 DNS 서버를 구성 합니다.

> [!IMPORTANT]
> 항상 메일 서버에 대 한 FQDN (정규화 된 도메인 이름), SQL Server 인스턴스 및 다른 서비스에 대 한 FQDN (정규화 된 도메인 이름)은 개인 DNS 영역 내에 있는 경우에도 사용 합니다. 예를 들어 메일 서버에 대해를 사용 하는 경우가 `smtp.contoso.com` `smtp` 올바르게 확인 되지 않기 때문입니다. 동일한 가상 네트워크 내의 Vm SQL Server 참조 하는 연결 된 서버 또는 복제를 만들려면 FQDN과 기본 DNS 접미사도 필요 합니다. 예들 들어 `SQLVM.internal.cloudapp.net`입니다. 자세한 내용은 [자체 DNS 서버를 사용 하는 이름 확인](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)을 참조 하세요.

> [!IMPORTANT]
> 가상 네트워크 DNS 서버를 업데이트 해도 SQL Managed Instance에 즉시 영향을 주지는 않습니다. SQL Managed Instance DNS 구성은 DHCP 임대가 만료 된 후 또는 플랫폼 업그레이드 후 (어느 쪽이 든 먼저 발생 하는 경우) 업데이트 됩니다. **사용자는 첫 번째 관리 되는 인스턴스를 만들기 전에 가상 네트워크 DNS 구성을 설정 하는 것이 좋습니다.**

## <a name="next-steps"></a>다음 단계

- 개요는 [AZURE SQL Managed Instance?](sql-managed-instance-paas-overview.md)을 참조 하세요.
- 새 관리 되는 인스턴스를 만드는 방법을 보여 주는 자습서는 [관리 되는 인스턴스 만들기](instance-create-quickstart.md)를 참조 하세요.
- 관리 되는 인스턴스에 대해 VNet을 구성 하는 방법에 대 한 자세한 내용은 [관리 되는 인스턴스의 vnet 구성](connectivity-architecture-overview.md)을 참조 하세요.
