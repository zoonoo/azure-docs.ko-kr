---
title: Azure Database for PostgreSQL - 유연한 서버의 보안
description: Azure Database for PostgreSQL - 유연한 서버에 대한 배포 옵션의 보안에 대해 알아보기
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 79e308605a0d6262e95dc6ff673d74a5a1eaaec7
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114732159"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 보안

Azure Database for PostgreSQL 서버의 데이터를 보호하는 데 사용할 수 있는 여러 보안 계층이 있습니다. 이 문서에서는 이러한 보안 옵션에 대해 간략히 설명합니다.

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="in-transit"></a>전송 중
 Azure Database for PostgreSQL은 전송 중인 데이터를 전송 계층 보안으로 암호화하여 데이터를 보호합니다. 암호화(SSL/TLS)는 기본적으로 적용됩니다.

### <a name="at-rest"></a>미사용
Azure Database for PostgreSQL 서비스는 미사용 데이터의 스토리지 암호화를 위해 FIPS 140-2 유효성 검사 암호화 모듈을 사용합니다. 백업을 포함한 데이터는 디스크에서 암호화되며, 쿼리를 실행하는 동안 만든 임시 파일은 제외됩니다. 이 서비스는 Azure 스토리지 암호화에 포함된 AES 256비트 암호화를 사용하며, 키는 시스템에서 관리됩니다. 이는 SQL Server 또는 Oracle 데이터베이스의 TDE(투명한 데이터 암호화)와 같은 다른 미사용 암호화 기술과 유사합니다. 스토리지 암호화는 항상 켜져 있고 해제할 수 없습니다.


## <a name="network-security"></a>네트워크 보안

Azure Database for PostgreSQL – 유연한 서버를 실행할 때 두 가지 주요 네트워킹 옵션을 선택할 수 있습니다. 하나는 프라이빗 액세스(VNet 통합) 이고 다른 하나는 퍼블릭 액세스(허용된 IP 주소) 입니다. 프라이빗 액세스를 활용하여 유연한 서버가 Azure Virtual Network에 배포됩니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 개인 IP 주소를 통해 통신할 수 있습니다.
공용 액세스를 사용하면 프라이빗 엔드포인트를 통해 유연한 서버에 액세스할 수 있습니다. 프라이빗 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소이며 기본적으로 모든 연결을 차단하는 방화벽을 통해 액세스할 수 있습니다. 



### <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 각 요청의 기존 IP 주소를 기준으로 하여 데이터베이스 액세스 권한을 부여합니다. 자세한 내용은 [방화벽 규칙 개요](concepts-firewall-rules.md)를 참조하세요.


### <a name="private-vnet-access"></a>프라이빗 VNET 액세스
유연한 서버를 Azure Virtual Network에 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 자세한 내용은 [유연한 서버](concepts-networking.md)를 참조하세요.

### <a name="network-security-groups-nsg"></a>NSG(네트워크 보안 그룹)
네트워크 보안 그룹의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다.  자세한 내용은 [네트워크 보안 그룹 개요](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview)를 참조하세요.

## <a name="access-management"></a>액세스 관리

Azure Database for PostgreSQL 서버를 만드는 동안 관리자 역할에 대한 자격 증명을 제공합니다. 이 관리자 역할은 추가 [PostgreSQL 역할](https://www.postgresql.org/docs/current/user-manag.html)을 만드는 데 사용할 수 있습니다.

[AAD(Azure Active Directory) 인증](../concepts-aad-authentication.md)을 사용하여 서버에 연결할 수도 있습니다.


### <a name="azure-defender-protection"></a>Azure Defender 보호

 Azure Database for PostgreSQL - 유연한 서버는 현재 [Azure Defender 보호](https://docs.microsoft.com/azure/security-center/azure-defender)를 지원하지 않습니다. 


[감사 로깅](../concepts-audit.md)은 데이터베이스의 활동을 추적하는 데 사용할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
  - 공용 액세스 네트워킹의 [IP](concepts-firewall-rules.md)에 대한 방화벽 규칙 사용
  - [Azure Database for PostgreSQL - 유연한 서버를 사용한 프라이빗 액세스 네트워킹](concepts-networking.md)에 대해 알아보기
  - Azure Database for PostgreSQL의 [Azure Active Directory 인증](../concepts-aad-authentication.md)에 대해 알아보기