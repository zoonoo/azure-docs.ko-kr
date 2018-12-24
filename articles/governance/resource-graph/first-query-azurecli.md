---
title: Azure CLI를 사용하여 첫 번째 쿼리 실행
description: 이 문서에서는 Azure CLI에 대한 Resource Graph 확장을 사용하도록 설정하고 첫 번째 쿼리를 실행하는 단계를 안내합니다.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 8f5d98ff591cb456cbbcb3d28a63f39ab3729152
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308518"
---
# <a name="run-your-first-resource-graph-query-using-azure-cli"></a>Azure CLI를 사용하여 첫 번째 Resource Graph 쿼리 실행

Azure Resource Graph를 사용하는 첫 번째 단계는 [Azure CLI](/cli/azure/)용 확장이 설치되어 있는지 확인하는 것입니다. 이 빠른 시작에서는 Azure CLI 설치에 확장을 추가하는 과정을 안내합니다. Azure CLI가 로컬로 설치된 상태로 또는 [Azure Cloud Shell](https://shell.azure.com)을 통해 확장을 사용할 수 있습니다.

이 프로세스가 끝나면 선택한 Azure CLI 설치에 확장이 추가되고 첫 번째 Resource Graph 쿼리를 실행하게 됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="add-the-resource-graph-extension"></a>Resource Graph 확장 추가

Azure CLI를 사용하도록 설정하여 Azure Resource Graph를 쿼리하려면 해당 확장을 추가해야 합니다. 이 확장은 [Windows 10의 bash](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com)(독립형 및 포털 내부형), [Azure CLI Docker 이미지](https://hub.docker.com/r/microsoft/azure-cli/)를 포함하여 Azure CLI가 사용되거나 로컬로 설치될 수 있는 모든 위치에서 작동됩니다.

1. 최신 Azure CLI(최소 **2.0.45**)가 설치되어 있는지 확인하세요. 아직 설치되지 않은 경우 [다음 지침](/cli/azure/install-azure-cli-windows?view=azure-cli-latest)을 따릅니다.

1. 선택한 Azure CLI 환경에서 다음 명령을 사용하여 가져옵니다.

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. 확장이 설치되었고 필요한 버전인지(최소 **0.1.7**) 유효성을 검사합니다.

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>첫 번째 Resource Graph 실행

Azure CLI 확장이 선택한 환경에 추가되었으므로 간단한 Resource Graph 쿼리를 시도해 볼 수 있습니다. 쿼리는 각 리소스의 **이름** 및 **리소스 형식**와 함께 처음 5개 Azure 리소스를 반환합니다.

1. `graph` 확장 및 `query` 명령을 사용하여 첫 번째 Azure Resource Graph 쿼리를 실행합니다.

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다.

1. 다음과 같이 **Name** 속성에 대해 `order by`를 수행하도록 쿼리를 업데이트합니다.

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이렇게 하면 먼저 쿼리 결과가 제한된 후 정렬됩니다.

1. **Name** 속성에 대해 `order by`를 먼저 수행한 후 상위 5개 결과로 `limit`를 수행하도록 쿼리를 업데이트합니다.

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

최종 쿼리가 여러 번 실행되는 경우, 사용자 환경이 전혀 변경되지 않는다고 가정하면, 반환되는 결과는 일치하며 예상대로 **Name** 속성별로 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

## <a name="cleanup"></a>정리

Azure CLI 환경에서 Resource Graph 확장을 제거하려면 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

> [!NOTE]
> 이전에 다운로드한 확장 파일은 삭제되지 않습니다. 실행 중인 Azure CLI 환경에서만 제거됩니다.

## <a name="next-steps"></a>다음 단계

- [쿼리 언어](./concepts/query-language.md)에 대해 자세히 알아보기
- [리소스 탐색](./concepts/explore-resources.md)에 대해 알아보기
- [Azure PowerShell](first-query-powershell.md)로 첫 번째 쿼리 실행
- [시작 쿼리](./samples/starter.md) 샘플 보기
- [고급 쿼리](./samples/advanced.md) 샘플 참조
- [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)에서 사용자 의견 제공