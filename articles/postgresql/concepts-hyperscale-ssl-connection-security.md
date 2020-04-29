---
title: TLS-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Citus (Azure Database for PostgreSQL-Hyperscale) 및 연결 된 응용 프로그램을 구성 하 여 TLS 연결을 올바르게 사용 하는 지침 및 정보입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422331"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale에서 TLS 구성 (Citus)
Citus (Hyperscale) 코디네이터 노드에 대 한 클라이언트 응용 프로그램 연결에는 TLS (전송 계층 보안)가 필요 하며,이는 이전에 SSL (SSL(Secure Sockets Layer)) 이라고 합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간에 TLS 연결을 적용 하면 서버와 응용 프로그램 간에 데이터 스트림을 암호화 하 여 "메시지 가로채기 (man-in-the-middle)" 공격 으로부터 보호 하는 데 도움이 됩니다.

## <a name="enforcing-tls-connections"></a>TLS 연결 적용
Azure Portal를 통해 프로 비전 된 모든 Azure Database for PostgreSQL 서버의 경우 TLS 연결 적용이 기본적으로 사용 하도록 설정 됩니다. 

마찬가지로 Azure Portal의 서버에 있는 "연결 문자열" 설정에서 미리 정의 된 연결 문자열에는 TLS를 사용 하 여 데이터베이스 서버에 연결 하는 데 사용 되는 공용 언어에 대 한 필수 매개 변수가 포함 됩니다. TLS 매개 변수는 커넥터에 따라 달라 집니다 (예: "ssl = true" 또는 "sslmode = required" 또는 "sslmode = required" 및 기타 변형).

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>응용 프로그램 또는 프레임 워크에서 TLS 연결을 지원 하는지 확인
데이터베이스 서비스에 대해 PostgreSQL를 사용 하는 일부 응용 프로그램 프레임 워크는 설치 하는 동안 기본적으로 TLS를 사용 하도록 설정 하지 않습니다. PostgreSQL 서버에서 TLS 연결을 적용 하지만 응용 프로그램이 TLS에 대해 구성 되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결 하지 못할 수 있습니다. 응용 프로그램의 설명서를 참조 하 여 TLS 연결을 사용 하도록 설정 하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 연결에 대 한 인증서 확인이 필요한 응용 프로그램
경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. Citus (Azure Database for PostgreSQL-Hyperscale)에 연결할 인증서는에 https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem있습니다. 인증서 파일을 다운로드 하 여 원하는 위치에 저장 합니다.

### <a name="connect-using-psql"></a>Psql을 사용 하 여 연결
다음 예제에서는 psql 명령줄 유틸리티를 사용 하 여 Citus (Hyperscale) 코디네이터 노드에 연결 하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용 하 여 TLS 인증서 확인을 적용 합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수로 전달 합니다.

다음은 psql 연결 문자열의 예입니다.
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 에 `sslrootcert` 전달 된 값이 저장 한 인증서의 파일 경로와 일치 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Database for PostgreSQL-Hyperscale (Citus)의 방화벽 규칙](concepts-hyperscale-firewall-rules.md)을 사용 하 여 보안을 강화 합니다.
