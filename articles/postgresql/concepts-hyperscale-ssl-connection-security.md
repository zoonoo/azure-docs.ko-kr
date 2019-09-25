---
title: Azure Database for PostgreSQL-Hyperscale에서 SSL 구성 (Citus)
description: SSL 연결을 올바르게 사용 하도록 Citus () 및 연결 된 응용 프로그램을 Azure Database for PostgreSQL 구성 하기 위한 지침 및 정보입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: be02c9114f30a63fe710cfd2fc56347bfc68fab5
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273726"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale에서 SSL 구성 (Citus)
Citus (Hyperscale) 코디네이터 노드에 대 한 클라이언트 응용 프로그램 연결에는 SSL(Secure Sockets Layer) (SSL)이 필요 합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간에 SSL 연결을 적용 하면 서버와 응용 프로그램 간에 데이터 스트림을 암호화 하 여 "메시지 가로채기 (man-in-the-middle)" 공격 으로부터 보호 합니다.

## <a name="enforcing-ssl-connections"></a>SSL 연결 적용
Azure Portal를 통해 프로 비전 된 모든 Azure Database for PostgreSQL 서버의 경우 SSL 연결 적용이 기본적으로 사용 하도록 설정 됩니다. 

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 SSL을 사용하여 데이터베이스 서버에 연결하기 위한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>애플리케이션 또는 프레임워크가 SSL 연결을 지원하는지 확인합니다.
데이터베이스 서비스에 대해 PostgreSQL를 사용 하는 일부 응용 프로그램 프레임 워크는 설치 하는 동안 기본적으로 SSL을 사용 하도록 설정 하지 않습니다. PostgreSQL 서버에서 SSL 연결을 적용 하지만 응용 프로그램이 SSL로 구성 되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결 하지 못할 수 있습니다. 애플리케이션의 설명서를 참조하여 SSL 연결을 설정하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>SSL 연결을 위해 인증서 확인이 필요한 애플리케이션
경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. Citus (Azure Database for PostgreSQL-Hyperscale)에 연결할 인증서는에 https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem 있습니다. 인증서 파일을 다운로드 하 여 원하는 위치에 저장 합니다.

### <a name="connect-using-psql"></a>Psql을 사용하여 연결
다음 예제에서는 psql 명령줄 유틸리티를 사용 하 여 Citus (Hyperscale) 코디네이터 노드에 연결 하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용 하 여 SSL 인증서 확인을 적용 합니다. 로컬 인증서 파일 경로 `sslrootcert` 를 매개 변수로 전달 합니다.

다음은 psql 연결 문자열의 예입니다.
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 에 `sslrootcert` 전달 된 값이 저장 한 인증서의 파일 경로와 일치 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Database for PostgreSQL-Hyperscale (Citus)의 방화벽 규칙](concepts-hyperscale-firewall-rules.md)을 사용 하 여 보안을 강화 합니다.
