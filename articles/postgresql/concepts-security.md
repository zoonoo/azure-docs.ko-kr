---
title: Azure Database for PostgreSQL의 보안 - 단일 서버
description: Azure Database for PostgreSQL의 보안 기능 개요 - 단일 서버
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 261f198542a7030f327dc8be055f5db0cb64f957
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952441"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL의 보안 - 단일 서버

Azure Database for PostgreSQL 서버의 데이터를 보호하는 데 사용할 수 있는 여러 보안 계층이 있습니다. 이 문서에서는 이러한 보안 옵션에 대해 간략히 설명합니다.

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="in-transit"></a>전송 중
Azure Database for PostgreSQL은 전송 중인 데이터를 전송 계층 보안으로 암호화하여 데이터를 보호합니다. 암호화(SSL/TLS)는 기본적으로 적용됩니다.

### <a name="at-rest"></a>미사용
Azure Database for PostgreSQL 서비스는 미사용 데이터의 스토리지 암호화를 위해 FIPS 140-2 유효성 검사 암호화 모듈을 사용합니다. 백업을 포함한 데이터는 디스크에서 암호화되며, 쿼리를 실행하는 동안 만든 임시 파일은 제외됩니다. 이 서비스는 Azure 스토리지 암호화에 포함된 AES 256비트 암호화를 사용하며, 키는 시스템에서 관리됩니다. 스토리지 암호화는 항상 켜져 있고 해제할 수 없습니다.


## <a name="network-security"></a>네트워크 보안
그러나 Azure Database for PostgreSQL 서버의 연결은 먼저 공개적으로 액세스할 수 있는 지역 게이트웨이를 통해 라우팅됩니다. 게이트웨이에는 공개적으로 액세스 가능한 IP가 있고 서버 IP 주소는 보호됩니다. 게이트웨이에 대한 자세한 내용은 [연결 아키텍처 문서](concepts-connectivity-architecture.md)를 참조하세요.  

새로 만든 Azure Database for PostgreSQL 서버에는 모든 외부 연결을 차단하는 방화벽이 있습니다. 이는 게이트웨이에 연결 되지만 서버에 연결할 수는 없습니다. 

### <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 각 요청의 기존 IP 주소를 기준으로 하여 데이터베이스 액세스 권한을 부여합니다. 자세한 내용은 [방화벽 규칙 개요](concepts-firewall-rules.md)를 참조하세요.

### <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙
가상 네트워크 서비스 엔드포인트는 Azure 백본을 통해 가상 네트워크 연결을 확장합니다. 가상 네트워크 규칙을 사용하여 Azure Database for PostgreSQL 서버가 선택한 가상 네트워크의 서브넷에서 연결을 허용하도록 설정할 수 있습니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트 개요](concepts-data-access-and-security-vnet.md)를 참조하세요.

### <a name="private-ip"></a>프라이빗 IP
Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure에서 Azure Database for PostgreSQL 단일 서버로 연결할 수 있습니다. Azure Private Link는 기본적으로 개인 VNet(Virtual Network) 내에 Azure 서비스를 제공합니다. PaaS 리소스는 VNet의 다른 리소스와 마찬가지로 개인 IP 주소를 사용하여 액세스할 수 있습니다. 자세한 내용은 [프라이빗 링크 개요](concepts-data-access-and-security-private-link.md)를 참조하세요.


## <a name="access-management"></a>액세스 관리

Azure Database for PostgreSQL 서버를 만드는 동안 관리자 역할에 대한 자격 증명을 제공합니다. 이 관리자 역할은 추가 [PostgreSQL 역할](https://www.postgresql.org/docs/current/user-manag.html)을 만드는 데 사용할 수 있습니다.

[AAD(Azure Active Directory) 인증](concepts-aad-authentication.md)을 사용하여 서버에 연결할 수도 있습니다.


## <a name="threat-protection"></a>위협 보호

서버에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 감지하는 [Advanced Threat Protection](../security-center/defender-for-databases-introduction.md)을 선택할 수 있습니다.

[감사 로깅](concepts-audit.md)은 데이터베이스의 활동을 추적하는 데 사용할 수 있습니다. 

## <a name="migrating-from-oracle"></a>Oracle에서 마이그레이션

Oracle은 테이블 및 테이블스페이스 데이터를 암호화하기 위한 TDE(투명한 데이터 암호화)를 지원합니다. Azure for PostgreSQL에서 데이터는 다양한 계층에서 자동으로 암호화됩니다. 이 페이지의 "미사용" 섹션을 참조하고 [고객 관리형 키](./concepts-data-encryption-postgresql.md) 및 [인프라 이중 암호화](./concepts-infrastructure-double-encryption.md)를 비롯한 다양한 보안 토픽을 참조하세요. [Azure for PostgreSQL](./concepts-extensions.md)에서 지원되는 [pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html) 확장을 사용하는 것을 고려할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
- [IP](concepts-firewall-rules.md) 또는 [가상 네트워크](concepts-data-access-and-security-vnet.md)에 방화벽 규칙 사용
- Azure Database for PostgreSQL의 [Azure Active Directory 인증](concepts-aad-authentication.md)에 대해 알아보기