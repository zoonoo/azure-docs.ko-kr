---
title: 방화벽 규칙 - Azure Database for PostgreSQL - 단일 서버
description: 이 문서에서는 방화벽 규칙을 사용하여 Azure DB for PostgreSQL - 단일 서버에 연결하는 방법을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: ba353cf41cf3876a681f8f18d4121401260ff4ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98877173"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Azure DB for PostgreSQL - 단일 서버의 방화벽 규칙
Azure DB for PostgreSQL 서버는 서버에 액세스할 수 있도록 허용할 IP 호스트를 지정할 때까지 데이터베이스 서버에 대한 모든 액세스를 방지하기 위해 기본적으로 보호됩니다. 방화벽은 각 요청이 시작된 IP 주소의 서버에 대한 액세스를 허용합니다.
방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 수준의 방화벽 규칙을 만들 수 있습니다.

**방화벽 규칙:** 이 규칙은 모든 PostgreSQL용 Azure 데이터베이스 서버, 즉, 동일한 논리 서버 내의 모든 데이터베이스에 클라이언트가 액세스할 수 있도록 합니다. Azure Portal 또는 Azure CLI 명령을 사용하여 서버 수준 방화벽 규칙을 구성할 수 있습니다. 서버 수준 방화벽 규칙을 만들려면 구독 소유자 또는 구독 참가자여야 합니다.

## <a name="firewall-overview"></a>방화벽 개요
기본적으로 방화벽을 통해 Azure DB for PostgreSQL 서버에 대한 모든 액세스가 차단됩니다. 다른 컴퓨터/클라이언트 또는 애플리케이션에서 서버에 액세스하려면 서버에 액세스할 수 있도록 하나 이상의 서버 수준 방화벽 규칙을 지정해야 합니다. 방화벽 규칙을 사용하여 허용되는 공용 IP 주소 범위를 지정할 수 있습니다. Azure Portal 웹 사이트 자체에 대한 액세스는 이 방화벽 규칙의 영향을 받지 않습니다.
인터넷과 Azure로부터의 연결 시도를 하려면 다음 다이어그램과 같이 PostgreSQL Database에 연결하기 전에 먼저 방화벽을 통과해야 합니다.

:::image type="content" source="media/concepts-firewall-rules/1-firewall-concept.png" alt-text="방화벽 작동 방식을 보여 주는 예제 흐름":::

## <a name="connecting-from-the-internet"></a>인터넷에서 연결하기
서버 수준 방화벽 규칙은 동일한 Azure Database for PostgreSQL 서버에 있는 모든 데이터베이스에 적용됩니다. 요청된 원본 IP 주소가 서버 수준 방화벽 규칙에 지정된 범위 안에 있을 경우 연결이 허용되며 그렇지 않은 경우에는 거부됩니다. 예를 들어 애플리케이션에서 PostgreSQL용 JDBC 드라이버에 연결하는 경우 방화벽이 연결을 차단할 때 연결하려고 하면 다음 오류가 발생할 수 있습니다.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Azure에서 연결
애플리케이션 또는 서비스의 발신 IP 주소를 찾고 이러한 개별 IP 주소 또는 범위에 명시적으로 액세스를 허용하는 것이 좋습니다. 예를 들어, Azure App Service의 나가는 IP 주소를 찾거나 가상 컴퓨터 또는 다른 리소스에 연결된 공용 IP를 사용할 수 있습니다(서비스 엔드포인트를 통해 가상 머신의 개인 IP와 연결하는 방법에 대한 정보는 아래 참조). 

고정된 발신 IP 주소를 Azure 서비스에 사용할 수 없는 경우 모든 Azure 데이터 센터 IP 주소에서 연결을 설정하는 것이 좋습니다. 이 설정은 Azure Portal에서 **Azure 서비스에 대한 액세스 허용** 옵션을 **연결 보안** 창에서 **켜짐** 으로 설정하고 **저장** 을 눌러 설정할 수 있습니다. Azure CLI에서 시작 주소와 끝 주소가 0.0.0.0인 방화벽 규칙 설정이 이와 동일합니다. 방화벽 규칙에 의해 연결 시도가 거부되면 Azure DB for PostgreSQL 서버에 연결되지 않습니다.

