---
title: SQL FQDNs를 사용하여 Azure 방화벽 응용 프로그램 규칙 구성
description: 이 문서에서는 Azure 방화벽 응용 프로그램 규칙에서 SQL FQD를 구성하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501504"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>SQL FQDNs를 사용하여 Azure 방화벽 응용 프로그램 규칙 구성

> [!IMPORTANT]
> SQL FQDNs를 사용하는 Azure 방화벽 응용 프로그램 규칙은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이제 SQL FQDN을 사용하여 Azure 방화벽 응용 프로그램 규칙을 구성할 수 있습니다. 이렇게 하면 가상 네트워크의 액세스를 지정된 SQL 서버 인스턴스로만 제한할 수 있습니다.

SQL FQDN을 사용하면 트래픽을 필터링할 수 있습니다.

- VNet에서 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에 이르기까지. 예: *sql-server1.database.windows.net*에 대한 액세스만 허용합니다.
- 온-프레미스에서 Azure SQL 관리 인스턴스 또는 VNet에서 실행되는 SQL IaaS에 이르기까지.
- VNet에서 실행되는 Azure SQL 관리 인스턴스 또는 SQL IaaS에 대한 스포크-투-스포크

공개 미리 보기 중에 SQL FQDN 필터링은 [프록시 모드(포트](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) 1433)에서만 지원됩니다. 기본 리디렉션 모드에서 SQL을 사용하는 경우 [네트워크 규칙의](overview.md#network-traffic-filtering-rules)일부로 SQL 서비스 태그를 사용하여 액세스를 필터링할 수 있습니다.
SQL IaaS 트래픽에 기본이 아닌 포트를 사용하는 경우 방화벽 응용 프로그램 규칙에서 해당 포트를 구성할 수 있습니다.

SQL FQDNs를 사용 하 여 응용 프로그램 규칙은 현재 Azure 포털, Azure CLI, REST 및 템플릿을 통해 모든 지역에서 사용할 수 있습니다.

## <a name="configure-using-azure-cli"></a>Azure CLI를 사용하여 구성

1. Azure [CLI를 사용하여 Azure 방화벽을](deploy-cli.md)배포합니다.
2. Azure SQL Database, SQL 데이터 웨어하우스 또는 SQL 관리 인스턴스로 트래픽을 필터링하는 경우 SQL 연결 모드가 **프록시로**설정되어 있는지 확인합니다. SQL 연결 모드를 전환하는 방법에 대해 알아보려면 [Azure SQL 연결 설정](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)을 참조하십시오.

   > [!NOTE]
   > SQL *프록시* 모드는 *리디렉션에*비해 대기 시간이 더 늘어질 수 있습니다. Azure 내에서 연결하는 클라이언트의 기본값인 리디렉션 모드를 계속 사용하려면 방화벽 [네트워크 규칙에서](tutorial-firewall-deploy-portal.md#configure-a-network-rule)SQL [서비스 태그를](service-tags.md) 사용하여 액세스를 필터링할 수 있습니다.

3. SQL 서버에 대한 액세스를 허용하도록 SQL FQDN을 사용하여 응용 프로그램 규칙을 구성합니다.

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Azure Portal을 사용한 구성
1. Azure [CLI를 사용하여 Azure 방화벽을](deploy-cli.md)배포합니다.
2. Azure SQL Database, SQL 데이터 웨어하우스 또는 SQL 관리 인스턴스로 트래픽을 필터링하는 경우 SQL 연결 모드가 **프록시로**설정되어 있는지 확인합니다. SQL 연결 모드를 전환하는 방법에 대해 알아보려면 [Azure SQL 연결 설정](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)을 참조하십시오.  

   > [!NOTE]
   > SQL *프록시* 모드는 *리디렉션에*비해 대기 시간이 더 늘어질 수 있습니다. Azure 내에서 연결하는 클라이언트의 기본값인 리디렉션 모드를 계속 사용하려면 방화벽 [네트워크 규칙에서](tutorial-firewall-deploy-portal.md#configure-a-network-rule)SQL [서비스 태그를](service-tags.md) 사용하여 액세스를 필터링할 수 있습니다.
3. 적절한 프로토콜, 포트 및 SQL FQDN을 사용 하 여 응용 프로그램 규칙을 추가 한 다음 **저장을**선택 합니다.
   ![SQL FQDN을 사용 하 여 응용 프로그램 규칙](media/sql-fqdn-filtering/application-rule-sql.png)
4. 방화벽을 통해 트래픽을 필터링하는 VNet의 가상 컴퓨터에서 SQL에 액세스합니다. 
5. Azure [방화벽 로그에서](log-analytics-samples.md) 트래픽이 허용되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

SQL 프록시 및 리디렉션 모드에 대해 자세히 알아보려면 [Azure SQL 데이터베이스 연결 아키텍처를](../sql-database/sql-database-connectivity-architecture.md)참조하십시오.