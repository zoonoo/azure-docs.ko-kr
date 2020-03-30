---
title: SSL - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: PostgreSQL - 단일 서버에 대한 Azure 데이터베이스에 대한 SSL 연결을 구성하는 방법에 대한 지침 및 정보입니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477003"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>PostgreSQL - 단일 서버에 대한 Azure 데이터베이스에서 SSL 연결 구성

PostgreSQL용 Azure 데이터베이스는 SSL(Secure Sockets Layer)을 사용해서 PostgreSQL 서비스에 클라이언트 애플리케이션을 연결하는 것을 선호합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간에 SSL 연결을 적용하면 서버와 응용 프로그램 간의 데이터 스트림을 암호화하여 "중간자" 공격으로부터 보호할 수 있습니다.

기본적으로 PostgreSQL 데이터베이스 서비스는 SSL 연결을 요구하도록 구성됩니다. 클라이언트 응용 프로그램이 SSL 연결을 지원하지 않는 경우 SSL 필요를 사용하지 않도록 선택할 수 있습니다.

## <a name="enforcing-ssl-connections"></a>SSL 연결 적용

Azure Portal 및 CLI를 통해 프로비전된 모든 MySQL용 Azure 데이터베이스 서버의 경우 SSL 연결 적용이 기본적으로 활성화됩니다. 

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 SSL을 사용하여 데이터베이스 서버에 연결하기 위한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

## <a name="configure-enforcement-of-ssl"></a>SSL 적용 구성

필요에 따라 SSL 연결 적용을 사용하지 않도록 설정할 수 있습니다. 보안 강화를 위해 항상 **SSL 연결** 적용을 사용하도록 설정하는 것이 좋습니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

PostgreSQL용 Azure 데이터베이스 서버를 방문하여 **연결 보안**을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정합니다. 그런 다음 **저장**을 클릭합니다.

![연결 보안 - SSL 적용 사용 안 함](./media/concepts-ssl-connection-security/1-disable-ssl.png)

**SSL 적용 상태** 표시기를 확인할 수 있는 **개요** 페이지에서 설정을 확인할 수 있습니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

Azure CLI에서 `Enabled` 또는 `Disabled` 값을 각각 사용하여 **ssl-enforcement** 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>애플리케이션 또는 프레임워크가 SSL 연결을 지원하는지 확인합니다.

데이터베이스 서비스에 PostgreSQL을 사용하는 일부 응용 프로그램 프레임워크는 설치 하는 동안 기본적으로 SSL을 사용 하지 않습니다. PostgreSQL 서버가 SSL 연결을 적용하지만 응용 프로그램이 SSL용으로 구성되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결하지 못할 수 있습니다. 애플리케이션의 설명서를 참조하여 SSL 연결을 설정하는 방법을 알아보세요.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>SSL 연결을 위해 인증서 확인이 필요한 애플리케이션

경우에 따라 안전한 연결을 위해 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다. PostgreSQL 서버에 대한 Azure 데이터베이스에 연결하는 인증서가 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem에 있습니다. 인증서 파일을 다운로드하여 원하는 위치에 저장합니다.

### <a name="connect-using-psql"></a>psql을 사용하여 연결

다음 예제에서는 psql 명령줄 유틸리티를 사용 하 여 PostgreSQL 서버에 연결 하는 방법을 보여 줍니다. `sslmode=verify-full` 연결 문자열 설정을 사용하여 SSL 인증서 확인을 적용합니다. 로컬 인증서 파일 경로를 `sslrootcert` 매개 변수에 전달합니다.

다음 명령은 psql 연결 문자열의 예입니다.

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 저장된 인증서의 파일 `sslrootcert` 경로와 일치하는 값으로 전달된 값이 일치하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[PostgreSQL에 대한 Azure 데이터베이스에 대한 연결 라이브러리에서](concepts-connection-libraries.md)다양한 응용 프로그램 연결 옵션을 검토합니다.
