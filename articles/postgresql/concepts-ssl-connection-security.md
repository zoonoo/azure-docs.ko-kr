---
title: SSL/TLS-Azure Database for PostgreSQL-단일 서버
description: Azure Database for PostgreSQL-단일 서버에 대 한 TLS 연결을 구성 하는 방법에 대 한 지침 및 정보입니다.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c98ee8f747975d4237c2906be2060eddbc7b9990
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123272"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 단일 서버에서 TLS 연결 구성

Azure Database for PostgreSQL TLS (Transport Layer Security)를 사용 하 여 클라이언트 응용 프로그램을 PostgreSQL 서비스에 연결 하는 것을 선호 합니다 (이전에는 SSL (SSL(Secure Sockets Layer)) 이라고 함). 데이터베이스 서버와 클라이언트 응용 프로그램 간에 TLS 연결을 적용 하면 서버와 응용 프로그램 간에 데이터 스트림을 암호화 하 여 "메시지 가로채기 (man-in-the-middle)" 공격 으로부터 보호 하는 데 도움이 됩니다.

기본적으로 PostgreSQL 데이터베이스 서비스는 TLS 연결을 요구 하도록 구성 되어 있습니다. 클라이언트 응용 프로그램이 TLS 연결을 지원 하지 않는 경우 TLS를 요구 하지 않도록 선택할 수 있습니다.

>[!NOTE]
> 고객의 의견에 따라 2021 (02/15/2021)까지 기존 Baltimore 루트 CA에 대 한 루트 인증서 사용 중단을 연장 했습니다.

> [!IMPORTANT] 
> SSL 루트 인증서가 2021 (02/15/2021)부터 만료 되도록 설정 되어 있습니다. [새 인증서](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)를 사용 하도록 응용 프로그램을 업데이트 하십시오. 자세히 알아보려면 [계획 된 인증서 업데이트](concepts-certificate-rotation.md) 를 참조 하세요.


## <a name="enforcing-tls-connections"></a>TLS 연결 적용

Azure Portal 및 CLI를 통해 프로 비전 된 모든 Azure Database for PostgreSQL 서버에 대해 TLS 연결 적용이 기본적으로 사용 됩니다. 

마찬가지로 Azure Portal의 서버에 있는 "연결 문자열" 설정에서 미리 정의 된 연결 문자열에는 TLS를 사용 하 여 데이터베이스 서버에 연결 하는 데 사용 되는 공용 언어에 대 한 필수 매개 변수가 포함 됩니다. TLS 매개 변수는 커넥터에 따라 달라 집니다 (예: "ssl = true" 또는 "sslmode = required" 또는 "sslmode = required" 및 기타 변형).

## <a name="configure-enforcement-of-tls"></a>TLS의 적용 구성

필요에 따라 TLS 연결 적용을 해제할 수 있습니다. 보안 강화를 위해 항상 **SSL 연결 적용** 설정을 사용 하도록 권장 Microsoft Azure 합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

PostgreSQL용 Azure 데이터베이스 서버를 방문하여 **연결 보안**을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정합니다. 그런 다음 **저장**을 클릭합니다.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="연결 보안-TLS/SSL 적용 사용 안 함":::

**SSL 적용 상태** 표시기를 확인할 수 있는 **개요** 페이지에서 설정을 확인할 수 있습니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

Azure CLI에서 `Enabled` 또는 `Disabled` 값을 각각 사용하여 **ssl-enforcement** 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>응용 프로그램 또는 프레임 워크에서 TLS 연결을 지원 하는지 확인

데이터베이스 서비스에 대해 PostgreSQL를 사용 하는 일부 응용 프로그램 프레임 워크는 설치 하는 동안 기본적으로 TLS를 사용 하도록 설정 하지 않습니다. PostgreSQL 서버에서 TLS 연결을 적용 하지만 응용 프로그램이 TLS에 대해 구성 되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결 하지 못할 수 있습니다. 응용 프로그램의 설명서를 참조 하 여 TLS 연결을 사용 하도록 설정 하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 연결에 대 한 인증서 확인이 필요한 응용 프로그램

