---
title: SQL Fqdn을 사용 하 여 응용 프로그램 Azure 방화벽 규칙 구성
description: 이 문서에서는 Azure 방화벽 응용 프로그램 규칙에서 SQL Fqdn을 구성 하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786574"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>SQL Fqdn을 사용 하 여 응용 프로그램 Azure 방화벽 규칙 구성

> [!IMPORTANT]
> SQL Fqdn을 사용 하 여 azure 방화벽 응용 프로그램 규칙은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이제 SQL Fqdn을 사용 하 여 Azure 방화벽 응용 프로그램 규칙을 구성할 수 있습니다. 이 옵션을 사용 하면 지정 된 SQL server 인스턴스만에 대 한 가상 네트워크에서 액세스를 제한할 수 있습니다.

SQL Fqdn을 사용 하 여 트래픽을 필터링 할 수 있습니다.

- Azure SQL Database 또는 Azure SQL Data Warehouse를 하기 위해 Vnet에서 합니다. 예를 들어: 에 대 한 액세스 허용 *sql server1.database.windows.net*합니다.
- 온-프레미스에서 Azure SQL 관리 되는 인스턴스 또는 SQL IaaS Vnet에서 실행 합니다.
- 스포크-에-스포크에서 Azure SQL 관리 되는 인스턴스 또는 SQL IaaS Vnet에서 실행 합니다.

공개 미리 보기에서 필터링 하는 SQL FQDN에서 지원 됩니다 [프록시 모드](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) 만 (포트 1433). 기본 리디렉션 모드에서 SQL을 사용 하는 경우의 일환으로 SQL 서비스 태그를 사용 하 여 액세스를 필터링 할 수 있습니다 [네트워크 규칙](overview.md#network-traffic-filtering-rules)합니다.
SQL IaaS 트래픽에 대해 기본이 아닌 포트를 사용 하는 경우에 응용 프로그램 방화벽 규칙에서 해당 포트를 구성할 수 있습니다.

> [!NOTE]
> SQL Fqdn을 사용 하 여 응용 프로그램 규칙은 Azure CLI, REST 및 템플릿을 통해 모든 지역에서 현재 사용할 수 있습니다. 포털 사용자 인터페이스 증분 영역에 추가 되 고 롤아웃이 완료 되 면 모든 지역에서 제공 됩니다.

## <a name="configure-using-azure-cli"></a>Azure CLI를 사용 하 여 구성

1. 배포를 [Azure CLI를 사용 하 여 Azure 방화벽](deploy-cli.md)합니다.
2. 트래픽을 Azure SQL Database, SQL Data Warehouse 또는 SQL Managed Instance를 필터링 할 경우 SQL 연결 모드가 설정 되어 있는지 확인 **프록시**합니다. SQL 연결 모드를 전환 하는 방법에 알아보려면 참조 [Azure SQL 연결 아키텍처](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy)합니다. 

   > [!NOTE]
   > SQL *프록시* 모드에 비해 더 많은 대기 시간이 발생할 수 있습니다 *리디렉션*합니다. Azure 내에서 연결 하는 클라이언트에 대 한 기본값인 리디렉션 모드를 사용 하 여 계속 하려는 경우 SQL을 사용 하 여 액세스를 필터링 할 수 있습니다 [서비스 태그](service-tags.md) 방화벽에서 [네트워크 규칙](tutorial-firewall-deploy-portal.md#configure-a-network-rule)합니다.

3. SQL server에 액세스할 수 있도록 SQL FQDN을 사용 하 여 응용 프로그램 규칙을 구성 합니다.

   ```azurecli
   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Azure portal을 사용 하 여 구성
1. 배포를 [Azure CLI를 사용 하 여 Azure 방화벽](deploy-cli.md)합니다.
2. 트래픽을 Azure SQL Database, SQL Data Warehouse 또는 SQL Managed Instance를 필터링 할 경우 SQL 연결 모드가 설정 되어 있는지 확인 **프록시**합니다. SQL 연결 모드를 전환 하는 방법에 알아보려면 참조 [Azure SQL 연결 아키텍처](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy)합니다. 

   > [!NOTE]
   > SQL *프록시* 모드에 비해 더 많은 대기 시간이 발생할 수 있습니다 *리디렉션*합니다. Azure 내에서 연결 하는 클라이언트에 대 한 기본값인 리디렉션 모드를 사용 하 여 계속 하려는 경우 SQL을 사용 하 여 액세스를 필터링 할 수 있습니다 [서비스 태그](service-tags.md) 방화벽에서 [네트워크 규칙](tutorial-firewall-deploy-portal.md#configure-a-network-rule)합니다.
3. 적절 한 프로토콜, 포트 및 SQL FQDN을 사용 하 여 응용 프로그램 규칙을 추가 하 고 선택한 **저장할**합니다.
   ![SQL FQDN을 사용 하 여 응용 프로그램 규칙](media/sql-fqdn-filtering/application-rule-sql.png)
4. SQL 방화벽을 통해 트래픽을 필터링 하는 VNet에서 가상 컴퓨터에서 액세스 합니다. 
5. 유효성 검사 [Azure 방화벽 로그](log-analytics-samples.md) 표시 트래픽이 허용 됩니다.

## <a name="next-steps"></a>다음 단계

SQL 프록시에 대해 알아보고 리디렉션 모드를 참조 하세요 [Azure SQL database 연결 아키텍처](../sql-database/sql-database-connectivity-architecture.md)합니다.