---
title: Synapse 및 Azure Machine Learning 작업 영역 (미리 보기)을 사용 하 여 연결 된 서비스 만들기
titleSuffix: Azure Machine Learning
description: 통합 데이터 랭 글 링 환경에 대 한 Azure Synapse 및 Azure Machine Learning 작업 영역을 연결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: dd62fb5e3c7450d50b9837ee5484ca480cab78aa
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640838"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Azure Synapse Analytics 및 Azure Machine Learning 작업 영역 연결 (미리 보기)

이 문서에서는 [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) 작업 영역 및 [Azure Machine Learning 작업 영역](concept-workspace.md)을 연결 하는 연결 된 서비스를 만드는 방법에 대해 알아봅니다.

Azure Synapse 작업 영역에 연결 된 Azure Machine Learning 작업 영역을 사용 하 여 Apache Spark 풀을 규모의 데이터 랭 글 링 전용 계산으로 연결 하 고 동일한 노트북에서 모델 학습을 수행할 수 있습니다.

[PYTHON SDK](#link-sdk) 또는 [Azure Machine Learning STUDIO](#link-studio)를 통해 ML 작업 영역 및 Synapse 작업 영역을 연결할 수 있습니다.

작업 영역을 연결 하 고 단일 [Azure Resource Manager (ARM) 템플릿을](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)사용 하 여 Synapse Spark 풀을 연결할 수도 있습니다.

>[!IMPORTANT]
> Azure Machine Learning 및 Azure Synapse 통합은 공개 미리 보기로 제공 됩니다. 패키지에서 제공 되는 `azureml-synapse` 기능은 [실험적](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능 이며 언제 든 지 변경 될 수 있습니다.

## <a name="prerequisites"></a>전제 조건

* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md?tabs=python).

* [Azure Portal에서 Synapse 작업 영역을 만듭니다](/azure/synapse-analytics/quickstart-create-workspace).

* [Azure Portal, 웹 도구 또는 Synapse Studio를 사용 하 여 Apache Spark 풀 만들기](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/intro) 를 설치 합니다.

* [Azure Machine Learning studio](https://ml.azure.com/)에 액세스 합니다.

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Python SDK를 사용 하 여 작업 영역 연결

> [!IMPORTANT]
> Synapse 작업 영역에 성공적으로 연결 하려면 Synapse 작업 영역의 **소유자** 역할을 부여 받아야 합니다. [Azure Portal](https://ms.portal.azure.com/)에서 액세스를 확인 합니다.
>
> **소유자** 가 아니고 Synapse 작업 영역에 대 한 **기여자** 인 경우에는 기존 연결 된 서비스만 사용할 수 있습니다. [기존 연결 된 서비스](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)를 검색 하 고 사용 하는 방법을 참조 하세요.

다음 코드는 [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) 및 클래스를에 채택 합니다. [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration)

* Azure Synapse 작업 영역을 사용 하 여 machine learning 작업 영역을 연결 `ws` 합니다.
* Azure Machine Learning에 연결 된 서비스로 Synapse 작업 영역을 등록 합니다.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> `system_assigned_identity_principal_id`연결 된 각 서비스에 대해 관리 id가 만들어집니다. Synapse 세션을 시작 하기 전에이 관리 되는 id에 Synapse 작업 영역의 **Synapse Apache Spark 관리자** 역할을 부여 해야 합니다. [Synapse Studio에서 관리 되는 id에 Synapse Apache Spark Administrator 역할을 할당](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)합니다.
>
> `system_assigned_identity_principal_id`특정 연결 된 서비스의를 찾으려면를 사용 `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` 합니다.

### <a name="manage-linked-services"></a>연결 된 서비스 관리

Machine learning 작업 영역과 연결 된 모든 연결 된 서비스를 확인 합니다.

```python
LinkedService.list(ws)
```

작업 영역 연결을 해제 하려면 메서드를 사용 합니다. `unregister()`

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Studio를 통해 작업 영역 연결

다음 단계를 사용 하 여 Azure Machine Learning studio를 통해 machine learning 작업 영역 및 Synapse 작업 영역을 연결 합니다. 

1. [Azure Machine Learning studio](https://ml.azure.com/)에 로그인 합니다.
1. 왼쪽 창의 **관리** 섹션에서 **연결 된 서비스** 를 선택 합니다.
1. **통합 추가** 를 선택 합니다.
1. **링크 작업 영역** 폼에서 필드를 채웁니다.

    |필드| 설명    
    |---|---
    |이름| 연결 된 서비스의 이름을 제공 합니다. 이 이름은이 특정 연결 된 서비스를 참조 하는 데 사용 됩니다.
    |구독 이름 | Machine learning 작업 영역에 연결 된 구독의 이름을 선택 합니다. 
    |Synapse 작업 영역 | 연결 하려는 Synapse 작업 영역을 선택 합니다.
    
1. **다음** 을 선택 하 여 **Spark 풀 선택 (선택 사항)** 양식을 엽니다. 이 양식에서 작업 영역에 연결할 Synapse Spark 풀을 선택 합니다.

1. **다음** 을 선택 하 여 **검토** 양식을 열고 선택 내용을 확인 합니다.
1. **만들기** 를 선택 하 여 연결 된 서비스 만들기 프로세스를 완료 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse (미리 보기)를 사용 하 여 데이터 준비를 위해 Synapse Spark 풀을 연결](how-to-data-prep-synapse-spark-pool.md)합니다.
* [Azure Synapse (미리 보기)를 사용 하 여 machine learning 파이프라인에서 Apache Spark를 사용 하는 방법](how-to-use-synapsesparkstep.md)
* [모델을 학습](how-to-set-up-training-targets.md)합니다.
