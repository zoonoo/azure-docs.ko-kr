---
title: PowerShell을 사용 하 여 Azure Cosmos DB를 만들고 설정 합니다.
description: Azure Powershell을 사용 하 여 Azure Cosmos DB 계정, 데이터베이스, 컨테이너 및 처리량을 관리 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: f720b678f2c7a6e564ef3e8fa9ae071b004ed1a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243398"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>PowerShell을 사용 하 여 Azure Cosmos DB SQL API 리소스 관리

다음 가이드에는 PowerShell 스크립트를 사용 하 여 Azure Cosmos DB 계정, 데이터베이스, 컨테이너, 처리량 등의 관리를 자동화 하는 방법을 설명 합니다. Azure Cosmos DB의 관리는 Azure Cosmos DB 전용 cmdlet을 통해 하지 않지만 AzResource cmdlet 통해 직접 리소스 공급자를 사용 하 여입니다. 모든 Azure Cosmos DB 리소스 공급자에 대 한 PowerShell을 사용 하 여 관리할 수 있는 속성을 보려면 참조 [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)

Azure Cosmos DB의 플랫폼 간 관리를 위해 사용할 수 있습니다 [Azure CLI](manage-with-cli.md)서 [REST API][rp-rest-api], 또는 [Azure portal](create-sql-api-dotnet.md#create-account)합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>시작하기

지침을 따릅니다 [Azure PowerShell 설치 및 구성 하는 방법] [ powershell-install-configure] 를 설치 하 여 Powershell에서 Azure 계정에 로그인 합니다.

* 사용자에게 확인을 요구하지 않고 다음 명령을 실행하려면 명령에 `-Force` 플래그를 추가합니다.
* 다음 명령은 모두 동기식입니다.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 계정

다음 섹션에서는 Azure Cosmos 계정을 관리 하는 방법을 보여 줍니다 포함:

* [Azure Cosmos 계정 만들기](#create-account)
* [Azure Cosmos 계정 업데이트](#update-account)
* [Azure Cosmos 계정 가져오기](#get-account)
* [Azure Cosmos 계정 삭제](#delete-account)
* [Azure Cosmos 계정의 태그 업데이트](#update-tags)
* [Azure Cosmos 계정의 키 나열](#list-keys)
* [Azure Cosmos 계정의 키를 다시 생성](#regenerate-keys)
* [Azure Cosmos 계정의 연결 문자열 나열](#list-connection-strings)
* [Azure Cosmos 계정의 장애 조치 우선 순위 수정](#modify-failover-priority)

### <a id="create-account"></a> Azure Cosmos 계정 만들기

이 명령을 사용하면 Azure Cosmos DB 데이터베이스 계정을 만들 수 있습니다. 새 데이터베이스 계정을 특정 [일관성 정책](consistency-levels.md)을 사용하여 단일 하위 지역 또는 [다중 하위 지역][distribute-data-globally]으로 구성합니다.

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
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
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Azure Cosmos 계정의 이름입니다. 허용 영숫자, 소문자 여야 하며 '-' 문자 및 3 자에서 50 자 사이입니다.
* `$location` Azure Cosmos 계정에 대 한 위치입니다.
* `$locations` 데이터베이스 계정에 대 한 복제본 영역입니다. 장애 조치 우선 순위 값이 0 사용 하 여 데이터베이스 계정 마다 쓰기 하위 지역 하나만 있어야 합니다.
* `$consistencyPolicy` Azure Cosmos 계정의 기본 일관성 수준입니다. 자세한 내용은 [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하세요.
* `$CosmosDBProperties` 속성 값을 계정을 프로 비전 하는 Cosmos DB Azure Resource Manager 공급자에 전달 합니다.

Azure Cosmos 가상 네트워크 뿐만 아니라 IP 방화벽을 사용 하 여 계정을 구성할 수 있습니다 서비스 끝점입니다. Azure Cosmos DB에 대 한 IP 방화벽을 구성 하는 방법에 대 한 자세한 내용은 [IP 방화벽을 구성](how-to-configure-firewall.md)합니다.  Azure Cosmos DB에 대 한 서비스 끝점을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [가상 네트워크에서 액세스를 구성](how-to-configure-vnet-service-endpoint.md) 합니다.

### <a id="get-account"></a> Azure Cosmos 계정의 속성을 가져옵니다.

이 명령은 사용 하면 기존 Azure Cosmos 계정의 속성을 가져올 수 있습니다.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Azure Cosmos 계정 업데이트

이 명령을 사용하면 Azure Cosmos DB 데이터베이스 계정 속성을 업데이트할 수 있습니다. 업데이트할 수 있는 속성은 다음과 같습니다.

* 추가 하거나 지역 제거
* 기본 일관성 정책 변경
* 장애 조치 정책 변경
* IP 범위 필터를 변경합니다.
* 가상 네트워크 구성 변경
* 다중 마스터를 사용 하도록 설정

> [!NOTE]
> 이 명령을 사용하면 하위 지역을 추가/제거할 수 있지만 장애 조치 우선 순위는 수정할 수 없습니다. 장애 조치 우선 순위를 수정 하려면 참조 [Azure Cosmos 계정의 장애 조치 우선 순위 수정](#modify-failover-priority)합니다.

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Azure Cosmos 계정 삭제

이 명령은 사용 하면 기존 Azure Cosmos 계정을 삭제할 수 있습니다.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Azure Cosmos 계정의 태그 업데이트

다음 예제에서는 설정 하는 방법에 설명 합니다 [Azure 리소스 태그] [ azure-resource-tags] Azure Cosmos 계정에 대 한 합니다.

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

Azure Cosmos 계정 액세스 키 연결을 더욱 안전 하 게 유지할 수 있도록 주기적으로 다시 생성 해야 합니다. 기본 및 보조 액세스 키를 계정에 할당 됩니다. 다른가 다시 생성 하는 동안 액세스를 유지 하기 위해 클라이언트가 있습니다. 네 가지 유형 (기본, 보조, PrimaryReadonly, 및 SecondaryReadonly)는 Azure Cosmos 계정에 대 한 키

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

다중 지역 데이터베이스 계정에 대 한 Azure Cosmos DB 데이터베이스 계정에 존재 하는 다양 한 지역 장애 조치 우선 순위를 변경할 수 있습니다. Azure Cosmos DB 데이터베이스 계정의 장애 조치(Failover)에 대한 자세한 내용은 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포][distribute-data-globally]를 참조하세요.

다음 예제에서는 westus의 현재 장애 조치 우선 순위에 따라 계정을 가정에 대 한 = 0, eastus = 1입니다. 아래 예제이에서는 지역 대칭 이동 합니다.

> [!CAUTION]
> 이 작업은 계정의 failoverPriority가 0을 사용 하 여 새 지역에 대 한 수동 장애 조치를 트리거합니다.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos 데이터베이스

다음 섹션에서는 Azure Cosmos 데이터베이스를 관리 하는 방법을 보여 줍니다 포함:

* [Azure Cosmos 데이터베이스 만들기](#create-db)
* [공유 처리량을 사용 하 여 Azure Cosmos 데이터베이스 만들기](#create-db-ru)
* [계정에 있는 모든 Azure Cosmos 데이터베이스 나열](#get-all-db)
* [단일 Azure Cosmos 데이터베이스 가져오기](#get-db)
* [Azure Cosmos 데이터베이스를 삭제 합니다.](#delete-db)

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

### <a id="create-db-ru"></a>공유 처리량을 사용 하 여 Azure Cosmos 데이터베이스 만들기

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

### <a id="get-all-db"></a>계정에서 모든 Azure Cosmos 데이터베이스 가져오기

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

### <a id="delete-db"></a>Azure Cosmos 데이터베이스를 삭제 합니다.

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

다음 섹션에서는 Azure Cosmos 컨테이너를 관리 하는 방법을 보여 줍니다 포함:

* [Azure Cosmos 컨테이너 만들기](#create-container)
* [공유 처리량을 사용 하 여 Azure Cosmos 컨테이너 만들기](#create-container-ru)
* [사용자 지정 인덱싱을 사용 하 여 Azure Cosmos 컨테이너 만들기](#create-container-custom-index)
* [해제 인덱싱을 사용 하 여 Azure Cosmos 컨테이너 만들기](#create-container-no-index)
* [고유 키 및 TTL을 사용 하 여 Azure Cosmos 컨테이너 만들기](#create-container-unique-key-ttl)
* [충돌 해결을 사용 하 여 Azure Cosmos 컨테이너 만들기](#create-container-lww)
* [데이터베이스의 모든 Azure Cosmos 컨테이너를 나열 합니다.](#list-all-container)
* [데이터베이스에서 단일 Azure Cosmos 컨테이너 가져오기](#get-container)
* [Azure Cosmos 컨테이너를 삭제 합니다.](#delete-container)

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

### <a id="create-container-ru"></a>공유 처리량을 사용 하 여 Azure Cosmos 컨테이너 만들기

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

### <a id="create-container-custom-index"></a>사용자 지정 인덱스 정책을 사용 하 여 Azure Cosmos 컨테이너 만들기

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
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
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

### <a id="create-container-no-index"></a>해제 인덱싱을 사용 하 여 Azure Cosmos 컨테이너 만들기

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

### <a id="create-container-unique-key-ttl"></a>고유 키 정책 및 TTL을 사용 하 여 Azure Cosmos 컨테이너 만들기

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
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
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

### <a id="create-container-lww"></a>충돌 해결을 사용 하 여 Azure Cosmos 컨테이너 만들기

만들려면 저장된 프로시저를 사용 하는 충돌 해결 정책 설정 `"mode"="custom"` 저장된 프로시저의 이름으로 확인 경로 설정 하 고 `"conflictResolutionPath"="myResolverStoredProcedure"`입니다. 모든 충돌을 ConflictsFeed 쓸 개별적으로 처리 하 고 설정 `"mode"="custom"` 및 `"conflictResolutionPath"=""`

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

### <a id="list-all-container"></a>데이터베이스의 모든 Azure Cosmos 컨테이너를 나열 합니다.

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

### <a id="get-container"></a>데이터베이스에서 단일 Azure Cosmos 컨테이너 가져오기

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

### <a id="delete-container"></a>Azure Cosmos 컨테이너를 삭제 합니다.

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
* [Azure Cosmos 계정을 관리 하는 방법](how-to-manage-database-account.md)
* [Azure Cosmos 컨테이너 만들기](how-to-create-container.md)
* [Azure Cosmos DB의 활성 시간을 구성](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/