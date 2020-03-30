---
title: PostgreSQL용 Azure 데이터베이스의 보안 - 단일 서버
description: PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 보안 기능에 대한 개요입니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972595"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>PostgreSQL용 Azure 데이터베이스의 보안 - 단일 서버

PostgreSQL 서버에 대한 Azure 데이터베이스의 데이터를 보호하는 데 사용할 수 있는 여러 보안 계층이 있습니다. 이 문서에서는 이러한 보안 옵션에 대해 간략하게 설명합니다.

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="in-transit"></a>환승 중
PostgreSQL용 Azure 데이터베이스는 전송 계층 보안을 사용하여 전송 중 데이터를 암호화하여 데이터를 보호합니다. 암호화(SSL/TLS)는 기본적으로 적용됩니다.

### <a name="at-rest"></a>휴식 중
Azure Database for PostgreSQL 서비스는 미사용 데이터의 스토리지 암호화를 위해 FIPS 140-2 유효성 검사 암호화 모듈을 사용합니다. 백업을 포함한 데이터는 쿼리를 실행하는 동안 생성된 임시 파일을 제외하고 디스크에서 암호화됩니다. 이 서비스는 Azure 스토리지 암호화에 포함된 AES 256비트 암호화를 사용하며, 키는 시스템에서 관리됩니다. 스토리지 암호화는 항상 켜져 있고 해제할 수 없습니다.


## <a name="network-security"></a>네트워크 보안
PostgreSQL 서버에 대한 Azure 데이터베이스에 대한 연결은 먼저 지역 게이트웨이를 통해 라우팅됩니다. 게이트웨이에는 공개적으로 액세스할 수 있는 IP가 있고 서버 IP 주소는 보호됩니다. 게이트웨이에 대한 자세한 내용은 [연결 아키텍처 문서를](concepts-connectivity-architecture.md)참조하십시오.  

PostgreSQL 서버에 대해 새로 생성된 Azure 데이터베이스에는 모든 외부 연결을 차단하는 방화벽이 있습니다. 게이트웨이에 도달하지만 서버에 연결할 수 없습니다. 

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 각 요청의 원래 IP 주소를 기반으로 서버에 대한 액세스 권한을 부여합니다. 자세한 내용은 [방화벽 규칙 개요를](concepts-firewall-rules.md) 참조하세요.

### <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙
가상 네트워크 서비스 끝점은 Azure 백본을 통해 가상 네트워크 연결을 확장합니다. 가상 네트워크 규칙을 사용하면 PostgreSQL 서버에 대한 Azure 데이터베이스를 활성화하여 가상 네트워크의 선택한 서브넷에서 연결을 허용할 수 있습니다. 자세한 내용은 가상 [네트워크 서비스 끝점 개요를](concepts-data-access-and-security-vnet.md)참조하십시오.

### <a name="private-ip"></a>프라이빗 IP
개인 링크를 사용하면 개인 끝점을 통해 Azure의 PostgreSQL 단일 서버에 대한 Azure 데이터베이스에 연결할 수 있습니다. Azure 개인 링크는 기본적으로 VNet(개인 가상 네트워크) 내에서 Azure 서비스를 제공합니다. PaaS 리소스는 VNet의 다른 리소스와 마찬가지로 개인 IP 주소를 사용하여 액세스할 수 있습니다. 자세한 내용은 개인 [링크 개요를 참조하십시오.](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>액세스 관리

PostgreSQL 서버에 대한 Azure 데이터베이스를 만드는 동안 관리자 역할에 대한 자격 증명을 제공합니다. 이 관리자 역할은 [PostgreSQL 역할을](https://www.postgresql.org/docs/current/user-manag.html)추가로 만드는 데 사용할 수 있습니다.

[AAD(Azure Active Directory) 인증을](concepts-aad-authentication.md)사용하여 서버에 연결할 수도 있습니다.


## <a name="threat-protection"></a>위협 보호

서버에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 감지하는 [고급 위협 보호를](concepts-data-access-and-security-threat-protection.md) 선택할 수 있습니다.

[감사 로깅을](concepts-audit.md) 사용하여 데이터베이스의 활동을 추적할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
- [IP](concepts-firewall-rules.md) 또는 [가상 네트워크에](concepts-data-access-and-security-vnet.md) 대한 방화벽 규칙 사용
- PostgreSQL용 Azure 데이터베이스에서 [Azure Active Directory 인증에](concepts-aad-authentication.md) 대해 알아보기
