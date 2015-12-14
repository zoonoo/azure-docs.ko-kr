<properties
   pageTitle="SQL 데이터베이스 방화벽 구성 | Microsoft Azure"
   description="서버 수준 및 데이터베이스 수준 방화벽 규칙으로 SQL 데이터베이스 방화벽을 구성하여 액세스를 관리하는 방법에 대해 알아봅니다."
   keywords="데이터베이스 방화벽"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="11/24/2015"
   ms.author="rickbyh"/>

# Azure SQL 데이터베이스 방화벽을 구성하는 방법

Microsoft Azure SQL 데이터베이스는 Azure 및 기타 인터넷 기반 응용 프로그램의 관계형 데이터베이스 서비스를 제공합니다. 데이터를 보호하기 위해, SQL 데이터베이스 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 SQL 데이터베이스 서버에 대한 모든 액세스를 금지합니다. 데이터베이스 방화벽은 각 요청이 시작된 IP 주소에 따라 액세스를 허용합니다.

데이터베이스 방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 및 데이터베이스 수준의 방화벽 규칙을 만들 수 있습니다.

- **서버 수준 방화벽 규칙:** 이 규칙은 모든 Azure SQL 데이터베이스 서버, 즉, 동일한 논리 서버 내의 모든 데이터베이스에 클라이언트가 액세스할 수 있도록 합니다. 이 규칙은 **master** 데이터베이스에 저장됩니다.
- **데이터베이스 수준 방화벽 규칙:** 이 규칙은 Azure SQL 데이터베이스 서버 내의 개별 데이터베이스에 클라이언트가 액세스할 수 있도록 합니다. 이 규칙은 데이터베이스 마다 생성되며 개별 데이터베이스에 저장됩니다(**master** 포함). 이 규칙은 동일 논리 서버 내의 특정(보안) 데이터베이스에 액세스를 제한할 때 유용합니다.

**권장사항:** Microsoft는 데이터베이스의 휴대성이 높아질수록 데이터베이스 수준 방화벽을 사용하도록 권장합니다. 동일한 액세스를 요구하는 데이터베이스가 많을 때, 서버 수준 방화벽을 사용하면 각 데이터베이스 개별적으로 구성할 필요가 없습니다.

