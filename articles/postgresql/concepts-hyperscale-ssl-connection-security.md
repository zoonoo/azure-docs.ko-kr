---
title: TLS (Transport Layer Security)-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Citus (Azure Database for PostgreSQL-Hyperscale) 및 연결 된 응용 프로그램을 구성 하 여 TLS 연결을 올바르게 사용 하는 지침 및 정보입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071465"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale에서 TLS 구성 (Citus)
Citus (Hyperscale) 코디네이터 노드에서는 클라이언트 응용 프로그램이 TLS (Transport Layer Security)와 연결 해야 합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간에 TLS를 적용 하면 데이터를 전송 하는 동안 데이터를 기밀로 유지할 수 있습니다. 아래에 설명 된 추가 확인 설정도 "메시지 가로채기 (man-in-the-middle)" 공격 으로부터 보호 합니다.

## <a name="enforcing-tls-connections"></a>TLS 연결 적용
응용 프로그램은 "연결 문자열"을 사용 하 여 연결에 대 한 대상 데이터베이스 및 설정을 식별 합니다. 클라이언트 마다 다른 설정이 필요 합니다. 공용 클라이언트에서 사용 하는 연결 문자열 목록을 보려면 Azure Portal에서 서버 그룹에 대 한 **연결 문자열** 섹션을 참조 하세요.

TLS 매개 변수 `ssl` 및는 `sslmode` 커넥터의 기능 (예: 또는 또는)에 따라 달라 집니다 `ssl=true` `sslmode=require` `sslmode=required` .

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>응용 프로그램 또는 프레임 워크에서 TLS 연결을 지원 하는지 확인
일부 응용 프로그램 프레임 워크는 기본적으로 PostgreSQL 연결에 대해 TLS를 사용 하도록 설정 하지 않습니다. 그러나 보안 연결을 사용 하지 않으면 응용 프로그램이 Citus (Hyperscale) 코디네이터 노드에 연결할 수 없습니다. 응용 프로그램의 설명서를 참조 하 여 TLS 연결을 사용 하도록 설정 하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 연결에 대 한 인증서 확인이 필요한 응용 프로그램
경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. Citus (Azure Database for PostgreSQL-Hyperscale)에 연결할 인증서는에 https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem 있습니다. 인증서 파일을 다운로드 하 여 원하는 위치에 저장 합니다.

> [!NOTE]
>
> 인증서의 신뢰성을 확인 하려면 OpenSSL 명령줄 도구를 사용 하 여 SHA-256 지문을 확인할 수 있습니다.
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>Psql을 사용 하 여 연결
다음 예제에서는 psql 명령줄 유틸리티를 사용 하 여 Citus (Hyperscale) 코디네이터 노드에 연결 하는 방법을 보여 줍니다. `sslmode=verify-full`연결 문자열 설정을 사용 하 여 TLS 인증서 확인을 적용 합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수로 전달 합니다.

다음은 psql 연결 문자열의 예입니다.
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 에 전달 된 값이 `sslrootcert` 저장 한 인증서의 파일 경로와 일치 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Database for PostgreSQL-Hyperscale (Citus)의 방화벽 규칙](concepts-hyperscale-firewall-rules.md)을 사용 하 여 보안을 강화 합니다.
