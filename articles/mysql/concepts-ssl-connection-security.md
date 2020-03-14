---
title: SSL 연결-Azure Database for MySQL
description: SSL 연결을 올바르게 사용하도록 MySQL용 Azure 데이터베이스 및 연결된 애플리케이션을 구성하는 방법에 대한 정보
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 7bc3a238ca2ff2861ec6fc65033738e741574321
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370851"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스의 SSL 연결

MySQL용 Azure 데이터베이스는 SSL(Secure Sockets Layer)을 사용하여 데이터베이스 서버를 클라이언트 애플리케이션에 연결하도록 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다.

## <a name="ssl-default-settings"></a>SSL 기본 설정

기본적으로 데이터베이스 서비스는 MySQL에 연결할 때 SSL 연결이 필요하도록 구성되어야 합니다.  가능하면 SSL 옵션을 해제하지 않는 것이 좋습니다.

Azure Portal 및 CLI를 통해 새로운 MySQL용 Azure 데이터베이스 서버를 프로비전할 때 SSL 연결 적용이 기본적으로 활성화됩니다. 

다양한 프로그래밍 언어에 대한 연결 문자열이 Azure Portal에 표시됩니다. 이러한 연결 문자열에는 데이터베이스에 연결하는 데 필요한 SSL 매개 변수가 포함되어 있습니다. Azure Portal에서 VM을 선택합니다. **설정** 제목 아래에서 **연결 문자열**을 선택합니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

애플리케이션을 개발할 때 SSL 연결을 사용하거나 사용하지 않도록 설정하는 방법을 알아보려면 [SSL 구성 방법](howto-configure-ssl.md)을 참조하세요.

## <a name="tls-connectivity-in-azure-database-for-mysql"></a>Azure Database for MySQL의 TLS 연결

Azure Database for MySQL는 TLS (전송 계층 보안)를 사용 하 여 데이터베이스 서버에 연결 하는 클라이언트에 대 한 암호화를 지원 합니다. TLS는 데이터베이스 서버와 클라이언트 응용 프로그램 간에 보안 네트워크 연결을 보장 하 여 규정 준수 요구 사항을 준수할 수 있게 해 주는 업계 표준 프로토콜입니다.

### <a name="tls-settings"></a>TLS 설정

이제 고객에 게 Azure Database for MySQL에 연결 하는 클라이언트에 TLS 버전을 적용할 수 있습니다. TLS 옵션을 사용 하려면 **최소 Tls 버전** 옵션 설정을 사용 합니다. 이 옵션 설정에 허용 되는 값은 다음과 같습니다.

|  최소 TLS 설정             | 지원 되는 TLS 버전                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (기본값) | TLS 필요 없음                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 이상 |
| TLS1_1                           | TLS 1.1, TLS 1.2 이상          |
| TLS1_2                           | TLS 버전 1.2 이상           |


예를 들어이 최소 TLS 설정 버전을 TLS 1.0로 설정 하면 서버에서 TLS 1.0, 1.1 및 1.2 +를 사용 하는 클라이언트의 연결을 허용 하는 것입니다. 또는이를 1.2으로 설정 하면 TLS 1.2을 사용 하는 클라이언트의 연결만 허용 하 고 TLS 1.0 및 TLS 1.1을 사용한 모든 연결이 거부 됩니다.

> [!Note] 
> Azure Database for MySQL 기본값은 모든 새 서버에서 TLS를 사용 하지 않도록 설정 됩니다.
>
> 현재 Azure Database for MySQL에서 지 원하는 TLS 버전은 TLS 1.0, 1.1 및 1.2입니다.

Azure Database for MySQL에 대 한 TLS 설정을 설정 하는 방법에 대 한 자세한 내용은 [tls 설정을 구성 하는 방법](howto-tls-configurations.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[MySQL용 Azure 데이터베이스에 대한 연결 라이브러리](concepts-connection-libraries.md)
