---
title: 연결 문제 해결-Azure Database for MySQL 유연한 서버
description: 유연한 서버 Azure Database for MySQL에 대 한 연결 문제를 해결 하는 방법을 알아봅니다.
keywords: mysql 연결, 연결 문자열, 연결 문제, 영구 오류, 연결 오류
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: b328da01cd3b2ecb3eb5f183296eab808de27399
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937277"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 유연한 서버에 대 한 연결 문제 해결

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

연결 문제는 다음과 같은 다양한 작업으로 인해 발생할 수 있습니다.

* 방화벽 설정
* 연결 제한 시간
* 잘못된 로그인 정보
* 일부 Azure Database for MySQL 유연한 서버 리소스에 대 한 최대 제한에 도달 했습니다.

이 문서에서는 몇 가지 일반적인 오류와 이러한 오류를 해결 하는 단계를 어떻게 해결할 수 있는지 설명 합니다.

## <a name="troubleshoot-common-errors"></a>일반 오류 문제 해결

응용 프로그램이 Azure Database for MySQL 유연한 서버에 영구적으로 연결 하지 못하는 경우 일반적으로 다음 중 하나에 해당 하는 문제가 있음을 나타냅니다.

* TLS/SSL을 사용 하는 암호화 된 연결: 유연한 서버는 1.2 TLS (전송 계층 보안)를 사용 하는 암호화 된 연결만 지원 하 고 **tls 1.0 및 tls 1.1를 사용 하는 모든 들어오는 연결은 거부**됩니다. TLS 버전을 사용 하지 않도록 설정 하거나 변경할 수 없습니다. [Azure Database for MySQL 유연한 서버에서 TLS 1.2 (Transport Layer Security)를 사용 하 여 암호화 된 연결](./how-to-connect-tls-ssl.md)에 대해 자세히 알아보세요.
- 전용 액세스의 유연한 서버 *(VNet 통합)*: 유연한 서버와 동일한 가상 네트워크 내에서 연결 하는지 확인 합니다. [Azure Database for MySQL 유연한 서버에서 virtual network]를 참조 하세요.<!--(./concepts-networking-virtual-network.md)-->
- 공용 액세스를 사용 하는 유연한 서버 *(허용 되는 IP 주소)*, 방화벽이 클라이언트의 연결을 허용 하도록 구성 되어 있는지 확인 합니다. Azure Portal를 [사용 하 여 유연한 서버 방화벽 규칙 만들기 및 관리](./how-to-manage-firewall-portal.md)를 참조 하세요.
* 클라이언트 방화벽 구성: 클라이언트의 방화벽은 데이터베이스 서버에 대 한 연결을 허용 해야 합니다. 일부 방화벽에서 MySQL과 같은 애플리케이션 이름뿐만 아니라 연결할 수 없는 서버의 IP 주소 및 포트도 허용되어야 합니다.
* 사용자 오류: 연결 문자열의 서버 이름과 같은 연결 매개 변수를 잘못 입력 했을 수 있습니다.

### <a name="resolve-connectivity-issues"></a>연결 문제 해결

* 암호화 된 연결에 대 한 자세한 내용은 [Azure Database for MySQL 유연한 > 서버에서 TLS 1.2 (Transport Layer Security)를 사용 하 여 암호화 된 연결](./how-to-connect-tls-ssl.md) 을 참조 하세요.
* **공용 액세스 (허용 된 ip 주소)** 를 사용 하는 경우 클라이언트 ip 주소를 허용 하도록 [방화벽 규칙](./how-to-manage-firewall-portal.md) 을 설정 합니다. 임시 테스트 용도로만 목적으로만 0.0.0.0을 시작 IP 주소로 사용하고 255.255.255.255를 끝 IP 주소로 사용하여 방화벽 규칙을 설정합니다. 이렇게 하면 서버가 모든 IP 주소로 열립니다. 이렇게 해서 연결 문제가 해결되면 이 규칙을 제거하고 적절하게 제한된 IP 주소 또는 주소 범위에 대해 방화벽 규칙을 만듭니다.
* 클라이언트와 인터넷 간의 모든 방화벽에서 아웃바운드 연결을 위해 3306 포트가 열려 있는지 확인합니다.
* 연결 문자열 및 기타 연결 설정을 확인합니다. 공용 언어에 대 한 Azure Portal에서 서버에 사용할 수 있는 **연결 문자열** 페이지에서 미리 정의 된 연결 문자열을 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [MySQL 워크 벤치를 사용 하 여 Azure Database for MySQL 유연한 서버에서 데이터를 연결 하 고 쿼리](./connect-workbench.md)합니다.
- [PHP를 사용 하 여 Azure Database for MySQL 유연한 서버에서 데이터를 연결 하 고 쿼리](./connect-php.md)합니다.
- [Python을 사용 하 여 Azure Database for MySQL 유연한 서버에서 데이터를 연결 하 고 쿼리](./connect-python.md)합니다.
