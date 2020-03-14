---
title: SSL-Azure Database for PostgreSQL-단일 서버
description: Azure Database for PostgreSQL-단일 서버에 대 한 SSL 연결을 구성 하는 방법에 대 한 지침 및 정보입니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c235562834ae78a12b690fcd1b96d6a3640e0c66
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371667"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-단일 서버에 대한 Azure Database에서 SSL 연결 구성

PostgreSQL용 Azure 데이터베이스는 SSL(Secure Sockets Layer)을 사용해서 PostgreSQL 서비스에 클라이언트 애플리케이션을 연결하는 것을 선호합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간에 SSL 연결을 적용 하면 서버와 응용 프로그램 간에 데이터 스트림을 암호화 하 여 "메시지 가로채기 (man-in-the-middle)" 공격 으로부터 보호 합니다.

기본적으로 PostgreSQL 데이터베이스 서비스는 SSL 연결을 요구하도록 구성됩니다. 클라이언트 응용 프로그램이 SSL 연결을 지원 하지 않는 경우 SSL 필요를 사용 하지 않도록 선택할 수 있습니다.

## <a name="enforcing-ssl-connections"></a>SSL 연결 적용

Azure Portal 및 CLI를 통해 프로비전된 모든 MySQL용 Azure 데이터베이스 서버의 경우 SSL 연결 적용이 기본적으로 활성화됩니다. 

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 SSL을 사용하여 데이터베이스 서버에 연결하기 위한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

## <a name="configure-enforcement-of-ssl"></a>SSL 적용 구성

필요에 따라 SSL 연결 적용을 사용하지 않도록 설정할 수 있습니다. Microsoft Azure는 항상 향상된 보안을 위해 **SSL 연결 적용** 설정을 사용하는 것을 권장합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

PostgreSQL용 Azure 데이터베이스 서버를 방문하여 **연결 보안**을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정합니다. 그런 다음 **저장**을 클릭합니다.

![연결 보안 - SSL 적용 사용 안 함](./media/concepts-ssl-connection-security/1-disable-ssl.png)

**SSL 적용 상태** 표시기를 확인할 수 있는 **개요** 페이지에서 설정을 확인할 수 있습니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

Azure CLI에서 **또는** 값을 각각 사용하여 `Enabled`ssl-enforcement`Disabled` 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>애플리케이션 또는 프레임워크가 SSL 연결을 지원하는지 확인합니다.

데이터베이스 서비스에 대해 PostgreSQL를 사용 하는 일부 응용 프로그램 프레임 워크는 설치 하는 동안 기본적으로 SSL을 사용 하도록 설정 하지 않습니다. PostgreSQL 서버에서 SSL 연결을 적용 하지만 응용 프로그램이 SSL로 구성 되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결 하지 못할 수 있습니다. 애플리케이션의 설명서를 참조하여 SSL 연결을 설정하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>SSL 연결을 위해 인증서 확인이 필요한 애플리케이션

경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. Azure Database for PostgreSQL 서버에 연결 하기 위한 인증서는 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem에 있습니다. 인증서 파일을 다운로드 하 여 원하는 위치에 저장 합니다.

### <a name="connect-using-psql"></a>Psql을 사용하여 연결

다음 예제에서는 psql 명령줄 유틸리티를 사용 하 여 PostgreSQL 서버에 연결 하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용 하 여 SSL 인증서 확인을 적용 합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수에 전달 합니다.

다음 명령은 psql 연결 문자열의 예입니다.

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> `sslrootcert` 전달 된 값이 저장 한 인증서의 파일 경로와 일치 하는지 확인 합니다.

## <a name="tls-connectivity-in-azure-database-for-postgresql-single-server"></a>단일 서버 Azure Database for PostgreSQL의 TLS 연결

Azure Database for PostgreSQL-단일 서버는 TLS (전송 계층 보안)를 사용 하 여 데이터베이스 서버에 연결 하는 클라이언트에 대 한 암호화를 지원 합니다. TLS는 데이터베이스 서버와 클라이언트 응용 프로그램 간에 보안 네트워크 연결을 보장 하 여 규정 준수 요구 사항을 준수할 수 있게 해 주는 업계 표준 프로토콜입니다.

### <a name="tls-settings"></a>TLS 설정

이제 고객은 Azure Database for PostgreSQL 단일 서버에 연결 하는 클라이언트에 TLS 버전을 적용할 수 있습니다. TLS 옵션을 사용 하려면 **최소 Tls 버전** 옵션 설정을 사용 합니다. 이 옵션 설정에 허용 되는 값은 다음과 같습니다.

|  최소 TLS 설정             | 지원 되는 TLS 버전                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (기본값) | TLS 필요 없음                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 이상 |
| TLS1_1                           | TLS 1.1, TLS 1.2 이상          |
| TLS1_2                           | TLS 버전 1.2 이상           |


예를 들어이 최소 TLS 설정 버전을 TLS 1.0로 설정 하면 서버에서 TLS 1.0, 1.1 및 1.2 +를 사용 하는 클라이언트의 연결을 허용 하는 것입니다. 또는이를 1.2으로 설정 하면 TLS 1.2을 사용 하는 클라이언트의 연결만 허용 하 고 TLS 1.0 및 TLS 1.1을 사용한 모든 연결이 거부 됩니다.

> [!Note] 
> 단일 서버 기본값은 모든 새 서버에서 TLS를 사용 하지 않도록 설정 Azure Database for PostgreSQL 합니다.
>
> 현재 byAzure Database for PostgreSQL가 지 원하는 TLS 버전은 TLS 1.0, 1.1 및 1.2입니다.

Azure Database for PostgreSQL 단일 서버에 대 한 TLS 설정을 설정 하는 방법에 대 한 자세한 내용은 [tls 설정을 구성 하는 방법](howto-tls-configurations.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL에 대 한 연결 라이브러리](concepts-connection-libraries.md)에서 다양 한 응용 프로그램 연결 옵션을 검토 합니다.
