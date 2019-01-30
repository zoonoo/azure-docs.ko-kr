---
title: 업데이트된 Azure Batch AI API로 마이그레이션 | Microsoft Docs
description: 작업 영역 및 실험 리소스를 사용하기 위한 Azure Batch AI 코드 및 스크립트 업데이트 방법
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 75a9a5e9bafd62b320397c00ef6574b7536d9e09
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407783"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>업데이트된 Batch AI API로 마이그레이션

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Batch AI REST API 2018-05-01 버전 및 관련 Batch AI SDK 및 도구에서 주요 변경 사항 및 새로운 기능이 도입되었습니다.

이전 버전의 Batch AI API를 사용한 경우 이 문서에서는 새 API와 함께 작동하기 위해 코드와 스크립트를 수정하는 방법을 설명합니다. 

## <a name="whats-changing"></a>변경 내용은?

고객 피드백에 대한 대응에서 작업 수에 제한을 제거하고 Batch AI 리소스 관리를 더욱 쉽게 만들고 있습니다. 새 API는 *작업 영역* 및 *실험*이라는 두 개의 새 리소스를 도입합니다. 실험 아래 관련 교육 작업을 수집하고, 작업 영역에서 모든 관련 Batch AI 리소스(클러스터, 파일 서버, 실험, 작업)를 구성합니다.  

* **작업 영역** - 모든 유형의 Batch AI 리소스의 최상위 컬렉션입니다. 클러스터와 파일 서버는 작업 영역에 포함됩니다. 작업 영역은 대개 다른 그룹이나 프로젝트에 속한 작업을 구분합니다. 예를 들어 개발 및 테스트 작업 영역이 있을 수 있습니다. 아마도 구독 당 작업 영역 수를 제한할 필요가 있습니다. 

* **실험** - 함께 관리되고 쿼리될 수 있는 관련 작업의 컬렉션입니다. 예를 들어 실험을 사용하여 하이퍼 매개 변수 튜닝 스윕의 일부로 수행되는 모든 작업을 그룹화합니다. 

다음 그림은 리소스 계층의 예를 보여 줍니다. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>기존 리소스 모니터링 및 관리
이미 Batch AI 클러스터, 작업 또는 파일 서버를 만든 각 리소스 그룹에서 Batch AI 서비스는 `migrated-<region>`(예를 들어 `migrated-eastus`)이라는 작업 영역 및 `migrated`라는 실험을 만듭니다. 이전에 만든 작업, 클러스터 또는 파일 서버에 액세스하려면 마이그레이션된 작업 영역 및 실험을 사용해야 합니다. 

### <a name="portal"></a>포털 
포털을 사용하여 이전에 만든 작업, 클러스터 또는 파일 서버에 액세스하려면 먼저 `migrated-<region>` 작업 영역을 선택합니다. 작업 영역을 선택한 후 클러스터 크기 조정이나 삭제 및 작업 상태와 출력 보기 등의 작업을 수행합니다. 

### <a name="sdks"></a>SDK 
Batch AI SDK를 통해 작업, 클러스터 또는 이전에 만든 파일 서버에 액세스하려면 작업 영역 이름 및 실험 이름 매개 변수를 제공합니다. 

Python SDK를 사용하는 경우 관련 변경 내용은 다음 예제에 표시됩니다. 변경 내용은 다른 Batch AI SDK에서도 유사합니다. 


#### <a name="get-old-cluster"></a>이전 클러스터 가져오기 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>이전 클러스터 삭제 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>이전 파일 서버 가져오기 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>이전 파일 서버 삭제  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>이전 작업 가져오기 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>이전 작업 삭제

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Azure CLI를 사용하여 이전에 만든 작업, 클러스터 또는 파일 서버에 액세스하는 경우 `-w` 및 `-e` 매개 변수를 사용하여 작업 영역 및 실험 이름을 제공합니다. 


#### <a name="get-old-cluster"></a>이전 클러스터 가져오기

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>이전 클러스터 삭제 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>이전 파일 서버 가져오기

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>이전 파일 서버 삭제 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>이전 작업 가져오기

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>이전 작업 삭제 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Batch AI 리소스 만들기 
 
기존 Batch AI SDK 중 하나를 사용하는 경우 코드 변경을 하지 않고 Batch AI 리소스(작업, 클러스터 또는 파일 서버)를 만드는 데 계속 사용할 수 있습니다. 그러나 새 SDK로 업그레이드한 후 다음과 같이 변경할 필요가 있습니다.
 
### <a name="create-workspace"></a>작업 영역 만들기 
시나리오에 따라 포털 또는 CLI를 통해 일회성 작업 영역을 수동으로 만들 수 있습니다. CLI를 사용하는 경우 다음 명령을 사용하여 작업 영역을 만듭니다. 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>실험 만들기 


시나리오에 따라 포털 또는 CLI를 통해 일회성 실험을 수동으로 만들 수 있습니다. CLI를 사용하는 경우 다음 명령을 사용하여 실험을 만듭니다. 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>클러스터, 파일 서버 및 작업 만들기
 
포털을 사용하여 작업, 클러스터 또는 파일 서버를 만드는 경우 포털은 실험 생성 동안 안내를 맡아 작업 영역 이름 및 실험 이름 매개 변수를 제공합니다.

업데이트된 SDK를 통해 작업, 클러스터 또는 파일 서버를 만들려면 작업 영역 이름 매개 변수를 제공합니다. Python SDK를 사용하는 경우 관련 변경 내용은 다음 예제에 표시됩니다. *workspace_name* 및 *experiment_name*을 이전에 만든 작업 영역 및 실험으로 대체합니다. 변경 내용은 다른 Batch AI SDK에서도 유사합니다. 


#### <a name="create-cluster"></a>클러스터 만들기 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>파일 서버 만들기 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>작업 만들기 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>다음 단계

* 다음 Batch AI API 참조를 참조하세요. [CLI](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai), [REST](/rest/api/batchai)