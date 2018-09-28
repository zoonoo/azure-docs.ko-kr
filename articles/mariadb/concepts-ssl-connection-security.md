---
title: Azure Database for MariaDB에 대한 SSL 연결
description: SSL 연결을 올바르게 사용하도록 Azure Database for MariaDB 및 연결된 응용 프로그램을 구성하는 방법에 대한 정보
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: dda5df58a83ddd3ce42fa887c3c32a3e23954920
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946649"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 SSL 연결
Azure Database for MariaDB는 SSL(Secure Sockets Layer)을 사용하여 데이터베이스 서버를 클라이언트 응용 프로그램에 연결하도록 지원합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간 SSL 연결을 적용하면 서버와 응용 프로그램 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다.

## <a name="default-settings"></a>기본 설정
기본적으로 데이터베이스 서비스는 MariaDB에 연결할 때 SSL 연결이 필요하도록 구성되어야 합니다.  가능하면 SSL 옵션을 해제하지 않는 것이 좋습니다.

Azure Portal 및 CLI를 통해 새로운 Azure Database for MariaDB 서버를 프로비전할 때 SSL 연결 적용이 기본적으로 활성화됩니다.

다양한 프로그래밍 언어에 대한 연결 문자열이 Azure Portal에 표시됩니다. 이러한 연결 문자열에는 데이터베이스에 연결하는 데 필요한 SSL 매개 변수가 포함되어 있습니다. Azure Portal에서 VM을 선택합니다. **설정** 제목 아래에서 **연결 문자열**을 선택합니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

<!-- To learn how to enable or disable SSL connection when developing application, refer to [How to configure SSL](howto-configure-ssl.md).-->

## <a name="next-steps"></a>다음 단계
- [서버 방화벽 규칙](concepts-firewall-rules.md)에 대해 자세히 알아보기
- [SSL을 구성](howto-configure-ssl.md)하는 방법에 대해 알아봅니다.