경우에 따라 응용 프로그램은 신뢰할 수 있는 CA (인증 기관) 인증서 파일에서 생성 된 로컬 인증서 파일을 안전 하 게 연결 해야 합니다. Azure Database for PostgreSQL 서버에 연결할 인증서는에 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 있습니다. 인증서 파일을 다운로드 하 여 원하는 위치에 저장 합니다. 

소 버린 클라우드의 서버 인증서에 대 한 다음 링크 ( [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure 중국](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)및 [azure 독일](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt))를 참조 하세요.

### <a name="connect-using-psql"></a>Psql을 사용 하 여 연결

다음 예제에서는 psql 명령줄 유틸리티를 사용 하 여 PostgreSQL 서버에 연결 하는 방법을 보여 줍니다. `sslmode=verify-full`연결 문자열 설정을 사용 하 여 TLS/SSL 인증서 확인을 적용 합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수로 전달 합니다.

다음 명령은 psql 연결 문자열의 예입니다.

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 에 전달 된 값이 `sslrootcert` 저장 한 인증서의 파일 경로와 일치 하는지 확인 합니다.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에서 TLS 적용

Azure Database for PostgreSQL-단일 서버는 TLS (전송 계층 보안)를 사용 하 여 데이터베이스 서버에 연결 하는 클라이언트에 대 한 암호화를 지원 합니다. TLS는 데이터베이스 서버와 클라이언트 응용 프로그램 간에 보안 네트워크 연결을 보장 하 여 규정 준수 요구 사항을 준수할 수 있게 해 주는 업계 표준 프로토콜입니다.

### <a name="tls-settings"></a>TLS 설정

Azure Database for PostgreSQL 단일 서버는 클라이언트 연결에 TLS 버전을 적용할 수 있는 기능을 제공 합니다. TLS 버전을 적용 하려면 **최소 tls 버전** 옵션 설정을 사용 합니다. 이 옵션 설정에 허용 되는 값은 다음과 같습니다.

|  최소 TLS 설정             | 지원 되는 클라이언트 TLS 버전                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (기본값) | TLS 필요 없음                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 이상 |
| TLS1_1                           | TLS 1.1, TLS 1.2 이상          |
| TLS1_2                           | TLS 버전 1.2 이상           |


예를 들어이 최소 TLS 설정 버전을 TLS 1.0로 설정 하면 서버에서 TLS 1.0, 1.1 및 1.2 +를 사용 하는 클라이언트의 연결을 허용 하는 것입니다. 또는이를 1.2로 설정 하면 TLS 1.2 +를 사용 하는 클라이언트만 연결을 허용 하 고 TLS 1.0 및 TLS 1.1를 사용 하는 모든 연결이 거부 됩니다.

> [!Note] 
> 기본적으로 Azure Database for PostgreSQL는 최소 TLS 버전 (설정)을 적용 하지 않습니다 `TLSEnforcementDisabled` .
>
> 최소 TLS 버전을 적용 한 후에는 나중에 최소 버전 적용을 사용 하지 않도록 설정할 수 없습니다.

Azure Database for PostgreSQL 단일 서버에 대 한 TLS 설정을 설정 하는 방법에 대 한 자세한 내용은 [tls 설정을 구성 하는 방법](howto-tls-configurations.md)을 참조 하세요.

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>단일 서버 Azure Database for PostgreSQL 암호화 지원

SSL/TLS 통신의 일부로 암호 그룹의 유효성을 검사 하 고 암호화 짝패만 데이터베이스 서비스와 통신할 수 있습니다. 암호 그룹 유효성 검사는 [게이트웨이 계층](concepts-connectivity-architecture.md#connectivity-architecture) 에서 제어 되며 노드 자체에서 명시적으로 제어 되지 않습니다. 암호 그룹이 아래 나열 된 도구 모음 중 하 나와 일치 하지 않으면 들어오는 클라이언트 연결이 거부 됩니다.

### <a name="cipher-suite-supported"></a>지원 되는 암호 그룹

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL에 대 한 연결 라이브러리](concepts-connection-libraries.md)에서 다양 한 응용 프로그램 연결 옵션을 검토 합니다.

- [TLS를 구성](howto-tls-configurations.md) 하는 방법 알아보기