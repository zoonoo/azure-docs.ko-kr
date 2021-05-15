---
title: TLS(Transport Layer Security) - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: TLS 연결을 올바르게 사용하기 위해 Azure Database for PostgreSQL - 하이퍼스케일(Citus) 및 연결된 애플리케이션을 구성하는 방법에 대한 지침 및 정보입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87071465"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 TLS 구성
하이퍼스케일(Citus) 코디네이터 노드에 TLS(전송 계층 보안)를 연결하려면 클라이언트 애플리케이션이 필요합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간에 TLS를 적용하면 데이터를 전송하는 동안 데이터를 기밀로 유지할 수 있습니다. 아래에 설명된 추가 확인 설정도 "메시지 가로채기 (man-in-the-middle)" 공격으로부터 보호합니다.

## <a name="enforcing-tls-connections"></a>TLS 연결 적용
애플리케이션은 "연결 문자열"을 사용하여 연결에 대한 대상 데이터베이스 및 설정을 식별합니다. 클라이언트마다 다른 설정이 필요합니다. 일반적인 클라이언트에서 사용하는 연결 문자열 목록을 보려면 Azure Portal에서 서버 그룹에 대한 **연결 문자열** 섹션을 참조하세요.

TLS 매개 변수 `ssl` 및 `sslmode`는 커넥터의 기능에 따라 달라집니다(예: `ssl=true`, `sslmode=require` 또는 `sslmode=required`).

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>애플리케이션 또는 프레임워크가 TLS 연결을 지원하는지 확인
일부 애플리케이션 프레임워크는 기본적으로 PostgreSQL 연결에 대해 TLS를 사용하도록 설정하지 않습니다. 그러나 보안 연결을 사용하지 않으면 애플리케이션이 하이퍼스케일(Citus) 코디네이터 노드에 연결할 수 없습니다. 애플리케이션의 설명서를 참조하여 TLS 연결을 사용하도록 설정하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 연결에 대한 인증서 확인이 필요한 애플리케이션
경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 연결할 인증서는 https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem 에 있습니다. 인증서 파일을 다운로드하여 원하는 위치에 저장합니다.

> [!NOTE]
>
> 인증서의 신뢰성을 확인하려면 OpenSSL 명령줄 도구를 사용하여 SHA-256 지문을 확인합니다.
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>psql을 사용하여 연결
다음 예제에서는 psql 명령줄 유틸리티를 사용하여 하이퍼스케일(Citus) 코디네이터 노드에 연결하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용하여 TLS 인증서 확인을 적용합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수에 전달합니다.

다음은 psql 연결 문자열의 예입니다.
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> `sslrootcert`에 전달된 값이 저장한 인증서의 파일 경로와 일치하는지 확인합니다.

## <a name="next-steps"></a>다음 단계
[Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 방화벽 규칙](concepts-hyperscale-firewall-rules.md)을 사용하여 보안을 강화합니다.
