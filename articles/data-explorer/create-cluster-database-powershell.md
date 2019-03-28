---
title: '빠른 시작: PowerShell을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기'
description: PowerShell을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법을 알아봅니다.
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287384"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>PowerShell을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


이 빠른 시작에서는 PowerShell을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법에 대해 설명합니다.

Windows, Linux 또는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)에서 PowerShell cmdlet 및 스크립트를 실행하여 [Azure Data Explorer](https://docs.microsoft.com/azure/kusto/ )를 만들고 구성할 수 있습니다.

[**Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )입니다. Azure PowerShell 및 **Az.Kusto**를 사용하여 다음 작업을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

## <a name="configure-parameters"></a>매개 변수 구성

Azure Cloud Shell에서 명령을 실행하는 경우에는 다음 단계가 필요하지 않습니다. 로컬에서 CLI를 실행하는 경우 다음 단계에 따라 Azure에 로그인하고 현재 구독을 설정합니다.

1. 다음 명령을 실행하여 Azure에 로그인합니다.

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 클러스터를 만들려는 구독을 설정합니다.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Az.Kusto 모듈을 디바이스에 설치합니다.
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터 만들기

1. 다음 명령을 사용하여 클러스터를 만듭니다.

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | Name | *mykustocluster* | 원하는 클러스터 이름입니다.|
   | SKU | *D13_v2* | 클러스터에 사용될 SKU입니다. |
   | ResourceGroupName | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |

    사용 가능한 선택적 매개 변수(예: 클러스터 용량)가 추가로 있습니다.

2. 다음 명령을 실행하여 클러스터가 성공적으로 만들어졌는지 확인합니다.

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

결과에 값이 `Succeeded`인 `provisioningState`가 있으면 클러스터가 성공적으로 만들어진 것입니다.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터에 데이터베이스 만들기

1. 다음 명령을 사용하여 데이터베이스를 만듭니다.

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | ClusterName | *mykustocluster* | 데이터베이스가 만들어지는 클러스터의 이름입니다.|
   | Name | *mykustodatabase* | 데이터베이스의 이름입니다.|
   | ResourceGroupName | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | SoftDeletePeriod | *3650:00:00:00* | 데이터를 쿼리할 수 있도록 유지되는 시간입니다. |
   | HotCachePeriod | *3650:00:00:00* | 데이터가 캐시에 유지되는 시간입니다. |

2. 다음 명령을 실행하여 직접 만든 데이터베이스를 살펴봅니다.

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

이제 클러스터와 데이터베이스가 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

* 다른 빠른 시작 및 자습서를 진행하려는 경우 만든 리소스를 그대로 둡니다.
* 리소스를 정리하려면 클러스터를 삭제합니다. 클러스터를 삭제하면 해당 클러스터에 있는 모든 데이터베이스도 함께 삭제됩니다. 다음 명령을 사용하여 클러스터를 삭제합니다.

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>다음 단계

[**여기서**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ) 더 많은 Az.Kusto 명령을 찾을 수 있습니다.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Data Explorer .NET Standard SDK(미리 보기)를 사용하여 데이터 수집](net-standard-ingest-data.md)
