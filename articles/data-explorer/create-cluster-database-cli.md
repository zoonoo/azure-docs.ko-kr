---
title: CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기
description: 이 문서에서는 Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법을 설명합니다.
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812687"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기

이 문서에서는 Azure CLI를 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법을 설명합니다.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>관리 평면과 데이터 평면 API 간의 차이점

API 라이브러리로는 관리 평면 API와 데이터 평면 API가 있습니다.
관리 API는 클러스터 만들기, 데이터베이스 만들기, 데이터 연결 삭제, 인스턴스 수 변경 등의 리소스 관리에 사용됩니다. 데이터 평면 API는 데이터와 상호 작용하고, 쿼리를 실행하고, 데이터를 수집하는 등의 용도로 사용됩니다.

## <a name="configure-the-cli-parameters"></a>CLI 매개 변수 구성

계정에 로그인

```Bash
az login
```

클러스터를 만들려는 구독을 설정합니다.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터 만들기

다음 명령을 사용하여 클러스터를 만듭니다.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

다음 값을 제공합니다.

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | 이름 | *azureclitest* | 원하는 클러스터 이름입니다.|
   | sku | *D13_v2* | 클러스터에 사용될 SKU입니다. |
   | resource-group | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |

원한다면 클러스터 용량 같은 추가적인 선택적 매개 변수를 사용할 수 있습니다.

클러스터가 성공적으로 만들어졌는지 확인하려면 다음 명령을 실행합니다.

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

결과에 값이 "Succeeded"인 "provisioningState"가 있으면 클러스터가 성공적으로 만들어진 것입니다.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터에 데이터베이스 만들기

다음 명령을 사용하여 데이터베이스를 만듭니다.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

다음 값을 제공합니다.

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | cluster-name | *azureclitest* | 데이터베이스가 만들어질 클러스터의 이름입니다.|
   | 이름 | *clidatabase* | 원하는 데이터베이스 이름입니다.|
   | resource-group | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | soft-delete-period | *3650:00:00:00* | 쿼리에 사용할 수 있도록 데이터를 유지해야 하는 시간입니다. |
   | hot-cache-period | *3650:00:00:00* | 데이터를 캐시에 유지해야 하는 시간입니다. |

다음 명령을 실행하여 자신이 만든 데이터베이스를 볼 수 있습니다.

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

이제 클러스터와 데이터베이스가 생겼습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작 및 자습서를 진행하려는 경우 만든 리소스를 그대로 둡니다.

클러스터를 삭제하면 해당 클러스터에 있는 모든 데이터베이스도 함께 삭제됩니다. 클러스터를 삭제하려면 아래 명령을 사용하세요.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 이벤트 허브에서 Azure Data Explorer로 데이터 수집](ingest-data-event-hub.md)
