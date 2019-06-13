---
title: C#을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기
description: C#을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법을 알아봅니다.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e51551d4ce8061122fce52b05e68e102b71c27a8
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494601"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>C#을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음, 데이터베이스에 대해 쿼리를 실행할 수 있도록 데이터베이스에 데이터를 수집(로드)합니다. 이 문서에서는 클러스터 및 데이터베이스를 사용 하 여 만든 C#입니다.

## <a name="prerequisites"></a>필수 조건

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="install-c-nuget"></a>C# nuget 설치

1. [Azure Data Explorer(Kusto) nuget 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)를 설치합니다.

1. 인증에 사용할 [Microsoft.IdentityModel.Clients.ActiveDirectory nuget 패키지](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)를 설치합니다.

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터 만들기

1. 다음 코드를 사용하여 클러스터를 만듭니다.

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | clusterName | *mykustocluster* | 원하는 클러스터 이름입니다.|
   | sku | *D13_v2* | 클러스터에 사용될 SKU입니다. |
   | resourceGroupName | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |

    사용 가능한 선택적 매개 변수(예: 클러스터 용량)가 추가로 있습니다.

1. [자격 증명](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)을 설정합니다.

1. 다음 명령을 실행하여 클러스터가 성공적으로 만들어졌는지 확인합니다.

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

결과에 값이 `Succeeded`인 `ProvisioningState`가 있으면 클러스터가 성공적으로 만들어진 것입니다.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터에 데이터베이스 만들기

1. 다음 코드를 사용하여 데이터베이스를 만듭니다.

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | clusterName | *mykustocluster* | 데이터베이스가 만들어지는 클러스터의 이름입니다.|
   | databaseName | *mykustodatabase* | 데이터베이스의 이름입니다.|
   | resourceGroupName | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | softDeletePeriod | *3650:00:00:00* | 데이터를 쿼리할 수 있도록 유지되는 시간입니다. |
   | hotCachePeriod | *3650:00:00:00* | 데이터가 캐시에 유지되는 시간입니다. |

2. 다음 명령을 실행하여 직접 만든 데이터베이스를 살펴봅니다.

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

이제 클러스터와 데이터베이스가 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

* 다른 문서를 수행 하려는 경우 사용자가 만든 리소스를 유지 합니다.
* 리소스를 정리하려면 클러스터를 삭제합니다. 클러스터를 삭제하면 해당 클러스터에 있는 모든 데이터베이스도 함께 삭제됩니다. 다음 명령을 사용하여 클러스터를 삭제합니다.

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer .NET Standard SDK(미리 보기)를 사용하여 데이터 수집](net-standard-ingest-data.md)
