---
title: TLS-Azure Database for PostgreSQL-단일 서버
description: Azure Database for PostgreSQL-단일 서버에 대 한 TLS 연결을 구성 하는 방법에 대 한 지침 및 정보입니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141737"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 단일 서버에서 TLS 연결 구성

Azure Database for PostgreSQL TLS (Transport Layer Security)를 사용 하 여 클라이언트 응용 프로그램을 PostgreSQL 서비스에 연결 하는 것을 선호 합니다 (이전에는 SSL (SSL(Secure Sockets Layer)) 이라고 함). 데이터베이스 서버와 클라이언트 응용 프로그램 간에 TLS 연결을 적용 하면 서버와 응용 프로그램 간에 데이터 스트림을 암호화 하 여 "메시지 가로채기 (man-in-the-middle)" 공격 으로부터 보호 하는 데 도움이 됩니다.

기본적으로 PostgreSQL 데이터베이스 서비스는 TLS 연결을 요구 하도록 구성 되어 있습니다. 클라이언트 응용 프로그램이 TLS 연결을 지원 하지 않는 경우 TLS를 요구 하지 않도록 선택할 수 있습니다.

## <a name="enforcing-tls-connections"></a>TLS 연결 적용

Azure Portal 및 CLI를 통해 프로 비전 된 모든 Azure Database for PostgreSQL 서버에 대해 TLS 연결 적용이 기본적으로 사용 됩니다. 

마찬가지로 Azure Portal의 서버에 있는 "연결 문자열" 설정에서 미리 정의 된 연결 문자열에는 TLS를 사용 하 여 데이터베이스 서버에 연결 하는 데 사용 되는 공용 언어에 대 한 필수 매개 변수가 포함 됩니다. TLS 매개 변수는 커넥터에 따라 달라 집니다 (예: "ssl = true" 또는 "sslmode = required" 또는 "sslmode = required" 및 기타 변형).

## <a name="configure-enforcement-of-tls"></a>TLS의 적용 구성

필요에 따라 TLS 연결 적용을 해제할 수 있습니다. 보안 강화를 위해 항상 **SSL 연결 적용** 설정을 사용 하도록 권장 Microsoft Azure 합니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

PostgreSQL용 Azure 데이터베이스 서버를 방문하여 **연결 보안**을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정합니다. 그런 다음 **저장**을 클릭합니다.

![연결 보안-TLS/SSL 적용 사용 안 함](./media/concepts-ssl-connection-security/1-disable-ssl.png)

**SSL 적용 상태** 표시기를 확인할 수 있는 **개요** 페이지에서 설정을 확인할 수 있습니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

Azure CLI에서 `Enabled` 또는 `Disabled` 값을 각각 사용하여 **ssl-enforcement** 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>응용 프로그램 또는 프레임 워크에서 TLS 연결을 지원 하는지 확인

데이터베이스 서비스에 대해 PostgreSQL를 사용 하는 일부 응용 프로그램 프레임 워크는 설치 하는 동안 기본적으로 TLS를 사용 하도록 설정 하지 않습니다. PostgreSQL 서버에서 TLS 연결을 적용 하지만 응용 프로그램이 TLS에 대해 구성 되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결 하지 못할 수 있습니다. 응용 프로그램의 설명서를 참조 하 여 TLS 연결을 사용 하도록 설정 하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>TLS 연결에 대 한 인증서 확인이 필요한 응용 프로그램

경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. Azure Database for PostgreSQL 서버에 연결할 인증서는에 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem있습니다. 인증서 파일을 다운로드 하 여 원하는 위치에 저장 합니다.

### <a name="connect-using-psql"></a>Psql을 사용 하 여 연결

다음 예제에서는 psql 명령줄 유틸리티를 사용 하 여 PostgreSQL 서버에 연결 하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용 하 여 TLS/SSL 인증서 확인을 적용 합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수로 전달 합니다.

다음 명령은 psql 연결 문자열의 예입니다.

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 에 `sslrootcert` 전달 된 값이 저장 한 인증서의 파일 경로와 일치 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL에 대 한 연결 라이브러리](concepts-connection-libraries.md)에서 다양 한 응용 프로그램 연결 옵션을 검토 합니다.