**페더레이션에 대해:**현재 구현된 페더레이션 기능은 웹 및 비즈니스 서비스 계층과 사용 중지될 예정입니다. 확장성, 유연성 및 성능을 최대화하기 위해 사용자 지정 분할 솔루션을 배포하는 것이 좋습니다. 사용자 지정 분할에 관한 자세한 내용은, [Azure SQL 데이터베이스 확장](https://msdn.microsoft.com/library/dn495641.aspx)을 참조하세요.

> [AZURE.NOTE]데이터베이스 수준 방화벽 규칙을 포함한 루트 데이터베이스에서 Azure SQL 데이터베이스의 데이터베이스 페더레이션을 생성하는 경우, 페더레이션 멤버 데이터베이스로 복사할 수 없습니다. 페더레이션 멤버에 관한 데이터베이스 수준 방화벽 규칙이 필요한 경우, 페더레이션에 관한 규칙을 재생성해야 합니다. 그러나, 데이터베이스 수준 방화벽 규칙을 포함한 페더레이션 멤버를 ALTER FEDERATION ... SPLIT 문을 사용하여 새 페더레이션 멤버로 분할할 경우, 대상 멤버는 원본 페더레이션 멤버처럼 동일한 데이터베이스 수준 방화벽 규칙을 가집니다. 페더레이션에 대한 자세한 내용은 [Azure SQL 데이터베이스의 페더레이션](https://msdn.microsoft.com/library/hh597452.aspx)을 참조하세요.

## SQL 데이터베이스 방화벽 개요

먼저, Azure SQL 데이터베이스 서버로의 모든 액세스는 방화벽에 의해 차단됩니다. Azure SQL 데이터베이스 서버를 사용하려면 클래식 포털로 가서 Azure SQL 데이터베이스 서버로 액세스를 가능하게 하는 하나 이상의 서버 수준 방화벽 규칙을 꼭 지정해야 합니다. 방화벽 규칙을 사용하여 인터넷이 허용하는 IP 주소 범위 및 Azure 응용 프로그램 Azure SQL 데이터베이스 서버의 연결 시도 가능 여부를 지정할 수 있습니다.

그러나, Azure SQL 데이터베이스 서버에 있는 단 하나의 데이터베이스에 선택적으로 권한을 부여하고자 하는 경우, 서버 수준 방화벽 규칙의 지정된 IP 주소 범위를 초과하는 IP 주소의 데이터베이스에 대한 데이터베이스 수준 규칙을 생성해야 하고, 사용자의 IP 주소가 데이터베이스 수준 규칙 내의 지정된 범위에 있는지 확인해 야합니다.

아래의 다이어그램이 보여주는 것처럼 인터넷 및 Azure에서 연결 시도는 Azure SQL Database 서버 또는 데이터베이스에 연결되기 전에 처음에 방화벽을 통과해야 합니다.

   ![SQL 데이터베이스 방화벽 구성을 설명하는 다이어그램][1]

## 인터넷에서 연결하기

컴퓨터가 인터넷의 데이터베이스로 연결을 시도할 때, 방화벽은 서버 수준 및 데이터베이스 수준(필요한 경우) 방화벽 규칙의 전체 집합 대응을 요청하는 본래 IP 주소를 확인합니다.

- 요청된 IP 주소가 서버 수준 방화벽 규칙의 지정된 범위 안에 있을 경우, Azure SQL Database 서버로 연결됩니다.

- 요청된 IP 주소가 서버 수준 방화벽 규칙의 지정된 범위 안에 없을 경우, 데이터베이스 수준 방화벽 규칙을 확인합니다. 요청된 IP 주소가 데이터베이스 수준 방화벽 규칙의 지정된 범위 안에 있을 경우, 일치하는 데이터베이스 수준 규칙을 가진 데이터베이스에만 연결됩니다.

- 요청된 IP 주소가 서버 수준 또는 데이터베이스 수준 방화벽 규칙의 지정된 범위 안에 없을 경우, 연결 요청이 실패합니다.

> [AZURE.NOTE]로컬 컴퓨터에서 Azure SQL Database로 액세스하려면, 네트워크의 방화벽과 로컬 컴퓨터가 TCP 포트 1433으로 나가는 통신을 허용하는지 확인합니다.


## Azure에서 연결

Azure의 응용 프로그램이 데이터베이스 서버로 연결을 시도할 때, 방화벽은 Azure 연결이 허용되는지 확인합니다. 0.0.0.0으로 된 시작 및 끝 주소가 포함된 방화벽 설정은 연결이 허용됨을 나타냅니다. 연결 시도가 허용되지 않는 경우, 요청은 Azure SQL Database 서버로 도달되지 않습니다.

[클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=161793)에서 Azure에서 연결을 두 가지 방법으로 사용할 수 있습니다.

- 새로운 서버를 만들 때 **Microsoft Azure 서비스가 서버에 액세스하도록 허용** 확인란을 선택합니다.

- 서버의 **구성** 탭, **허용되는 서비스** 섹션에서 **Microsoft Azure 서비스**에 대해 **예**를 클릭합니다.

## 첫 번째 서버 수준 방화벽 규칙 만들기

첫 번째 서버 수준 방화벽 설정은 [클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=161793)을 사용하거나 REST API 또는 Azure PowerShell을 사용하여 프로그램에 따라 설정할 수 있습니다. 후속 서버 수준 방화벽 규칙은 Transact-SQL과 같은 메서드를 사용하여 생성할 수 있습니다. 서버 수준 방화벽 규칙에 대한 자세한 내용은 [방법: 방화벽 설정 구성(Azure SQL 데이터베이스)](sql-database-configure-firewall-settings.md)을 참조하세요.

## 데이터베이스 수준 방화벽 규칙 만들기.

첫 번째 수준 방화벽을 구성한 후, 특정 데이터베이스에 대한 액세스를 제한하고 싶을 수도 있습니다. 서버 수준 방화벽 규칙 내 지정된 범위 밖의 데이터베이스 수준 방화벽 규칙의 IP 주소 범위를 지정한 경우, 데이터베이스 수준 범위 내에 IP 주소를 가진 클라이언트만이 데이터베이스에 액세스할 수 있습니다. 데이터베이스에 대해 최대 128개의 데이터베이스 수준 방화벽 규칙을 가질 수 있습니다. 마스터 및 사용자 데이터베이스에 대한 데이터베이스 수준 방화벽 규칙은 Transact-SQL을 통해 생성 및 관리됩니다. 자세한 내용은 [방법: 방화벽 설정 구성(Azure SQL 데이터베이스)](sql-database-configure-firewall-settings.md)을 참조하세요.

## 데이터베이스 방화벽 규칙을 프로그래밍 방식으로 관리

Azure 클래식 포털 외에도 Transact-SQL, REST API 및 Azure PowerShell을 사용하여 방화벽 규칙을 프로그래밍 방식으로 관리할 수 있습니다. 아래의 표는 각 메서드에 사용 가능한 명령의 집합을 보여줍니다.


### Transact-SQL

| 카탈로그 뷰 또는 저장된 프로시저 | Level | 설명 |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | 서버 | 현재 서버 수준 방화벽 규칙 표시 |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | 서버 | 서버 수준 방화벽 규칙 생성 및 업데이트 |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | 서버 | 서버 수준 방화벽 규칙 제거 |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | 데이터베이스 | 현재 데이터베이스 수준 방화벽 규칙 표시 |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | 데이터베이스 | 데이터베이스 수준 방화벽 규칙 생성 및 업데이트 |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | 데이터베이스 | 데이터베이스 수준 방화벽 규칙 제거 |

### REST API

| API | Level | 설명 |
|----------------------|--------|------------------------------------------------------------------|
| [방화벽 규칙 나열](https://msdn.microsoft.com/library/azure/dn505715.aspx) | 서버 | 현재 서버 수준 방화벽 규칙 표시 |
| [방화벽 규칙 만들기](https://msdn.microsoft.com/library/azure/dn505712.aspx) | 서버 | 서버 수준 방화벽 규칙 생성 및 업데이트 |
| [방화벽 규칙 설정](https://msdn.microsoft.com/library/azure/dn505707.aspx) | 서버 | 기존 서버 수준 방화벽 규칙 속성 업데이트 |
| [방화벽 규칙 삭제](https://msdn.microsoft.com/library/azure/dn505706.aspx) | 서버 | 서버 수준 방화벽 규칙 제거 |


### Azure PowerShell

| Cmdlet | Level | 설명 |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [AzureSqlDatabaseServerFirewallRule가져오기](https://msdn.microsoft.com/library/azure/dn546731.aspx) | 서버 | 현재 서버 수준 방화벽 규칙 반환 |
| [신규 AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | 서버 | 새 서버 수준 방화벽 규칙 만들기 |
| [AzureSqlDatabaseServerFirewallRule집합](https://msdn.microsoft.com/library/azure/dn546739.aspx) | 서버 | 기존 서버 수준 방화벽 규칙 속성 업데이트 |
| [AzureSqlDatabaseServerFirewallRule삭제](https://msdn.microsoft.com/library/azure/dn546727.aspx) | 서버 | 서버 수준 방화벽 규칙 제거 |

> [AZURE.NOTE]방화벽 설정의 변경 내용이 적용되기까지는 5분 정도의 시간이 소요될 수 있습니다.

## 데이터베이스 방화벽 문제 해결

Microsoft Azure SQL 데이터베이스 서비스로의 연결이 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

- **로컬 방화벽 구성:** 사용자의 컴퓨터가 Azure SQL 데이터베이스에 액세스할 수 있게 되기 전에, 사용자의 컴퓨터에 TCP 포트 1433에 대한 방화벽 예외를 만드는 것이 필요할 수 있습니다. Azure 클라우드 경계 내에서 연결하는 경우 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)의 **SQL 데이터베이스의 V12: 내부 vs 외부** 섹션을 참조하세요.

- **NAT(Network Address Translation):** NAT로 인해 Azure SQL 데이터베이스로 연결할 때 컴퓨터에서 사용하는 IP 주소는 컴퓨터 IP 구성 설정에서 나타나는 IP 주소와 다를 수도 있습니다. 컴퓨터가 Azure에 연결할 때 사용하는 IP 주소를 보려면, 클래식 포털에 로그인하고 데이터베이스를 호스트하는 서버의 **구성** 탭을 탐색합니다. **허용된 IP 주소** 섹션에 **현재 클라이언트 IP 주소**가 표시됩니다. **허용된 IP 주소**에 **추가**를 클릭하여 이 컴퓨터가 서버에 액세스할 수 있도록 합니다.

- **허용 목록의 변경사항이 아직 적용되지 않았습니다.** Azure SQL 데이터베이스 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

- **로그인이 올바르지 않거나 암호가 올바르지 않습니다.** 로그인에 Azure SQL 데이터베이스 서버에 대한 권한이 없거나 사용한 암호가 틀렸을 경우 Azure SQL 데이터베이스 서버에 대한 연결이 거부됩니다. 방화벽 설정은 클라이언트에게 서버에 연결을 시도할 수 있는 기회를 제공합니다. 각 클라이언트는 꼭 필요한 보안 자격 증명을 제공해야 합니다. 로그인 준비에 대한 자세한 내용은 Azure SQL Database에서 데이터베이스, 로그인, 사용자 관리를 참조하세요.

- **동적 IP 주소:** 동적 IP 주소를 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도할 수 있습니다.

 - 인터넷 서비스 공급자(ISP)는 Azure SQL Database 서버에 연결될 클라이언트에 할당된 IP 주소 범위를 요청하고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.

 - 클라이언트 컴퓨터 대신 고정 IP 주소를 얻고, 방화벽 규칙에 따라 IP 주소 범위를 추가합니다.

## 참고 항목

[방법: 데이터베이스 방화벽 설정 구성(Azure SQL 데이터베이스)](sql-database-configure-firewall-settings.md)

[SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=AcomDC_1203_2015-->