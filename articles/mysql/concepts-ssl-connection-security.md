---
title: SSL/TLS 연결-Azure Database for MySQL
description: SSL 연결을 올바르게 사용하도록 MySQL용 Azure 데이터베이스 및 연결된 애플리케이션을 구성하는 방법에 대한 정보
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 97f9635cf6bba9ee276e6775c1718a5d83e9af3e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282129"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Azure Database for MySQL의 SSL/TLS 연결

MySQL용 Azure 데이터베이스는 SSL(Secure Sockets Layer)을 사용하여 데이터베이스 서버를 클라이언트 애플리케이션에 연결하도록 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다.

> [!NOTE]
> `require_secure_transport`서버 매개 변수 값을 업데이트 해도 MySQL 서비스의 동작에는 영향을 주지 않습니다. 이 문서에 설명 된 SSL 및 TLS 적용 기능을 사용 하 여에 대 한 연결을 보호 합니다.

## <a name="ssl-default-settings"></a>SSL 기본 설정

기본적으로 데이터베이스 서비스는 MySQL에 연결할 때 SSL 연결이 필요하도록 구성되어야 합니다.  가능하면 SSL 옵션을 해제하지 않는 것이 좋습니다.

Azure Portal 및 CLI를 통해 새로운 MySQL용 Azure 데이터베이스 서버를 프로비전할 때 SSL 연결 적용이 기본적으로 활성화됩니다. 

다양한 프로그래밍 언어에 대한 연결 문자열이 Azure Portal에 표시됩니다. 이러한 연결 문자열에는 데이터베이스에 연결하는 데 필요한 SSL 매개 변수가 포함되어 있습니다. Azure Portal에서 VM을 선택합니다. **설정** 제목 아래에서 **연결 문자열**을 선택합니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

경우에 따라 응용 프로그램은 신뢰할 수 있는 CA (인증 기관) 인증서 파일에서 생성 된 로컬 인증서 파일을 안전 하 게 연결 해야 합니다. 현재 고객은 미리 정의 된 인증서 **만 사용** 하 여에 있는 Azure Database for MySQL 서버에 연결할 수 있습니다 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

마찬가지로, 다음 링크는 소 버린 클라우드의 서버에 대 한 인증서 ( [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure 중국](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)및 [azure 독일](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt))를 가리킵니다.

애플리케이션을 개발할 때 SSL 연결을 사용하거나 사용하지 않도록 설정하는 방법을 알아보려면 [SSL 구성 방법](howto-configure-ssl.md)을 참조하세요.

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 TLS 적용

Azure Database for MySQL는 TLS (전송 계층 보안)를 사용 하 여 데이터베이스 서버에 연결 하는 클라이언트에 대 한 암호화를 지원 합니다. TLS는 데이터베이스 서버와 클라이언트 응용 프로그램 간에 보안 네트워크 연결을 보장 하 여 규정 준수 요구 사항을 준수할 수 있게 해 주는 업계 표준 프로토콜입니다.

### <a name="tls-settings"></a>TLS 설정

Azure Database for MySQL은 클라이언트 연결에 TLS 버전을 적용할 수 있는 기능을 제공 합니다. TLS 버전을 적용 하려면 **최소 tls 버전** 옵션 설정을 사용 합니다. 이 옵션 설정에 허용 되는 값은 다음과 같습니다.

|  최소 TLS 설정             | 지원 되는 클라이언트 TLS 버전                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (기본값) | TLS 필요 없음                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 이상           |
| TLS1_1                           | TLS 1.1, TLS 1.2 이상                   |
| TLS1_2                           | TLS 버전 1.2 이상                     |


예를 들어 최소 TLS 설정 버전 값을 TLS 1.0로 설정 하면 서버에서 TLS 1.0, 1.1 및 1.2 +를 사용 하는 클라이언트의 연결을 허용 한다는 의미입니다. 또는이를 1.2로 설정 하면 TLS 1.2 +를 사용 하는 클라이언트만 연결을 허용 하 고 TLS 1.0 및 TLS 1.1를 사용 하는 모든 연결이 거부 됩니다.

> [!Note] 
> 기본적으로 Azure Database for MySQL는 최소 TLS 버전 (설정)을 적용 하지 않습니다 `TLSEnforcementDisabled` .
>
> 최소 TLS 버전을 적용 한 후에는 나중에 최소 버전 적용을 사용 하지 않도록 설정할 수 없습니다.

Azure Database for MySQL에 대 한 TLS 설정을 설정 하는 방법에 대 한 자세한 내용은 [tls 설정을 구성 하는 방법](howto-tls-configurations.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [MySQL용 Azure 데이터베이스에 대한 연결 라이브러리](concepts-connection-libraries.md)
- [SSL을 구성](howto-configure-ssl.md) 하는 방법 알아보기
- [TLS를 구성](howto-tls-configurations.md) 하는 방법 알아보기
