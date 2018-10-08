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
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/23/2018
ms.openlocfilehash: f26ea763d48d03fe7e981b7abbbe64e573ec0b3a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224276"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 대한 사용자 지정 DNS 구성

Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 내에서 Azure SQL Database Managed Instance를 배포해야 합니다. 개인 호스트 이름이 Managed Instance에서 해결되어야 하는 몇 가지 시나리오(클라우드 또는 하이브리드 환경의 다른 SQL 인스턴스에 연결된 서버)가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. Managed Instance가 내부 작업에서 동일한 DNS를 사용하므로 가상 네트워크 DNS 구성은 Managed Instance와 호환되어야 합니다. 

사용자 지정 DNS 구성이 Managed Instance와 호환되도록 하려면 다음을 수행해야 합니다. 
- 공용 도메인 이름을 확인할 수 있도록 사용자 지정 DNS 서버 구성 
- Azure 재귀 확인자 DNS IP 주소 168.63.129.16을 가상 네트워크 DNS 목록 끝에 배치 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>사용자 지정 DNS 서버 구성 설정

1. Azure Portal에서 VNet에 대한 사용자 지정 DNS 옵션을 찾습니다.

   ![사용자 지정 DNS 옵션](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. 사용자 지정으로 전환하고 Azure의 재귀 해결자 IP 주소 168.63.129.16만 아니라 사용자 지정 DNS 서버 IP 주소를 입력합니다. 

   ![사용자 지정 DNS 옵션](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > DNS 목록에서 Azure의 재귀 확인자를 설정하지 않으면 어떤 이유로 사용자 지정 DNS 서버를 사용할 수 없는 경우 Managed Instance는 잘못된 상태가 될 수 있습니다. 해당 상태에서 복구하려면 네트워킹 준수 정책을 사용하여 VNet에서 새 인스턴스를 만들고, 인스턴스 수준 데이터를 만들고, 데이터베이스를 복원해야 합니다. DNS 목록의 마지막 항목이 보장하는 것처럼 Azure의 재귀 확인자를 설정하면, 모든 사용자 지정 DNS 서버가 실패하는 경우에도 공용 이름은 계속 확인할 수 있습니다. [VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- Managed Instance를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- Managed Instance에 대해 VNet을 구성하는 방법에 대한 내용은 [Managed Instance의 VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요.
