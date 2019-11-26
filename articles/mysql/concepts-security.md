---
title: Security in Azure Database for MySQL - Single Server
description: An overview of the security features in Azure Database for MySQL - Single Server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 06f895d2e8cd7936141c9f35c5ed0c81fb7e3e15
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485056"
---
# <a name="security-in-azure-database-for-mysql---single-server"></a>Security in Azure Database for MySQL - Single Server

There are multiple layers of security that are available to protect the data on your Azure Database for MySQL server. This article outlines those security options.

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="in-transit"></a>In-transit
Azure Database for MySQL secures your data by encrypting data in-transit with Transport Layer Security. Encryption (SSL/TLS) is enforced by default.

### <a name="at-rest"></a>At-rest
Azure Database for MySQL 서비스는 미사용 데이터의 스토리지 암호화를 위해 FIPS 140-2 유효성 검사 암호화 모듈을 사용합니다. Data, including backups, are encrypted on disk, with the exception of temporary files created while running queries. 이 서비스는 Azure 스토리지 암호화에 포함된 AES 256비트 암호화를 사용하며, 키는 시스템에서 관리됩니다. 스토리지 암호화는 항상 켜져 있고 해제할 수 없습니다.


## <a name="network-security"></a>네트워크 보안
Connections to an Azure Database for MySQL server are first routed through a regional gateway. The gateway has a publicly accessible IP, while the server IP addresses are protected. For more information about the gateway, visit the [connectivity architecture article](concepts-connectivity-architecture.md).  

A newly created Azure Database for MySQL server has a firewall that blocks all external connections. Though they reach the gateway, they are not allowed to connect to the server. 

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP firewall rules grant access to servers based on the originating IP address of each request. See the [firewall rules overview](concepts-firewall-rules.md) for more information.

### <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙
Virtual network service endpoints extend your virtual network connectivity over the Azure backbone. Using virtual network rules you can enable your Azure Database for MySQL server to allow connections from selected subnets in a virtual network. For more information, see the [virtual network service endpoint overview](concepts-data-access-and-security-vnet.md).


## <a name="access-management"></a>액세스 관리

While creating the Azure Database for MySQL server, you provide credentials for an administrator user. This administrator can be used to create additional MySQL users.


## <a name="threat-protection"></a>위협 보호

You can opt in to [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) which detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit servers.

[Audit logging](concepts-audit-logs.md) is available to track activity in your databases. 


## <a name="next-steps"></a>다음 단계
- Enable firewall rules for [IPs](concepts-firewall-rules.md) or [virtual networks](concepts-data-access-and-security-vnet.md)