---
title: 방화벽 규칙 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: 이 문서에서는 PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에 대한 방화벽 규칙에 대해 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975570"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL에 대 한 Azure 데이터베이스에서 방화벽 규칙 - 하이퍼 스케일 (Citus)
PostgreSQL 서버 방화벽용 Azure 데이터베이스는 권한이 있는 컴퓨터를 지정할 때까지 하이퍼스케일(Citus) 코디네이터 노드에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 서버에 대한 액세스를 허용합니다.
방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 수준의 방화벽 규칙을 만들 수 있습니다.

**방화벽 규칙:** 이러한 규칙을 사용하면 클라이언트가 하이퍼스케일(Citus) 코디네이터 노드, 즉 동일한 논리 서버 내의 모든 데이터베이스에 액세스할 수 있습니다. 서버 수준 방화벽 규칙은 Azure 포털을 사용하여 구성할 수 있습니다. 서버 수준 방화벽 규칙을 만들려면 구독 소유자 또는 구독 참가자여야 합니다.

## <a name="firewall-overview"></a>방화벽 개요
코디네이터 노드에 대한 모든 데이터베이스 액세스는 기본적으로 방화벽에 의해 차단됩니다. 다른 컴퓨터에서 서버를 사용하려면 해당 서버에 대한 액세스를 허용하는 하나 이상의 서버 수준 방화벽 규칙을 지정해야 합니다. 방화벽 규칙을 사용하여 허용할 인터넷에서의 IP 주소 범위를 지정합니다. Azure Portal 웹 사이트 자체에 대한 액세스는 이 방화벽 규칙의 영향을 받지 않습니다.
인터넷과 Azure로부터의 연결 시도는 다음 다이어그램과 같이 PostgreSQL Database에 연결하기 전에 먼저 방화벽을 통과해야 합니다.

![방화벽 작동 방식을 보여 주는 예제 흐름](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>인터넷 및 Azure에서 연결

그룹의 코디네이터 노드에 연결할 수 있는 하이퍼스케일(Citus) 서버 그룹 방화벽 제어. 방화벽은 구성 가능한 규칙 목록을 참조하여 액세스를 결정합니다. 각 규칙은 허용되는 IP 주소 또는 주소 범위입니다.

방화벽이 연결을 차단하면 응용 프로그램 오류가 발생할 수 있습니다. 예를 들어 PostgreSQL JDBC 드라이버를 사용하면 다음과 같은 오류가 발생합니다.

> java.util.concurrent.ExecutionException: 자바.lang.RuntimeException: org.postgresql.util.PSQLException: 치명적: 호스트에 대한 PG\_hba.conf 항목 "123.45.67.890", 사용자 "시투스", 데이터베이스 "시투스", SSL

[방화벽 규칙 만들기 및 관리를](howto-hyperscale-manage-firewall-using-portal.md) 참조하여 규칙이 정의되는 방법을 알아봅니다.

## <a name="troubleshooting-the-database-server-firewall"></a>데이터베이스 서버 방화벽 문제 해결
PostgreSQL - 하이퍼스케일(Citus) 서비스에 대한 Microsoft Azure 데이터베이스에 대한 액세스가 예상대로 작동하지 않는 경우 다음 사항을 고려하십시오.

* **허용 목록에 대한 변경 사항은 아직 적용되지 않았습니다.** 하이퍼스케일(Citus) 방화벽 구성을 변경하는 데 최대 5분 정도 지연될 수 있습니다.

* **사용자에게 권한이 없거나 잘못된 암호를 사용했습니다.** 사용자가 서버에 대한 사용 권한이 없거나 사용된 암호가 올바르지 않으면 서버에 대한 연결이 거부됩니다. 방화벽 설정을 생성하면 클라이언트에게 서버 연결을 시도할 수 있는 기회만 제공되며 각 클라이언트는 필요한 보안 자격 증명을 제공해야 합니다.

예를 들어 JDBC 클라이언트를 사용하는 경우 다음 오류가 나타날 수 있습니다.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **동적 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.

* 하이퍼스케일(Citus) 코디네이터 노드에 액세스하는 클라이언트 컴퓨터에 할당된 IP 주소 범위에 대해 ISP(인터넷 서비스 공급자)에게 요청한 다음 IP 주소 범위를 방화벽 규칙으로 추가합니다.

* 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.

## <a name="next-steps"></a>다음 단계
서버 수준 및 데이터베이스 수준 방화벽 규칙 만들기에 대한 문서를 보려면 다음을 참조하세요.
* [Azure Portal을 사용한 PostgreSQL용 Azure 데이터베이스 방화벽 규칙 만들기 및 관리](howto-hyperscale-manage-firewall-using-portal.md)
