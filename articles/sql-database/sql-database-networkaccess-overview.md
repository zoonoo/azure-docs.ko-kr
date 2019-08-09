---
title: Azure SQL Database 및 데이터 웨어하우스 네트워크 액세스 제어 | Microsoft Docs
description: 액세스를 관리 하 고 단일 또는 풀링된 데이터베이스를 구성 하기 위한 Azure SQL Database 및 데이터 웨어하우스의 네트워크 액세스 제어 개요입니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 4722cda0506493671ff872d75e7376f3de74b1b8
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886651"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database 및 데이터 웨어하우스 네트워크 액세스 제어

> [!NOTE]
> 이 문서는 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하자면, SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다.

> [!IMPORTANT]
> 이 문서는 **Azure SQL Database Managed Instance**에 적용되지 *않습니다*. 네트워킹 구성에 대 한 자세한 내용은 [Managed Instance에 연결](sql-database-managed-instance-connect-app.md) 을 참조 하세요.

[Azure Portal에서](sql-database-single-database-get-started.md)새 Azure SQL Server를 만들 때 결과는 *yourservername.database.windows.net*형식의 공용 끝점입니다. 설계상 공용 끝점에 대 한 모든 액세스는 거부 됩니다. 그런 다음, 다음 네트워크 액세스 제어를 사용 하 여 공용 끝점을 통해 SQl Database에 대 한 액세스를 선택적으로 허용할 수 있습니다.
- Azure 서비스 허용: 사용으로 설정 되 면 azure 경계 내의 다른 리소스 (예: Azure Virtual Machine)는에 액세스할 수 있습니다 SQL Database

- IP 방화벽 규칙:-이 기능을 사용 하 여 온-프레미스 컴퓨터 등 특정 IP 주소에서의 연결을 명시적으로 허용 합니다.

- Virtual Network 방화벽 규칙:-이 기능을 사용 하 여 Azure 경계 내에서 특정 Virtual Network의 트래픽을 허용 합니다.


## <a name="allow-azure-services"></a>Azure 서비스 허용 
[Azure Portal에서](sql-database-single-database-get-started.md)새 Azure SQL Server를 만드는 동안이 설정은 선택 하지 않은 상태로 유지 됩니다.

 ![새 서버 만들기의 스크린샷][1]

다음과 같이 Azure SQL Server를 만든 후 방화벽 창을 통해이 설정을 변경할 수도 있습니다.
  
 ![서버 방화벽 관리 스크린샷][2]

Azure SQL Server **에서** 로 설정 하면 azure 경계 내의 모든 리소스에서 통신할 수 있으며,이는 구독의 일부가 될 수도 있고 그렇지 않을 수도 있습니다.

대부분의 경우에는 대부분의 고객이 원하는 것 보다는 **ON** 설정이 더 허용 됩니다. 이 설정을 **OFF** 로 설정 하 고 더 제한적인 IP 방화벽 규칙 또는 Virtual Network 방화벽 규칙으로 대체 하는 것이 좋습니다. 이렇게 하면 다음 기능이 영향을 받습니다.

### <a name="import-export-service"></a>가져오기/내보내기 서비스

Azure SQL Database 가져오기/내보내기 서비스는 Azure의 VM에서 실행됩니다. 이러한 VM은 VNet에 있지 않으므로 데이터베이스에 연결할 때 Azure IP를 가져옵니다. **Azure 서비스의 서버 액세스 허용**을 제거하면 이러한 VM에서 데이터베이스에 액세스할 수 없게 됩니다.
DACFx API를 사용 하 여 코드에서 직접 BACPAC 가져오기 또는 내보내기를 실행 하 여 문제를 해결할 수 있습니다.

### <a name="sql-database-query-editor"></a>SQL Database 쿼리 편집기

Azure SQL Database 쿼리 편집기는 Azure의 VM에 배포됩니다. 이러한 VM은 VNet에 있지 않습니다. 따라서 VM은 데이터베이스에 연결할 때 Azure IP를 가져옵니다. **Azure 서비스의 서버 액세스 허용**을 제거하면 이러한 VM에서 데이터베이스에 액세스할 수 없게 됩니다.

### <a name="table-auditing"></a>테이블 감사

현재 SQL Database에서 감사를 사용 하도록 설정 하는 방법에는 두 가지가 있습니다. Azure SQL Server에서 서비스 엔드포인트를 활성화한 후에는 테이블 감사에 실패합니다. 여기서 완화책은 Blob 감사로 이동하는 것입니다.

### <a name="impact-on-data-sync"></a>데이터 동기화에 대한 영향

Azure SQL Database에는 Azure IP를 사용하여 데이터베이스에 연결하는 데이터 동기화 기능이 있습니다. 서비스 끝점을 사용 하는 경우 Azure 서비스에서 SQL Database 서버에 대 한 **서버 액세스를 허용** 하도록 설정 하 고 데이터 동기화 기능을 중단 합니다.

