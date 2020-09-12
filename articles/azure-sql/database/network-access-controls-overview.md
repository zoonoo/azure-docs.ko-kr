---
title: 네트워크 액세스 컨트롤
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Azure SQL Database 및 Azure Synapse Analytics (이전의 SQL Data Warehouse)에 대 한 네트워크 액세스를 관리 하 고 제어 하는 방법에 대 한 개요입니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: caad78bf61c9ad470464d69c7320aa1d08dcee09
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435374"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database 및 Azure Synapse Analytics 네트워크 액세스 제어

Azure SQL Database 및 Azure Synapse Analytics에 대 한 [Azure Portal](single-database-create-quickstart.md) 에서 논리 SQL server를 만들 때 결과는 *yourservername.database.windows.net*형식의 공용 끝점입니다.

다음 네트워크 액세스 제어를 사용 하 여 공용 끝점을 통해 데이터베이스에 대 한 액세스를 선택적으로 허용할 수 있습니다.

- Azure 서비스 허용: 설정 된 경우 azure 가상 컴퓨터와 같은 Azure 경계 내의 다른 리소스는에 액세스할 수 있습니다 SQL Database
- IP 방화벽 규칙:이 기능을 사용 하 여 온-프레미스 컴퓨터 등 특정 IP 주소에서의 연결을 명시적으로 허용 합니다.

