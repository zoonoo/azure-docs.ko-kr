---
title: "DocumentDB 자동화 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 DocumentDB 데이터베이스 계정을 관리합니다. DocumentDB는 JSON 데이터에 대한 클라우드 기반 NoSQL 데이터베이스입니다."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b286a93d7cc5f962f969e877b2f487e56cbb1a95
ms.lasthandoff: 03/30/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Azure DocumentDB 계정 관리 자동화
> [!div class="op_single_selector"]
> * [Azure 포털](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

다음 가이드에서는 Azure CLI 2.0에서 사용할 수 있는 DocumentDB 미리 보기 명령을 사용하여 DocumentDB 데이터베이스 계정 관리를 자동화하는 명령에 대해 설명합니다. [다중 하위 지역 데이터베이스 계정][scaling-globally]에서 계정 키 및 장애 조치 우선 순위를 관리하는 명령도 포함되어 있습니다. 데이터베이스 계정을 업데이트하면 일관성 정책을 수정하고 하위 지역을 추가/제거할 수 있습니다. DocumentDB 데이터베이스 계정의 플랫폼 간 관리를 위해 [Azure Powershell](documentdb-manage-account-with-powershell.md), [리소스 공급자 REST API][rp-rest-api] 또는 [Azure Portal](documentdb-create-account.md)을 사용할 수 있습니다.

## <a name="getting-started"></a>시작

[Azure CLI 2.0 설치 및 구성 방법][install-az-cli2]의 지침에 따라 Azure CLI 2.0을 사용하여 개발 환경을 설정합니다.

다음 명령을 실행하고 화면에 나타나는 단계를 따라 Azure 계정에 로그인합니다.

    az login

기존 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)이 없는 경우 리소스 그룹을 만듭니다.

    az group create --name <resourcegroupname> --location <resourcegrouplocation>
    az group list

`<resourcegrouplocation>`은 DocumentDB를 일반적으로 사용할 수 있는 지역 중 하나여야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.

### <a name="notes"></a>참고 사항

* 'az documentdb -h'를 실행하여 사용할 수 있는 명령의 전체 목록을 가져오거나 [참조 페이지][az-documentdb-ref]로 이동합니다.
* 'az documentdb <command> -h'를 실행하여 명령 당 필수 및 선택적 매개 변수의 세부 정보 목록을 가져옵니다.

## <a id="create-documentdb-account-cli"></a> DocumentDB 데이터베이스 계정 만들기

이 명령을 사용하면 DocumentDB 데이터베이스 계정을 만들 수 있습니다. 새 데이터베이스 계정을 특정 [일관성 정책](documentdb-consistency-levels.md)을 사용하여 단일 하위 지역 또는 [다중 하위 지역][scaling-globally]으로 구성합니다. 

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
                                    To enable portal access, include 104.42.195.92.
    --kind                        : The type of DocumentDB database account to create.  Allowed
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

예제: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>참고 사항
* 위치는 일반적으로 DocumentDB를 사용할 수 있는 하위 지역이어야 합니다. 현재 하위 지역 목록은 [Azure 지역 페이지](https://azure.microsoft.com/regions/#services)에 제공됩니다.

## <a id="update-documentdb-account-cli"></a> DocumentDB 데이터베이스 계정 업데이트

이 명령을 사용하면 DocumentDB 데이터베이스 계정 속성을 업데이트할 수 있습니다. 여기에는 일관성 정책과 데이터베이스 계정이 있는 위치가 포함됩니다.

> [!NOTE]
> 이 명령을 사용하면 하위 지역을 추가/제거할 수 있지만 장애 조치 우선 순위는 수정할 수 없습니다. 장애 조치 우선 순위를 수정하려면 [아래 예제](#modify-failover-priority-powershell)를 참조하세요.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
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

## <a id="add-remove-region-documentdb-account-cli"></a> DocumentDB 데이터베이스 계정에서 지역 추가/제거

기존 DocumentDB 데이터베이스 계정에서 지역을 추가하거나 제거하려면 `--locations` 플래그가 있는 [업데이트](#update-documentdb-account-cli) 명령을 사용합니다. 아래 예제에서는 새 계정을 만들고 이후에 해당 계정에서 지역을 추가하고 제거하는 방법을 보여 줍니다.

예제:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> DocumentDB 데이터베이스 계정 삭제

이 명령을 사용하면 기존 DocumentDB 데이터베이스 계정을 삭제할 수 있습니다.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

예제:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> DocumentDB 데이터베이스 계정 속성 가져오기

이 명령을 사용하면 기존 DocumentDB 데이터베이스 계정의 속성을 가져올 수 있습니다.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

예제:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> 계정 키 나열

DocumentDB 계정을 만들면 해당 서비스에서 DocumentDB 계정에 액세스할 때 인증에 사용할 수 있는 2개의 마스터 액세스 키가 생성됩니다. DocumentDB에서는 2개의 액세스 키를 제공해서 사용자가 DocumentDB 계정에 대한 중단 없이 키를 다시 생성할 수 있습니다. 읽기 전용 작업을 인증하기 위한 읽기 전용 키도 사용할 수 있습니다. 두 개의 읽기-쓰기 키(기본 및 보조) 및 두 개의 읽기 전용 키(기본 및 보조)가 있습니다.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

예제:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> 계정 키 다시 생성

저장소 연결을 더욱 안전하게 유지할 수 있도록 정기적으로 DocumentDB 계정의 액세스 키를 변경해야 합니다. 두 개의 액세스 키가 할당되므로 액세스 키 하나를 다시 생성하는 동안 다른 액세스 키를 사용하여 DocumentDB 계정에 대한 연결을 유지할 수 있습니다.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

예제:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> DocumentDB 데이터베이스 계정의 장애 조치 우선 순위 수정

다중 하위 지역 데이터베이스 계정의 경우 DocumentDB 데이터베이스 계정이 있는 다양한 하위 지역의 장애 조치 우선 순위를 변경할 수 있습니다. DocumentDB 데이터베이스 계정의 장애 조치(Failover)에 대한 자세한 내용은 [DocumentDB로 데이터를 글로벌 배포](documentdb-distribute-data-globally.md)를 참조하세요.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

예:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>다음 단계
이제 DocumentDB 계정을 만들었으므로 다음 단계에서는 DocumentDB 데이터베이스를 만들게 됩니다. 다음 중 하나를 사용하여 데이터베이스를 만들 수 있습니다.

* [Azure Portal을 사용하여 DocumentDB 컬렉션 및 데이터베이스 만들기](documentdb-create-collection.md)에 설명된 Azure Portal
* GitHub에서 [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 리포지토리의 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 프로젝트에 있는 C# .NET 샘플.
* [DocumentDB SDK](documentdb-sdk-dotnet.md). DocumentDB에는 .NET, Java, Python, Node.js 및 JavaScript API SDK가 있습니다.

데이터베이스를 만든 후에 데이터베이스에 [하나 이상의 컬렉션을 추가](documentdb-create-collection.md)한 다음, 이 컬렉션에 [문서를 추가](documentdb-view-json-document-explorer.md)해야 합니다.


컬렉션에 문서를 추가한 후에 포털의 [쿼리 탐색기](documentdb-query-collections-query-explorer.md), [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 또는 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) 중 하나를 사용하여 문서에 [쿼리를 실행](documentdb-sql-query.md#ExecutingSqlQueries)하기 위해 [DocumentDB SQL](documentdb-sql-query.md)을 사용할 수 있습니다.

DocumentDB에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

* [DocumentDB 학습 경로](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [DocumentDB 리소스 모델 및 개념](documentdb-resources.md)


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/en-us/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/

