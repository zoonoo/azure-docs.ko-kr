---
title: Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기
description: Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법 알아보기
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e771def95db00b5de8c27011641a628560952970
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494797"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음, 데이터베이스에 대해 쿼리를 실행할 수 있도록 데이터베이스에 데이터를 수집(로드)합니다. 이 문서에서는 클러스터 및 데이터베이스를 사용 하 여 만든 Azure CLI.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료 하려면 Azure 구독이 필요 합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 문서에서는 Azure CLI 버전 2.0.4 설치 하 고 Azure CLI를 로컬로 사용 하려는 경우 이상. `az --version`을 실행하여 버전을 확인합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="configure-the-cli-parameters"></a>CLI 매개 변수 구성

Azure Cloud Shell에서 명령을 실행하는 경우에는 다음 단계가 필요하지 않습니다. 로컬에서 CLI를 실행하는 경우 다음 단계에 따라 Azure에 로그인하고 현재 구독을 설정합니다.

1. 다음 명령을 실행하여 Azure에 로그인합니다.

    ```azurecli-interactive
    az login
    ```

1. 클러스터를 만들려는 구독을 설정합니다. `MyAzureSub`를 사용할 Azure 구독의 이름으로 바꿉니다.

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
   | name | *azureclitest* | 원하는 클러스터 이름입니다.|
   | sku | *D13_v2* | 클러스터에 사용될 SKU입니다. |
   | resource-group | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |

    사용 가능한 선택적 매개 변수(예: 클러스터 용량)가 추가로 있습니다.

1. 다음 명령을 실행하여 클러스터가 성공적으로 만들어졌는지 확인합니다.

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

결과에 값이 `Succeeded`인 `provisioningState`가 있으면 클러스터가 성공적으로 만들어진 것입니다.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터에 데이터베이스 만들기

1. 다음 명령을 사용하여 데이터베이스를 만듭니다.

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | cluster-name | *azureclitest* | 데이터베이스가 만들어지는 클러스터의 이름입니다.|
   | name | *clidatabase* | 데이터베이스의 이름입니다.|
   | resource-group | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | soft-delete-period | *P365D* | 데이터를 쿼리할 수 있도록 유지해야 하는 시간을 나타냅니다. 자세한 내용은 [보존 정책](/azure/kusto/concepts/retentionpolicy)을 참조하세요. |
   | hot-cache-period | *P31D* | 데이터가 캐시에 유지되는 시간을 나타냅니다. 자세한 내용은 [캐시 정책](/azure/kusto/concepts/cachepolicy)을 참조하세요. |

1. 다음 명령을 실행하여 직접 만든 데이터베이스를 살펴봅니다.

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

이제 클러스터와 데이터베이스가 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

* 다른 문서를 수행 하려는 경우 사용자가 만든 리소스를 유지 합니다.
* 리소스를 정리하려면 클러스터를 삭제합니다. 클러스터를 삭제하면 해당 클러스터에 있는 모든 데이터베이스도 함께 삭제됩니다. 다음 명령을 사용하여 클러스터를 삭제합니다.

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer Python 라이브러리를 사용하여 데이터 수집](python-ingest-data.md)