## <a name="ip-firewall-rules"></a>IP 방화벽 규칙
Ip 기반 방화벽은 클라이언트 컴퓨터의 [ip 주소](sql-database-server-level-firewall-rule.md) 를 명시적으로 추가할 때까지 데이터베이스 서버에 대 한 모든 액세스를 차단 하는 Azure SQL Server의 기능입니다.


## <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙

IP 규칙 외에도 Azure SQL Server 방화벽을 사용 하 여 *가상 네트워크 규칙*을 정의할 수 있습니다.  
자세한 내용은 [Azure SQL Database에 대한 Virtual Network 서비스 엔드포인트 및 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 참조하세요.

 ### <a name="azure-networking-terminology"></a>Azure 네트워킹 용어  
Virtual Network 방화벽 규칙을 탐색 하면서 다음과 같은 Azure 네트워킹 용어를 알고 있어야 합니다.

**가상 네트워크:** Azure 구독과 연결 된 가상 네트워크를 사용할 수 있습니다. 

**서브넷:** 가상 네트워크에 **서브넷**이 포함됩니다. 소유한 Azure VM(가상 머신)은 서브넷에 할당됩니다. 하나의 서브넷에 여러 VM 또는 다른 컴퓨팅 노드가 포함될 수 있습니다. 액세스를 허용하도록 보안을 구성해야 가상 네트워크 외부의 컴퓨팅 노드가 가상 네트워크에 액세스할 수 있습니다.

**Virtual Network 서비스 엔드포인트:** [Virtual Network service endpoint] [vm-가상-네트워크-서비스-끝점-b y d]는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함 된 서브넷입니다. 이 문서에서는 SQL Database라는 Azure 서비스를 나타내는 **Microsoft.Sql**의 형식 이름을 살펴봅니다.

**가상 네트워크 규칙:** SQL Database 서버에 대한 가상 네트워크 규칙은 SQL Database 서버의 ACL(액세스 제어 목록)에 나열된 서브넷입니다. SQL Database에 대한 ACL에 나열되려면 서브넷에 **Microsoft.Sql** 형식 이름이 있어야 합니다. 가상 네트워크 규칙은 서브넷에 있는 모든 노드에서 보낸 통신을 수락하도록 SQL Database 서버에 지시합니다.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 및 Virtual Network 방화벽 규칙

Azure SQL Server 방화벽을 사용 하 여 SQL Database에 대 한 통신을 허용 하는 IP 주소 범위를 지정할 수 있습니다. 이 방법은 Azure 프라이빗 네트워크 외부에 있는 안정적인 IP 주소에 적합합니다. 그러나 Azure 개인 네트워크 내의 가상 머신 (Vm)는 *동적* IP 주소로 구성 됩니다. 동적 IP 주소는 VM이 다시 시작 될 때 변경 될 수 있으며, 그런 다음 IP 기반 방화벽 규칙을 무효화 합니다. 프로덕션 환경의 방화벽 규칙에서는 동적 IP 주소를 지정하면 안 됩니다.

VM에 대 한 *고정* IP 주소를 가져오면 이러한 제한을 해결할 수 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 가상 컴퓨터에 대 한 개인 IP 주소 구성] [vm--------------------------------------------- 그러나 고정 IP 접근 방식은 관리 하기 어려울 수 있으며 대규모로 작업 하는 경우 비용이 많이 듭니다. 

가상 네트워크 규칙은 Vm이 포함 된 특정 서브넷의 액세스를 설정 하 고 관리 하는 보다 쉬운 방법입니다.

> [!NOTE]
> 아직 서브넷에 SQL Database가 있을 수 없습니다. Azure SQL Database 서버가 가상 네트워크에 있는 서브넷의 노드인 경우 가상 네트워크 내의 모든 노드가 SQL Database와 통신할 수 있습니다. 이 경우 VM은 가상 네트워크 규칙이나 IP 규칙이 없어도 SQL Database와 통신할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 서버 수준 IP 방화벽 규칙을 만드는 방법에 대 한 빠른 시작은 [AZURE SQL 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조 하세요.

- 서버 수준 Vnet 방화벽 규칙을 만드는 방법에 대 한 빠른 시작은 [Virtual Network 서비스 끝점 및 Azure SQL Database에 대 한 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 참조 하세요.

- 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL database에 연결 하는 데 도움이 필요한 경우 [클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조 하 여 SQL Database 합니다.

- 추가로 열어야 하는 포트에 대한 자세한 내용은 **SQL Database: 내부 및 외부** 섹션([ADO.NET 4.5 및 SQL Database에 대한 1433 이외 포트](sql-database-develop-direct-route-ports-adonet-v12.md))를 참조하세요.

- Azure SQL Database 연결에 대 한 개요는 [AZURE SQL 연결 아키텍처](sql-database-connectivity-architecture.md) 를 참조 하세요.

- Azure SQL Database 보안 개요는 [데이터베이스 보안 설정](sql-database-security-overview.md)을 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
