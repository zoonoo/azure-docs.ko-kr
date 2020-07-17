---
title: '자습서: 서버 디자인 - Azure PowerShell - Azure Database for MySQL'
description: 이 자습서에서는 PowerShell을 사용하여 Azure Database for MySQL 서버 및 데이터베이스를 만들고 관리하는 방법에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: mvc
ms.openlocfilehash: 6bb3c25d4d4d24e626ad210c78c6ac64c560e43e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613909"
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-powershell"></a>자습서: PowerShell을 사용하여 Azure Database for MySQL 디자인

Azure Database for MySQL은 MySQL Community Edition 데이터베이스 엔진을 기반으로 한 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다 이 자습서에서는 PowerShell 및 다른 유틸리티를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - Azure Database for MySQL 만들기
> - 서버 방화벽 구성
> - [mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)를 사용하여 데이터베이스 만들기
> - 샘플 데이터 로드
> - 쿼리 데이터
> - 데이터 업데이트
> - 데이터 복원

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

> [!IMPORTANT]
> Az.MySql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.MySql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.MySql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

Azure Database for MySQL 서비스를 처음 사용하는 경우 **Microsoft.DBforMySQL** 리소스 공급자를 등록해야 합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독 ID를 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 **myresourcegroup**이라는 리소스 그룹을 **미국 서부** 지역에 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기

`New-AzMySqlServer` cmdlet을 사용하여 Azure Database for MySQL 서버를 만듭니다. 서버는 여러 데이터베이스를 관리할 수 있습니다. 일반적으로 각 프로젝트 또는 각 사용자에 대해 별도의 데이터베이스가 사용됩니다.

다음 예제에서는 **myadmin**의 서버 관리자 로그인을 사용하여 **myresourcegroup** 리소스 그룹의 **mydemoserver**라는 MySQL 서버를 **미국 서부** 지역에 만듭니다. 이는 2개 vCore 및 지역 중복 백업을 사용하도록 설정된 범용 가격 책정 계층의 5세대 서버입니다. MySQL 서버 관리자 계정의 암호이므로 예제의 첫 번째 줄에 사용되는 암호를 문서화합니다.

> [!TIP]
> 서버 이름은 DNS 이름에 매핑되며 Azure에서 글로벌하게 고유해야 합니다.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

**Sku** 매개 변수 값은 다음 예제와 같이 **pricing-tier\_compute-generation\_vCores** 규칙을 따릅니다.

- `-Sku B_Gen5_1`은 기본, 5세대 및 1개 vCore에 매핑됩니다. 이 옵션은 사용 가능한 가장 작은 SKU입니다.
- `-Sku GP_Gen5_32`는 범용, 5세대 및 vCore 32개에 매핑됩니다.
- `-Sku MO_Gen5_2`는 메모리 최적화, 5세대 및 vCore 2개에 매핑됩니다.

