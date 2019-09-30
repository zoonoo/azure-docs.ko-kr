---
title: PowerShell을 사용하여 Azure Cosmos DB 만들기 및 관리
description: Azure Powershell을 사용하여 Azure Cosmos DB 계정, 데이터베이스, 컨테이너 및 처리량을 관리합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 5b041fecfaa5a84ed5a04a3a8c53de10b9efd65b
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155361"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>PowerShell을 사용하여 Azure Cosmos DB SQL API 리소스 관리

다음 지침에서는 PowerShell을 사용하여 계정, 데이터베이스, 컨테이너 및 처리량을 포함한 Azure Cosmos DB 리소스의 관리를 스크립팅하고 자동화하는 방법에 대해 설명합니다. Azure Cosmos DB 관리는 AzResource cmdlet을 통해 Azure Cosmos DB 리소스 공급자에 직접 처리됩니다. Azure Cosmos DB 리소스 공급자에 대해 PowerShell을 사용하여 관리할 수 있는 모든 속성을 보려면 [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)를 참조하세요.

Azure Cosmos DB의 플랫폼 간 관리를 위해 [Azure CLI](manage-with-cli.md), [REST API][rp-rest-api] 또는 [Azure Portal](create-sql-api-dotnet.md#create-account)을 사용할 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>시작하기

[Azure PowerShell을 설치하고 구성하는 방법][powershell-install-configure]의 지침에 따라 Powershell에서 Azure 계정을 설치하고 로그인합니다.

* 사용자에게 확인을 요구하지 않고 다음 명령을 실행하려면 명령에 `-Force` 플래그를 추가합니다.
* 다음 명령은 모두 동기식입니다.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 계정

다음 섹션에서는 아래 작업을 포함하여 Azure Cosmos 계정을 관리하는 방법을 보여 줍니다.

* [Azure Cosmos 계정 만들기](#create-account)
* [Azure Cosmos 계정 업데이트](#update-account)
* [구독의 모든 Azure Cosmos 계정 나열](#list-accounts)
* [Azure Cosmos 계정 가져오기](#get-account)
* [Azure Cosmos 계정 삭제](#delete-account)
* [Azure Cosmos 계정에 대한 태그 업데이트](#update-tags)
* [Azure Cosmos 계정에 대한 키 나열](#list-keys)
* [Azure Cosmos 계정에 대한 키 다시 생성](#regenerate-keys)
* [Azure Cosmos 계정에 대한 연결 문자열 나열](#list-connection-strings)
* [Azure Cosmos 계정에 대한 장애 조치 우선 순위 수정](#modify-failover-priority)
* [Azure Cosmos 계정에 대한 수동 장애 조치 트리거](#trigger-manual-failover)

### <a id="create-account"></a> Azure Cosmos 계정 만들기

이 명령은 [다중 지역][distribute-data-globally], 제한된 부실 [일관성 정책](consistency-levels.md)을 사용하여 Azure Cosmos 데이터베이스 계정을 만듭니다.

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Azure Cosmos 계정의 이름입니다. 소문자여야 하며, 영숫자와 '-' 문자를 사용할 수 있고, 3~31자여야 합니다.
* `$location` Azure Cosmos 계정 리소스의 위치입니다.
* `$locations` 데이터베이스 계정에 대한 복제본 지역입니다. 장애 조치 우선 순위 값이 0인 데이터베이스 계정당 하나의 쓰기 지역이 있어야 합니다.
* `$consistencyPolicy` Azure Cosmos 계정의 기본 일관성 수준입니다. 자세한 내용은 [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하세요.
* `$CosmosDBProperties` 계정을 프로비저닝하기 위해 Cosmos DB Azure Resource Manager에 전달되는 속성 값입니다.

Azure Cosmos 계정은 Virtual Network 서비스 엔드포인트와 IP 방화벽으로 구성할 수 있습니다. Azure Cosmos DB에 대해 IP 방화벽을 구성하는 방법에 대한 자세한 내용은 [IP 방화벽 구성](how-to-configure-firewall.md)을 참조하세요.  Azure Cosmos DB에 대한 서비스 엔드포인트를 사용하도록 설정하는 방법에 대한 자세한 내용은 [가상 네트워크에서 액세스 구성](how-to-configure-vnet-service-endpoint.md)을 참조하세요.

### <a id="list-accounts"></a> 구독의 모든 Azure Cosmos 계정 나열

이 명령을 사용하면 구독에 있는 모든 Azure Cosmos 계정을 나열할 수 있습니다.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a> Azure Cosmos 계정의 속성 가져오기

이 명령을 사용하면 기존 Azure Cosmos 계정의 속성을 가져올 수 있습니다.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Azure Cosmos 계정 업데이트

이 명령을 사용하면 Azure Cosmos 데이터베이스 계정 속성을 업데이트할 수 있습니다. 업데이트할 수 있는 속성은 다음과 같습니다.

* 지역 추가 또는 제거
* 기본 일관성 정책 변경
* IP 범위 필터 변경
* Virtual Network 구성 변경
* 다중 마스터 사용

> [!NOTE]
> `locations` 영역을 추가하거나 제거하면서 동시에 Azure Cosmos 계정에 대한 다른 속성을 변경할 수 없습니다. 지역 수정은 계정 리소스에 대한 다른 변경 사항보다 별도의 작업으로 수행해야 합니다.
> [!NOTE]
> 이 명령을 사용하면 지역을 추가 및 제거할 수 있지만 장애 조치(failover) 우선 순위를 수정하거나 수동 장애 조치(failover)를 트리거할 수 없습니다. [장애 조치(failover) 우선 순위 수정](#modify-failover-priority) 및 [수동 장애 조치(failover) 트리거](#trigger-manual-failover)를 참조하세요.

```azurepowershell-interactive
# Get an Azure Cosmos Account (assume it has two regions currently West US 2 and East US 2) and add a third region

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="South Central US"; "failoverPriority"=2 }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Azure Cosmos 계정 삭제

이 명령을 사용하면 기존 Azure Cosmos 계정을 삭제할 수 있습니다.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Azure Cosmos 계정의 태그 업데이트

다음 예제에서는 Azure Cosmos 계정에 대해 [Azure 리소스 태그][azure-resource-tags]를 설정하는 방법을 설명합니다.

> [!NOTE]
> 이 명령은 `-Tags` 플래그에 해당 매개 변수를 추가하여 만들기 또는 업데이트 명령과 결합할 수 있습니다.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> 계정 키 나열

Azure Cosmos DB 계정을 만들면 해당 서비스에서 Azure Cosmos DB 계정에 액세스할 때 인증에 사용할 수 있는 2개의 마스터 액세스 키가 생성됩니다. Azure Cosmos DB에서는 2개의 액세스 키를 제공해서 사용자가 Azure Cosmos DB 계정에 대한 중단 없이 키를 다시 생성할 수 있습니다. 읽기 전용 작업을 인증하기 위한 읽기 전용 키도 사용할 수 있습니다. 두 개의 읽기-쓰기 키(기본 및 보조) 및 두 개의 읽기 전용 키(기본 및 보조)가 있습니다.

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> 연결 문자열 나열

MongoDB 계정의 경우 MongoDB 앱을 데이터베이스 계정에 연결하기 위한 연결 문자열은 다음 명령을 사용하여 검색할 수 있습니다.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> 계정 키 다시 생성

Azure Cosmos 계정에 대한 액세스 키는 연결을 더 안전하게 유지할 수 있도록 정기적으로 다시 생성해야 합니다. 기본 및 보조 액세스 키가 계정에 할당됩니다. 이렇게 하면 다른 액세스 키가 다시 생성되는 동안 클라이언트에서 액세스를 유지할 수 있습니다. Azure Cosmos 계정에는 네 가지 유형의 키(Primary, Secondary, PrimaryReadonly 및 SecondaryReadonly)가 있습니다.

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> 장애 조치 우선 순위 수정

자동 장애 조치(failover)를 사용하여 구성된 계정의 경우 주 복제본을 사용할 수 없게 될 때 Cosmos가 보조 복제본을 주 복제본으로 승격하는 순서를 변경할 수 있습니다.

아래 예제에서는 현재 장애 조치(failover) 우선 순위 `West US 2 = 0`, `East US 2 = 1`, `South Central US = 2`를 가정합니다.

> [!CAUTION]
> `failoverPriority=0`에 대한 `locationName`을 변경하면 Azure Cosmos 계정에 대한 수동 장애 조치가 트리거됩니다. 다른 우선 순위 변경은 장애 조치를 트리거하지 않습니다.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="South Central US"; "failoverPriority"=1 },
    @{ "locationName"="East US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

### <a id="trigger-manual-failover"></a> 수동 장애 조치(failover) 트리거

수동 장애 조치(failover)를 사용하여 구성된 계정의 경우 `failoverPriority=0`으로 수정하여 모든 보조 복제본을 주 복제본으로 장애 조치(failover)하고 승격할 수 있습니다. 이 작업은 재해 복구 계획을 테스트하기 위한 재해 복구 훈련을 시작하는 데도 사용할 수 있습니다.

아래 예제에서는 계정의 현재 장애 조치(failover) 우선순위가 `West US 2 = 0` 및 `East US 2 = 1`이라고 가정하고 지역을 대칭 이동시킵니다.

> [!CAUTION]
> `failoverPriority=0`에 대한 `locationName`을 변경하면 Azure Cosmos 계정에 대한 수동 장애 조치가 트리거됩니다. 다른 우선 순위 변경은 장애 조치(failover)를 트리거하지 않습니다.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="South Central US"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="West US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos 데이터베이스

다음 섹션에서는 Azure Cosmos 데이터베이스를 관리하는 방법을 보여 줍니다.

* [Azure Cosmos 데이터베이스 만들기](#create-db)
* [공유 처리량을 사용하여 Azure Cosmos 데이터베이스 만들기](#create-db-ru)
* [Azure Cosmos 데이터베이스의 처리량 가져오기](#get-db-ru)
* [계정의 모든 Azure Cosmos 데이터베이스 나열](#list-db)
* [단일 Azure Cosmos 데이터베이스 가져오기](#get-db)
* [Azure Cosmos 데이터베이스 삭제](#delete-db)

### <a id="create-db"></a>Azure Cosmos 데이터베이스 만들기

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>공유 처리량을 사용하여 Azure Cosmos 데이터베이스 만들기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Azure Cosmos 데이터베이스의 처리량 가져오기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>계정의 모든 Azure Cosmos 데이터베이스 가져오기

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>단일 Azure Cosmos 데이터베이스 가져오기

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Azure Cosmos 데이터베이스 삭제

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos 컨테이너

다음 섹션에서는 아래 작업을 포함하여 Azure Cosmos 컨테이너를 관리하는 방법을 보여 줍니다.

* [Azure Cosmos 컨테이너 만들기](#create-container)
* [큰 파티션 키를 사용하여 Azure Cosmos 컨테이너 만들기](#create-container-big-pk)
* [Azure Cosmos 컨테이너의 처리량 가져오기](#get-container-ru)
* [공유 처리량을 사용하여 Azure Cosmos 컨테이너 만들기](#create-container-ru)
* [사용자 지정 인덱싱을 사용하여 Azure Cosmos 컨테이너 만들기](#create-container-custom-index)
* [해제된 인덱싱을 사용하여 Azure Cosmos 컨테이너 만들기](#create-container-no-index)
* [고유 키와 TTL을 사용하여 Azure Cosmos 컨테이너 만들기](#create-container-unique-key-ttl)
* [충돌 해결을 사용하여 Azure Cosmos 컨테이너 만들기](#create-container-lww)
* [데이터베이스의 모든 Azure Cosmos 컨테이너 나열](#list-containers)
* [데이터베이스의 단일 Azure Cosmos 컨테이너 가져오기](#get-container)
* [Azure Cosmos 컨테이너 삭제](#delete-container)

### <a id="create-container"></a>Azure Cosmos 컨테이너 만들기

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>큰 파티션 키 크기를 사용하여 Azure Cosmos 컨테이너 만들기

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Azure Cosmos 컨테이너의 처리량 가져오기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>공유 처리량을 사용하여 Azure Cosmos 컨테이너 만들기

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>사용자 지정 인덱스 정책을 사용하여 Azure Cosmos 컨테이너 만들기

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>해제된 인덱싱을 사용하여 Azure Cosmos 컨테이너 만들기

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>고유 키 정책과 TTL을 사용하여 Azure Cosmos 컨테이너 만들기

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>충돌 해결을 사용하여 Azure Cosmos 컨테이너 만들기

저장 프로시저를 사용하는 충돌 해결 정책을 만들려면 `"mode"="custom"`을 설정하고 해결 경로를 저장 프로시저의 이름(`"conflictResolutionPath"="myResolverStoredProcedure"`)으로 설정합니다. 모든 충돌을 개별적으로 ConflictsFeed에 기록하고 처리하려면 `"mode"="custom"` 및 `"conflictResolutionPath"=""`를 설정합니다.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>데이터베이스의 모든 Azure Cosmos 컨테이너 나열

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>데이터베이스의 단일 Azure Cosmos 컨테이너 가져오기

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Azure Cosmos 컨테이너 삭제

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>다음 단계

* [모든 PowerShell 샘플](powershell-samples.md)
* [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)
* [Azure Cosmos 컨테이너 만들기](how-to-create-container.md)
* [Azure Cosmos DB에서 TTL(Time to Live) 구성](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