다음을 통해 [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 에서 데이터베이스에 대 한 개인 액세스를 허용할 수도 있습니다.

- 가상 네트워크 방화벽 규칙:이 기능을 사용 하 여 Azure 경계 내에서 특정 가상 네트워크의 트래픽을 허용 합니다.
- 개인 링크:이 기능을 사용 하 여 특정 가상 네트워크 내에서 [논리 SQL server](logical-servers.md) 에 대 한 개인 끝점을 만들 수 있습니다.

> [!IMPORTANT]
> 이 문서는 **SQL Managed Instance**에는 적용 *되지* 않습니다. 네트워킹 구성에 대 한 자세한 내용은 [AZURE SQL Managed Instance에 연결](../managed-instance/connect-application-instance.md) 을 참조 하세요.

이러한 액세스 제어 및 기능에 대 한 개략적인 설명은 아래 비디오를 참조 하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure 서비스 허용

[Azure Portal에서](single-database-create-quickstart.md)새 논리 SQL server를 만드는 동안이 설정은 선택 하지 않은 상태로 유지 됩니다.

다음과 같이 논리 SQL server를 만든 후 방화벽 창을 통해이 설정을 변경할 수도 있습니다.
  
![서버 방화벽 관리 스크린샷][2]

**ON**으로 설정 하면 서버는 Azure 경계 내의 모든 리소스에서 통신을 허용 하며,이는 구독에 포함 되거나 포함 되지 않을 수 있습니다.

대부분의 경우에는 대부분의 고객이 원하는 것 보다는 **ON** 설정이 더 허용 됩니다. 이 설정을 **OFF** 로 설정 하 고 더 제한적인 IP 방화벽 규칙 또는 가상 네트워크 방화벽 규칙으로 바꿀 수 있습니다. 

그러나 가상 네트워크의 일부가 아닌 Azure의 가상 머신에서 실행 되는 다음 기능에 영향을 주므로 Azure IP 주소를 통해 데이터베이스에 연결 합니다.

### <a name="import-export-service"></a>가져오기/내보내기 서비스

**Azure 서비스에 대 한 액세스 허용** 이 **OFF**로 설정 되어 있으면 가져오기 내보내기 서비스가 작동 하지 않습니다. 그러나 [AZURE VM에서 sqlpackage.exe를 수동으로 실행 하거나](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) DACFx API를 사용 하 여 코드에서 직접 내보내기를 수행 하 여 문제를 해결할 수 있습니다.

### <a name="data-sync"></a>데이터 동기화

**Azure 서비스에 대 한 액세스 허용** 을 **OFF**로 설정 하 여 데이터 동기화 기능을 사용 하려면 **허브** 데이터베이스를 호스트 하는 지역에 대 한 **Sql 서비스 태그** 에서 [IP 주소를 추가](firewall-create-server-level-portal-quickstart.md) 하는 개별 방화벽 규칙 항목을 만들어야 합니다.
**허브** 와 **구성원** 데이터베이스를 호스트 하는 서버에 이러한 서버 수준 방화벽 규칙을 추가 합니다 (다른 지역에 있을 수 있음).

다음 PowerShell 스크립트를 사용 하 여 미국 서 부 지역에 대 한 SQL 서비스 태그에 해당 하는 IP 주소를 생성 합니다.

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> AzNetworkServiceTag은 Location 매개 변수를 지정 하는 경우에도 SQL 서비스 태그의 전역 범위를 반환 합니다. 동기화 그룹에서 사용 하는 허브 데이터베이스를 호스트 하는 지역으로 필터링 해야 합니다.

PowerShell 스크립트의 출력은 클래스 없는 CIDR (도메인 간 라우팅) 표기법으로 작성 됩니다. 다음과 같이 [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) 를 사용 하 여 시작 및 끝 IP 주소 형식으로 변환 해야 합니다.

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

이 추가 PowerShell 스크립트를 사용 하 여 모든 IP 주소를 CIDR에서 Start 및 End IP 주소 형식으로 변환할 수 있습니다.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

이제 이러한 규칙을 별도의 방화벽 규칙으로 추가한 다음 **Azure 서비스에서 서버에 액세스 하도록 허용**  을 OFF로 설정할 수 있습니다.

## <a name="ip-firewall-rules"></a>IP 방화벽 규칙

Ip 기반 방화벽은 클라이언트 컴퓨터의 [ip 주소](firewall-create-server-level-portal-quickstart.md) 를 명시적으로 추가할 때까지 서버에 대 한 모든 액세스를 차단 하는 Azure의 논리적 SQL server 기능입니다.

## <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙

IP 규칙 외에도 서버 방화벽을 사용 하 여 *가상 네트워크 규칙*을 정의할 수 있습니다.  
자세한 내용은 [Azure SQL Database의 가상 네트워크 서비스 끝점 및 규칙](vnet-service-endpoint-rule-overview.md) 을 참조 하거나이 비디오를 시청 하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Azure 네트워킹 용어

가상 네트워크 방화벽 규칙을 살펴보기 위해 다음 Azure 네트워킹 용어를 알고 있어야 합니다.

**가상 네트워크:** Azure 구독과 연결 된 가상 네트워크를 사용할 수 있습니다.

**서브넷:** 가상 네트워크에 **서브넷**이 포함됩니다. 소유한 Azure VM(가상 머신)은 서브넷에 할당됩니다. 하나의 서브넷에 여러 VM 또는 다른 컴퓨팅 노드가 포함될 수 있습니다. 가상 네트워크 외부에 있는 계산 노드는 액세스를 허용 하도록 보안을 구성 하지 않는 한 가상 네트워크에 액세스할 수 없습니다.

**가상 네트워크 서비스 끝점:** [가상 네트워크 서비스 끝점](../../virtual-network/virtual-network-service-endpoints-overview.md) 은 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함 된 서브넷입니다. 이 문서에서는 SQL Database 이라는 Azure 서비스를 참조 하는 **Microsoft .sql**의 형식 이름에 관심이 있습니다.

**가상 네트워크 규칙:** 서버에 대 한 가상 네트워크 규칙은 서버의 ACL (액세스 제어 목록)에 나열 되는 서브넷입니다. SQL Database에서 데이터베이스에 대 한 ACL에 있도록 하려면 서브넷에 **Microsoft .sql** 형식 이름을 포함 해야 합니다. 가상 네트워크 규칙은 서브넷에 있는 모든 노드의 통신을 허용 하도록 서버에 지시 합니다.

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP와 가상 네트워크 방화벽 규칙 비교

Azure SQL Database 방화벽을 사용 하 여 SQL Database에 대 한 통신을 허용 하는 IP 주소 범위를 지정할 수 있습니다. 이 방법은 Azure 프라이빗 네트워크 외부에 있는 안정적인 IP 주소에 적합합니다. 그러나 Azure 개인 네트워크 내의 가상 머신 (Vm)는 *동적* IP 주소로 구성 됩니다. 동적 IP 주소는 VM이 다시 시작 될 때 변경 될 수 있으며, 그런 다음 IP 기반 방화벽 규칙을 무효화 합니다. 프로덕션 환경의 방화벽 규칙에서는 동적 IP 주소를 지정하면 안 됩니다.

VM에 대 한 *고정* IP 주소를 가져오면 이러한 제한을 해결할 수 있습니다. 자세한 내용은 Azure Portal를 [사용 하 여 고정 공용 IP 주소](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md)를 사용 하 여 가상 머신 만들기를 참조 하세요. 그러나 고정 IP 접근 방식은 관리 하기 어려울 수 있으며, 대규모로 작업 하는 경우 비용이 많이 듭니다.

가상 네트워크 규칙은 Vm이 포함 된 특정 서브넷의 액세스를 설정 하 고 관리 하는 보다 쉬운 방법입니다.

> [!NOTE]
> 아직 서브넷에 SQL Database가 있을 수 없습니다. 서버가 가상 네트워크의 서브넷에 있는 노드인 경우 가상 네트워크 내의 모든 노드가 SQL Database 통신할 수 있습니다. 이 경우 VM은 가상 네트워크 규칙이나 IP 규칙이 없어도 SQL Database와 통신할 수 있습니다.

## <a name="private-link"></a>Private Link

개인 링크를 사용 하면 **개인 끝점**을 통해 서버에 연결할 수 있습니다. 개인 끝점은 특정 [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 및 서브넷의 개인 IP 주소입니다.

## <a name="next-steps"></a>다음 단계

- 서버 수준 IP 방화벽 규칙을 만드는 방법에 대 한 빠른 시작은 [SQL Database에서 데이터베이스 만들기](single-database-create-quickstart.md)를 참조 하세요.

- 서버 수준 가상 네트워크 방화벽 규칙을 만드는 방법에 대 한 빠른 시작은 [Virtual Network 서비스 끝점 및 Azure SQL Database에 대 한 규칙](vnet-service-endpoint-rule-overview.md)을 참조 하세요.

- 오픈 소스 또는 타사 응용 프로그램에서 SQL Database의 데이터베이스에 연결 하는 방법에 대 한 도움말은 [클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 사용 하 여 SQL Database를 참조 하세요.

- 열어야 할 수 있는 추가 포트에 대한 자세한 내용은 [ADO.NET 4.5와 SQL Database에 대한 1433 이외 포트](adonet-v12-develop-direct-route-ports.md)의 **SQL Database: 내부 및 외부** 섹션을 참조하세요.

- Azure SQL Database 연결에 대 한 개요는 [AZURE SQL 연결 아키텍처](connectivity-architecture.md) 를 참조 하세요.

- Azure SQL Database 보안 개요는 [데이터베이스 보안 설정](security-overview.md)을 참조하세요.

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
 
