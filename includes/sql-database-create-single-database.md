---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: eff121cfaf4473607110de4553a9bb8021990caf
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444683"
---
이 단계에서는 리소스 그룹 및 Azure SQL Database 단일 데이터베이스를 만듭니다. 

> [!IMPORTANT]
> 이 문서의 단계를 수행 하는 컴퓨터의 공용 IP 주소를 사용 하도록 방화벽 규칙을 설정 해야 합니다. 데이터베이스 수준 방화벽 규칙은 보조 서버에 자동으로 복제됩니다.
>
> 자세한 내용은 [데이터베이스 수준 방화벽 규칙 만들기](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)를 참조하세요. 사용 중인 컴퓨터용 서버 수준 방화벽 규칙에 사용되는 IP 주소를 확인하려면 [서버 수준 방화벽 만들기](../articles/sql-database/sql-database-server-level-firewall-rule.md)를 참조하세요.  

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Azure Portal를 사용 하 여 리소스 그룹 및 단일 데이터베이스를 만듭니다. 

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
2. **데이터베이스**를 선택한 다음, **SQL Database**를 선택하여 **SQL Database 만들기** 페이지를 엽니다.

   ![단일 데이터베이스 만들기](../articles/sql-database/media/sql-database-get-started-portal/create-database-1.png)

3. **기본 사항** 탭의 **프로젝트 세부 정보** 섹션에서 다음 값을 입력하거나 선택합니다.

   - **구독**: 표시되지 않는 경우 올바른 구독을 드롭다운하고 선택합니다.
   - **리소스 그룹**: **새로 만들기**를 선택하고, `myResourceGroup`을 입력하고, **확인**을 선택합니다.

     ![새 SQL 데이터베이스 - 기본 탭](../articles/sql-database/media/sql-database-get-started-portal/new-sql-database-basics.png)

4. **데이터베이스 세부 정보** 섹션에서 다음 값을 입력하거나 선택합니다.

   - **데이터베이스 이름**:  `mySampleDatabase` 을 입력합니다.
   - **서버**: **새로 만들기**를 선택하고 다음 값을 입력한 다음, **선택**을 클릭합니다.
       - **서버 이름**: `mysqlserver`에 숫자를 붙여서 고유한 이름을 입력합니다.
       - **서버 관리자 로그인**: `azureuser`을 입력합니다.
       - **암호**: 암호 요구 사항을 충족하는 복잡한 암호를 입력합니다.
       - **위치**: 드롭다운 목록에서 위치를 선택합니다(예: `West US 2`).

         ![새 서버](../articles/sql-database/media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > 빠른 시작을 위해 서버 및 데이터베이스에 로그인할 수 있도록 서버 관리자 로그인 및 암호를 적어 둡니다. 로그인 또는 암호를 잊은 경우 **SQL 서버** 페이지에서 로그인 이름을 얻거나 암호를 다시 설정할 수 있습니다. **SQL 서버** 페이지를 열려면 데이터베이스를 만든 후 데이터베이스 **개요** 페이지에서 서버 이름을 선택합니다.

        ![SQL Database 세부 정보](../articles/sql-database/media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **SQL 탄력적 풀을 사용하시겠습니까?** : **아니요** 옵션을 선택합니다.
   - **컴퓨팅 및 스토리지**: **데이터베이스 구성**을 선택하고 이 빠른 시작에 대해서는 **vCore 기반 구매 옵션**을 선택합니다.

     ![vCore 기반 구매 옵션](../articles/sql-database/media/sql-database-get-started-portal/create-database-vcore.png)

   - **프로 비전** 된 및 **Gen4**를 선택 합니다.

     ![서버리스 컴퓨팅 계층](../articles/sql-database/media/sql-database-get-started-portal/create-database-serverless.png)

   - **최대 vCore 수**, **최소 vCore 수**, **자동 일시 중지 지연** 및 **데이터 최대 크기**에 대한 설정을 검토합니다. 원하는 대로 변경합니다.
   - 미리 보기 조건을 수락하고 **확인**을 클릭합니다.
   -           **적용**을 선택합니다.

5. **추가 설정** 탭을 선택합니다. 
6. **데이터 원본** 섹션의 **기존 데이터 사용** 아래에서 `Sample`을 선택합니다. 

   ![추가 SQL DB 설정](../articles/sql-database/media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > 이 데이터를 사용하는 Azure SQL Database 빠른 시작을 쉽게 따라 할 수 있도록 **샘플(AdventureWorksLT)** 데이터를 선택해야 합니다.

7. 나머지 값은 기본값으로 두고 양식 맨 아래에서 **검토 + 만들기**를 선택합니다.
8. 최종 설정을 검토하고 **만들기**를 선택합니다.

9. **SQL Database** 양식에서 **만들기**를 선택하여 리소스 그룹, 서버 및 데이터베이스를 배포하고 프로비전합니다.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](updated-for-az.md)]

PowerShell을 사용 하 여 리소스 그룹 및 단일 데이터베이스를 만듭니다. 

   ```powershell-interactive
   # Set variables for your server and database
   $SubscriptionId = '<Your Azure subscription ID>'
   $ResourceGroupName = "myResourceGroup" # to randomize: "myResourceGroup-$(Get-Random)"
   $Location = "westus2"
   $AdminLogin = "azureuser"
   $Password = "ChangeYourAdminPassword1"
   $ServerName = "mysqlserver" # to randomize: "mysqlserver-$(Get-Random)"
   $DatabaseName = "mySampleDatabase"
   
   
   # The ip address range that you want to allow to access your server 
   #(leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"
   
   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   
   # Create a server with a system wide unique server name
   $server = New-AzSqlServer -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -Location $Location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AdminLogin, $(ConvertTo-SecureString -String $Password -AsPlainText -Force))
   
   # Create a server firewall rule that allows access from the specified IP range
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   
   # Create General Purpose Gen4 database with 1 vCore
   $database = New-AzSqlDatabase  -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -DatabaseName $DatabaseName `
      -Edition GeneralPurpose `
      -VCore 1 `
      -ComputeGeneration Gen4  `
      -MinimumCapacity 1 `
      -SampleName "AdventureWorksLT"
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
AZ CLI를 사용 하 여 리소스 그룹 및 단일 데이터베이스를 만듭니다. 


   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   export subscriptionID=<Your Subscription ID>
   export ResourceGroupName=myResourceGroup # to randomize: myResourceGroup-$RANDOM
   export Location=WestUS2
   export AdminLogin=azureuser
   export Password=ChangeYourAdminPassword1
   export ServerName="mysqlserver" # to randomize: mysqlserver-$RANDOM
   export DatabaseName=mySampleDatabase
   
   # The ip address range that you want to allow to access your DB. 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   export startip=0.0.0.0
   export endip=0.0.0.0
  
   # Connect to Azure
   az login

   $ Set subscription ID
   az account set --subscription $SubscriptionID
   
   # Create a resource group
   az group create \
      --name $ResourceGroupName \
      --location $Location
   
   # Create a logical server in the resource group
   az sql server create \
      --name $ServerName \
      --resource-group $ResourceGroupName \
      --location $Location  \
      --admin-user $AdminLogin\
      --admin-password $Password
   
   # Configure a firewall rule for the server
   az sql server firewall-rule create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a database in the server 
   az sql db create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      --name $DatabaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen4 \
      --capacity 1 \
   ```

---