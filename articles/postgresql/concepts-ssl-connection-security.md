---
title: SSL/TLS - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL - 단일 서버에 대해 TLS 연결을 구성하는 방법에 관한 지침과 정보입니다.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c98ee8f747975d4237c2906be2060eddbc7b9990
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000949"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버에서 TLS 연결 구성

Azure Database for PostgreSQL은 이전에 SSL(Secure Sockets Layer)로 알려진 TLS(전송 계층 보안)를 사용하여 PostgreSQL 서비스에 클라이언트 애플리케이션을 연결하는 것을 선호합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간에 TLS 연결을 적용하면 서버와 애플리케이션 간의 데이터 스트림을 암호화하여 “메시지 가로채기(man-in-the-middle)” 공격으로부터 보호할 수 있습니다.

기본적으로 PostgreSQL 데이터베이스 서비스는 TLS 연결을 요구하도록 구성됩니다. 클라이언트 애플리케이션에서 TLS 연결을 지원하지 않는 경우 TLS 요구를 사용하지 않도록 선택할 수 있습니다.

>[!NOTE]
> 고객의 피드백에 따라 2021년 2월 15일까지 기존 볼티모어 루트 CA에 대한 루트 인증서 사용 중단 기간을 연장했었습니다.

> [!IMPORTANT] 
> SSL 루트 인증서가 2021년 2월 15일에 만료되도록 설정되어 있습니다. [새 인증서](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)를 사용하도록 애플리케이션을 업데이트해 주세요. 자세히 알아보려면 [계획된 인증서 업데이트](concepts-certificate-rotation.md)를 참조하세요.


## <a name="enforcing-tls-connections"></a>TLS 연결 적용

Azure Portal 및 CLI를 통해 프로비저닝된 모든 Azure Database for PostgreSQL 서버의 경우 기본적으로 TLS 연결 적용이 사용하도록 설정됩니다. 

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 TLS를 사용하여 데이터베이스 서버에 연결하기 위한 공용 언어의 필수 매개 변수가 포함됩니다. TLS 매개 변수는 “ssl=true”, “sslmode=require” 또는 “sslmode=required”와 같은 커넥터 및 다른 변형에 따라 달라집니다.

## <a name="configure-enforcement-of-tls"></a>TLS 적용 구성

필요에 따라 TLS 연결 적용을 사용하지 않도록 설정할 수 있습니다. Microsoft Azure는 항상 향상된 보안을 위해 **SSL 연결 적용** 설정을 사용하는 것을 권장합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

PostgreSQL용 Azure 데이터베이스 서버를 방문하여 **연결 보안** 을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정합니다. 그런 다음 **저장** 을 클릭합니다.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="연결 보안 - TLS/SSL 적용 사용 안 함":::

**SSL 적용 상태** 표시기를 확인할 수 있는 **개요** 페이지에서 설정을 확인할 수 있습니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

Azure CLI에서 `Enabled` 또는 `Disabled` 값을 각각 사용하여 **ssl-enforcement** 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>애플리케이션 또는 프레임워크가 TLS 연결을 지원하는지 확인

데이터베이스 서비스용 PostgreSQL을 사용하는 일부 애플리케이션 프레임워크는 기본적으로 설치하는 동안 TLS를 사용하도록 설정하지 않습니다. PostgreSQL 서버가 TLS 연결을 적용하지만 애플리케이션이 TLS에 대해 구성되지 않은 경우 애플리케이션이 데이터베이스 서버에 연결하지 못할 수 있습니다. 애플리케이션의 설명서를 참조하여 TLS 연결을 사용하도록 설정하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 연결에 대한 인증서 확인이 필요한 애플리케이션

경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일에서 생성되는 로컬 인증서 파일이 필요합니다. Azure Database for PostgreSQL 서버에 연결할 인증서는 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 에 있습니다. 인증서 파일을 다운로드하여 원하는 위치에 저장합니다. 

소버린 클라우드의 서버 인증서에 대한 링크([Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure 중국](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 및 [Azure 독일](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt))를 참조하세요.

### <a name="connect-using-psql"></a>psql을 사용하여 연결

다음 예제에서는 psql 명령줄 유틸리티를 사용하여 PostgreSQL 서버에 연결하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용하여 TLS/SSL 인증서 확인을 적용합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수에 전달합니다.

다음 명령은 psql 연결 문자열의 예제입니다.

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> `sslrootcert`에 전달된 값이 저장한 인증서의 파일 경로와 일치하는지 확인합니다.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버의 TLS 적용

Azure Database for PostgreSQL - 단일 서버 TLS(전송 계층 보안)를 사용하여 데이터베이스 서버에 연결하는 클라이언트에 대한 암호화를 지원합니다. TLS는 데이터베이스 서버와 클라이언트 애플리케이션 간에 보안 네트워크 연결을 보장하여 규정 준수 요구 사항을 준수할 수 있도록 하는 업계 표준 프로토콜입니다.

### <a name="tls-settings"></a>TLS 설정

Azure Database for PostgreSQL 단일 서버는 클라이언트 연결에 TLS 버전을 적용하는 기능을 제공합니다. TLS 버전을 적용하려면 **최소 TLS 버전** 옵션 설정을 사용합니다. 이 옵션 설정에 허용되는 값은 다음과 같습니다.

|  최소 TLS 설정             | 지원되는 클라이언트 TLS 버전                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled(기본값) | TLS 필요 없음                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 이상 |
| TLS1_1                           | TLS 1.1, TLS 1.2 이상          |
| TLS1_2                           | TLS 버전 1.2 이상           |


예를 들어, 이 최소 TLS 설정 버전을 TLS 1.0으로 설정하면 서버에서 TLS 1.0, 1.1, 1.2 이상을 사용하는 클라이언트의 연결을 허용한다는 의미입니다. 또는 이를 1.2로 설정하면 TLS 1.2 이상을 사용하는 클라이언트의 연결만 허용되며 TLS 1.0과 TLS 1.1을 사용하는 모든 연결이 거부됩니다.

> [!Note] 
> 기본적으로 Azure Database for PostgreSQL은 최소 TLS 버전(설정 `TLSEnforcementDisabled`)을 적용하지 않습니다.
>
> 최소 TLS 버전을 적용한 후에는 나중에 최소 버전 적용을 사용하지 않도록 설정할 수 없습니다.

Azure Database for PostgreSQL 단일 서버의 TLS 설정을 지정하는 방법에 관한 자세한 내용은 [TLS 설정을 구성하는 방법](howto-tls-configurations.md)을 참조하세요.

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에서 암호화 지원

SSL/TLS 통신의 일부로 암호 그룹의 유효성을 검사하고 암호 그룹만 데이터베이스 서비스와 통신할 수 있습니다. 암호 그룹 유효성 검사는 [게이트웨이 계층](concepts-connectivity-architecture.md#connectivity-architecture)에서 제어되며 노드 자체에서 명시적으로 제어되지 않습니다. 암호 그룹이 아래 나열된 도구 모음 중 하나와 일치하지 않으면 들어오는 클라이언트 연결이 거부됩니다.

### <a name="cipher-suite-supported"></a>지원되는 암호 그룹

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL을 위한 연결 라이브러리](concepts-connection-libraries.md)에서 다양한 애플리케이션 연결 옵션을 검토합니다.

- [TLS를 구성](howto-tls-configurations.md)하는 방법 알아보기