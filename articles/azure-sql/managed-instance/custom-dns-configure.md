---
title: 사용자 지정 DNS
titleSuffix: Azure SQL Managed Instance
description: 이 항목에서는 Azure SQL Managed Instance를 사용하는 사용자 지정 DNS의 구성 옵션에 대해 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: deb7c89099a53b77d7c56b68173c071355baea1e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110707196"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대한 사용자 지정 DNS 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 Azure [VNet(가상 네트워크)](../../virtual-network/virtual-networks-overview.md) 내에 배포해야 합니다. 프라이빗 호스트 이름이 SQL Managed Instance에서 확인되어야 하는 몇 가지 시나리오(예: db 메일, 클라우드 또는 하이브리드 환경의 다른 SQL Server 인스턴스에 연결된 서버)가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. 

SQL Managed Instance는 내부 작동에 동일한 DNS를 사용하므로 공용 도메인 이름을 확인할 수 있도록 사용자 지정 DNS 서버를 구성합니다.

> [!IMPORTANT]
> 메일 서버, SQL Server 인스턴스 및 기타 서비스가 프라이빗 DNS 영역 내에 있는 경우에도 항상 FQDN(정규화된 도메인 이름)을 사용하세요. 예를 들어 `smtp`가 제대로 확인되지 않으므로 메일 서버에 `smtp.contoso.com`을 사용합니다. 동일한 가상 네트워크 내에서 SQL Server VM을 참조하는 연결된 서버 또는 복제를 만들려면 FQDN과 기본 DNS 접미사도 필요합니다. 예들 들어 `SQLVM.internal.cloudapp.net`입니다. 자세한 내용은 [자체 DNS 서버를 사용하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

> [!IMPORTANT]
> 가상 네트워크 DNS 서버를 업데이트해도 SQL Managed Instance에 즉시 영향을 주지 않습니다. 자세한 내용은 [SQL Managed Instance 가상 클러스터에서 가상 네트워크 DNS 서버 설정을 동기화하는 방법](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- 관리되는 인스턴스를 새로 만드는 방법을 보여 주는 자습서는 [관리되는 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- 관리되는 인스턴스의 VNet을 구성하는 방법에 대해서는 [관리되는 인스턴스의 VNet 구성](connectivity-architecture-overview.md)을 참조하세요.