> [!IMPORTANT]
> **Azure 서비스에 대한 액세스 허용** 옵션은 다른 고객의 구독으로부터의 연결을 비롯한 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
> 

:::image type="content" source="media/concepts-firewall-rules/allow-azure-services.png" alt-text="포털에서 Azure 서비스 방문 허용 구성":::

## <a name="connecting-from-a-vnet"></a>VNet에서 연결
VNet에서 Azure DB for PostgreSQL 서버에 안전하게 연결하려면 [VNet 서비스 엔드포인트](./concepts-data-access-and-security-vnet.md)를 사용하는 것이 좋습니다. 

## <a name="programmatically-managing-firewall-rules"></a>방화벽 규칙을 프로그래밍 방식으로 관리
Azure Portal 외에도 Azure CLI를 사용하여 방화벽 규칙을 프로그래밍 방식으로 관리할 수 있습니다.
[Azure CLI를 사용한 PostgreSQL용 Azure 데이터베이스 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-cli.md)도 참조하세요.

## <a name="troubleshooting-firewall-issues"></a>방화벽 문제 해결
PostgreSQL용 Microsoft Azure 데이터베이스 서버 서비스로의 연결이 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

* **허용 목록의 변경사항이 아직 적용되지 않았습니다.** PostgreSQL용 Azure 데이터베이스 서버 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **로그인이 올바르지 않거나 암호가 올바르지 않습니다.** 로그인에 PostgreSQL용 Azure 데이터베이스 서버에 대한 권한이 없거나 사용한 암호가 틀렸을 경우 PostgreSQL용 Azure 데이터베이스 서버에 대한 연결이 거부됩니다. 방화벽 설정을 생성하면 클라이언트에게 서버 연결을 시도할 수 있는 기회만 제공되며 각 클라이언트는 필요한 보안 자격 증명을 제공해야 합니다.

   예를 들어 JDBC 클라이언트를 사용하는 경우 다음 오류가 나타날 수 있습니다.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **동적 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.

   * ISP(인터넷 서비스 공급자)는 PostgreSQL용 Azure 데이터베이스 서버에 연결될 클라이언트에 할당된 IP 주소 범위를 요청하고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.

   * 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.

* **공용으로 표시되는 서버 IP:** Azure DB for PostgreSQL 서버로의 연결은 공용으로 액세스할 수 있는 Azure 게이트웨이를 통해 라우팅됩니다. 그러나 실제 서버 IP는 방화벽으로 보호됩니다. 자세한 내용은 [연결 아키텍처 문서](concepts-connectivity-architecture.md)를 참조하세요.

* **허용되는 IP를 사용하여 Azure 리소스에서 연결할 수 없음:** 연결 중인 서브넷에 대해 **Microsoft.Sql** 서비스 엔드포인트를 사용할 수 있는지 여부를 확인합니다. **Microsoft.Sql** 을 사용하는 경우에는 해당 서브넷에서 [VNet 서비스 엔드포인트 규칙](concepts-data-access-and-security-vnet.md)만 사용하려고 함을 나타냅니다.

   예를 들어, **Microsoft.Sql** 을 사용하지만 해당 VNet 규칙이 없는 서브넷의 Azure VM에서 연결하는 경우 다음과 같은 오류가 표시될 수 있습니다.  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* **IPv6 형식에 방화벽 규칙을 사용할 수 없음:** 방화벽 규칙은 IPv4 형식이어야 합니다. IPv6 형식으로 방화벽 규칙을 지정하는 경우 유효성 검사 오류가 표시됩니다.


## <a name="next-steps"></a>다음 단계
* [Azure Portal을 사용한 PostgreSQL용 Azure 데이터베이스 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-portal.md)
* [Azure CLI를 사용한 PostgreSQL용 Azure 데이터베이스 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-cli.md)
* [Azure DB for PostgreSQL의 VNet 서비스 엔드포인트](./concepts-data-access-and-security-vnet.md)