유효한 지역별 및 계층별 **Sku** 값에 대한 자세한 내용은 [Azure Database for MySQL 가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.

간단한 컴퓨팅 및 I/O가 워크로드에 적합한 경우 기본 가격 책정 계층을 사용하는 것이 좋습니다.

> [!IMPORTANT]
> 기본 가격 책정 계층에서 만든 서버는 나중에 범용 또는 메모리 최적화로 크기 조정할 수 없으며 지리적으로 복제할 수 없습니다.

## <a name="configure-a-firewall-rule"></a>방화벽 규칙 구성

`New-AzMySqlFirewallRule` cmdlet을 사용하여 Azure Database for MySQL 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 `mysql` 명령줄 도구 또는 외부 애플리케이션(예: MySQL Workbench)에서 Azure Database for MySQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다.

다음 예제에서는 특정 192.168.0.1 IP 주소에서 연결하도록 허용하는 **AllowMyIP**라는 방화벽 규칙을 만듭니다. 연결하는 위치에 해당하는 IP 주소 또는 IP 주소 범위로 바꿉니다.

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Azure Database for MySQL에 대한 연결은 포트 3306을 통해 통신합니다. 회사 네트워크 내에서 연결하려고 하면 3306 포트를 통한 아웃바운드 트래픽이 허용되지 않을 수 있습니다. 이 시나리오에서는 IT 부서에서 3306 포트를 여는 경우에만 서버에 연결할 수 있습니다.

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다. 다음 예제를 사용하여 연결 정보를 확인합니다. **FullyQualifiedDomainName** 및 **AdministratorLogin** 값을 적어 둡니다.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>mysql 명령줄 도구를 사용하여 서버에 연결

`mysql` 명령줄을 사용하여 서버에 연결합니다. 명령줄 도구를 다운로드하여 설치하려면 [MySQL 커뮤니티 다운로드](https://dev.mysql.com/downloads/shell/)를 참조하세요. 이 문서의 코드 샘플에서 **사용해 보세요** 단추를 선택하여 Azure Cloud Shell에서 미리 설치된 버전의 `mysql` 명령줄 도구에 액세스할 수도 있습니다. Azure Cloud Shell에 액세스하는 또 다른 방법으로, Azure Portal의 오른쪽 위 도구 모음에서 **>_** 단추를 선택하거나 [shell.azure.com](https://shell.azure.com/)을 방문합니다.

```azurepowershell-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>데이터베이스 만들기

서버에 연결되면 빈 데이터베이스를 만듭니다.

```sql
mysql> CREATE DATABASE mysampledb;
```

프롬프트에서 다음 명령을 실행하여 새로 만든 이 데이터베이스에 대한 연결로 전환합니다.

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기

이제 Azure Database for MySQL 데이터베이스에 연결하는 방법을 알았으므로 몇 가지 기본 작업을 수행할 수 있습니다.

먼저 테이블을 만들고 일부 데이터와 함께 로드합니다. 인벤토리 정보를 저장하는 테이블을 만들어 보겠습니다.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>테이블에 데이터 로드

이제 테이블을 만들었으므로 이 테이블에 일부 데이터를 삽입합니다. 열린 명령 프롬프트 창에서 다음 쿼리를 실행하여 데이터 행을 일부 삽입합니다.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

이제 앞에서 만든 테이블에 두 개의 샘플 데이터 행이 있습니다.

## <a name="query-and-update-the-data-in-the-tables"></a>테이블의 데이터 쿼리 및 업데이트

다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다.

```sql
SELECT * FROM inventory;
```

또한 테이블의 데이터를 업데이트할 수도 있습니다

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

이에 따라 데이터를 검색할 때 해당 행이 업데이트됩니다.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>이전 시점으로 데이터베이스 복원

이전의 특정 시점으로 서버를 복원할 수 있습니다. 복원된 데이터는 새 서버에 복사되고 기존 서버는 변경되지 않은 상태로 유지됩니다. 예를 들어 테이블이 실수로 삭제된 경우 삭제가 발생한 바로 전 시간으로 복원할 수 있습니다. 그런 다음 서버의 복원된 복사본에서 누락된 테이블 및 데이터를 검색할 수 있습니다.

서버를 복원하려면 `Restore-AzMySqlServer` PowerShell cmdlet을 사용합니다.

### <a name="run-the-restore-command"></a>복원 명령 실행

서버를 복원하려면 PowerShell에서 다음 예제를 실행합니다.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

서버를 이전 특정 시점으로 복원하는 경우 새 서버가 만들어집니다. 지정된 특정 시점의 원본 서버 및 해당 데이터베이스가 새 서버에 복사됩니다.

복원된 서버에 대한 위치 및 가격 책정 계층 값은 원본 서버와 같게 유지됩니다.

복원 프로세스가 완료된 후 새 서버를 찾아 데이터가 예상대로 복원되었는지 확인합니다. 새 서버에는 복원이 시작된 당시의 기존 서버에 유효한 동일한 서버 관리자 로그인 이름 및 암호가 있습니다. 암호는 새 서버의 **개요** 페이지에서 변경할 수 있습니다.

복원 중에 만든 새 서버에는 원래 서버에 존재했던 VNet 서비스 엔드포인트가 없습니다. 이러한 규칙은 새 서버에 대해 개별적으로 설정돼야 합니다. 원본 서버의 방화벽 규칙이 복원됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 Azure Database for MySQL 서버를 백업 및 복원하는 방법](howto-restore-server-powershell.md)
