---
title: "Azure Cosmos DB 자동화 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 Azure Cosmos DB 계정을 만들고 관리합니다. Azure Cosmos DB는 가용성이 매우 뛰어난, 전역적으로 분산된 데이터베이스입니다."
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.custom: quick start create
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6150914e623cb6c54f1257c772be62150ab81e17
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos DB 계정 만들기

다음 가이드에서는 Azure CLI 2.0에서 사용할 수 있는 미리 보기 명령을 사용하여 Azure Cosmos DB 데이터베이스 계정 관리를 자동화하는 명령에 대해 설명합니다. [다중 하위 지역 데이터베이스 계정][scaling-globally]에서 계정 키 및 장애 조치 우선 순위를 관리하는 명령도 포함되어 있습니다. 데이터베이스 계정을 업데이트하면 일관성 정책을 수정하고 하위 지역을 추가/제거할 수 있습니다. Azure Cosmos DB 데이터베이스 계정의 플랫폼 간 관리를 위해 [Azure Powershell](documentdb-manage-account-with-powershell.md), [리소스 공급자 REST API][rp-rest-api] 또는 [Azure Portal](documentdb-create-account.md)을 사용할 수 있습니다.

## <a name="getting-started"></a>시작

[Azure CLI 2.0 설치 및 구성 방법][install-az-cli2]의 지침에 따라 Azure CLI 2.0을 사용하여 개발 환경을 설정합니다.

다음 명령을 실행하고 화면에 나타나는 단계를 따라 Azure 계정에 로그인합니다.

```azurecli
az login
```

기존 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)이 없는 경우 리소스 그룹을 만듭니다.

```azurecli
az group create --name <resourcegroupname> --location <resourcegrouplocation>
az group list
```

`<resourcegrouplocation>`은 Azure Cosmos DB를 일반적으로 사용할 수 있는 하위 지역 중 하나여야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.

### <a name="notes"></a>참고 사항

* 'az documentdb -h'를 실행하여 사용할 수 있는 명령의 전체 목록을 가져오거나 [참조 페이지][az-documentdb-ref]로 이동합니다.
* 'az documentdb &lt;command&gt; -h'를 실행하여 명령 당 필수 및 선택적 매개 변수의 세부 정보 목록을 가져옵니다.

## <a name="register-your-subscription-to-use-azure-cosmos-db"></a>Azure Cosmos DB를 사용하기 위해 구독 등록

이 명령은 CLI 통해 Azure Cosmos DB를 사용할 수 있도록 구독을 등록합니다.

```azurecli
az provider register -n Microsoft.DocumentDB 
```

## <a id="create-documentdb-account-cli"></a> Azure Cosmos DB 데이터베이스 계정 만들기

이 명령을 사용하면 Azure Cosmos DB 데이터베이스 계정을 만들 수 있습니다. 새 데이터베이스 계정을 특정 [일관성 정책](documentdb-consistency-levels.md)을 사용하여 단일 하위 지역 또는 [다중 하위 지역][scaling-globally]으로 구성합니다.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account. The account 
                                    name must be unique.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of Azure Cosmos DB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

```azurecli
az documentdb create -g <resourcegroupname> -n <uniquedocumentdbaccountname> --kind <typeofdatabaseaccount>
```

예제: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>참고 사항 
* 위치는 Azure Cosmos DB를 일반적으로 사용할 수 있는 하위 지역이어야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.
* 포털 액세스를 사용하도록 설정하려면 [IP 액세스 제어 정책 구성](documentdb-firewall-support.md#configure-ip-policy)에 지정된 대로 ip-range-filter에 사용자의 하위 지역에 대한 Azure Portal의 IP 주소를 포함합니다.

## <a id="update-documentdb-account-cli"></a> Azure Cosmos DB 데이터베이스 계정 업데이트

이 명령을 사용하면 Azure Cosmos DB 데이터베이스 계정 속성을 업데이트할 수 있습니다. 여기에는 일관성 정책과 데이터베이스 계정이 있는 위치가 포함됩니다.

> [!NOTE]
> 이 명령을 사용하면 하위 지역을 추가/제거할 수 있지만 장애 조치 우선 순위는 수정할 수 없습니다. 장애 조치 우선 순위를 수정하려면 [아래 예제](#modify-failover-priority-powershell)를 참조하세요.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

예제: 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a> Azure Cosmos DB 데이터베이스 계정에서 하위 지역 추가/제거

기존 Azure Cosmos DB 데이터베이스 계정에서 하위 지역을 추가하거나 제거하려면 `--locations` 플래그가 있는 [업데이트](#update-documentdb-account-cli) 명령을 사용합니다. 아래 예제에서는 새 계정을 만들고 이후에 해당 계정에서 지역을 추가하고 제거하는 방법을 보여 줍니다.

예제:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Azure Cosmos DB 데이터베이스 계정 삭제

이 명령을 사용하면 기존 Azure Cosmos DB 데이터베이스 계정을 삭제할 수 있습니다.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

예제:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> Azure Cosmos DB 데이터베이스 계정의 속성 가져오기

이 명령을 사용하면 기존 Azure Cosmos DB 데이터베이스 계정의 속성을 가져올 수 있습니다.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

예제:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> 계정 키 나열

Azure Cosmos DB 계정을 만들면 해당 서비스에서 Azure Cosmos DB 계정에 액세스할 때 인증에 사용할 수 있는 2개의 마스터 액세스 키가 생성됩니다. Azure Cosmos DB에서는 2개의 액세스 키를 제공해서 사용자가 Azure Cosmos DB 계정에 대한 중단 없이 키를 다시 생성할 수 있습니다. 읽기 전용 작업을 인증하기 위한 읽기 전용 키도 사용할 수 있습니다. 두 개의 읽기-쓰기 키(기본 및 보조) 및 두 개의 읽기 전용 키(기본 및 보조)가 있습니다.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

예제:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> 연결 문자열 나열

MongoDB 계정의 경우 MongoDB 앱을 데이터베이스 계정에 연결하기 위한 연결 문자열은 다음 명령을 사용하여 검색할 수 있습니다.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

예제:

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> 계정 키 다시 생성

저장소 연결을 더욱 안전하게 유지할 수 있도록 정기적으로 Azure Cosmos DB 계정의 액세스 키를 변경해야 합니다. 두 개의 액세스 키가 할당되므로 액세스 키 하나를 다시 생성하는 동안 다른 액세스 키를 사용하여 Azure Cosmos DB 계정에 대한 연결을 유지할 수 있습니다.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

예제:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Azure Cosmos DB 데이터베이스 계정의 장애 조치(Failover) 우선 순위 수정

다중 하위 지역 데이터베이스 계정의 경우 Azure Cosmos DB 데이터베이스 계정이 있는 다양한 하위 지역의 장애 조치(Failover) 우선 순위를 변경할 수 있습니다. Azure Cosmos DB 데이터베이스 계정의 장애 조치(Failover)에 대한 자세한 내용은 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포](documentdb-distribute-data-globally.md)를 참조하세요.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

예제:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>다음 단계

* .NET을 사용하여 연결하려면 [.NET을 사용하여 연결 및 쿼리](../cosmos-db/create-documentdb-dotnet.md)를 참조하세요.
* .NET Core를 사용하여 연결하려면 [.NET Core를 사용하여 연결 및 쿼리](../cosmos-db/create-documentdb-dotnet-core.md)를 참조하세요.
* Node.js를 사용하여 연결하려면 [Node.js 및 MongoDB 앱을 사용하여 연결 및 쿼리](../cosmos-db/create-mongodb-nodejs.md)를 참조하세요.

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/

