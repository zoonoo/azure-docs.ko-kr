---
title: Azure Database for PostgreSQL 유연한 서버에서 TLS/SSL을 사용 하 여 암호화 된 연결
description: Azure Database for PostgreSQL 유연한 서버에서 TLS/SSL을 사용 하 여 연결 하는 방법에 대 한 지침 및 정보입니다.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0ba04ff14e62cd411515a765dc95ef7a3e72b709
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938923"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 유연한 서버에서 전송 계층 보안을 사용 하 여 암호화 된 연결

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

Azure Database for PostgreSQL 유연한 서버는 TLS (전송 계층 보안)를 사용 하 여 클라이언트 응용 프로그램을 PostgreSQL 서비스에 연결 하는 기능을 지원 합니다 (이전에는 SSL (SSL(Secure Sockets Layer))). TLS는 데이터베이스 서버와 클라이언트 응용 프로그램 간에 암호화 된 네트워크 연결을 보장 하 여 규정 준수 요구 사항을 준수할 수 있는 업계 표준 프로토콜입니다.

Azure Database for PostgreSQL 유연한 서버는 TLS (전송 계층 보안)를 사용 하 여 암호화 된 연결을 지원 하 고 TLS 1.0 및 TLS 1.1을 사용 하 여 들어오는 모든 연결을 거부 합니다. 모든 유연한 서버에서 TLS 연결의 적용을 사용 하도록 설정 하 고 유연한 서버에 연결 하는 데 TLS/SSL을 사용 하지 않도록 설정할 수 없습니다.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>TLS/SSL 연결을 위해 인증서를 확인 해야 하는 응용 프로그램
경우에 따라 응용 프로그램은 신뢰할 수 있는 CA (인증 기관) 인증서 파일에서 생성 된 로컬 인증서 파일을 안전 하 게 연결 해야 합니다. Azure Database for PostgreSQL 유연한 서버는 *DigiCert Global ROOT CA*를 사용 합니다. [DigiCert Global ROOT CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 에서 SSL을 통해 통신 하는 데 필요한이 인증서를 다운로드 하 고 인증서 파일을 원하는 위치에 저장 합니다. 예를 들어 이 자습서에서는 `c:\ssl`을 사용합니다.


### <a name="connect-using-psql"></a>Psql을 사용 하 여 연결
*프라이빗 액세스(VNet 통합)* 를 사용하여 유연한 서버를 만든 경우 서버와 동일한 VNet 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고 유연한 서버를 사용하여 만든 VNet에 추가할 수 있습니다.

*퍼블릭 액세스(허용되는 IP 주소)* 를 사용하여 유연한 서버를 만든 경우 서버의 방화벽 규칙 목록에 로컬 IP 주소를 추가할 수 있습니다.

다음 예에서는 psql 명령줄 인터페이스를 사용 하 여 서버에 연결 하는 방법을 보여 줍니다. `sslmode=verify-full`연결 문자열 설정을 사용 하 여 TLS/SSL 인증서 확인을 적용 합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수로 전달 합니다.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> 에 전달 된 값이 `sslrootcert` 저장 한 인증서의 파일 경로와 일치 하는지 확인 합니다.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>응용 프로그램 또는 프레임 워크에서 TLS 연결을 지원 하는지 확인

데이터베이스 서비스에 대해 PostgreSQL를 사용 하는 일부 응용 프로그램 프레임 워크는 설치 하는 동안 기본적으로 TLS를 사용 하도록 설정 하지 않습니다. PostgreSQL 서버는 TLS 연결을 적용 하지만 응용 프로그램이 TLS에 대해 구성 되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결 하지 못할 수 있습니다. 응용 프로그램의 설명서를 참조 하 여 TLS 연결을 사용 하도록 설정 하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계
- [Azure CLI를 사용 하 여 Azure Database for PostgreSQL 유연한 서버 가상 네트워크를 만들고 관리](./how-to-manage-virtual-network-cli.md)합니다.
- [Azure Database for PostgreSQL 유연한 서버에서 네트워킹](./concepts-networking.md) 에 대 한 자세한 정보
- [Azure Database for PostgreSQL 유연한 서버 방화벽 규칙](./concepts-networking.md#public-access-allowed-ip-addresses) 에 대해 자세히 알아보기
