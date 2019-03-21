---
title: '빠른 시작: CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기'
description: Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법 알아보기
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: ca4ffce1ae966d2bb8fb187c9b04938330969641
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286332"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-the-cli"></a>CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

로컬에서 Azure CLI를 설치하여 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 확인합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="configure-the-cli-parameters"></a>CLI 매개 변수 구성

Azure Cloud Shell에서 명령을 실행하는 경우에는 다음 단계가 필요하지 않습니다. 로컬에서 CLI를 실행하는 경우 다음 단계에 따라 Azure에 로그인하고 현재 구독을 설정합니다.

1. 다음 명령을 실행하여 Azure에 로그인합니다.

    ```azurecli-interactive
    az login
    ```

2. 클러스터를 만들려는 구독을 설정합니다. `MyAzureSub`를 사용할 Azure 구독의 이름으로 바꿉니다.

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터 만들기

1. 다음 명령을 사용하여 클러스터를 만듭니다.

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | 이름 | *azureclitest* | 원하는 클러스터 이름입니다.|
   | sku | *D13_v2* | 클러스터에 사용될 SKU입니다. |
   | resource-group | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |

    사용 가능한 선택적 매개 변수(예: 클러스터 용량)가 추가로 있습니다.

2. 다음 명령을 실행하여 클러스터가 성공적으로 만들어졌는지 확인합니다.

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

결과에 값이 `Succeeded`인 `provisioningState`가 있으면 클러스터가 성공적으로 만들어진 것입니다.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터에 데이터베이스 만들기

1. 다음 명령을 사용하여 데이터베이스를 만듭니다.

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | cluster-name | *azureclitest* | 데이터베이스가 만들어지는 클러스터의 이름입니다.|
   | 이름 | *clidatabase* | 데이터베이스의 이름입니다.|
   | resource-group | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | soft-delete-period | *3650:00:00:00* | 데이터를 쿼리할 수 있도록 유지되는 시간입니다. |
   | hot-cache-period | *3650:00:00:00* | 데이터가 캐시에 유지되는 시간입니다. |

2. 다음 명령을 실행하여 직접 만든 데이터베이스를 살펴봅니다.

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

이제 클러스터와 데이터베이스가 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

* 다른 빠른 시작 및 자습서를 진행하려는 경우 만든 리소스를 그대로 둡니다.
* 리소스를 정리하려면 클러스터를 삭제합니다. 클러스터를 삭제하면 해당 클러스터에 있는 모든 데이터베이스도 함께 삭제됩니다. 다음 명령을 사용하여 클러스터를 삭제합니다.

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: Azure Data Explorer Python 라이브러리를 사용하여 데이터 수집](python-ingest-data.md)
